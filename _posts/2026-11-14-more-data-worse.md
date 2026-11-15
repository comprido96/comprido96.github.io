---
layout: note
title: "When more training data performs worse"
slug: more-data-worse
date: 2026-11-14 08:00:00 +0100
last_modified: 2026-11-14
order: 4
category: Evaluation
read_time: 6
description: "Why a 1,500-example LoRA run outperformed 5,000 examples—and what the result does not prove."
homepage_description: "The non-monotonic data-size result from comando, published rather than explained away."
archive_description: "A 1,500-example adapter beat the 5,000-example run. The useful result is the unanswered question, not a tidy story."
deck: "The 1,500-example adapter beat the 5,000-example run. Publishing the unresolved result is more useful than inventing a clean explanation."
visual: ablation
source_prefix: "All data, raw predictions, limitations, and reproduction commands are public in"
source_label: "comando"
source_url: "https://github.com/comprido96/comando"
cta_label: "Discuss an evaluation"
---

<p class="article-lead">In comando, I fine-tuned Qwen3-4B to parse Italian smart-home commands into structured actions. Before touching the frozen evaluation set, I compared adapters trained on 500, 1,500, and 5,000 examples. The middle run won.</p>

## A result designed to be comparable

The three datasets were nested prefixes of one deterministic shuffle: 500 is contained in 1,500, which is contained in 5,000. The model, LoRA rank, batch size, learning rate, and one-epoch training rule stayed fixed.

That matters because the curve asks a narrow question: does adding more examples from this generator improve the result under the same training recipe?

<div class="article-table" role="table" aria-label="Ablation results"><div role="row"><span role="columnheader">Train size</span><span role="columnheader">Exact match</span><span role="columnheader">Action F1</span><span role="columnheader">Invalid</span></div><div role="row"><span role="cell">500</span><span role="cell">73.12%</span><span role="cell">76.61%</span><span role="cell">1.61%</span></div><div role="row" class="table-best"><span role="cell">1,500</span><span role="cell">95.70%</span><span role="cell">96.76%</span><span role="cell">0.54%</span></div><div role="row"><span role="cell">5,000</span><span role="cell">94.62%</span><span role="cell">95.61%</span><span role="cell">0.0%</span></div></div>

## What the result might mean

All runs used one epoch. The 5,000-example model therefore saw about 3.3 times as many gradient steps, but each pattern appeared proportionally less often. For a structured task with a closed intent and slot vocabulary, repetition may have mattered more than additional surface diversity.

That is a hypothesis, not a conclusion. A matched-iterations versus matched-epochs experiment would separate the effects, and I did not run it.

<aside class="article-callout"><strong>The useful discipline</strong><span>State the strongest untested explanation, then label it as untested. Do not convert a surprising curve into a confident story.</span></aside>

## Why 1,500 became the final adapter

The selection rule was fixed before the frozen evaluation run: choose the adapter with the best development score, then touch the 400-example frozen set once. The 1,500-example adapter reached 98.5% exact match there, versus 90.25% for the strongest prompted baseline, gpt-5-mini.

The headline is not that less data is generally better. It is that dataset size, repetition, diversity, and optimization budget interact—and the development protocol must decide before the final result is known.
