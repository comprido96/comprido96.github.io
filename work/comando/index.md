---
layout: case-study
permalink: /work/comando/
case_study: true
sitemap: true
order: 2
case_number: "002"
discipline: "Fine-tuning · Evaluation"
role: "Research and implementation"
title: "The evaluation was designed before the model won."
description: "How a frozen evaluation, shared parser, and failure analysis showed a fine-tuned 4B model outperforming frontier-model prompting."
index_description: "A reproducible fine-tuning experiment where the protocol, frozen set, baselines, and limitations are published with the result."
deck: "A small local model reached 98.5% exact match on a frozen evaluation set. The useful artifact is the protocol that makes the number defensible."
visual: evaluation
last_modified: 2026-11-15
metrics:
  - value: "98.5%"
    label: "exact match"
  - value: "+8.25pp"
    label: "over best prompted baseline"
  - value: "400"
    label: "frozen examples"
  - value: "0%"
    label: "invalid outputs"
source_label: "comando repository and raw reports"
source_url: "https://github.com/comprido96/comando"
cta_label: "Discuss an evaluation"
---

## The question

Italian smart-home commands must become structured actions: twelve intents, validated slots, multi-action utterances, and an explicit refusal class. The engineering question was narrow on purpose:

> Can a fine-tuned 4B model own this task more accurately than prompted frontier models—and can the comparison survive inspection?

The success criterion was fixed before training: beat the strongest prompted baseline on exact match, or land within two percentage points at substantially lower cost.

## Build the measurement before the result

The target schema rejects invalid shapes rather than repairing them. Seeded templates generate labelled pairs; an LLM may alter only the surface wording, and every rewritten pair is validated again.

From 639 candidates, 586 were kept after a complete manual review by one annotator. The first 400 became the frozen evaluation set. The remaining 186 formed the development set used for iteration. Each final model touched the frozen set once.

<aside class="article-callout"><strong>Comparison rule</strong><span>Every model used the same prompt contract and strict parser. Provider-specific structured-output modes were excluded so the experiment compared models rather than tooling.</span></aside>

## Result

The 1,500-example LoRA adapter reached 98.5% exact match and 98.73% action F1, with no invalid outputs. The strongest prompted baseline, gpt-5-mini, reached 90.25% exact match and 91.72% action F1.

<div class="work-result-bars" aria-label="Exact-match comparison"><div><span>Qwen3-4B + LoRA</span><i style="--score:98.5%"></i><strong>98.5%</strong></div><div><span>gpt-5-mini · 8-shot</span><i style="--score:90.25%"></i><strong>90.25%</strong></div><div><span>Claude Haiku 4.5 · 8-shot</span><i style="--score:88%"></i><strong>88.0%</strong></div></div>

## Why the smaller model won

Most frontier-model errors were not reasoning failures. They were coverage failures: Italian synonyms for a room had to map to one canonical slug, but eight examples could not communicate the complete normalization convention. Fine-tuning examples taught that mapping implicitly.

The unfine-tuned local model mostly failed on output shape rather than intent. Fine-tuning closed that gap completely, taking the invalid rate from 5.5% to zero.

## The ablation that stayed unresolved

On the development set, 1,500 training examples scored 95.70% exact match while the 5,000-example run scored 94.62%. The datasets were nested prefixes of the same shuffle, and both runs used one epoch.

The strongest untested explanation is that repetition mattered more than additional surface diversity for this closed vocabulary. A matched-iterations versus matched-epochs experiment would test that explanation. It was not run, so the result remains an explicit open question rather than a retrospective story.

## What still failed

The final adapter missed six of 400 frozen examples. Four involved debatable room carryover across multi-action commands. Two involved terse scene invocations whose longer form appeared in training but whose two-to-four-word form did not.

That distinction matters: one category exposes a label-design convention; the other exposes a real generalization gap.

## Limits

- One person reviewed all retained labels; there is no inter-annotator agreement measurement.
- The refusal class represents about five percent of the frozen set.
- Latency was measured in an unoptimized generation loop, not a production serving stack.
- The project proves a narrow structured task, not general superiority of small models.

The outcome is useful because the limitations, raw predictions, scorecards, and reproduction commands ship beside the headline number.
