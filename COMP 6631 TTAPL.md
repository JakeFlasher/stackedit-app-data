# TTAPL
The  theory  of  programming  languages  is  the  mathematical study  of  the meaning  of  programs.
>1. precise  so  that  we  can  use  mathematical  tools  to formalize  and  check  interesting  properties
>2. abstract  so  that  properties  of  interest  can  be  discussed clearly, without  getting  bogged  down  in  low-level  details
## Purpose of TTALP
1. To prove specifc properties of particular programs
2. To develop intuitions for informal reasoning of programs
3. To prove general facts about all programs in a programming language
4. To understand language features and develop principles for better design
## Different approaches of analysis
**1. Denotational semantics and domain theory**
- http://www.cs.unc.edu/~stotts/COMP723-s15/Denote/simple.html
- https://www.cl.cam.ac.uk/~gw104/dens.pdf
- https://en.wikipedia.org/wiki/Denotational_semantics

**2. Operational semantics**
- https://en.wikipedia.org/wiki/Operational_semantics


<!-- This is commented out. -->


 # Learning memory patterns
## Background acknowledgements

```
@article{Zhang2021CMemMAPCC, title={C-MemMAP: clustering-driven compact, adaptable, and generalizable meta-LSTM models for memory access prediction}, author={Pengmiao Zhang and Ajitesh Srivastava and Ta-Yang Wang and C{\'e}sar A. F. De Rose and Rajgopal Kannan and Viktor K. Prasanna}, journal={International Journal of Data Science and Analytics}, year={2021}, volume={13}, pages={3 - 16}, url={https://api.semanticscholar.org/CorpusID:237863924} }
@inproceedings{10.1145/3528416.3530236,
author = {Zhang, Pengmiao and Srivastava, Ajitesh and Nori, Anant V. and Kannan, Rajgopal and Prasanna, Viktor K.},
title = {Fine-grained address segmentation for attention-based variable-degree prefetching},
year = {2022},
isbn = {9781450393386},
publisher = {Association for Computing Machinery},
address = {New York, NY, USA},
url = {https://doi.org/10.1145/3528416.3530236},
doi = {10.1145/3528416.3530236},
abstract = {Machine learning algorithms have shown potential to improve prefetching performance by accurately predicting future memory accesses. Existing approaches are based on the modeling of text prediction, considering prefetching as a classification problem for sequence prediction. However, the vast and sparse memory address space leads to large vocabulary, which makes this modeling impractical. The number and order of outputs for multiple cache line prefetching are also fundamentally different from text prediction.We propose TransFetch, a novel way to model prefetching. To reduce vocabulary size, we use fine-grained address segmentation as input. To predict unordered sets of future addresses, we use delta bitmaps for multiple outputs. We apply an attention-based network to learn the mapping between input and output. Prediction experiments demonstrate that address segmentation achieves 26\% - 36\% higher F1-score than delta inputs and 15\% - 24\% higher F1-score than page \& offset inputs for SPEC 2006, SPEC 2017, and GAP benchmarks. Simulation results show that TransFetch achieves 38.75\% IPC improvement compared with no prefetching, outperforming the best-performing rule-based prefetcher BOP by 10.44\% and ML-based prefetcher Voyager by 6.64\%.},
booktitle = {Proceedings of the 19th ACM International Conference on Computing Frontiers},
pages = {103–112},
numpages = {10},
keywords = {address segmentation, attention, machine learning, prefetching},
location = {Turin, Italy},
series = {CF '22}
}
@inproceedings{10.1145/3581784.3607043,
author = {Zhang, Pengmiao and Kannan, Rajgopal and Prasanna, Viktor K.},
title = {Phases, Modalities, Spatial and Temporal Locality: Domain Specific ML Prefetcher for Accelerating Graph Analytics},
year = {2023},
isbn = {9798400701092},
publisher = {Association for Computing Machinery},
address = {New York, NY, USA},
url = {https://doi.org/10.1145/3581784.3607043},
doi = {10.1145/3581784.3607043},
abstract = {Memory performance is a key bottleneck in accelerating graph analytics. Existing Machine Learning (ML) prefetchers encounter challenges with phase transitions and irregular memory accesses in graph processing. We propose MPGraph, an ML-based Prefetcher for Graph analytics using domain specific models. MPGraph introduces three novel optimizations: soft detection of phase transitions, phase-specific multi-modality models for access delta and page predictions, and chain spatio-temporal prefetching (CSTP) for prefetch control.Our transition detector achieves 34.17--82.15\% higher precision compared with Kolmogorov-Smirnov Windowing and decision tree. Our predictors achieve 6.80--16.02\% higher F1-score for delta and 11.68--15.41\% higher accuracy-at-10 for page prediction compared with LSTM and vanilla attention models. Using CSTP, MP-Graph achieves 12.52--21.23\% IPC improvement, outperforming state-of-the-art non-ML prefetcher BO by 7.58--12.03\% and ML-based prefetchers Voyager and TransFetch by 3.27--4.58\%. For practical implementation, we compress the prediction models to reduce the storage and latency overhead. MPGraph with the compressed models still shows significantly superior accuracy and coverage compared to BO, with 3.58\% IPC improvement.},
booktitle = {Proceedings of the International Conference for High Performance Computing, Networking, Storage and Analysis},
articleno = {91},
numpages = {15},
keywords = {graph analytics, domain specific, prefetcher, machine learning},
location = {<conf-loc>, <city>Denver</city>, <state>CO</state>, <country>USA</country>, </conf-loc>},
series = {SC '23}
}
```


