---
layout: note
title: "Freeze the evaluation before improving the model"
slug: freeze-evaluation-before-optimization
date: 2026-11-15 18:30:00 +0100
last_modified: 2026-11-15
order: 1
category: Evaluation
read_time: 5
description: "A practical protocol for keeping model selection separate from the final measurement—and for knowing what a frozen evaluation does not prove."
homepage_description: "A frozen holdout keeps model selection separate from the result you eventually publish."
archive_description: "How I separated development from final measurement, versioned the evaluation contract, and kept corrections auditable."
deck: "If the final test changes with the model, improvement becomes impossible to distinguish from adaptation to the test."
visual: eval-freeze
source_prefix: "The frozen dataset policy, provenance, checksum, and raw results are public in"
source_label: "comando"
source_url: "https://github.com/comprido96/comando"
cta_label: "Discuss an evaluation"
syndicate_threads: true
threads_text: >-
  If the final test changes with the model, you are not measuring improvement. I used a frozen 400-example holdout and touched it once, after model selection. The protocol matters more than the headline score.
---

<p class="article-lead">A model evaluation can look rigorous and still become part of the training loop. Change the prompt after seeing final-test failures, replace disputed labels in place, or keep choosing checkpoints against the same holdout, and the reported score gradually stops being an independent measurement.</p>

For a small-model fine-tuning experiment, I treated the evaluation set as a versioned release artifact. Development could move quickly; the final measurement could not move with it.

## Separate selection from measurement

The source pool contained 639 generated candidates. I manually reviewed every candidate against explicit rejection criteria: changed meaning, broken Italian, or a disputable gold label. That left 586 verified examples.

The first 400 became `eval_v0`; the remaining 186 became the development set. Prompt changes, training-size comparisons, and adapter selection used development data only. The chosen adapter then touched the frozen set once for its reported result.

<div class="article-metric-row"><div><strong>639</strong><span>candidates</span></div><div><strong>586</strong><span>verified</span></div><div><strong>400</strong><span>frozen</span></div></div>

This separation does not make the evaluation perfect. It gives each dataset one job. Development data helps choose a system; frozen data estimates how that chosen system performs on examples it did not optimize against.

## Freeze the contract, not just the file

Read-only data is not enough if the surrounding rules can drift. The frozen artifact therefore includes its provenance, a checksum, rejection criteria, metrics, and a usage policy.

The rule is deliberately inconvenient: do not silently fix the frozen file. If later analysis exposes a questionable label, record the dispute in an audit trail. A material correction belongs in a deliberately cut `eval_v1`, where results can be compared with the evaluation version named beside them.

<aside class="article-callout"><strong>The freeze rule</strong><span>Iteration happens on development data. The final set is opened only for a selected candidate, and any correction creates an auditable version change.</span></aside>

Without that rule, a benchmark can improve at the same time as the model. The number rises, but nobody can tell how much came from a better system and how much came from moving the measuring instrument.

## Decide success before seeing the outcome

The experiment also fixed its acceptance rule before fine-tuning: beat the strongest prompted baseline on exact match, or finish within two percentage points while reducing cost by at least an order of magnitude. A negative result would still be published.

That rule limits retrospective storytelling. Once the numbers exist, it is easy to choose whichever metric, slice, or comparison flatters the preferred model. Precommitting to a primary metric and decision rule makes that choice inspectable.

The selected 1,500-example adapter ultimately reached 98.5% exact match on the 400 frozen examples, compared with 90.25% for the strongest prompted baseline. Those scores are useful because the selection procedure existed before either was known.

## What a freeze cannot prove

A clean protocol protects against one family of leakage. It does not establish that the benchmark represents production.

In this experiment, one person reviewed every retained label, the refusal class made up only about five percent of the frozen set, and some room-carryover labels encode a debatable convention. The evaluation is internally consistent, but it has no inter-annotator agreement measurement and cannot tightly bound behavior on underrepresented cases.

That distinction matters in client work. A trustworthy score requires two questions, not one:

- Was the measurement kept independent from system selection?
- Does the measurement cover the decisions and failure costs that matter in operation?

Freezing answers the first. Representative sampling, slice design, review quality, and production monitoring must answer the second.

## A reusable freeze protocol

For a new AI feature, I would keep the artifact small and explicit:

1. Define the target behavior, primary metric, and acceptance rule before optimization.
2. Record dataset provenance and review criteria.
3. Split development data from the final holdout before model selection.
4. Version and checksum the holdout and its scoring code.
5. Iterate on development data only.
6. Run the frozen set on selected candidates, not every experiment.
7. Log disputes separately; cut a new version for material corrections.
8. Report coverage gaps and review limitations beside the score.

The goal is not ceremonial process. It is to preserve one measurement that can still disagree with the system you want to ship.
