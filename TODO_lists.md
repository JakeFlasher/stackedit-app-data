500 Ground truth for GAP, SPEC06, SPEC17
- 3 prefetchers
-
Enumerating Reduction Rate
- set model = application-specific ImputeFormer
- benchmarking = GAP, SPEC17, SPEC06 (ongoinh)
	- GemsTD, astar
- simulation = all 3 prefetchers (done all spp, done berti,
	- done 0.75/0.5/0.25 spec/gap )

Enumerating training dataset on pre-trained models
- set model = ImputeFormer (application specific	)
- runed SPEC17,GAP (3 reduction rate)
- trained SPEC06 
- inferenced

- set model = ImputeFormer (pre-trained)
	- {gcc, aster, bfs10, mcf}.pypots	
	- aster: inferenced  SPEC06, gap, spec17
		- cpded: ???spec06 (skip them next time)
		- synced:
	- gcc: inferendced SPEC17, gap, spec06(ongoing partial)
		- cpded:
		- synced:
	- bfs-10 inferenced SPEC17, gap, spec06


- benchmarking = SPEC06, SPEC17
- simulation = 1 prefetcher

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model = SAITS
	- 1,324,934
	- inferenced SPEC06, SPEC17, gap
	- cpded all (ongoing)
	- syned trace all: /champsim_traces/traces/syn_SAITS_0.5
	- results: spp, bingo finished, berti rerunning
	
- set model = TEFN
	- 552
	- inferenced SPEC17, SPEC06, GAP
	- cpded ongoing SPEC06(imputeformer_aster), SPEC17, GAP (gz)
	- 

-set model = ModernTCN
	- params:  475,049
	- inferenced spec06, gap, spec17 (ongoing, partial, remainder spec17)
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
eyJoaXN0b3J5IjpbLTEyODM4ODg1NDcsOTczOTkwNTkxLDE0OD
YzMjg1MTEsMTk0ODk4OTU4MywxOTQ4OTg5NTgzLC0xNjkwNjQ3
NzcyLC0xOTYyODQ1MTE2LDI5MTMyODkxOSwxMDY1MjU1MTUxLC
03Njg5NTQxNTYsMTE3MDg0Mjc5NywtMTIxNDY0MTYxOCwtMTA0
NDcyNjEyNCwyMDI0MDU5NzM4LDEzNTc3MTgwOSwtMTk1MTc0OT
YyOSw2MTI1MjY1NDMsMTM4MDYxMjc0OSw0NzE5MzE0MDQsMTc5
OTQwMTgxNF19
-->