### Blueprints
- 1. Split the program based on BBV-Simpoints in  terms of assembly and traces-for-simulators
- 2. Run each trace piece in the simulator, and extract information during running (hardware occup, Cache usage etc.) Match these information with the corresponding assembly code region in the original program.
- 3. Learning from these patterns, possibly using DL or LLM, hoping to get a relation between assembly code sections and running simulator's information
- 4. Given a prompt describing the desired performance or hardware occup, generate a synthetic benchmark consisting of best fit for these selected features during runtime in the prompts. 

# Terminology
***BBL = Basic Block***
***RD = Reuse Distance, HRD = Hierarchical Reuse Distance***
***ROI = Region of interest***
***DFG = data flow graph***
# Memory Profiling
### Example of visualizing the memory usage and footprint
![enter here](https://easyperf.net/img/posts/MemoryProfiling/MemoryUsageAndFootprint.png)

```
VSZ: 200K + 500K + 2500K = 3200K
RSS: 100K + 400K + 1000K = 1500K
```
As we would expect, the RSS is always less or equal to the VSZ. Looking at the chart, we can spot four phases in the program. Phase 1 is the ramp-up of the program during which it allocates its memory. Phase 2 is when the algorithm starts using this memory, notice that the memory usage stays constant. During phase 3, the program deallocates part of the memory and then allocates a slightly higher amount of memory. Phase 4 is a lot more chaotic than phase 2 with many objects allocated and deallocated. Notice, that the spikes in VSZ are not necessarily followed by corresponding spikes in RSS. That might happen when the memory was reserved by an object but never used.

## Observations from previous paper
- Uncontrollable (few-shot learning for 1 piece of raw trace only, no other params to control the aspect/quality of generated trace), see 
- Intra/Inter-dataset relationship not well learned in vanilla form (poor synthesis quality)
- Locality-enhanced (RD/HRD seq. ) data improves significantly on the quality of synthetic data
## Progression Route
### 1. Datasets of raw traces needs careful feature-extraction/data-enhancement for better synthesis quality. (Like the way we use RD/HRD seq. as an intra-dataset relationship representation)
Thus, I put a heavy weight on dealing with the dataset of raw traces/Basic Blocks induced from traces. Since this part of work is model-independent, I chose the [NetShare](https://github.com/netsharecmu/NetShare) framework for explore various data processing techniques. The ultimate goal is not to train another GAN for synthetic data, but to learn from the data itself and apply successful techniques into later transformer training. 

### 2. Labelling on the raw trace/ROI of trace/BBs of trace for supervised learning and use effective labels on the trace pieces as parameters for clustering/customized trace synthesis.

## Related Works  
![enter image description here](https://i.imgur.com/AuMWcnX.png)
### On [2] and [3]:
Machine Learning these days has been playing an important role in Compiler Optimization [(ASHOURI et al., 2018)](https://arxiv.org/abs/1801.04405). Accurately and efficiently predicting the throughput of a basic block is an essential requirement is an essential requirement in these machine learning based compiler optimization for guiding the algorithm to find the global optimal(See below figure for more details). Measuring basic block throughput by running the code on real hardware is too expensive for machine learning based compilers and most state-of-the-art analytical models(hand-written models), including `llvm-mca` [(Di Biagio & Davis, 2018)](https://llvm.org/docs/CommandGuide/llvm-mca.html) and `ICIA` [(Intel, 2017)](https://www.intel.com/content/www/us/en/developer/articles/tool/architecture-code-analyzer.html), fail to capture the exact mechanisms of the processors as underlying microarchitectures are upgrading frequently.

To alleviate this issue, Deep Neural Networks based performance model, `Ithemal`[(Mendis, 2019)](https://arxiv.org/abs/1808.07412), was born. It utilizes sequential Long-Short Term Memory (LSTM) to learn a representation of basic blocks followed by a linear transformation to predict the throughput values that has less than half the error of `llvm-mca` and `ICIA`. However, `Ithemal` does not capture data dependency in basic blocks and has to train entire model separately on different microarchitectures. `Granite`[(Sýkora et al., 2022)](https://ieeexplore.ieee.org/document/9975403) addresses these issues by using graph neural network that takes advantage of the relational information captured in the graph and multi-task learning [(Caruana, 1997)](https://link.springer.com/article/10.1023/A:1007379606734) to reduce training cost. `Granite` reduced the error by 1.7% wile improving training and inference throughput by approximately 3.0x [(Sýkora et al., 2022)](https://ieeexplore.ieee.org/document/9975403) compared to `Ithemal`.
## Undertaking:  Basic Blocks as a natural program segmentation 

 ![enter image description here](https://i.imgur.com/Hvx56wY.png)
 -  BBV/BB counts contain necessary information to restore program behaviors. (see [SimPoint](https://cseweb.ucsd.edu/~calder/simpoint/) checkpointing)
 -  Compared to the length of a raw trace, BB counts typically range between 1K to 10K, which is doable for labelling or classification.

One possible way to present the updated model workflow: ""
- [ ] Profile each program trace with BBLs information (control-flow, DFGs, superblocks) and cut the traces into small pieces accordingly, such that one program is represented as a DFG of BBL, and each BBL is represented as a specific sequence of memory trace.
- [ ] Categorize and label each profiled BBLs (refer to SimPoint clustering) from a pre-defined pool of unique labels. Optimize the DFG for each profiled program, and save the optimized graphs/mappings. (see LLVM deduplication, decoding etc.)
- [ ] Deploy a LLM/Transformer-based model to learn the connections between a sequence of trace, and its represented label of BBL, also the DFG of the given program containing BBLs. After training, the human input will be a specific detailed description of a program (control-intensive, computation-intensive etc.), the model should read the description and interpret the program as a correct DFG of BBLs, then generate a synthetic trace cemented from pieces of trace sequence generated from the BBLs in the DFG.
- [ ] If the above model is too complex to be trained, then just downgrade the input to be a specific DFG of BBLs, the model should correctly interpret the DFG and generate a synthetic trace (by generating each small piece of trace sequence for each BBL in the DFG) that mimics the real behavior of a program.
   

## Possible Challenges:

1.  **Basic Block Labeling**: While BBs can indeed capture essential program behavior information, labeling a large number of BBs manually could be a daunting task. ***Maybe consider exploring techniques for automatic labeling or classification of BBs based on their control-flow patterns, data dependencies, or other relevant features?***
    
2.  **Data Flow Graph (DFG) Representation**: It's worth noting that DFGs can become quite complex for large programs, making it challenging for a model to learn and generate accurate synthetic traces. You might want to explore techniques to simplify or abstract DFGs while preserving essential program behavior.
    
3.  **Model Complexity**: The proposed model workflow involving LLMs/Transformers to learn connections between trace sequences, BBL labels, and DFGs is ambitious but potentially complex. As you mentioned, you might need to consider simpler models or approaches, at least initially, to make the problem more tractable.

4.  **Evaluation Metrics**: While generating synthetic traces that mimic real program behavior is the goal, we should also consider appropriate evaluation metrics to assess the quality and utility of the generated traces. This could include metrics for distributional similarity, code coverage, or performance on simulators.

> 4.  **Domain Knowledge Integration**: Since you're working with program traces and BBs, it might be beneficial to integrate domain
> knowledge from compiler optimizations, code analysis, and other
> relevant areas. This could help in designing more effective feature
> representations, labeling strategies, or model architectures.

    
## Implementations:
- [ ] Profiling with BBL as well as perf metrics, decide whether to do BBL clustering or use unique BBL (addr ID) as labels
- [ ] Which base model to use? (Transformer, DGN, LLM?)
 1. Train a transformer from the start
 2. Fine-tuning LLMs
	 - Before going on real training, test it on Claude/GPT using few-shot examples (provide bbl/code and raw trace), tell him to learn the combination and relationship, then test outputs
	 - Google colab 4.20 expiration, use at least 500 units

Certainly, I'll pose some potential challenges and questions that reviewers of top computer architecture conferences (e.g., HPCA, ASPLOS, ISCA, and MICRO) might raise regarding your proposed idea:

1. **Novelty and Significance**:
   - How does your approach differentiate itself from existing techniques for synthetic trace generation or program behavior modeling?
   - What are the specific limitations of current state-of-the-art methods that your approach aims to address?
   - What is the expected impact of your work on downstream applications, such as performance modeling, simulation, or architecture exploration?

2. **Basic Block Labeling and Representation**:
   - Your proposal to label Basic Blocks (BBs) for supervised learning is intriguing, but the reviewers might question the scalability and generalizability of manual labeling efforts.
   - How do you plan to automate the labeling process while ensuring accurate and meaningful labels?
   - Have you considered alternative representations or features beyond BBs that could capture program behavior more effectively?

3. **Data Flow Graph (DFG) Complexity**:
   - Representing programs as DFGs of BBs could lead to significant complexity, especially for large-scale applications. Reviewers might raise concerns about the tractability of learning and generating accurate synthetic traces from such complex representations.
   - Have you explored techniques to simplify or abstract DFGs while preserving essential program behavior? Reviewers might ask for more details on your approach to handle DFG complexity.

4. **Model Architecture and Training**:
   - The proposed model workflow involving LLMs/Transformers to learn connections between trace sequences, BBL labels, and DFGs is ambitious. Reviewers might question the feasibility of training such a complex model effectively.
   - Have you considered alternative model architectures or training strategies that could simplify the problem while still capturing the desired program behavior?
   - What are the anticipated challenges in terms of data requirements, training time, and computational resources for your proposed model?

5. **Evaluation and Validation**:
   - Reviewers might inquire about your evaluation methodology and metrics to assess the quality and fidelity of the generated synthetic traces.
   - How do you plan to validate that the generated traces accurately represent real program behavior, beyond just matching statistical properties?
   - Have you considered evaluating the usefulness of your synthetic traces for downstream applications, such as performance modeling or architecture simulation?

6. **Scalability and Generalization**:
   - Reviewers might question the scalability of your approach to handle large and diverse codebases, ranging from different application domains, programming languages, or hardware architectures.
   - How do you plan to ensure the generalizability of your approach across different types of programs or workloads?
   - Have you considered the potential impact of code optimizations, compiler transformations, or hardware-specific characteristics on the effectiveness of your approach?

These are some potential challenges and questions that reviewers might raise to evaluate the novelty, significance, technical soundness, and potential impact of your proposed idea. Addressing these concerns effectively in your submission could increase the likelihood of acceptance at top computer architecture conferences.


Thank you for sharing your research notes and thoughts on using generative AI for memory workload synthesis. It's an interesting and novel approach to this problem. Here are some thoughts and suggestions:

1. Feature engineering and data representation are crucial for effective synthesis using generative AI models like transformers. Your ideas of extracting features like basic blocks, control flow graphs, data flow graphs, and representing programs using these abstractions seem promising.

2. Labeling basic blocks/regions of traces and using supervised learning could help the model better learn the semantics and relationships within program executions. Unsupervised clustering of basic blocks, as done in SimPoint, could also provide useful representations.

3. The idea of generating synthetic traces by composing basic block or region-level trace snippets produced by the model is interesting. Having the model learn the mappings between high-level program descriptions/characteristics and low-level execution patterns could enable controllable synthesis.

4. Breaking down the complex problem into subproblems like basic block labeling, constructing program graphs, and then generating traces piece-by-piece is a reasonable approach, especially if an end-to-end model is too complex initially.

5. Incorporating domain knowledge like compiler analyses (e.g., LLVM) and using techniques like deduplication could help in extracting robust representations from the raw traces.

Some additional thoughts and suggestions:

- Explore different ways of representing basic blocks/regions beyond just their memory access traces, e.g., incorporating control flow, data dependencies, instruction mixes, etc.

- Investigate transfer learning approaches where you pre-train the model on a large corpus of programs and then fine-tune on specific workloads of interest.

- Ensemble multiple generative models trained on different views of the data (e.g., temporal, spatial, control-flow) to improve accuracy.

- Devise techniques to ensure that the synthesized traces respect critical invariants like data dependencies, memory consistency models, etc.

- Explore evaluation metrics beyond cache simulation to measure the behavioral similarity between original and synthetic workloads, e.g., instruction mix, branch behaviors, kernel execution patterns for parallel workloads.

Overall, your research direction of using generative AI for workload synthesis while leveraging insights from program analysis and compilation seems very promising. Keeping an open mind, extensively evaluating different approaches, and continuously refining the techniques based on empirical results will be key to making further progress. Let me know if you have any other questions!

While the idea of using generative AI models for memory workload synthesis is novel and intriguing, there are several significant challenges and potential criticisms that need to be addressed for it to be accepted in top computer architecture conferences like HPCA, ISCA, MICRO, or ASPLOS. Here are some major issues that reviewers and the community might raise:

1. **Lack of Interpretability and Explainability**: Transformer-based generative AI models are often criticized for being black-box models that lack interpretability and explainability. It might be challenging to convince reviewers that the generated synthetic traces truly capture the intrinsic behaviors and characteristics of the original workloads, especially when dealing with complex workloads with intricate control flow and data dependencies.

2. **Sensitivity to Data Representation**: The performance of generative AI models heavily depends on the input data representation. Reviewers might question the effectiveness of the proposed data representations (e.g., basic blocks, control flow graphs, data flow graphs) in capturing the essential characteristics of the workloads. They might also raise concerns about the scalability and generalizability of the proposed representations across a diverse set of workloads.

3. **Difficulty in Controlling and Customizing Synthesis**: One of the critical requirements for workload synthesis is the ability to control and customize the generated workloads to explore specific architectural scenarios or behaviors. Reviewers might raise concerns about the lack of control and customization capabilities in the proposed generative AI approach, especially if the model is treated as a black box.

4. **Lack of Theoretical Foundations**: While generative AI models have shown impressive empirical results in various domains, they often lack strong theoretical foundations. Reviewers might criticize the lack of theoretical analysis and guarantees regarding the properties of the generated synthetic traces, such as preserving data dependencies, memory consistency models, or other critical invariants.

5. **Evaluation Challenges**: Evaluating the quality and accuracy of synthetic workloads is a complex task. Reviewers might question the evaluation methodology and metrics used in the study. They might argue that cache simulation alone is insufficient to capture the behavioral similarity between original and synthetic workloads, and might demand more comprehensive evaluation metrics and techniques.

6. **Computational Complexity and Scalability**: Training and inferencing with large transformer models can be computationally expensive and resource-intensive. Reviewers might raise concerns about the scalability of the proposed approach, especially when dealing with large and complex workloads, or when targeting resource-constrained environments.

7. **Lack of Comparative Analysis**: Reviewers might expect a comprehensive comparative analysis against state-of-the-art workload synthesis techniques, both in terms of synthesis quality and computational complexity. Failure to provide such an analysis might weaken the claims of the proposed approach's effectiveness.

8. **Reproducibility and Accessibility**: Reviewers might demand access to the generated synthetic traces, pre-trained models, and detailed experimental setups to ensure reproducibility and enable further research in this area.

To address these challenges and increase the chances of acceptance in top computer architecture conferences, it is crucial to conduct rigorous experimental evaluations, provide strong theoretical foundations or analysis, and clearly articulate the advantages and limitations of the proposed approach. Additionally, it might be beneficial to collaborate with domain experts in program analysis, compilation, and computer architecture to strengthen the research and address potential criticisms effectively.


## On-goings
### 1. Basic Block Vector Generation and Similarity
[bbv_research](https://web.eece.maine.edu/~vweaver/projects/bbv_research/)
BBV generations:
- Qemu-based plugins
- Valgrind
- PinPoints (pin-play)
- Dynamorio

### 2. Basic Block Vector File Format
[valgrind -tool=exp-bbv](https://valgrind.org/docs/manual/bbv-manual.html)
The Basic Block Vector is dumped at fixed intervals. This is commonly done every 100 million instructions; the  `--interval-size`  option can be used to change this.

The output file looks like this:

>T:45:1024 :189:99343
T:11:78573 :15:1353  :56:1
T:18:45 :12:135353 :56:78 314:4324263

Each new interval starts with a T. This is followed on the same line by a series of basic block and frequency pairs, one for each basic block that was entered during the interval. The format for each block/frequency pair is a colon, followed by a number that uniquely identifies the basic block, another colon, and then the frequency (which is the number of times the block was entered, multiplied by the number of instructions in the block). The pairs are separated from each other by a space.

The frequency count is multiplied by the number of instructions that are in the basic block, in order to weigh the count so that instructions in small basic blocks aren't counted as more important than instructions in large basic blocks.

The SimPoint program only processes lines that start with a "T". All other lines are ignored. Traditionally comments are indicated by starting a line with a "#" character. Some other BBV generation tools, such as PinPoints, generate lines beginning with letters other than "T" to indicate more information about the program being run. We do not generate these, as the SimPoint utility ignores them.

The below is the distinguish between static vs dynamic basic blocks (bbls):

> A dynamic basic block is defined as a sequential run of instructions that starts with an instruction that is a target of a  **taken**  branch and ends with the first taken branch in sequence. Thus, one dynamic basic block may contain one or more static basic blocks that are defined as follows: a sequential run of instructions that starts with an instruction that is a target of a taken branch and ends with the first  **branch**  in sequence

[Intel Pintool User Manual](https://software.intel.com/sites/landingpage/pintool/docs/98484/Pin/html/index.html)  defines their basic block (BBLs) as:

> Pin breaks the trace into basic blocks, BBLs. A BBL is a single entrance, single exit sequence of instructions. Branches to the middle of a bbl begin a new trace and hence a new BBL

### What we have so far?
 1. QEMU + plugins support for RISC-V, working tgt with ChampSim
	> 	- Plugins for **champsim.trace.xz** decoding/dumping and BBV generation
	> - Plugins for **champsim.trace.xz** generation from SimPoints results
	> - Using Simpoints (phases clustering based on BBV) bc instruction dependencies are usually represented in complex graphs. 
 2. Modified ChampSim with trace format extended with **execution result** and **instruction code**
 ![enter image description here](https://i.imgur.com/MbZpGG3.png)

>Champsim traces collected at the interval of each Simpoint![enter image description here](https://i.imgur.com/MxyJ6Uk.png)

> Decoded human-readable champsim.trace trace file
![enter image description here](https://i.imgur.com/df5vDPw.png)

> Decoded human-readable memory.bin trace file
![enter image description here](https://i.imgur.com/pVWpLQp.png)

## Basic Block Profiling from ASM

![enter image description here](https://i.imgur.com/01yQk8w.png)


![enter image description here](https://i.imgur.com/pNww05E.png)lueprints
**Previous Proposed Framework**
![enter image description here](https://i.imgur.com/hsvJ5Z4.png)
**Change Trace Synthesis to Code Synthesis**
![enter image description here](https://i.imgur.com/BwSnBw0.png)
- 1. Split the program based on BBV-Simpoints in  terms of assembly and traces-for-simulators
- 2. Run each trace piece in the simulator, and extract information during running (hardware occup, Cache usage etc.) Match these information with the corresponding assembly code region in the original program.
- 3. Learning from these patterns, possibly using DL or LLM, hoping to get a relation between assembly code sections and running simulator's information
- 4. Given a prompt describing the desired performance or hardware occup, generate a synthetic benchmark consisting of best fit for these selected features during runtime in the prompts. 

![enter image description here](https://i.imgur.com/pNww05E.png)
### Basic Block Profiling from ASM

![enter image description here](https://i.imgur.com/01yQk8w.png)



## Machine Learning Explanation Framework for Cost Prediction Models*
COMET is a framework for explaining individual performance predictions given by x86 assembly cost models for basic blocks. COMET can be used to identify the features of the input basic block which are important for the throughput prediction made by the cost model being explained. COMET is compatible with any x86 assembly input basic block and is optimized to explain throughput predictions. In this implementation, we provide functionalities to explain the cost models [Ithemal](https://github.com/ithemal/Ithemal) and [uiCA](https://github.com/andreas-abel/uiCA) with COMET. Note that COMET is compatible with other cost models as well, with minor adjustments.


![enter image description here](https://i.imgur.com/8OAKIOJ.png)


# BB Datasets

This article is aimed to provide an overview on what applications are included
in current BB datasets (particularly BHive/Ithemal).

### BHIVE

| Application | Number of Basic Blocks |
| ----------- | ---------------------- |
| OpenBlas | 19032 |
| Redis | 9343 |
| SQLite | 8871 |
| GZip | 2272 |
| Tensorflow | 71988 |
| Clang/LLVM | 212758 |
| Eigen | 4545 |
| Embree | 12602 |
| FFmpeg | 17150 |

### Ithemal

| Application | Number of Basic Blocks |
| ----------- | ---------------------- |
| Linux shared libraries | 313846 |
| SPEC2006 | 247047 |
| SPEC2017 | 234588 |
| NAS | 3935 |
| polybench-3.1 | 1900 |
| TSVC | 5129 |
| cortexsuite | 6582 |
| simd | 212544 |
| Clang/Python 2.7/Python 3.5 | 2746275 |
| GIMP/Firefox/Open-office/Rhythmbox | 83555 |

### Total vs Unique Counts

I'm reasonably confident the number of basic blocks reported for BHive are the
numbers of unqiue basic blocks contributed per application. For Ithemal I've
reported the total number of basic blocks. There will always be significantly
less unique basic blocks than the total number of basic blocks. For the ithemal
dataset, only about 25% of the basic blocks are unique.

### Other Notes

It's also important to note that the Ithemal and BHive datasets were collected
using runtime instrumentation with DynamoRIO. These counts might seem suspicously
low (especially for something like Firefox), but it's important to note that
this is most likely because most of the code paths were simply not touched
so the runtime instrumentation never picked up those blocks. New static tools
for extracting basic blocks should help with this.

## On the accuracy of LLVM
- [optimization - Does the llvm-bolt instrumentation mode result in less accurate BOLT profiles? - Stack Overflow](https://stackoverflow.com/questions/75203453/does-the-llvm-bolt-instrumentation-mode-result-in-less-accurate-bolt-profiles)
- [LLVM’s Analysis and Transform Passes — LLVM 19.0.0git documentation](https://www.llvm.org/docs/Passes.html)
- [llvm-mca - LLVM Machine Code Analyzer — LLVM 19.0.0git documentation](https://llvm.org/docs/CommandGuide/llvm-mca.html))



## Learning from COMET
```python
Cost model made explainable by ML
```
we have:
-	Dynamic profiled ASM, BBs
-	Dynamic profiled Inst, Mem event trace
-	Static analysis (cost model, LLVM, BBs)
-	Simulator performance metrics

Can we go to the ISCA paper, TEA and TIP, gaining insight, instead of mimicing the real-world behaviour (more precisely, real-machine simulator behaviors, cause our simulators is biased against validated hardware/FPGA). We trying to explain the delta (difference between two applications/ two run, and why) since simulator is biased, the ground truth value may not serve as a proper approximation, but the deltas between them (study how they vary under different circumstances, is probably the same). Once we know the mechanism (learned) of why two programs behavior differ, we can transfer one to another maybe? That is our way of learning to synthesize a bunch of different programs, we choose a base and study the deltas between them, after learning, every time encountering a new situation, we take the delta and try to explain/predict what caused (input) this different behavior (output) and apply this offset onto the base, gaining a different program synthesized.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MzM3MTgyNDVdfQ==
-->