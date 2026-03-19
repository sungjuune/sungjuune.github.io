---
title: 'Do VLA policies really know what they are doing?'
date: 2025-09-01 15:00:00 +0200
categories: [logbook]
math: true
description: "On the commonnsense catastrophic forgetting of LM in VLAs"
author: me
# tags: [thesis, software]     # TAG names should always be lowercase
---


A typical choice for training an embodied vision-language action (VLA) policy is to fine-tune a large vision language models (LVLM) on a large amount of robotic action data. By doing so, the trained policy can leverage the multimodal and spatial reasoning capabilities of LVLMs to easily map those understandings into low-level actions. In other words, the language model (LM) backbone inside the trained VLA acts as a *brain* for the policies. However, just as any other ML/DL models, VLAs also face the inevitable distribution shift issue at test time, failing to correctly follow the given instructions and act accordingly. 

Many possible explanations for this could exist (e.g., data scarcity, model capacity, architectural fail), but I would like to focus on a more subtle but critical factor: ***catastrophic forgetting of commonsense reasoning in the LM backbone.*** During large-scale fine-tuning on robotic action datasets, the LM component of a VLA can gradually adapt to the narrow distribution of action–observation pairs, potentially overwriting portions of the general reasoning capabilities learned during pretraining. As a result, the policy may lose some of the commonsense and compositional reasoning abilities that originally enabled robust language grounding, making it more brittle when encountering novel instructions or environmental variations at test time.

## 1. Empirical Verification

To see if this really is the case, I have conducted experiments comparing the performance of LMs before and after VLA fine-tuning. The experiments are conducted on three commonsense benchmarks for natural language models.

> - **PIQA**: A benchmark that tests commonsense reasoning about physical interactions.
> - **HellaSwag**: challenges models to choose the most logical and contextually appropriate ending for a given text from four possible continuations.
> - **SIQA**: A question-answering benchmark for testing social commonsense intelligence.

The LM backbone, VLA policy and the evaluation protocols are:

> - LM: Llama-2 (meta-llama/Llama-2-7b-hf)
> - VLA: OpenVLA (openvla/openvla-7b)
> - Evaluation Protocol: lm-eval-harness

---
### Results


|**PIQA**|0-shot Acc.|0-shot Acc. (Norm)|5-shot Acc.|5-shot Acc. (Norm)|  
|---|---|---|---|---|
|LM|78.1|79.2|79.0|80.0|
|LM in VLA|52.0|49.9|53.5|49.8|

|**HellaSwag**|0-shot Acc.|0-shot Acc. (Norm)|5-shot Acc.|5-shot Acc. (Norm)|  
|---|---|---|---|---|
|LM|57.2|76.0|58.5|78.2|
|LM in VLA|25.7|25.3|25.7|25.8|

|**SIQA**|0-shot Acc.|0-shot Acc. (Norm)|5-shot Acc.|5-shot Acc. (Norm)|  
|---|---|---|---|---|
|LM|46.1|-|51.8|-|
|LM in VLA|34.9|-|34.2|-|

---
### Analysis

From the results above, we can observe a clear performance drop in commonsense reasoning across all three benchmarks. Especially, the degradations in the PIQA benchmark implies that the policy, or the underlying LM backbone to be more precise, loses physical understanding, which is critical for reasoning about actions and their effects in the physical world. Also, the severe drops in the HellaSwag benchmark show that the LM now almost loses its ability to properly interpret the context and logic from the natural language. Taken together, these results suggest that fine-tuning VLAs on large-scale robotic action data can inadvertently erode the pretrained language model’s general commonsense reasoning abilities. In a nutshell, while such fine-tuning improves task-specific action grounding, it may simultaneously diminish the broader reasoning competencies that originally enabled robust language understanding and generalization.


## 2. Research Directions

To enable robust generalization under distribution shifts and achieve general embodied intelligence, retaining those multiple dimensions of commonsense reasoning capabilities of LMs are crucial. Those knowledge should not be taken for granted, as they are expensively obtained in LMs by training on billions of data for months and months. These overall findings suggests several important research directions:

**1. Commonsense-preserving VLA fine-tuning**
- The most straightforward approach would be to come up with a VLA fine-tuning method that well preserves the commonsense inside LMs. It would be interesting to first identify which commonsense has their affect on low-level action prediction. Then, several methods such as data replay or regularization techniques to mitigate catastrophic forgetting could be explored.

**2. Enhancing zero-shot low-level control**
- An orthogonal direction is to avoid fine-tuning the LM altogether. By keeping the pretrained LM frozen, we can better preserve its embedded commonsense and reasoning capabilities, and directly leverage them for grounding language into low-level actions. However, purely zero-shot approaches typically suffer from lower task performance, since the model is not adapted to the specific dynamics and action distributions of robotic environments. Therefore, an important research direction is to develop methods that strike a balance between preserving the pretrained reasoning abilities of LMs and achieving sufficient adaptation for accurate low-level control.

