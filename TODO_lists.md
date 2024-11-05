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
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk3NDE2MTIwMiwtMTM2MjcxMjk5MSwtMz
UyNjU4NTA2LDIxNDA1NDA0MjQsLTE4MTgxODU2MjgsMTAxMTc4
NDgwMSwxNDk1OTM3Mjc2LC0xMzI4NDQ5NDQ4LC0xMzY2MzIwMj
Y4LC04MjQ4Nzk3MzgsLTEzNjYzMjAyNjgsLTcyMDI1MjAzNyw0
OTk4MjU0NTMsLTE1MTQ3NjYyLDQ2MjcxNTIzMCwyMTM3NTcyMT
czLC0xMzc2MzA5NDI2LDE4MTU2NDE4MzgsOTk1ODE2NDUzLC0x
MDY4NTA3Ml19
-->