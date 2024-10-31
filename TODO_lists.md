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
eyJoaXN0b3J5IjpbLTI1MjkxMjA4MiwyMTE1MjEwODQ4LDE2OT
Y3MzY5NjgsLTkxNTg1ODAzMSwtNTA3Njg3ODY0LC0xNjI2NDQy
OTU0LC04NDA2NDcwMjcsMTg5NDIwMDUyMSwxNjQxMDI2MjMyLD
E3MTU3NTk0MDksMTk2MzMwOTg2OSwtODI4MzExNTEzLDQ1ODYw
NTUzMyw2NzQ1OTkzOTYsMjM1MjEwMzgxLC01ODgyMzEzNjIsLT
QxNzE0OTAyLDg5MTAzNDU4LDQ0MDkwNTYxOV19
-->