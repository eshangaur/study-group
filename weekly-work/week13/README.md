# Session XIII: Model Architectures for Answering Questions and Overcoming NLP Limits

*Meeting date: August __XX__, 2017* 

Lucky Session Numero 13 marks the end of our journey through the Stanford [Winter 2017 CS224N lectures](https://www.youtube.com/playlist?list=PL3FW7Lu3i5Jsnh1rnUwq_TcylNr7EkRe6) on *Natural Language Processing with Deep Learning*. This session marks the first anniversary of the Deep Learning Study Group, which held its first meeting on August 17th, 2016. Woot woot. 

A summary blog post, replete with photos of the session, can be found __[here](ADD LINK)__. In addition to wrapping up the CS224N lectures (detailed notes from the final four lectures below), we enjoyed presentations and discussions from: 

* [Marianne Monteiro](https://www.linkedin.com/in/mariannelinharesm/) on TensorFlow Recurrent Neural Network implementations
* [Druce Vertes](https://www.linkedin.com/in/drucevertes/) on predicting which financial stories go viral on social media




---
## Recommended Preparatory Work

The recommended preparatory work for this session was double the usual, i.e., lectures ten through fifteen: 

1. [Coreference Resolution](https://www.youtube.com/watch?v=rpwEWLaueRk&index=16&list=PL3FW7Lu3i5Jsnh1rnUwq_TcylNr7EkRe6)
2. [Dynamic Neural Networks for Question Answering](https://www.youtube.com/watch?v=T3octNTE7Is&index=17&list=PL3FW7Lu3i5Jsnh1rnUwq_TcylNr7EkRe6)
3. [Issues in NLP and Possible Architectures for NLP](https://www.youtube.com/watch?v=B4v545V3Dq0&index=18&list=PL3FW7Lu3i5Jsnh1rnUwq_TcylNr7EkRe6)
4. [Tackling the Limits of Deep Learning for NLP](https://www.youtube.com/watch?v=JYwNmSe4HqE&index=19&list=PL3FW7Lu3i5Jsnh1rnUwq_TcylNr7EkRe6)



---
## Lecture Notes

### Lecture 15: Coreference Resolution

#### What is Coreference Resolution? 

* identification of all noun phrases (i.e., mentions) that refer to the same real-world entity
* applications:
	* full text understanding, e.g., understanding an extended discourse
	* machine translation (especially if languages have different features of gender, number, etc.)
	* text summarisation, e.g., web snippets
	* information extraction / question-answering
	
#### Evaluation

* **B^3** algorithm for evaluation
* in essence, is precision and recall calculations for individual entity buckets: 

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/b_cubed.png)

#### Kinds of Reference

1. referring expressions
2. free variables
3. bound variables

#### Anaphora

* refers back to a word used earlier (e.g., "Jon typed *his* Markdown file.")
* contrasts with less-common, "never-modelled" **cataphora**, which reference a later word
* not all anaphoric relations are coreferential

#### Building Coreference Architectures

* fundamentally, these need to model nouns and pronouns (which are often anaphores) as distinct

##### Traditional Approach

* **Hobbs' Naïve Algorithm** (1976)
	* a complex sequence or rules to follow (nine steps, with "go to" elements)
	* used a feature as most NLP ML systems until a few years ago
	* what Hobbs was really interested in was **Knowledge-based Pronomial Coreference**
		* this requires an understanding of the world and a representation of likely relationships 
		* can be tested with Winograd (1972) challenges; resurrected in 2015

##### Types of Coreference Models

Each of these model types is introduced by Manning: 

1. Mention Pair models
2. Mention Ranking models
3. Entity-Mention models

##### Neural Coreference Models

* "not much has been done" (Manning)
* there are four relevant papers
	* by two author sets 
	* all since 2015
	* two of four Manning co-authored with Kevin Clark
		* one of the Clark & Manning papers involves deep reinforcement learning and is a Mention-Ranking model; Manning describes this approach in detail





---
### Lecture 16: Dynamic Neural Networks for Question Answering

#### Can *Any* NLP Task be Thought of as a Question-Answering Task?

* consider the following:

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/qa_examples.png)

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/more_qa_examples.png)

##### Major Obstacles

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/obstacle_1.png)

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/obstacle_2.png)

#### Dynamic Memory Networks

* an architecture that resolves the first major obstacle (but makes no in-roads on the second)
* multi-task learning
* if we have a problem to tackle like this...

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/harder_questions.png)

* ...a dynamic memory network is well-suited to the task: 

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/dynamic_memory_network.png)

* as a standard computer-science practice, modules should be independent; changes in one shouldn't affect interoperability with the others at their interfaces

##### Modules

* input module
	* computes hidden state for every word in a single continuous RNN (e.g., GRU) sequence
	* hidden state from last word in previous sentence forming starting point for first word in next sentence

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/input_module.png)

* question vector
	* also RNN, e.g., GRU
	* *can* share weights with input module
	* output hidden state *q* after going through hidden state of every word in question
	* *q*, e.g, incorporating hidden state for "football", triggers attentional mechanisms over input module

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/question_module.png)

* episodic module 
	* incorporates attention by having a global gate that turns off GRUs if deemed irrelevant to the question or memory
	* memory state *m* is final hidden state
	* *m* therefore stores any relevant facts, e.g., about "John" and "football"

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/episodic_module.png)

* *q* and *m* are considered jointly by answer module (softmax with cross-entropy error)
	* facilitates end-to-end training
* this architecture facilitates **transitive reasoning** (understanding relationship of A and D by A->B->C->D logic) by taking multiple passes over the inputs
	* in the example:
		1. first pass picks up significance of "John" to "football"
		2. second pass ties "John" to the hallway where he left the football
* **co-attention mechanisms** to revise context of question by inputs (e.g., "can I cut you?" means different things if input involves knife-wielding vs involving a queue of people)
* "surprisingly broad" set of "reasoning" capabilities as long as that type of reasoning is in the training set

#### Breadth of DMN Tasks with Single Architecture

Involves different hyperparameters between tasks (with number of a passes being a hyperparameter), but same architecture; tasks include: 

* **sentiment analysis** (two passes better than one as attention is sharper

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/sharper_attn.png)

* **part-of-speech tagging** (one pass sufficient)
* **visual question-answering** (state-of-the-art "out of the box")
	* doesn't, however, distinguish distinct objects
	* and can't count a large number of objects (anything more than a handful)

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/diff_inputs.png)

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/visual_attn.png)

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/visual_attn_2.png)

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/visual_attn_3.png)

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/tennis_Qs.png)

##### Summary

* most NLP tasks can be reducd to QA
* DMN accurately solves a variety of tasks
* more advanced option: **Dynamic Coattention Networks** (next lecture)

---
### Lecture 17: Issues in NLP and Possible Architectures for NLP

#### HEADING

* text

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/img.png)



---
### Lecture 18: Tackling the Limits of Deep Learning for NLP

#### HEADING

* text

![](https://github.com/the-deep-learners/study-group/blob/master/weekly-work/week13/img/img.png)



---
## Up Next

For our next session, the recommended preparatory work is wrapping up viewing the CS224N lectures, i.e.:

1. text
2. text