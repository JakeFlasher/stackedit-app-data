500 Ground truth for GAP, SPEC06, SPEC17
- 3 prefetchers
-
Enumerating Reduction Rate
- set model = application-specific ImputeFormer
- benchmarking = GAP, SPEC17
- simulation = all 3 prefetchers (done all spp, done 0.25 berti, )

Enumerating training dataset on pre-trained models
- set model = ImputeFormer
	- {gcc. xalanmb, aster}.pypots
	- done aster SPEC06
- benchmarking = GAP, SPEC06, SPEC17
- simulation = 1 prefetcher

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjc0NTk5Mzk2LDIzNTIxMDM4MSwtNTg4Mj
MxMzYyLC00MTcxNDkwMiw4OTEwMzQ1OCw0NDA5MDU2MTldfQ==

-->