# Integration Scoping Checklist — The Semantic Layer

**Purpose:** The questions that have to be answered before an integration can be scoped, estimated, or built.

**Scope:** This is not about transport. REST, webhooks, OAuth and iPaaS platforms move data between systems, and when they fail they fail loudly — a call errors, a token expires, a queue backs up. This checklist covers the layer underneath: whether the data still means the same thing after it arrives. That layer fails **silently**. The interface reports success, the records land, and the divergence surfaces weeks later in a reconciliation, a report, or an audit.

No platform answers these questions. They are answered by someone who understands what the data means in the business process it came from.

---

## 1. Meaning

**Establish:** For every field in scope, what does it actually represent in the source system — not what it is called.

- What business event causes this field to be populated, and by whom?
- Is the field used as designed, or has it been repurposed? (Legacy systems accumulate fields used for something other than their label.)
- Are there values that carry meaning by convention rather than by definition — a prefix, a flag, a reserved code?
- Where the same concept exists in both systems, do the two definitions actually coincide, or merely share a name?

**If skipped:** The mapping is built on field names. Data arrives in the right columns and means something different.

---

## 2. Identity and Matching Keys

**Establish:** How the two systems recognize that two records are the same thing.

- What is the key? Is it stable over time, or can it be re-used or re-issued?
- Is it unique in both systems, or unique in one and not the other?
- If no common key exists, what composite key or cross-reference table has to be maintained, and by whom?
- What happens to records that exist on one side and have no counterpart on the other?

**If skipped:** What looked like field mapping turns out to be master data governance. This is a common source of underestimated effort in integration projects.

---

## 3. Grain and Cardinality

**Establish:** One record in the source corresponds to how many in the target.

- Is the relationship one-to-one, one-to-many, or many-to-one?
- Does the source hold detail the target cannot represent, or the reverse?
- Where the grains differ, is aggregation or splitting required — and on what rule?

**If skipped:** Totals reconcile while detail does not, which can be difficult to detect and especially damaging to trust once found.

---

## 4. Status and Enumeration Mapping

**Establish:** How the status model of one system maps onto the other.

- List both enumerations in full. They will not be the same length.
- Where several source statuses collapse into one target status, what business distinction is being lost, and does anyone depend on it?
- Where one source status expands into several, what decides which one?
- Are there statuses that exist only as transitional states and should not be transmitted at all?

**If skipped:** This looks like a configuration task and is actually a business decision. Collapsing "quoted" and "awaiting customer confirmation" into a single "pending" removes a distinction someone was using to run their day.

---

## 5. Direction and Ownership

**Establish:** Which way data flows, and — where it flows both ways — who wins.

- Is the exchange one-way or bidirectional?
- For a bidirectional exchange, ownership must be defined **per field, not per object.** The same customer record may be authoritative for address in one system and for credit limit in the other.
- On conflict, what is the rule: last write wins, source system wins, or human resolution?
- Can a field be edited on the non-authoritative side at all? If yes, what happens to that edit?

**If skipped:** The two systems overwrite each other after go-live, and the fix requires re-deciding ownership under production pressure.

---

## 6. Sequence and Dependency

**Establish:** The order in which things must exist.

- What must be present in the target before a given record can be accepted? (A customer before an order; a part before a receipt.)
- What happens when messages arrive out of order — rejected, queued, or accepted with a dangling reference?
- Are there business processes that constrain timing independently of the interface, such as period close?

**If skipped:** The integration works in testing, where data was loaded in a sensible order, and fails in production, where it is not.

---

## 7. Failure Semantics and Replay

**Establish:** What state the target is in when something fails halfway.

- Is the unit of transfer atomic, or can it partially apply?
- Is the operation idempotent — does re-sending the same message produce the same result, or a duplicate?
- How is a failure detected, by whom, and how is it retried?
- If the source record is corrected after transmission, does the correction propagate, or does the error persist downstream?

**If skipped:** Errors propagate rather than being contained. A posted transaction that has already crossed an interface cannot be corrected by editing the source.

---

## 8. Reconciliation

**Establish:** How anyone proves, after go-live, that the two systems agree.

- What is counted or compared, at what grain, and how often?
- What tolerance is acceptable, and what constitutes a break?
- Who looks at it, and what do they do when it breaks?

**If skipped:** No one knows whether the integration is working until a customer or an auditor finds out first.

---

## What Scoping Should Produce

- A field-level mapping that records **meaning**, not just source and target names
- The identity and key strategy, including any cross-reference to be maintained
- Full enumeration mappings, with the business decisions behind collapses noted
- A per-field ownership matrix for anything bidirectional
- Sequencing and dependency rules
- Failure handling and replay behaviour
- The reconciliation method
- An explicit list of **what is not yet known**, because that list is what the estimate is actually built on

---

## A Note on Effort Estimation

The estimate does not follow from the number of fields or endpoints. It follows from **how many of the questions above have determinate answers.**

An integration with two hundred well-defined fields and a stable common key is smaller than one with twenty fields where no one can say which system owns them. Much of the variance in integration projects comes from decisions that were deferred, not from work that was underestimated.

So the scoping deliverable is not only a design. It is a statement of which answers exist, which have to be decided, and who has to decide them.

---

## Principle

> Transport failures announce themselves. Semantic failures do not. The work of scoping an integration is to make every assumption about meaning explicit while it is still cheap to be wrong.
