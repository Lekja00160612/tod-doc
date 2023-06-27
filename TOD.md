- task oriented previously only include [[slots]] and next actions, while it is effective in some cases, it is not extendable to deal with real world complex cases.
- TOD is complex for its real world usage, we do not want to spend time training model to adapt only a single job (say fight booking), thus, `extensibility` is a must.
- Traditional system suffers from hallucination (for example, the song "Animal" might be interpreted as living object without proper understanding of the context). One propose solution is to `symbolize` any possible values and train the model while blind folding the value from the model.
## Extensibility
- [[AnyTOD]] proposed an programmable [[dialogue schema]] includes [[params]], [[user actions]], [[system actions]]. The idea is fairly distinguished as it includes new fields [[user actions]] and [[system actions]] which never mentioned in any previous research.
- The new fields developed advanced method to further control [[dialogue flow]]. Defining possible actions allow the model to understand the context and act based on concrete examples. The idea was great but not perfectly flawless as the system did not predict next action.
- We believe having similar format like [[AnyTOD]] could help to predict next actions, based on [[user actions]] and [[system actions]]. We proposed additional tags [[constraints]], [[dependencies]], [[target actions]], [[tags]] for the inputs and [[history]], [[next actions]] for outputs.
### Goal
-  Our main goal is to define a `Domain Specific Language for TOD`, reasoned from 2 main points:
	- If NLP is a programming interface, the interface would not be capable of trustfully transmitting the programmer intention. While C or Python as programming interfaces compress of rules which programmers via these rules can express their program logics. -> There must be an alternative interface exist in the middle that includes some rules to follow and flexibility of NLP.
	- [[dialogue flow]] can never be captured without prior instruction, even in the case of human worker. Through commonsense, human can easily extract `slots` and `actions` of peer speaker, this commonsense can be thought as a model fine-tuned on mass amount of data and do not need extra contexts during inference to generalize well. This is not the case with AI model, pretrained-models are weak at [[dialogue flow]] recognition and data for domain-tuning in rarely exist in reality. -> Some form of instructions must be available during inference and they must be `enough`.
- This is the part where we enforce some rules and support the model externally by utilizing structured format
### Format
- Every [[TOD task]] needs a target. If the target is not always achievable (for example offer user movie), the system will fail. Thus, the [[target actions]] must be actions occur sooner than any branching conditions. Query DB is perfectly matched with the criteria. To get to the [[target actions]], we must specify [[dependencies]], guiding the model how to reach [[target actions]] - this is the [[main flow]].  In the process, user might requests [[slots]] from [[item character]], to make sure system inform these requests, we use [[constraints]] as a way to remind the system to process [[sub flow]]s, for example putting "user request x -> system inform x" might help the model to decide to inform information user asked previously.
- Example of our format:

```
INPUTS
tasks: [params], [conversation] -> [states]; [useracts], [sytemacts], [dependencies], [constraints] -> [next actions]; ... -> [history]
tags: flight booking, customer support, ...

[params] p0=[slot0 desc] p1=[slot1 desc]...

[useracts] u0=[useraction0] u1=[useraction1]... u4=out of doamin

[sysacts] s0=[systemaction0] s1=[systemaction1]... s5=query data base s6=out of domain

[dependencies] u2,u3->s5; u2,s3->s6

[target actions] s5

[constraints] user request pi -> system inform pi; target action depend on pi -> system request pi

[conversation] [user] utterance [system] utterance... \n

  
OUTPUTS
[states] p_i=[value_i] p_j=[value_j]...

[history] u_i u_k; s_j;...

[next actions] s3 s4 s1
``` 
### Issues
- [[OOD]]: users and systems do not always behave as defined in the instructions, we have to gracefully handle these cases. 
	![[OOD#Solution]]
- [[option selection]]: systems might offers user multiple items, it is sometime confusing even for  human worker, multiple confirm turns usually involved and create further complexity.
	![[option selection#Solution]]
## Context enhancement
- To fully control [[dialogue flow]], utilizing format is not enough, the model inner must also capable of following the context. 


## Related
- [[TOD]] involves several modules, including:
	- Module 1: [[DST]]
	- Module 2: [[policy manager]]
	- Module 3: [[TOD generator]]
- Our proposed [[TOD]] system includes structured-granularity levels, each level may have their independent [[params]], [[user actions]], [[system actions]]:
	- [[TOD domain]]
	- [[TOD task]]
	- [[item character]] 