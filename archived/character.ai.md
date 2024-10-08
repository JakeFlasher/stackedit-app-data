## LLM optimizations from Character.AI
### A glimpse

[Optimizing AI Inference at Character.AI](https://research.character.ai/optimizing-inference/) mentions a highly efficient framework for LLM inference, consisting of the following three parts: 
1.  **Multi-Query Attention (MQA)**: Using a single key for all queries.
![输入图片说明](https://arxiv.org/html/2404.01322v1/extracted/5502412/Attention-Mechanisms.png)
2.  **Hybrid Attention Horizons**: Some layers have global attention, while others have only local attention.
3.  **Cross Layer KV-sharing***: Storing the KV cache in memory close to the GPU. During inference, a model determines which KV entries are important and retrieves them to the GPU.
![noam-attention](https://research.character.ai/content/images/2024/06/figure1-2-1.png)
*[Figure 1.](https://research.character.ai/optimizing-inference/) Left: Standard transformer design where every attention is global attention. Right: The attention design in our production model. Blue boxes indicate global attention, green boxes indicate local attention, and curves indicate KV-sharing. For global attention layers, we share KV across multiple non-adjacent layers. This illustration depicts only a subset of the layers in the full model.*
4. - **Quantization for Training and Serving**: Implemented customized int8 kernels for  model weights, activations, and attention KV cache. 
>Different from commonly adopted "post-training quantization" techniques, we natively train our models in int8 precision, eliminating the risk of training/serving mismatch while also significantly improving training efficiency. 
### A detailed look
>The key bottleneck of LLM inference throughput is the size of the cache of attention keys and values (KV). It not only determines the maximum batch size that can fit on a GPU, but also dominates the I/O cost on attention layers. We use the following techniques to reduce KV cache size by more than 20X without regressing quality. With these techniques, GPU memory is no longer a bottleneck for serving large batch sizes.
### A proof from tested results
> [Transformer升级之路：9、一种全局长度外推的新思路](https://kexue.fm/archives/9603)
> [Transformer升级之路：14、当HWFA遇见ReRoPE](https://kexue.fm/archives/9731) 

> 说到Transformer无法处理超长序列的原因，大家的第一反应通常都是Self Attention的二次复杂度。但事实上，即便忽略算力限制，常规的Transformer也无法处理超长序列，因为它们的长度外推性（Length Extrapolation）并不好，具体表现为当输入序列明显超过训练长度时，模型的效果通常会严重下降。


> HWFA是一种注意力的组合方式，它可以用于标准的多头注意力中，也可以用于[GAU](https://kexue.fm/archives/8934)等注意力变体中。笔者在[GAU_alpha](https://kexue.fm/archives/9052)的基础上进行了实验：训练长度512，24层GAU，前23层用Window Attention，Window大小w=16𝑤=16，测试的是逐token准确率，对比的Baseline是全部层都是Full Attention+RoPE（即常规的默认用法）。


  | 测试长度|512 |4096 |
|----------------|--------------------------------| ------------------------------|
|Baseline|`49.41%` |`24.17%` |
|HFWA|`48.70%` |`80.84%` |  

> 512代表训练准确率（也可以叫内插准确率），4096代表外推准确率。为什么训练准确率才40多，而外推能到80多这么夸张？这是因为笔者在构造测试样本的时候，包含了部分重复拼接样本，即同一段不超过4096长度的文本，通过重复拼接达到4096长度，由于这些样本的后面部分是前面部分的重复，因此这部分准确率很高（即前面已经给出了标准答案），这说明跟我们想象的一样，这样的设计下的长度外推是不牺牲全局依赖能力的。
如果把重复样本剔掉，只保留正常的自然文本样本，那么结果也还能看：

  | 测试长度|512 |4096 |
|----------------|--------------------------------| ------------------------------|
|Baseline|`49.41%` |`23.17%` |
|HFWA|`48.70%` |`48.15%` |  

> 1、Window Attention不加RoPE，内插和外推效果都会下降；
>  2、Full Attention加上RoPE，外推效果会下降；
> 3、Full Attention不加lognlog⁡𝑛因子，外推效果会下降；
>4、全用Window Attention，内插和外推效果都会下降；
> 5、改为L−2𝐿−2层Window Attention + 2层Full Attention，外推效果会下降；
> 6、w=32𝑤=32（此时(w−1)(L−1)>N(𝑤−1)(𝐿−1)>𝑁），外推效果会下降

### A comparison with counterparts
- [StreamingLLM](https://arxiv.org/abs/2309.17453): 

> We have reduced serving costs by a factor of 33 compared to when we began in late 2022. Today, if we were to serve our traffic using leading commercial APIs, it would cost at least 13.5X more than with our systems.


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0Mzk4ODUxMDVdfQ==
-->