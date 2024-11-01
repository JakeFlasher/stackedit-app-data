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
	- aster: inferenced  SPEC06
		- cpded: spec06
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
	- broken for layer=4 (layer=2 can work)
	- 16,861,805
- set model = TimesNet
	- 36,721,669
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU5ODE3OTc5Myw0NzE5MzE0MDQsMTc5OT
QwMTgxNCw2NzYyODE1NjQsLTE4MDYzMTQ0NTgsMTczNTM0MzI2
NSwyMDczNjY0MDA4LC0xMTY1OTU2MDYwLC0xNTg5OTg0Mjg0LD
M1NjI5MTA0MywtMTEyMDgwMDU1OCwtMjYyODkwNjg2LC0xNDk4
NDY1OTgwLC0xNjM4MDQ1NTkzLC0xOTM3NDc0Njc4LDE5Njk0Mj
g0OCwxMDQ2NDA5ODk0LC0xNDY2MjUyMjQxLC0xMzQ3MjM0MzA5
LC0yMDQwOTM1NzYzXX0=
-->