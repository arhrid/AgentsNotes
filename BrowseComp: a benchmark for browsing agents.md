#BrowseComp: a benchmark for browing agents
https://openai.com/index/browsecomp/

Created challenging questions through creating an "inverted" question,
where the answer is hard to find but easy to verify. 
Trainers began with a "seed" (could be a person, event, or artifact),
find several characteristics w/ a large search space, and create a question from them.

Ex. Give me the title of the scientific paper published in the EMNLP conference between 2018-2023 where the first author did their undergrad at Dartmouth College and the fourth author did their undergrad at University of Pennsylvania. (Answer: Frequency Effects on Syntactic Rule Learning in Transformers, EMNLP 2021)

Humans also tried to solve questions through browsing.
<figure>
  <img src="./images/Figure3.png" alt="Figure 3" width="450">
  <figcaption>Figure 3: Histogram of Human Performance on BrowseComp</figcaption>
</figure>

Asymmetry of Verification (Questions that are difficult to solve but easy to verify)

Useful Browsing for an AI Agent:
*Reason about the factuality of content on the Internet
*Have persistence depth of browsing ability
*Model should be creative in searches to find accurate answer since brute-force searches are too time-intensive to find answers.

A reliable agent should not only produce correct answers, but also accurately assess its own uncertainty.
GPT-4o with Browsing and Deep Research exhibit higher calibration error, so there may be greater confidence in 
incorrect answers from access to web tools.

For agents, performance scales with respect to the amounf o compute used at inference time,
(aka test-time compute)
## Aggregation Strategies leveraging additional compute


##



##
