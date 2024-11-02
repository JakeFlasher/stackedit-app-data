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
	- {gcc, aster, bfs10, mcf}.pypots	
	- aster: inferenced  SPEC06, gap
		- cpded: spec06 (skip them next time)
		- synced:
	- gcc: inferendced SPEC17, gap, spec06
		- cpded:
		- synced:
	- bfs-10 inferenced SPEC17, gap


- benchmarking = SPEC06, SPEC17
- simulation = 1 prefetcher

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model = SAITS
	- 1,324,934
	- inferenced SPEC06, SPEC17, gap
	- cpded all (ongoing)
	- syned trace all: /champsim_traces/traces/syn_SAITS_0.5
	- results
	
- set model = TEFN
	- 552
	- inferenced SPEC17, SPEC06, GAP
	- cpded SPEC06(imputeformer_aster), SPEC17, GAP (gz)
	- 

-set model = ModernTCN
	- params:  475,049
	- inferenced spec06, gap, spec17 (remainder spec17)
	- cpded
	- 
- set model = ImputeFormer 
	- 1,194,433
- set model = TimeMixer
	- broken for layer=4 (layer=2 can work)
	- 16,861,805
- set model = TimesNet
	- 36,721,669
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNDQ3MjYxMjQsMjAyNDA1OTczOCwxMz
U3NzE4MDksLTE5NTE3NDk2MjksNjEyNTI2NTQzLDEzODA2MTI3
NDksNDcxOTMxNDA0LDE3OTk0MDE4MTQsNjc2MjgxNTY0LC0xOD
A2MzE0NDU4LDE3MzUzNDMyNjUsMjA3MzY2NDAwOCwtMTE2NTk1
NjA2MCwtMTU4OTk4NDI4NCwzNTYyOTEwNDMsLTExMjA4MDA1NT
gsLTI2Mjg5MDY4NiwtMTQ5ODQ2NTk4MCwtMTYzODA0NTU5Mywt
MTkzNzQ3NDY3OF19
-->