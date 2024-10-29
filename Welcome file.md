Since we're only imputing about 50% of missing IPC values, and when we use a change point detection method, we usually set a jump scale to be 50000, meaning only changes at every 50000 are considered. The problem is, would change point detection be the pure and only reason for good instruction reduction? Is it possible that IPC imputation can be safely eliminated from the whole process? 

You need more experiments to show that your IPC imputation approach in this scenario is imdisplacable and necessary. For example, you can try change point detection on 3 scales of IPC imputations:
	1. no IPC imputation
	2. fast learning imputation, average quality
	3. complex learning imputation, better quality
and discuss the final performance metrics given the above scenarios.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5MjM5MDg5NTJdfQ==
-->