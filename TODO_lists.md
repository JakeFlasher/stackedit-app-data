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
	- inferenced SPEC06
- set model = ImputeFormer 
- set model = TimeMixer
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY0MTAyNjIzMiwxNzE1NzU5NDA5LDE5Nj
MzMDk4NjksLTgyODMxMTUxMyw0NTg2MDU1MzMsNjc0NTk5Mzk2
LDIzNTIxMDM4MSwtNTg4MjMxMzYyLC00MTcxNDkwMiw4OTEwMz
Q1OCw0NDA5MDU2MTldfQ==
-->