---
layout: note
title: "What 1,000+ tests change in a solo-built SaaS"
slug: testing-architecture
date: 2026-11-14 09:00:00 +0100
last_modified: 2026-11-14
order: 2
category: Architecture
read_time: 7
description: "How 1,000+ tests shaped the architecture and operation of a solo-built production SaaS."
homepage_description: "Testing as an architectural constraint, not a number collected at the end."
archive_description: "Why a large test suite matters less as a number than as a constraint on architecture, boundaries, and operational risk."
deck: "The useful number is not the test count. It is the architecture the tests force you to build."
visual: test-stack
source_prefix: "This note is adapted from the public, sanitized"
source_label: "Dymora engineering case study"
source_url: "https://github.com/comprido96/dymora-engineering"
cta_label: "Discuss a system"
---

<p class="article-lead">Dymora’s core service has 1,049 automated tests: roughly 500 unit tests and 540 integration tests, including database-invariant coverage. That number sounds reassuring, but counting tests is a weak proxy for confidence. What matters is what the suite is allowed to observe—and what the codebase had to become to make that possible.</p>

## The suite exercises a running application

The integration tests start the real HTTP application on an ephemeral port. Requests pass through routing, authentication, validation, domain logic, and response serialization. The difference from production is at the boundaries: persistence, payments, push delivery, and device access use in-memory implementations.

This makes the HTTP contract fast enough to run routinely without pretending that a collection of isolated handler tests represents the system.

<aside class="article-callout"><strong>Architecture consequence</strong><span>Vendor types cannot leak into the domain if every external system must be replaceable by an in-memory adapter.</span></aside>

## Testability becomes a design constraint

The application state is built around explicit ports. The domain layer imports no payment or infrastructure SDKs; adapters map vendor data at the boundary. This is more ceremony than calling an SDK directly, but it buys two things a solo operator needs: fast tests and a small blast radius when a vendor changes.

The payment SDK, for example, belongs in one adapter rather than across handlers and business logic. A test double implements the same port. The suite therefore checks domain behavior without reproducing the vendor’s internals.

## In-memory tests are not enough

A second suite runs against Postgres to verify what the type system and in-memory adapters cannot prove: database constraints, retention behavior, guarded lifecycle transitions, and append-only audit history.

This division is deliberate. Most behavior should be tested quickly at the application boundary. Database-specific guarantees deserve slower, focused tests against the real database—not faith that the fake behaves identically.

<div class="article-metric-row" aria-label="Selected Dymora engineering metrics"><div><strong>1,049</strong><span>tests</span></div><div><strong>~63k</strong><span>Rust LOC</span></div><div><strong>49</strong><span>API paths</span></div></div>

## The operational payoff

When one person builds and operates the service, architecture and operations are the same decision. A deploy at 1 a.m. is less frightening when illegal transitions are rejected at multiple layers, webhook replays are idempotent, and the full HTTP surface can be exercised before promotion.

The lesson is not “write a thousand tests.” It is: make important behavior observable through stable boundaries, then test each guarantee at the layer that actually owns it.
