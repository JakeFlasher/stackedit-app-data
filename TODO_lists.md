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
	- gcc: inferendced SPEC17
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
	- inferenced SPEC17, SPEC06
	
- set model = ImputeFormer 
- 1,194,433
- set model = TimeMixer
- set model = TimesNet
- 36,721,669
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzI2MTM4NjYzLC0xMzQ3MjM0MzA5LC0yMD
QwOTM1NzYzLDIxMTUyMTA4NDgsMTY5NjczNjk2OCwtOTE1ODU4
MDMxLC01MDc2ODc4NjQsLTE2MjY0NDI5NTQsLTg0MDY0NzAyNy
wxODk0MjAwNTIxLDE2NDEwMjYyMzIsMTcxNTc1OTQwOSwxOTYz
MzA5ODY5LC04MjgzMTE1MTMsNDU4NjA1NTMzLDY3NDU5OTM5Ni
wyMzUyMTAzODEsLTU4ODIzMTM2MiwtNDE3MTQ5MDIsODkxMDM0
NThdfQ==
-->