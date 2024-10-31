500 Ground truth for GAP, SPEC06, SPEC17
- 3 prefetchers
-
Enumerating Reduction Rate
- set model = application-specific ImputeFormer
- benchmarking = GAP, SPEC17
- simulation = all 3 prefetchers (done all spp, done berti,
	- done 0.75/0.5/0.25 spec/gap )

Enumerating training dataset on pre-trained models
- set model = ImputeFormer
	- {gcc. xalanmb, aster}.pypots
	- done aster SPEC06
- benchmarking = GAP, SPEC06, SPEC17
- simulation = 1 prefetcher

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model = SAITS
	- inferenced SPEC06, SPEC17 gap
	- cpded
	- syned trace
	
	
- set model = ImputeFormer 
- set model = TimeMixer
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg0MDY0NzAyNywxODk0MjAwNTIxLDE2ND
EwMjYyMzIsMTcxNTc1OTQwOSwxOTYzMzA5ODY5LC04MjgzMTE1
MTMsNDU4NjA1NTMzLDY3NDU5OTM5NiwyMzUyMTAzODEsLTU4OD
IzMTM2MiwtNDE3MTQ5MDIsODkxMDM0NTgsNDQwOTA1NjE5XX0=

-->