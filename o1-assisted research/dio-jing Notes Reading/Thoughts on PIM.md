作者：Dio-晶  
链接：https://zhuanlan.zhihu.com/p/82392062  
来源：知乎  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。  
  

早上刷手机刚好看到winnie姐姐转发upmem的内容，对这个东西还算蛮熟，中秋月圆，借机刚一波。

in memory computing，简称PIM。

![](https://pica.zhimg.com/v2-0e1a752e3a1a5de5979cf135455c98f5_720w.jpg?source=d16d100b)
 
 
首先需要明确一下near memory computing和in memory computing的定义，这事我和业界灌水王onur mutlu吃饭还刚过一波，结论是：真理掌握在英语表达能力范围内。┐(‘～`；)┌

很多时候这是一个参照系问题，如果严格要以in为前缀，只有把memory cell本体对信号的模拟特性的变化用于计算才是真正的in，在cell旁边加MAC都不能算，这就很苛刻了，业界除了AI有一些其他都只能算near。

实际上这某些时候是视角问题，站在CPU的角度，内存条上的运算都是in memory，哪管那么多。如果按照DIE的纬度来看，HBM包含了多层DRAM和一层logic，PIM通常会把计算逻辑放在logic层，设计上也是near但从CPU角度看也是in memory。UPMEM其实只是更进一步，把逻辑直接放到了DRAM工艺上，最靠近CELL ARRAY的位置。算IN还是算NEAR呢？

而我的定义是：只有将原本MEM器件的bandwidth具有展宽机制的才算in-memoy。举例说，在HBM2带宽256GB，在logic DIE做计算如果还是按照HBM原本接口结构用到256GB带宽，那么还是near，如果打破了DRAM DIE原本结构和接口，引入更多TSV扩大了带宽，那么这就是in-memory了。UPMEM把计算单元放到了DRAM 每个CHIP内，比DIMM条原本DDR接口获得了更大带宽，我的认定是属于in-memoy computing！

定义完成了，讲骗人<(｀^´)>

事件任何技术都是有损益的，业界的PIM看上去除了技术难度没啥损失，那这么好的东西为啥没大量商用呢？

PIM最大的障碍是memory interleave，所有PIM的议题，如果在内存交织上避而不谈的，都归入骗子，不听不听，简单直接。

一个大SOC系统，内存都不是单一的，以DDR4-3200为例，一根DIMM条的带宽是25GB，那么全芯片的总带宽200GB是8个channel交织达成的。这是为了保证最大带宽效率，以及系统在多核下的共享。以INTEL为例，多个channel的地址是按照256B为粒度交织的，即4KB的数据会拆分成16份，每个DDR channel得2份，其中为了保证系统地址更加均匀，交织还会引入更高位地址打乱，即16份中的第0份并不会固定在channel-0。

所以，每个DIMM只能拿到连续数据的一部分，并且对于交织算法的不感知，DIMM甚至无法知道自己拿到了数据的什么部分。

绝大多数的应用，都会涉及到数据的连续性，例如SORT，是不能只对部分数据进行computing的。

所以，市面上的PIM都有一个潜台词是去掉interleave，但是为了表现PIM的先进性，在性能比较时，PIM都是忽略interleave，直接和一个巨大的无需交织的单个memory比较，而这样的memory并不存在。

如果系统去掉interleave，DDR CHANNEL就需要按照核分组或者业务分组来分配channel，按照操作系统理论，实际上需要引入额外的NUMA分层，这个损失在某些业务下是很悲惨的。所以，任何PIM的方案吹嘘，如果不敢直面interleave的问题，堂堂正正讲出来其性能收益大于去掉interleave的损伤，都是骗人的。

综述：在大型SOC系统中，CPU是分布式的，memory也是分布式的，总线互联把两者联和在一起，通常无法找到一个公共点能高效解决问题。

以UPMEM为例，为了使能其功能，就需要把某特定业务的数据放到一根DIMM，假设系统是8通道交织200GB，先不考虑CACHE一致性的损伤（PIM加速的数据需要FLUSH到内存），那这个单一业务去交织后就只能得到1/8的25GB带宽了，等价于使能PIM后至少需要获得大于8倍带宽的收益才是赚的，算一算，很难噢。当然这样比较也不是特别合适，如果有8个同构，size恰当，时间上并行度也很好业务，并不会带宽受损。

额外一说，UPMEM的方案是DIMM结构，其DIMM上包含了8颗独立的DRAM芯片，每一颗都只有1/8的容量、带宽和计算能力，业务数据依旧可能分割放在了多课DRAM芯片内。UPMEM亦需要额外的DRAM芯片间的通信才能完成一个完整的运算。
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA1NzE0NTAzLC01NTg1MzAzMTddfQ==
-->