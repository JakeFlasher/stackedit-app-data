
| Literal RD threshold (64x64x12) | Cache Miss Error (geomean) | Cache Latency Error (geomean) | IPC Error (geomean) | Avg Speedup | Avg Instr Reduction  |
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



![输入图片说明](https://raw.githubusercontent.com/JakeFlasher/stackedit-app-data/refs/heads/master/img/Camouflage/rd_hist.png) 
![输入图片说明](https://raw.githubusercontent.com/JakeFlasher/stackedit-app-data/refs/heads/master/img/Camouflage/rd_density.png)


1. 由于s
2. 对IPC和Cache影响小的指令probably是被删除后加速比提升也很小的指令 ：**加速比小，在仿真中latency和cycles数少，往往是data reuse (RD小)**
3. 删除指令对IPC和Cache的影响probably是相互独立的：**删除某些指令，对Cache影响很小但是对IPC影响巨大**

<!--stackedit_data:
eyJoaXN0b3J5IjpbNDMwNjk2NDgxLC0xNDU4NTk2ODMxLC0xNT
I1NTc0NDc0LDEyNDM2NTAyNzYsMTg2MzI1OTc5MywtNDg3MTgz
NTM5LC0xMzYyMzE4MDMsLTg3MjE2NzMsLTE5MTA5MjIxODMsMj
A5NjgwMDgyM119
-->