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
- runed SPEC17,GAP (3 reduction rate)
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
	- 1,324,934
	- inferenced SPEC06, SPEC17 gap
	- cpded all (ongoing)
	- syned trace all: /champsim_traces/traces/syn_SAITS_0.5

- set model = TEFN
	- 552
	- inferenced SPEC17, SPEC06, GAP
	- cpded SPEC06(imputeformer_aster), SPEC17, GAP (gz)
	- 

-set model = ModernTCN
	- params:  475,049
	- inferenced spec06, gap, spec17
	- 
- set model = ImputeFormer 
	- 1,194,433
- set model = TimeMixer
	- 16,861,805
- set model = TimesNet
	- 36,721,669
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg4MjE3NzU5NSwyMDczNjY0MDA4LC0xMT
Y1OTU2MDYwLC0xNTg5OTg0Mjg0LDM1NjI5MTA0MywtMTEyMDgw
MDU1OCwtMjYyODkwNjg2LC0xNDk4NDY1OTgwLC0xNjM4MDQ1NT
kzLC0xOTM3NDc0Njc4LDE5Njk0Mjg0OCwxMDQ2NDA5ODk0LC0x
NDY2MjUyMjQxLC0xMzQ3MjM0MzA5LC0yMDQwOTM1NzYzLDIxMT
UyMTA4NDgsMTY5NjczNjk2OCwtOTE1ODU4MDMxLC01MDc2ODc4
NjQsLTE2MjY0NDI5NTRdfQ==
-->