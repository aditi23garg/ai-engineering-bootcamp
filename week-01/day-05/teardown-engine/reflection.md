# Reflection — AI Product Teardown Engine

## Question 1
### Which of the 6 layers surprised you the most in terms of 
### complexity for YouTube? Why?

Layer 2 (Statistics & Analysis) surprised me the most. I expected 
the ML models to be the hardest engineering problem, but the 
teardown revealed that metric design is arguably the most 
consequential intellectual work at YouTube. The fact that 
optimizing watch-time — a metric that seemed perfectly reasonable 
— produced the "rabbit hole" effect where users watched more but 
reported lower satisfaction showed that getting the math right 
matters less than deciding what to measure in the first place. 
This layer is invisible to users and rarely discussed in ML 
courses, yet a wrong metric choice at this layer created a 
decade-long trust and product problem that no amount of better 
neural network architecture could fix.

---

## Question 2
### What was the single biggest difference you noticed between 
### the LLMs you tested?

The biggest difference was how each LLM handled uncertainty. 
ChatGPT was the most conservative — it only named technologies 
it was confident about and explicitly flagged uncertainty, making 
it the safest choice for accuracy but sometimes less specific. 
Gemini was the most confident and vivid, naming Google-specific 
internal tools like Maglev and MMoE that turned out to be 
verifiable, but also occasionally naming things like "Halfshell" 
that could not be confirmed — showing that confidence and accuracy 
are not the same thing. Claude consistently produced the most 
non-obvious engineering insights, such as the ScaNN approximate 
nearest neighbor tradeoff (accepting 5% recall loss for 100x 
speed improvement) and the observation that defining the right 
satisfaction metric matters more than any model architecture 
choice — which is exactly the kind of thinking that distinguishes 
a strong engineering candidate from someone who has just memorized 
a list of technologies.
