500 Ground truth for GAP, SPEC06, SPEC17
- 3 prefetchers
-
Enumerating Reduction Rate
- set model = application-specific ImputeFormer
- benchmarking = GAP, SPEC17
- simulation = all 3 prefetchers (done all spp, done berti,
	- done 0.75/0.5/0.25 spec/gap )

Enumerating training dataset on pre-trained models
- set model = ImputeFormer (application specific	)
- runed SPEC17,GAP
- trained SPEC06
- inferenced

- set model = ImputeFormer (pre-trained)
	- {gcc. xalanmb, aster, bfs10, mcf}.pypots	
	- aster: inferenced  SPEC06
	- gcc: inferendced SPEC17, gap, spec06
	- bfs-10 inferenced
- benchmarking = SPEC06, SPEC17
- simulation = 1 prefetcher

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model = SAITS
	- inferenced SPEC06, SPEC17 gap
	- cpded all (ongoing)
	- syned trace

- set model = TEFN
	- inferenced SPEC17, SPEC06, GAP
	- cpded SPEC06, SPEC17
	- syned trace

-set model = ModernTCN
	- params: 
	- inferenced spec06
	- 
- set model = ImputeFormer 
- 1,194,433
- set model = TimeMixer
- set model = TimesNet
- 36,721,669
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0OTg0NjU5ODAsLTE2MzgwNDU1OTMsLT
E5Mzc0NzQ2NzgsMTk2OTQyODQ4LDEwNDY0MDk4OTQsLTE0NjYy
NTIyNDEsLTEzNDcyMzQzMDksLTIwNDA5MzU3NjMsMjExNTIxMD
g0OCwxNjk2NzM2OTY4LC05MTU4NTgwMzEsLTUwNzY4Nzg2NCwt
MTYyNjQ0Mjk1NCwtODQwNjQ3MDI3LDE4OTQyMDA1MjEsMTY0MT
AyNjIzMiwxNzE1NzU5NDA5LDE5NjMzMDk4NjksLTgyODMxMTUx
Myw0NTg2MDU1MzNdfQ==
-->