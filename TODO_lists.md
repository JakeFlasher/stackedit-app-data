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
	- {gcc. xalanmb, aster, bfs10, mcf}.pypots
	- inferenced aster SPEC06
- benchmarking = SPEC06, SPEC17
- simulation = 1 prefetcher

./run_sim.sh ./models ./reference 500000000 SPP_single_core_dev_results > log_spp 2>&1 &

Enumerating pre-trained models
- set model = SAITS
	- inferenced SPEC06, SPEC17 gap
	- cpded all (ongoing)
	- syned trace

- set model = TEFN
	- inferenced SPEC17, SPEC06
	
- set model = ImputeFormer 
- 1,194,433
- set model = TimeMixer
- 
- set model = ReuseDist
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNDA5MzU3NjMsMjExNTIxMDg0OCwxNj
k2NzM2OTY4LC05MTU4NTgwMzEsLTUwNzY4Nzg2NCwtMTYyNjQ0
Mjk1NCwtODQwNjQ3MDI3LDE4OTQyMDA1MjEsMTY0MTAyNjIzMi
wxNzE1NzU5NDA5LDE5NjMzMDk4NjksLTgyODMxMTUxMyw0NTg2
MDU1MzMsNjc0NTk5Mzk2LDIzNTIxMDM4MSwtNTg4MjMxMzYyLC
00MTcxNDkwMiw4OTEwMzQ1OCw0NDA5MDU2MTldfQ==
-->