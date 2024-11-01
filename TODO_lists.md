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
	
- set model = ImputeFormer 
- 1,194,433
- set model = TimeMixer
- set model = TimesNet
- 36,721,669
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM0NDkxNDE2NiwxMDQ2NDA5ODk0LC0xND
Y2MjUyMjQxLC0xMzQ3MjM0MzA5LC0yMDQwOTM1NzYzLDIxMTUy
MTA4NDgsMTY5NjczNjk2OCwtOTE1ODU4MDMxLC01MDc2ODc4Nj
QsLTE2MjY0NDI5NTQsLTg0MDY0NzAyNywxODk0MjAwNTIxLDE2
NDEwMjYyMzIsMTcxNTc1OTQwOSwxOTYzMzA5ODY5LC04MjgzMT
E1MTMsNDU4NjA1NTMzLDY3NDU5OTM5NiwyMzUyMTAzODEsLTU4
ODIzMTM2Ml19
-->