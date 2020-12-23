---
layout: post
title: "Google ML Jam Course 1: How google does Machine learning?"
published: true
mathjax: true
date: "2019-09-01"
category: [Deep-Learning]
---
 

### Module 1&2

* Model: math function
* Supervised Learning: adjust model to match output and label

> input, label --> model --> output


* ML have two stages: training and inference
    * Both are important for ML be useful.
    * Inference takes inputs that model has not seen.

* replace heuristics with model
* starts with heuristics but use ML when have data

### Use case questions:
* If the use case was an ML problem....
    1. What is being predicted?
    2. What data is needed?

* Now imagine the ML problem is a question of software:
    1. What is the API for the problem during prediction?
        * Inputs & Outputs
    2. Who will use this service? How are they doing it today?

* Cast it in the framework of a data problem. What are some key actions to collect, analyze, predict, and react to the data/predictions
    1. What data are we collecting?
    2. What data are we analyzing?
    3. What data are we predicting?
    4. What data are we reacting to?


> ML is scaling beyond hand-written rules.
> Simple model + more data > Complex model + less data

* Don't jump into ML! Start with manual data analysis to know your data!

##### Training-serving skew
* Batch data in training v.s. streaming data in prediction
* The pipeline should handle both to get the same input for the model.

> Fail fast and iterate!


---
### ML business (Module 3)

* Defining KPI's
* Collecting data
* Building infra
* Optimization
* Integration

* Effort Allocation
    * Top1: Collecting data: to gather more good-quality data to serve this problem.
    * Top2: Building infra: make sure to serve smoothly to users.
    * Top3: Integration

* What is the application for?
    * Is your ML is improving the real world?

> ML value comes along the way.

##### The generic feed-back loop

```flow
st=>start: inputs 
e=>end: done
process=>operation: Process
output=>operation: Output
ig=>operation: Insight generation (Big Data)
tune=>operation: Tuning (ML)
cond=>condition: Good enough?

st->process->output->ig->tune->cond
cond(yes)->e
cond(no)->process
```

* Process: 
    * Individual: prototyping, trying new ideas.
    * Delegation: Gently increase people involved, formalize the process.
    * Digitization: Automate repetitive parts. Need IT + ML to both succeed.

* Insight generation (Analytics):
    * Measure data and reflect the product to tune it.
    * Redefine success.

* Tuning: Automated feedback that is faster than many humans.

---
### Inclusive ML: fairness of ML (Module 4)

* Interactive bias: user interactions may inclined to interact with one over the other.
* Latent bias: More male scientists than female ones during history
* Selection bias: do you sure data represent everyone?


* Confusion matrix: False Positive v.s. False negative
    * Select the appropriate metric according to the problem (Avoid FP or avoid FN is more important?)

* Equality of oppurtunity: equal True positive rate for each groups.
* A data visualization tool: Facets


---
### Python notebooks in the Cloud

* Serverless service:
    * Jupyter notebooks: DataLab.
    * BigQuery: aggregate big dataset using SQL.

* Google pre-trained APIs:
    * vision
    * intelligence (for image/video)
    * speech
    * translation
    * natural-language

---
### Thoughts

* It is cool that google shared its procedure of transforming to a AI-first company by going through the five steps. 
