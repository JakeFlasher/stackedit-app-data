500 Ground truth for GAP, SPEC06, SPEC17
- 3 prefetchers
-
Enumerating Reduction Rate
- set model = application-specific ImputeFormer
- benchmarking = GAP, SPEC17, SPEC06 (ongoinh)
	- GemsTD, astar ... etc spec06 (inferenced)
	- cped: spec06
	- synced and results
- simulation = all 3 prefetchers (done all spp, done berti,
	- done 0.75/0.5/0.25 spec/gap )

Enumerating training dataset on pre-trained models
- set model = ImputeFormer (application specific	)
- runed SPEC17,GAP (3 reduction rate)
- trained SPEC06 
- inferenced all
- synced all
- simulation: run all

- set model = ImputeFormer (pre-trained)
	- {gcc, aster, bfs10, mcf}.pypots	
	- aster: inferenced  SPEC06, gap, spec17 (imputation_astar		
		- cpded: ???spec06 (skip them next time), spec17, gap
		- synced: all
		- simultion: run all (ongoing) 
		- 
	- gcc: inferendced SPEC17, gap, spec06(ongoing partial)
		- cpded: all (ongoing)
		- synced: 
	- bfs-10 inferenced SPEC17, gap, spec06
		- cpded:  all traces (ongoing)
		- synced:

- benchmarking = SPEC06, SPEC17
- simulation = 1 prefetcher

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model = SAITS (2 layers)
	- 1,324,934 (2 layers, d_model = 256)
	- 37,824,902 (4 layers, d_model = 1024)
	- inferenced SPEC06, SPEC17, gap
	- cpded all (ongoing)
	- syned trace all: /champsim_traces/traces/syn_SAITS_0.5
	- results: spp, bingo finished, berti (rerunning, need to summarize)
	
- set model = TEFN
	- 552
	- inferenced SPEC17, SPEC06, GAP
	- cpded ongoing SPEC06(imputeformer_aster), SPEC17, GAP (gz)
	- synced all traces
	- simulation:  runned all

-set model = ModernTCN
	- params:  475,049
	- inferenced spec06, gap, spec17 (ongoing, partial, remainder spec17)
	- cpded gap, spec06, spec17
	- 
	- 
- set model = ImputeFormer 
	- 1,194,433n
- set model = TimeMixer
	- broken for layer=4 (layer=2 can work)
	- 8,479,341


- set model = TimesNet
	- 36,721,669
	- inferenced spec17, GAP spec06
-

- set model = ReuseDist
	- Simulation: re-running results
	- 
	- 
- set model = naive change point detection
	- CPD: re-running cpd for all traces
	- synced: all traces (ongoing)
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTU3NzYwNzIyLDE5NzQxNjEyMDIsLTEzNj
I3MTI5OTEsLTM1MjY1ODUwNiwyMTQwNTQwNDI0LC0xODE4MTg1
NjI4LDEwMTE3ODQ4MDEsMTQ5NTkzNzI3NiwtMTMyODQ0OTQ0OC
wtMTM2NjMyMDI2OCwtODI0ODc5NzM4LC0xMzY2MzIwMjY4LC03
MjAyNTIwMzcsNDk5ODI1NDUzLC0xNTE0NzY2Miw0NjI3MTUyMz
AsMjEzNzU3MjE3MywtMTM3NjMwOTQyNiwxODE1NjQxODM4LDk5
NTgxNjQ1M119
-->