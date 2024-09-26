


| Literal RD threshold (64x64x12) | Cac
he Miss Error (geomean) | Cache Latency Error (geomean) | IPC Error (geomean) | Avg Speedup | Avg Instr Reduction  |
|-------------------------|----------------------------|-------------------------------|---------------------|-------------|----------------------|
| 49152                   | 20.52766049                | 4.288978837                   | 8.119020266         | 112.643219  | 15.23203454          |
| 24576                   | 11.46614849                | 2.779277441                   | 9.258425954         | 110.4203876 | 14.11482383          |
| 12288                   | 5.401353645                | 1.937779051                   | 8.847979845         | 110.0942809 | 13.07478774          |
| 512                     | 2.509287692                | 0.857150494                   | 7.149924391         | 108.4858667 | 10.72975028          |
| 256                     | 2.829008832                | 0.794718217                   | 7.051488982         | 108.6694079 | 10.56544998          |
| 64                      | 2.941739265                | 0.677701461                   | 6.876926394         | 107.6435901 | 10.29106503          |

| RD Histogram Cut-off | Cache Miss Error (geomean) | Cache Latency Error (geomean) | IPC Error (geomean) | Avg Speedup | Avg Instr Reduction  |
|----------------------|-------------|---------------|-------------|-------------|----------------|
| 10th percentile      | 27.33536502 | 6.478540375   | 18.93003391 | 146.3384793 | 18.38060444    |
| 20th percentile      | 28.86031297 | 6.772132787   | 18.53796118 | 146.4992429 | 18.66504081    |
| 30th percentile      | 21.30779885 | 11.82680148   | 14.26130813 | 178.0835691 | 19.65638909    |
| 40th percentile      | 22.643441   | 12.30648968   | 25.36967507 | 193.2707652 | 20.50298836    |

| Sorted RD Density Cut-off          | Cache Miss Error (geomean) | Cache Latency Error (geomean) | IPC Error (geomean) | Avg Speedup | Avg Instr Reduction |
|------------------------------------|----------------------------|-------------------------------|---------------------|-------------|---------------------|
| 10th percentile total Instructions | 2.158186267                | 0.711853104                   | 3.668325697         | 105.1641335 | 5.780579489         |
| 20th percentile total Instructions | 3.31476966                 | 0.964130122                   | 6.826707658         | 106.1212914 | 9.390411534         |
| 30th percentile total Instructions | 5.809361025                | 1.397870322                   | 5.649304913         | 109.5223588 | 11.41986073         |
| 40th percentile total Instructions | 7.001040846                | 1.367277811                   | 10.33490331         | 117.0958631 | 13.68665289         |

 ![输入图片说明](https://raw.githubusercontent.com/JakeFlasher/stackedit-app-data/refs/heads/master/img/combined.png)


1. 由于所有的Dynamic Time Warping都基本位于0.18-0.35之间，与ground truth差距较小，差值主要跟instruction reduction有关，可以基本认为按照小RD删除指令对IPC Curve影响很小 
2. 对IPC和Cache影响小的指令probably是被删除后加速比提升也很小的指令 ：**加速比小，在仿真中latency和cycles数少，往往是data reuse (RD小)**
3. 删除指令对IPC和Cache的影响probably是相互独立的：**删除某些指令，对Cache影响很小但是对IPC影响巨大**


TODO:
4. rd范围和cache param关系（）
5. 2. 全局上删除，时间轴收缩可能不等比例， 平均ipc可能影响较大，局部删除，可能保存了两者等比例变化
6.   partitioned rd具有全局和局部的性质
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY1NjQyMDg2OCwtNjI1Nzc3NTUyLDExNz
M5NzU0NjEsLTE3OTA4NTY2MzgsLTE0NzM5MDI2OTIsLTE0NTg1
OTY4MzEsLTE1MjU1NzQ0NzQsMTI0MzY1MDI3NiwxODYzMjU5Nz
kzLC00ODcxODM1MzksLTEzNjIzMTgwMywtODcyMTY3MywtMTkx
MDkyMjE4MywyMDk2ODAwODIzXX0=
-->