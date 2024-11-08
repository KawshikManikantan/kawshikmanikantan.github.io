---
layout: post
permalink: /MEI/
title: Research
description: Should contain info on current research. For now placeholder for MEI.
nav: true
nav_order: 2
date: 2024-11-8 00:22:16
_styles: >
    .list-container {
        background-color: #f8f9fa;
        border: 1px solid #dee2e6;
        border-radius: 8px;
        padding: 20px;
        margin-bottom: 20px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
    .list-group-item {
        display: flex;
        align-items: center;
        font-weight: 500;
        transition: background-color 0.2s ease-in-out;
    }
    .list-group-item:hover {
        background-color: #e9ecef;
    }
    .list-icon {
        font-size: 1.2rem;
        color: #007bff;
        margin-right: 10px;
    }
    
    .block-tip {
        border-color: var(--global-tip-block);
        background-color: var(--global-tip-block-bg);

        p {
        color: var(--global-tip-block-text);
        }

        h1,
        h2,
        h3,
        h4,
        h5,
        h6 {
        color: var(--global-tip-block-title);
        }
    
---

## Major Entity Identification

##### [Paper](https://arxiv.org/abs/2406.14654) &nbsp; &nbsp;  [Code](https://github.com/KawshikManikantan/MEI) &nbsp; &nbsp;  [Demo](https://huggingface.co/spaces/KawshikManikantan/MEIRa) 
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/CR.gif" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/MEI.gif" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    CR vs. MEI. The CR task aims to detect and cluster all mentions into different entities, shown in various colors. MEI takes major entities as additional input and aims to detect and classify the mentions that refer only to these entities.
</div>

The limited generalization of coreference resolution (CR) models has been a major bottleneck in the task’s broad application. Prior work has identified annotation differences, especially for mention detection, as one of the main reasons for the generalization gap and proposed using additional annotated target domain data. Rather than relying on this additional annotation, we propose an alternative referential task, Major Entity Identification (MEI), where we: (a) assume the target entities to be specified in the input, and (b) limit the task to only the frequent entities. Through extensive experiments, we demonstrate that MEI models generalize well across domains on multiple datasets with supervised models and LLM-based few-shot prompting. Additionally, MEI fits the classification framework, which enables the use of robust and intuitive classification-based metrics. Finally, MEI is also of practical use as it allows a user to search for all mentions of a particular entity or a group of entities of interest.

<hr>

#### Motivation
Let us consider the following applications and their common features. MEI is a direct solution for all these applications. `MEI` is **more reliable** than other solutions like `heuristic prompting` and `coreference resolution`. It is more generalizable, tailored for extraction of specific entities and is faster w/ parallelization.
<div class="container mt-5">
  <div class="row">
    <!-- First List -->
    <div class="col-md-6">
      <div class="list-container">
        <h4 class="text-primary mb-3">Applications: </h4>
        <ul class="list-group">
          <li class="list-group-item">
            <span class="list-icon">&#9733;</span> <!-- Star icon -->
            Character Understanding in Movies, Novels
          </li>
          <li class="list-group-item">
            <span class="list-icon">&#9733;</span> 
            Pseudonymization
          </li>
          <li class="list-group-item">
            <span class="list-icon">&#9733;</span> 
            Financial Data Analysis
          </li>
          <li class="list-group-item">
            <span class="list-icon">&#9733;</span> 
            Entity/Concept-based Context Retrieval
          </li>
        </ul>
      </div>
    </div>
    <!-- Second List -->
    <div class="col-md-6">
      <div class="list-container">
        <h4 class="text-primary mb-3">Common Requirements</h4>
        <ul class="list-group">
          <li class="list-group-item">
            <span class="list-icon">&#9733;</span> 
            Long Documents
          </li>
          <li class="list-group-item">
            <span class="list-icon">&#9733;</span> 
            Pre-defined set of entities
          </li>
          <li class="list-group-item">
            <span class="list-icon">&#9733;</span> 
            Interest in occurence of specific entities
          </li>
          <li class="list-group-item">
            <span class="list-icon">&#9733;</span> 
            Dense Annotations
          </li>
        </ul>
      </div>
    </div>
  </div>
</div>
<div class="caption">
    Tracking certain entities for applications listed above have multiple common features and require a common, reliable and faster solution - MEI.
</div>

<hr>

#### MEIRa Models
We propose a family of models -- Major Entity Identification via Ranking (`MEIRa`) to perform MEI.MEIRa takes inspiration from the Entity Ranking (ER) family of models from coreference resolution. ER models maintain a list of entity representations that is dynamically updated upon association of mentions.

MEIRa takes as input a document and a set of succinct representative phrases, each uniquely identifying a set of entities to track. The document is then encoded with a language model like `LongFormer` and then passed through a mention detector. There are two-types of linking modules: a `static` linking module that is **effective and efficient** and is parallelizable, a `hybrid` linking module which is equally fast as any coreference resolution model and offers SOTA performance on the task. For more details, visit the [paper](https://arxiv.org/abs/2406.14654).

<div class="container center-container">
    <div class="row">
        <div class="col-12 text-center">
            {% include figure.liquid loading="eager" path="assets/img/method.png" class="img-fluid rounded z-depth-1" %}
        </div>
    </div>
</div>
<div class="caption">
   Identification module of MEIRa. A mention encoding mi is concatenated with each entity’s embedding in EW and the metadata χ(mi, ej ). Network f scores the likelihood of assigning mi to each major entity. If the highest score s∗i is above the threshold τ , mi is associated with the highest scoring major entity e∗i or discarded. In MEIRa-S, the entity memory EW remains static. For MEIRa-H (blue path), the assigned entity’s working memory is updated, and both the static (top half) and updated working memory (bottom half) are utilized to compute a final score.
</div>

<hr>

### Evaluation of LLMs
We also evaluate LLMs for their end-to-end referential capabilities on MEI. MEI is a generalizable across annotation strategies and is hence more reliable. We provide the **first** evaluation of referential capabilities that measures both the `mention detection` and `association`. We design a **novel** two-stage prompt that performs word-level coreference and later builds the entire span. We highlight the higher efficiency of this prompting strategy over a single-stage prompt that performs MEI at a span-level.


> ##### Instruction
>
> You will receive a text along with a list of Key Entities and their corresponding Cluster IDs as input. Your task is to perform Coreference Resolution on the provided text to categorize "each word belonging to a cluster" with its respective cluster id. Also briefly describe the key entities in 1-2 sentences before starting the coreference task. Follow the format below to label a word with its cluster ID: word#cluster_id

<div class="container mt-5">
  <div class="row align-items-stretch">
    <!-- First text block -->
    <div class="col-md-6 d-flex">
      <div class="p-3 bg-light border rounded">
        <h5>Input</h5>
        <p>
            <h6> Key Entities: </h6>
            <ol>
                <li>#1 - Katherine Hilbery</li>
                <li>#2 - Mrs. Hilbery</li>
            </ol>
        </p>
        <p>
            <h6> Text: </h6>
            CHAPTER I It was a Sunday evening in October, and in common with many other young ladies of her class, Katharine Hilbery was pouring out tea. Perhaps a fifth part of her mind was thus occupied, and the remaining parts leapt over the little barrier of day which interposed between Monday morning and this rather subdued moment, and played with the things one does voluntarily and normally in the daylight. But although she was silent, she was evidently mistress of a situation which was familiar enough to her, and inclined to let it take its way for the six hundredth time, perhaps, without bringing into play any of her unoccupied faculties. A single glance was enough to show that Mrs. Hilbery was so rich in the gifts which make tea-parties of elderly distinguished people successful, that she scarcely needed any help from her daughter.
        </p>
      </div>
    </div>

    <!-- Second text block -->
    <div class="col-md-6 d-flex">
      <div class="p-3 bg-light border rounded">
        <h5>Output</h5>
        <p>
            <h6> Description: </h6>
            <ol>
                <li>#1- Katharine Hilbery: A young and apparently rich lady and the daughter of Mrs. Hilbery. She and Mrs. Hilbery were organizing a party for some distinguished elders.</li>
                <li>#2- Mrs. Hilbery: She is the mother of Katharine Hilbery and is a well-to-do member of the society and a very efficient and able hostess</li>
            </ol>
        </p>
        <p>
            <h6> Output: </h6>
            CHAPTER I It was a Sunday evening in October, and in common with many other young ladies of her#1 class, Katharine#1 Hilbery#1 was pouring out tea. Perhaps a fifth part of her#1 mind was thus occupied, and the remaining parts leapt over the little barrier of day which interposed between Monday morning and this rather subdued moment, and played with the things one does voluntarily and normally in the daylight. But although she#1 was silent, she#1 was evidently mistress of a situation which was familiar enough to her#1 , and inclined to let it take its way for the six hundredth time, perhaps, without bringing into play any of her#1 unoccupied faculties. A single glance was enough to show that Mrs.#2 Hilbery#2 was so rich in the gifts which make tea-parties of elderly distinguished people successful, that she#2 scarcely needed any help from her#2 daughter#1
        </p>
      </div>
    </div>
  </div>
</div>

<hr>

#### Sample Outputs
