# CUBE Buildathon 2026 · 01 · Receiving Manager

**Commerce Context stream · Sydon Symphony sandbox · Two-week build**

> Five agents, one unit, one record that follows it.
> A physical product arrives, gets prepped, gets shipped, comes back. At every step a person makes a fast judgment that nobody records. Your pod builds the agent that makes one of those judgments, and leaves proof.

**New here? Read these first:**
1. [`GITHUB-GUIDE.md`](GITHUB-GUIDE.md) explains how to create your branch, where to put your work and how to open a PR.
2. [`RULES.md`](RULES.md) covers the repository rules (enforced) and the five engineering rules (assessed).

---

## Your problem statement: Receiving Manager

| | |
|---|---|
| **Position in the chain** | Step 1 of 5. Supplier delivery. |
| **Customer** | Seller or 3PL taking supplier delivery |
| **What gets recorded** | Condition on arrival |
| **Who consumes your output** | Prep Manager (next in the chain) and Recovery Manager (supplier and inbound claims) |

A pallet arrives from a manufacturer, often overseas. Someone opens the cartons and decides whether what arrived is what was ordered: right SKU, right count, undamaged, to the quality agreed. Today this is a spot check at best. Shortages and defects surface weeks later when units fail in prep or come back as returns, by which point the supplier conversation is unwinnable because nothing was recorded on arrival.

**What the agent returns, from photographs at the point of receipt:**

- Identity of the goods against the purchase order line
- Quantity received against quantity ordered, including carton count and units per carton
- Damage visible on cartons and units: crushing, water, tears
- Quality flags against the agreed spec: wrong colour, wrong variant, missing components, obvious defects

> This is where supplier disputes originate, and the only point at which a claim against the supplier is still possible. Every downstream problem in this chain is cheaper if it was caught here.

### The chain you are part of

```
 Supplier delivery      Inbound to Amazon     Outbound to buyer     Customer return        Money back
 ┌──────────────┐      ┌──────────────┐      ┌──────────────┐      ┌──────────────┐      ┌──────────────┐
 │ 01 Receiving │ ───▶ │ 02 Prep      │ ───▶ │ 03 Pack      │ ───▶ │ 04 Returns   │      │ 05 Recovery  │
 │ condition on │      │ compliance   │      │ contents at  │      │ condition &  │      │ reads all    │
 │ arrival      │      │ proof        │      │ seal         │      │ disposition  │      │ four → claim │
 └──────┬───────┘      └──────┬───────┘      └──────┬───────┘      └──────┬───────┘      └──────▲───────┘
        └─────────────────────┴─────────────────────┴─────────────────────┴─────────────────────┘
```

The first four are the same machine: a camera, a model, and a decision bound to a record. What changes is the ruleset, the buyer and the moment. The fifth has no camera. It turns the other four's records into a claim.

Your output has to be usable by another pod. That's deliberate, and it's scored.

---

## Reference data

`data/` holds a **dummy** CSV for reference while you design and build. Its columns and meanings are listed in [`data/README.md`](data/README.md).

**The data is synthetic.** The SKUs, ASINs, FNSKUs, orders, suppliers, operators and amounts are all invented. The requirement flags and fee amounts are **not** Amazon's real rules or fees. Engineering rule 5 applies: look the authoritative rule up. The `photo_refs` paths are placeholders, and no images ship with this repo. Your fixtures and eval set are yours to capture.

All five buildathon repos share the same `unit_id` values (`UNIT-0001` … `UNIT-0100`). You can follow one unit from receiving through recovery, the same way the real records will be joined. In the sample, each unit takes one route: **FBA** (prep, then Amazon ships it and charges fees) or **merchant-fulfilled / 3PL** (the seller packs it). So a unit has a Prep record or a Pack record, never both.



---

## How this works

**You will not be handed a spec.** Real products are built backwards from the customer and forwards through the evidence. You write what the customer would say before you write code. You write the press release as if it already shipped. You write down the number that would make you stop. Then you build, and then you measure whether any of it was true.

Every one of those documents exists to be proven wrong cheaply. A wrong assumption caught in a paragraph costs an hour. The same assumption caught in code costs a week. You are assessed on that as much as on running software.

### What you're given
- This problem statement
- A domain brief covering the real economics, fee structures and what a working day in a warehouse looks like *(shared by the organisers)*
- The engineering rules in [`RULES.md`](RULES.md)
- Sandbox access and a shared catalogue
- One fully worked package for Returns Manager (customer letter, PR/FAQ, one-pager) as a reference for the standard expected. **Read it. Don't copy it.**

### What you produce, in `submissions/<your-github-username>/`
- A customer letter in your customer's voice
- A PR/FAQ, including the questions you'd rather not answer
- A one-pager with a metrics table and a **kill condition**
- A `CLAUDE.md` and a build brief
- A build log you keep current
- An eval report with numbers and named failure modes
- A working agent

## The build sequence: six faces, in order

Each face has a deliverable. Don't skip forward.

| Face | Deliverable | The point |
|---|---|---|
| **1 · Outcome first** | Customer letter, PR/FAQ, one-pager. **No code.** | Write it honestly enough that it might argue against your own agent. Include at least one kill condition. |
| **2 · Context** | `CLAUDE.md` | Durable constraints, hard rules, where things live, and language you are not allowed to use. |
| **3 · Tools** | A working agent, headless first | Get it running against fixture images from a CLI before any UI. |
| **4 · Evals & guardrails** | A measured number, with its method | 50 unseen units. Two humans label each one independently, and you measure them against each other first. Report per check, with FP and FN separately. |
| **5 · Decision tracing** | The evidence record | Photos, timestamp, operator, every check and verdict, model version, overrides with reasons, and a content hash. Build it for a customer to read. |
| **6 · Agent comms** | Your output, consumed by another pod | Agree the cross-pod contract in week one, then hold it. |

## Two weeks

| Days | Work | Gate to move on |
|---|---|---|
| 1–2 | Customer letter, PR/FAQ, one-pager, CLAUDE.md. No code. | Another pod reads your one-pager and states your kill condition back to you |
| 3–4 | Schema and tenancy isolation. Headless agent, batched call, structured output. | Isolation test green. Agent runs on fixtures from a CLI. |
| 5–7 | Capture surface, decision screen, override capture. | Works on a real phone, on cellular, not office wifi |
| 8–10 | Evidence record page. Cross-pod contract. Fail-open behaviour. | Another pod's agent can read your records |
| 11–13 | Eval set, two human labellers, measurement. Fix what it surfaces. | A number per check, with failure modes written down |
| 14 | Present | — |

## Day 14: what you present (five minutes, in this order)

1. **The customer.** Who they are and what their day looks like (30 seconds).
2. **What you measured.** Accuracy per check, false positives and negatives, failure modes.
3. **The record,** as a customer would see it.
4. **One unit, live, end to end.** If it fails live, explain why.
5. **Your kill condition,** and whether your evidence tripped it.

> A pod reporting an honest 61% that knows exactly why will score above a pod reporting 95% it cannot break down.

## What we're being straight with you about

- **The core assumption is untested.** Nobody knows yet whether vision models can identify products and grade condition on long-tail catalogues without per-SKU training. Finding out that it doesn't hold, and documenting that clearly, counts as a successful outcome.
- **Nobody has spoken to a customer yet.** If you can get a real prep center or seller on a call, ask them to rank the five problems by urgency. Don't ask whether they'd buy what you're building.
- **The background documents disagree in places.** A contradiction is a finding. Raise it as an Issue labelled `finding`.

---

*CUBE Buildathon · Commerce Context stream · Sydon Symphony sandbox*
