500 Ground truth for GAP, SPEC06, SPEC17
- 3 prefetchers
- 
./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &
./run_sim.sh ../traces/astar_specific_imputeformer ./models 500000000 Berti_single_core_dev_results/astar_specific_imputeformer > log_berti 2>&1 &
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
	- aster: inferenced  SPEC06, gap, spec17 (imputation_astar		)
		- cpded: ???spec06 (skip them next time), spec17, gap
		- synced: all
		- simultion: run all (ongoing) BERTI (left-ongoing)
		- 
	- gcc: inferendced SPEC17, gap, spec06(ongoing partial)
		- cpded: all (ongoing)
		- synced: compled ongoimg
		- simulation:
		
	- bfs-10 inferenced SPEC17, gap, spec06
		- cpded:  all traces (ongoing)
		- synced: all traces (ongoing)
		- simulation:  fini
		- 

add results from spec06 to reduction rate experiments and Modify the trace reduction rate tables
TimesNet l2: bingo, berti, spp
TimesNet ar
TimeMixer l2 : bingo, berti, spp
TimeMixer ar


## Training
spring, PRE
twitter, APP, Pre (truncated to 500M)
kafka, PRE
tomcat, PRE
fpm, PRE
http, pre
chirper, pre
wikipedia, APP, Pre
tpcc, pre
nodeJS,

spring_bakup!!!

spec06 0.25: spp, bingo, berti
spec06 0.75: bingo, berti, spp
# paper writing
## Polish abstract (ddl: 11.16 UTC)
## Review Time Costs for training and inference (using 4090 experimental values)
## Radar Graph value fillings
## Comparison of different CPD cost functions.
## limitations: only tested simpoint traces, which are traces that are already clustered
## further analysis: pre-trained models reached very well performance, why? Using time series imputation to enhance ipc values increase performance to a great deal than purely CPD, why?
## Testing different cost functions for CPD (ar, l2)


# paper writing
## introduce the importance and popularity of simpoints traces in cache size/t
		- synced:
	- gcc: inferendced SPEC17, gap, spec06(ongoing partial)
		- cpded:
		- synced:
	- bfs-10 inferenced SPEC17, gap, spec06


- benchmarking = SPEC06, SPEC17
- simulation = 1 prefetecher etc simulations, i.e. emuerating simulation settings and run various simulations on 1 simpoint trace.
## training time/inference time cost table
## optimize cpd

	

-set model = ModernTCN
	- params:  475,049
	- inferenced spec06, gap, spec17 (ongoing, partial, remainder spec17)
	- cpded gap, spec06, spec17
	-  simuation ongoin all tracs
	- 

- set model = TimeMixer
	- broken for layer=4 (layer=2 can work)
	- 8,479,341

- set model = TimesMixer
	- 8,479,341
	- inferenced spec17,  gap
	-

- set model = TimesNet
	- 36,721,669
	- inferenced spec17, GAP spec06
	-

- set model = ReuseDist
	- Simulation: re-running results (completed)
	- 
	- 
- set model = naive change point detection
	- CPD: re-running cpd for all traces
	- sync

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model = SAITS
	- 1,324,934
	- inferenced SPEC06, SPEC17, gap
	- cpded: all traces (ongoing)


# Finished

- set model = ImputeFormer 
	- 1,194,433

Enumerating Reduction Rate
- set model = application-specific ImputeFormer
- benchmarking = GAP, SPEC17, SPEC06 (ongoinh)
	- GemsTD, astar ... etc spec06 (inferenced)
	- cped: spec06
	- synced and results
	- simulation = all 3 prefetchers (done all spp, done berti,
	- done 0.75/0.5/0.25 spec/gap )
	- benchmarking = SPEC06, SPEC17
	- simulation = 3 prefetcher

Enumerating training dataset on pre-trained models
- set model = ImputeFormer (application specific	)
- runed SPEC17,GAP (3 reduction rate)
- trained SPEC06 
- inferenced all
- synced all
- simulation: run all
- 	- syned trace all: /champsim_traces/traces/syn_SAITS_0.5
	- results: spp, bingo finished, berti rerunning
	
- set model = TEFN
	- 552
	- inferenced SPEC17, SPEC06, GAP
	- cpded ongoing SPEC06(imputeformer_aster), SPEC17, GAP (gz)
	- synced all traces
	- simulation:  runned all

Enumerat

-set model = ModernTCN
	- params:  475,049
	- inferenced spec06, gap, spec17 (ongoing, pre-trained models
- set model = SAITS (2 layers)
	- 1,324,934 (2 layers, d_model = 256)
	- 37,824,902 (4 layers, d_model = 1024)
	- inferenced SPEC06, SPEC17, gap
	- cpded all (ongoing)
	- syned trace all: /champsim_traces/traces/syn_SAITS_0.5
	- results: spp, bingo finished, berti (rerunning, need to summarize)artial, remainder spec17)
	- cpded gap, spec06
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
eyJoaXN0b3J5IjpbLTIxMjQ1NTQzNjksLTIwMjQzNDE2NzAsLT
E0NDEyMDA0NzIsLTQwNzI0NjQxMywxNjY3MjMyMjQwLDI5OTE0
MzMzOSw2NTIyOTAyNzIsMTE3MjYyMjA2LC0xMDk0ODA4MTA3LC
00NDMyNTUzNDgsOTg0MjQzMDI0LDY5NjAxNjg3LDE0MDI4Mjg3
MzYsMTA4NDM4NjM4MSwyNDgzOTMxMjcsLTE2ODQyMzk4NjcsMT
Q1ODAxNzczNywxMjgyMjY3MzgzLC0zNTcwNzMzMzEsLTM3MTA4
NjQ1MF19
-->