# Introduction to training LLMs for AI agents

## Pretraining, Postraining, Reasoning RL and Classic post-training / RLHF
Pretrain: Predict next work on internet,
Reasoning RL: Think on questions with objective answers
RLHF: Max user utility & prefs
Second and third stages may be bundled together and collectively referred to as post-training.

## LLM training pipeline
Architecture, Training algorithm/loss, Data & RL env, Evaluation, Systems and infra to scale
Data, Evals, and Systems are what matter in practice.

## LLM specializing pipeline
Prompting: Art of asking the model what you want
Finetuning: Second stage of postraining to domain specific data

Pretraining: Goal -> teach the model everything in the world
Task -> predict the next word
Data -> any reasonable data on internet
Since GPT-2 (2019), pretraining has been key

AR Language Models
Steps -> 
1. tokenize {She} {likely} {prefers} 
2. forward pass 
3. predict probability of next token
4. sample (What exactly does this entail?)
5. detokenize

Simple Language Model: N-grams (non-parametric estimator)
Forward pass -> predict the distribution
Stats: Take all occurrences of {the grass is} on Wikipedia
Predicted probability for X is
P(x| {the grass is}) = Count(X | {the grass is}) / Count({the grass is})
Issues: 
1. Need to keep count of all occurrences for each n-gram (Huge Memory Overhead)
2. Most sentences are unique: this can't generalize

Solution: neural networks (approximate prediction using parametric estimator)

## Neural Language Models
Split (I saw a cat on a ) into five input word embeddings {I}, {saw}, ...
Use non-linear aggregator of these vectors
A very simple neural network could just be an average of this.
<figure>
  <img src="./images/Figure1.png" alt="Figure 1" width="450">
  <figcaption>Figure 1: Neural LM schematic</figcaption>
</figure>
 
get probability distribution for the next token and then process the context (previous history)

## Pretraining Data
Idea: use all of clean internet because itnernet is dirty & not representative of what we want.
1. Download all of internet. Common crawl: 250 billion pages,
2. Text extraction from HTML (challenges: math, boiler plate, etc.)
3. Filter undesirable content (harmful content)
4. Dedeuplicates (ur/document/line) E.g. headersfooters/menu in forums
5. Heuristic filtering. Remove low quality document (e.g. #words, word length, outlier or dirty tokens)
6. Model based filtering. Predict if page could be references by Wikipedia
7. Data mix. Classify data categories (code/books/entertainment). Reweight domains using scaling laws to get high downstream performance
Finally: you can collect high-quality data, continual pretraining with longer context {midtraining: optimize model on high quality data}

HuggingFace FineWeb Datasets paper reveals how performance improves after improving data quality

## Midtraining: 
Continued pretraining to adapt the model to desired properties/ higher quality data (<1T tokens>)
Data mix changes shifts: eg more scientific, coding, multilingual data
Longer context extension: bump (eg 4 -> 128k for DSv3)
Desired fomratting/isntruction following
Higher quality data

Summary of Pre/mid training Data:
Collecting data well is a huge part of practical LLM
Lot of research to be done! (How do you process well and efficiently?)
Secrecy due to competitive dynamics and copyright liability
C4 is a common academic dataset (150B tokens | 800GB)

## Compute
Compute: how much data you put in the model and the size of the model
Predict performance with compute using scaling laws so you can do research at low scale -> then later predict how the research holds at larger scale

The larger the scale, the better that transformers perform versus LSTMs
Chinchilla does not take into acccount if you have larger models, at inference time, it will cost more.
Bitter [Sutton 2019] => "only thing that matters in the long run is the leveraging of computation."

Estimated Cost of SOTA model (LLaMA 3 400B)
Data: 15.6T tokens and Parameters: 405B
FLOPs: 6NP = 6 * 15.6e12 * 405e9 = 3.8 e25 FLOPs
Compute: 16K H100 with average throughput of 400 TFLOPS
Time: 3.8e25 / (400e12 * 3600) = 26M GPU hour / (16e3 * 24) = 70 days (From paper: ~30M h)
Cost: retned compute + salary = $2/h *26Mh = ~$52M ($50-80M)
Carbon emitted=26Mh*0.7kW*0.24kg/kWh = 4400 tCO2eq (~2k return tickets JFK-LHR)

## Post-training
Problem: kabguage modeling wis not what we want
Classic post-training, alignment, or instruction-following
maximize answer preferences of humans in order to steer the model to be useful on real-world tasks

## Reasoning 
Goal: teach the model to reason
Task: answer correctly
Data: any hard task with verifiable answer

Task: "alignment"
We want LLM to follow user instructions and designer's designers (eg moderation)
Background:
* data of desired behaviors is what we want but scarce and expensive
* pretraining data scales but is not what we want

Supervised finetuning (SFT)
* Idea: finetune the LLM with language modeling of the desired answers
* How do we collect the data? Ask humans
* Problem: human data is slow to collect & expensive
* Idea: use LLMs to scale data collection

DeepSeek-R1 used rejection sampling based on verifiers
Since [1]. Temporarly LLM generates many answers
[2]. Keep answer if it's correct (eg, passes test case), or preferred over others)

Instruction following
Desired format or style
Tool use [eg Kimi 2 or xLAM]
Early reasoning [eg DeepSeek R1]
...anything where you can get good input, output pairs

SFT is either seen as a final stage or preparation stage for RL
SFT uses (a) synthesizing tool specs, agents and tasks, (b) Generating agent trajectories 

Scalable data for SFT:quantity
You learn quickly from SFT! ~ 10k are sufficient for learning style & IF

Problems with SFT:
1. Bound by human abilities: humans may prefer things that they are not able to generate
2. Hallucination: cloning correct asnwer teaches LLM to hallucinate if it didn't know about it

GRPO algorithm from DeepSeek-R1 used for RL
Infra is key: especially for agents, environemnt feedback can be slow: Kimi uses concurrent rollouts & dedicated services for envs

RL from Human Feedback (RLHF)
Idea: maximize human preference rather than clone their behavior
RLHF worked better than SFT and Pretraining algorithms.

Close-ended Evaluation:
few possible answers=>automate verification
MMLU [Hendrycks+ 2020]

Open-ended tasks => hard to automate
Idea: ask for annotator preference between answers

## Systems
Problem: everyone is bottlenecked by compute!
Why not buy more GPUs?
* GPUs are expensive and scarce!
* Physical limitations (eg communication between GPUs)
=> importance of resource allocation (scaling laws) and optimized pipelines

GPUs are massively parallel: same instruction applied on all threads but different inputs =>
optimized for throughput!

Compute improved faster than memory & communication. tough to keep the processor occupied with data

Memory hierarchy: Closer to cores => faster but less memory
Further from cores => more memory but slower

Metric: Model Flop Utilization (MFU)
Ratio: observed throughput / theoretical best for that GOU
50% is great!

## Systems: low precision
Fewer bits => faster communication & lower memory consumption
Matrix multiplications can use bf16 instead of fp32
Activation in bf16
matrix mul in bf16
gradient s in bf16
master weights updated fp32

every new PyTorch line moves variables to global memory

Order in which you perform operation can impact how you read from memory
With less data, you can do more computation

## Systems: parallelization
Want to use as many GPUs as possible
Split memory and compute across GPUs
You can have each GPU work on the same model but different set of data; later you can sum the gradients

For model parallelism, have every GPU take care of applying specific parameters;
Tensor parallel: split single matrix across GPUs and use partial sum

Models are huge => not every datapoint needs to go through every parameter;
MoE (Mixture of Experts): use a selector layer to have less "active" parameter => same FLOPs more parameters




