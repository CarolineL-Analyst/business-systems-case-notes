# Case: Diagnosing Cross-System Failures Without Access to the Interface

**Domain:** Aviation MRO / Parts Trading — ERP integrated with customer-side financial systems
**Role:** Business systems advisor — first point of diagnosis
**Nature of the problem:** Recurring class of problem rather than a single incident: locating the point of failure in a system-to-system exchange with no access to the interface, its logs, or the receiving system

---

## 1. The Presenting Question

A customer sends a screenshot of an API error, or reports that a figure in their financial system does not match the ERP. The question is always the same:

*"Where did this go wrong?"*

The constraint is also always the same: **I have no access to the interface, its logs, or the customer's financial system.** The only things available are the ERP side, whatever the customer has sent, and whatever I can ask them to send next.

---

## 2. Why the Standard Approach Was Not Available

With interface access, this is a mechanical investigation: read the log, inspect the request and response payloads, reproduce the call. None of that was available.

- The error message arrives as an image, detached from its request context.
- The receiving system can only be observed through what the customer chooses to show.
- The exchange cannot be replayed.

So the investigation could not proceed by observation. It had to proceed by **inference** — forming a hypothesis about what failed, deriving what that failure would look like on the far side, and then asking for exactly the evidence that would confirm or eliminate it.

---

## 3. What Had to Be Established First

- What does the error text actually indicate — authentication, a rejected payload, a validation failure, a missing or malformed field, or a transport-level failure?
- Did the transaction leave the ERP at all, and in what state is the ERP record now?
- Does a counterpart record exist on the receiving side — absent, present but wrong, or present and partially populated?
- If the counterpart holds the values that were sent, what has the receiving side done with them since — which account, status, or rule was applied?
- Has the same exchange worked before? If so, what changed between the last success and the first failure?
- Is this a single transaction or a pattern? A pattern points toward a rule or mapping; a single occurrence more often suggests transaction-specific data.

The third question separates a failed exchange from one that delivered; the fourth separates a translation problem from a treatment problem. Between them, they place a discrepancy in one row of the table in §5. The fifth is often the fastest route of all: an exchange that worked last period and fails this one points at something that changed in between, on one side or the other.

---

## 4. The Method

**Form the hypothesis before requesting the evidence.**

The useful question is never "send me a screenshot of your system." It is: *if the failure were X, what specifically would the receiving system show?* That prediction determines which screen, which record, and which field to ask for. I ask for the single piece of evidence that would distinguish the hypotheses — which makes one request sufficient rather than starting a long exchange of images.

This requires understanding the relevant source and target data structures well enough to predict the far-side appearance of a given failure before seeing it. That knowledge, not interface access, is what makes the diagnosis possible.

### An example

A customer's period-end journal was rejected by their financial system. A colleague was handling the customer and consulted me. Beneath the noise of the API response, the core of the message was that a general ledger account could not be found on the receiving side.

The same journal had posted the previous month. My view was that an exchange which works one period and fails the next points at something that changed in between — and since the message named an account, the place to look was changes to the chart of accounts in the financial system during the failing period. My colleague confirmed the ERP side was unchanged: the account and its mapping had not been touched. The customer then checked their own system and found that the account had been removed during a clean-up the previous month.

Nothing was escalated to engineering. The exchange had done exactly what it should: it sent a valid reference and reported, accurately, that the far side no longer recognized it.

---

## 5. The Distinction That Resolves It

I use four working categories to localize cross-system discrepancies. In practice they are commonly collapsed into one:

| Origin | What it means | What it looks like |
|---|---|---|
| **Source logic** | The ERP record was wrong before transmission. The exchange worked correctly and faithfully carried an error. | Both sides agree — and both are wrong. The ERP record itself does not reflect what happened operationally. |
| **Mapping / reference resolution** | Both records are internally correct, but a field, code, or status was translated into something that means something different on the far side — or a transmitted reference no longer resolves in the target system. | Both sides look plausible in isolation; the divergence is in meaning, not in value. Status and enumeration fields are the usual site for translation errors; for reference resolution, the target rejects or cannot find something the source still points to. |
| **Transmission** | The exchange itself failed or partially applied. | A record is missing, duplicated, or incomplete; or the two sides disagree on a value neither system would have produced on its own. |
| **Downstream treatment** | The exchange delivered correctly; the receiving system or its users then applied different accounting treatment, status handling, or business rules to what arrived. | Both records trace back to the same transmitted values, but the outcome differs — a different account, a different status, a different downstream consequence. The interface is not implicated. |

Naming which of the four it is determines who owns the fix. A source-logic problem is corrected in the ERP and the exchange will carry the correction. A mapping problem requires a definition decision and will recur on every similar transaction until it is made. A reference-resolution problem is fixed wherever the reference broke — and when the far side removed or changed what was referenced, that fix sits with the receiving organization. A transmission problem belongs to engineering and the interface. A downstream-treatment problem belongs to the receiving organization and cannot be fixed on the sending side at all.

The example in §4 was a reference-resolution failure caused by a change on the receiving side: the source record and mapping were unchanged, and the transmission worked, but the referenced account no longer existed in the target system. Only the customer could fix it.

Treating all four as "an integration issue" sends the problem to the wrong team and lets it return.

---

## 6. What Most Would Get Wrong

- **Requesting evidence before forming a hypothesis.** This produces a long back-and-forth of screenshots, exhausts the customer's patience, and still does not isolate the failure.
- **Trusting the error text as the diagnosis.** An error message may identify where the exchange stopped without identifying the root cause. A rejected payload and a bad source record can surface as the same message.
- **Assuming a discrepancy means transmission failed.** Often the exchange worked exactly as designed. It may have transmitted a value that was already wrong, or delivered a correct value that the receiving side then treated under different rules. Both are routinely escalated to engineering as interface defects, and engineering correctly finds nothing — after which the problem returns, because no one has looked at the layer it actually came from.
- **Stopping at the single transaction.** If the cause is a mapping or a rule, every comparable transaction is already affected, and the customer has not noticed yet.

---

## 7. Transferable Principle

> Without access to the interface, diagnosis stops being observation and becomes prediction: decide what the failure would look like on the far side, then ask for exactly that. The constraint forces a discipline that access makes it easy to skip — knowing the relevant structures on both sides well enough to say in advance what the evidence should show.

---

**Related:** [Integration Scoping Checklist](integration_scoping_checklist.md) — §1 Meaning and §4 Status Mapping (translation errors), §2 Identity and §6 Sequence (reference resolution: what must exist in the target before a record is accepted), §7 Failure Semantics (the transmission row).
