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
	- syned trace all: /champsim_traces/traces/syn_SAITS_0.5

- set model = TEFN
	- 552
	- inferenced SPEC17, SPEC06, GAP
	- cpded SPEC06(imputeformer_aster), SPEC17, GAP (gz)
	- 

-set model = ModernTCN
	- params: 
	- inferenced spec06, gap, spec17
	- 
- set model = ImputeFormer 
- 1,194,433
- set model = TimeMixer
- set model = TimesNet
- 36,721,669
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY5OTM3OTAzMiwtMTE2NTk1NjA2MCwtMT
U4OTk4NDI4NCwzNTYyOTEwNDMsLTExMjA4MDA1NTgsLTI2Mjg5
MDY4NiwtMTQ5ODQ2NTk4MCwtMTYzODA0NTU5MywtMTkzNzQ3ND
Y3OCwxOTY5NDI4NDgsMTA0NjQwOTg5NCwtMTQ2NjI1MjI0MSwt
MTM0NzIzNDMwOSwtMjA0MDkzNTc2MywyMTE1MjEwODQ4LDE2OT
Y3MzY5NjgsLTkxNTg1ODAzMSwtNTA3Njg3ODY0LC0xNjI2NDQy
OTU0LC04NDA2NDcwMjddfQ==
-->