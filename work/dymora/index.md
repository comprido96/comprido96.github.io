---
layout: case-study
permalink: /work/dymora/
case_study: true
sitemap: true
order: 1
case_number: "001"
discipline: "Production SaaS"
role: "Founder · Solo engineer"
title: "Correctness when the team is one."
description: "How a solo-built Rust SaaS uses explicit boundaries, guarded transitions, and 1,049 tests to make production operation manageable."
index_description: "A live Rust SaaS with paying customers, explicit architectural boundaries, and operating rules shaped by production incidents."
deck: "A live subscription product designed, built, and operated by one engineer—where every architecture decision eventually becomes an operations decision."
visual: system
last_modified: 2026-11-15
metrics:
  - value: "1,049"
    label: "automated tests"
  - value: "~63k"
    label: "lines of Rust"
  - value: "49"
    label: "API paths"
  - value: "41"
    label: "reversible migrations"
source_label: "Public Dymora engineering case study"
source_url: "https://github.com/comprido96/dymora-engineering"
cta_label: "Discuss a production system"
---

## The constraint

Dymora is a production SaaS with paying customers. I built and operate its core service alone. That changes the optimization target: a design is not successful merely because it ships; it must remain understandable when an event arrives twice, two actors race, or a release needs to be reversed under pressure.

The service owns identity, customer lifecycle, billing, notifications, and operational workflows. The difficult part is not the number of features. It is keeping those workflows consistent while independent actors can act at the same time.

<aside class="article-callout"><strong>Operating principle</strong><span>When the builder and operator are the same person, architecture is part of the incident-response system.</span></aside>

## Decision 01 — Guard transitions at the owner of truth

Lifecycle changes pass through one guarded transition operation: the write includes its allowed predecessor conditions, and zero affected rows means the precondition lost a race. Database constraints and a dedicated test suite enforce the same rules again.

The point is not the state-machine vocabulary. It is that competing events cannot both half-win. Invalid transitions fail at the layer that owns the invariant rather than depending on every caller remembering the rule.

## Decision 02 — Put infrastructure behind explicit ports

The application is organized around roughly twenty explicit ports. Domain code does not import vendor SDK types; adapters translate at the boundary.

This makes the real HTTP application testable against fast in-memory implementations, while a separate database suite verifies constraints, retention behavior, and append-only history against the actual database.

<div class="work-decision-grid"><div><strong>~500</strong><span>unit tests</span></div><div><strong>~540</strong><span>integration tests</span></div><div><strong>real HTTP</strong><span>application boundary</span></div></div>

## Decision 03 — Make duplicate events boring

Billing events are unordered and at-least-once. A fast duplicate filter removes routine replays, while the durable event record is written in the same transaction as the state change. If the durable record already exists, the operation becomes a no-op.

The fast layer can disappear without affecting correctness. It improves cost and latency; it is not the source of truth.

## Decision 04 — Separate building from promotion

Production artifacts are immutable. Promotion is a deliberate human action against a pinned artifact, and schema changes have tested reverse paths. Configuration is validated before migrations advance the database, so a broken release fails before creating an incompatible schema boundary.

## What the evidence supports

- The product is live and serves paying customers.
- The core service contains about 63,000 lines of Rust and exposes 49 documented API paths.
- The suite contains 1,049 tests across unit, HTTP-level integration, and database-invariant coverage.
- All 41 migrations have tested down paths.

These numbers do not prove that defects are impossible. They show where correctness is owned and how the system makes regression and recovery observable.

## Honest costs

I overbuilt an internal administration surface before evaluating off-the-shelf alternatives hard enough. Some automation also arrived after the first production workflows and had to converge on reality rather than define it from day one.

The lesson was not “automate everything.” It was to attach engineering effort to the next operational risk or customer constraint, and deliberately leave lower-value work unshipped.
