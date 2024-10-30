500 Ground truth for GAP, SPEC06, SPEC17
	- 3 prefetchers
Enumerating Reduction Rate
- set model = application-specific ImputeFormer
- benchmarking = GAP, SPEC17
- simulation = all 3 prefetchers (done all spp, done 0.25 berti, )

Enumerating training dataset on pre-trained models
- set model = ImputeFormer
	- {gcc. xalanmb, bfs}.pypots
- benchmarking = GAP, SPEC06, SPEC17
- simulation = 1 prefetcher

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2MzY4OTk5ODgsLTU4ODIzMTM2MiwtND
E3MTQ5MDIsODkxMDM0NTgsNDQwOTA1NjE5XX0=
-->