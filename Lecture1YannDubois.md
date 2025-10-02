# Introduction to training LLMs for AI agents

#Pretraining, Postraining, Reasoning RL and Classic post-training / RLHF
Pretrain: Predict next work on internet,
Reasoning RL: Think on questions with objective answers
RLHF: Max user utility & prefs
Second and third stages may be bundled together and collectively referred to as post-training.

# LLM training pipeline
Architecture, Training algorithm/loss, Data & RL env, Evaluation, Systems and infra to scale
Data, Evals, and Systems are what matter in practice.

#LLM specializing pipeline
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
Issues: 1. Need to keep count of all occurrences for each n-gram (Huge Memory Overhead)
2. Most sentences are unique: this can't generalize

Solution: neural networks (approximate prediction using parametric estimator)

#Neural Language Models
Split (I saw a cat on a ) into five input word embeddings {I}, {saw}, ...
Use non-linear aggregator of these vectors

A very simple neural network could just be an average of this.
<figure>
  <img src="./images/Figure1.png" alt="Figure 1" width="300">
  <figcaption>Figure 1: Neural LM schematic</figcaption>
</figure>
get probability distribution for the next token and then process the context (previous history)

#Pretraining Data
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

#Midtraining: 
->Continued pretraining to adapt the model to desired properties/ higher quality data (<1T tokens>)
->Data mix changes shifts: eg more scientific, coding, multilingual data
->Longer context extension: bump (eg 4 -> 128k for DSv3)
->Desired fomratting/isntruction following
->Higher quality data

Summary of Pre/mid training Data:
Collecting data well is a huge part of practical LLM
Lot of research to be done! (How do you process well and efficiently?)
Secrecy due to competitive dynamics and copyright liability
C4 is a common academic dataset (150B tokens | 800GB)

#Compute
Compute: how much data you put in the model and the size of the model
Predict performance with compute using scaling laws so you can do research at low scale -> then later predict how the research holds at larger scale

The larger the scale, the better that transformers perform versus LSTMs
Chinchilla does not take into acccount if you have larger models, at inference time, it will cost more.
Bitter [Sutton 2019] => "only thing that matters in the long run is the leveraging of computation."





