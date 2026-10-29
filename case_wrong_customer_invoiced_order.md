# Case: Correcting a Sales Order Raised Against the Wrong Customer — After Invoicing and Shipment

**Domain:** Aviation MRO / Parts Trading — ERP (Sales, Inventory, Finance, Airworthiness Documentation)

**Role:** Business systems advisor to the customer

**Nature of the problem:** Operational error with cascading financial, compliance, and traceability consequences

---

## 1. The Presenting Question

A customer reported that a sales order had been created against the **wrong customer account**. By the time the error was discovered:

- the **invoice had already been posted**,
- the **goods had already shipped**,
- and the invoice had been **pushed to the downstream financial system via interface**.

The question as asked was simple: *"How do we fix this?"*

---

## 2. Why the Simple Fix Was Wrong

The instinctive response — correct the customer on the order, or amend the record directly in the database — was not viable:

- **The invoice was posted.** Revenue and accounts receivable were already recognized against the wrong customer.
- **An interface pushes invoices to the financial system.** A direct database edit would leave the ERP and the finance system in divergent states, and the interface would propagate rather than contain the error.
- **Goods had shipped.** Inventory had been relieved and cost of sales recognized; the transaction was no longer reversible at the order level alone.

The problem was therefore not a data-entry error. It was a **posted transaction with downstream dependencies across finance, inventory, documentation, and the customer's own records.**

---

## 3. Questions I Needed Answered Before Acting

- Has the invoice been posted, and has it already been interfaced to the financial system?
- Have the goods physically shipped, and are they in the hands of the wrong party or the right one?
- Was a **parts certificate** issued against the incorrect sales order?
- What period are we in, and does the correction cross a closed accounting period?
- What records does the customer hold on their side that reference the incorrect order number?

Each answer changes the correction path. The scope of the fix is determined by how far the transaction has already travelled.

---

## 4. The Correction Path I Recommended

I recommended a controlled reversal-and-reissue rather than an in-place edit. Each step repairs a different layer of state:

1. **Credit the original sales invoice** — repairs **financial state**. Revenue and AR are reversed out of the incorrect customer through a proper accounting document, preserving the audit trail rather than erasing it.

2. **Create a new sales order against the correct customer** — repairs **commercial record**. The transaction that actually occurred now exists as a clean, self-consistent order.

3. **Invoice the new sales order for the correct amount** — repairs **revenue attribution**. Revenue and AR land against the party who actually owes them.

4. **Journal the cost of sales from the original order to the new order** — repairs **cost attribution**. Cost follows revenue to the correct customer, so margin and cost reporting remain accurate at the customer and order level rather than only in aggregate.

5. **Record cross-referencing notes on both sales orders** — repairs **audit linkage**. The relationship between the voided and replacement transactions becomes explainable to finance, to auditors, and to anyone reviewing the records months later.

**A condition on the above:** this path assumes the credit and the reissue fall within an open accounting period. If the original invoice sits in a closed period, the reversal cannot simply be booked back to its original date — the correction has to be recognized in the current period, and finance has to agree the treatment before anything is posted. This is why the period question belongs in the list above rather than being discovered halfway through the fix.

---

## 5. The Compliance Dimension

Beyond the financial correction, I asked a further question the customer had not raised:

**Was a parts certificate issued against the incorrect sales order?**

In an aviation context this matters more than it first appears. An airworthiness or parts certificate carries the originating order reference. If one was issued:

- the certificate in the customer's possession points to an order that has now been voided,
- the document must be **cancelled and reissued against the new sales order**,
- and the customer must be **notified directly** so they can treat the reissued certificate as authoritative.

Without this step, the end customer is left holding traceability documentation that references a cancelled transaction — a problem that surfaces later, during audit, resale, or component traceability review, when it is far more costly to resolve.

---

## 6. The Customer-Side Impact

The correction does not end inside the ERP. The customer's own records carry the original order number, and the replacement invoice carries a different one. I recommended notifying them directly so they can reconcile the credit against the new invoice and repoint their records, rather than discovering the mismatch at payment or at audit.

---

## 7. What This Case Illustrates

| Layer | What had to be considered |
|---|---|
| **System** | Posted transactions and active interfaces constrain what can be corrected and how |
| **Financial** | Revenue, AR, and cost of sales must all be moved — not just the customer name |
| **Audit** | The correction must remain explainable months later; notes are part of the fix, not an afterthought |
| **Regulatory** | Airworthiness documentation carries order references and must be reissued, not ignored |
| **Customer** | The downstream party holds records that the correction invalidates |

The question asked was operational. The answer required reasoning across five layers, and identifying the parts of the problem the customer had not asked about.

---

## 8. Transferable Principle

> When a transaction has already posted, the task is not to erase the error but to **create a correct, traceable path from what happened to what should have happened** — and to find every downstream record that still carries the original reference.

---

**Related:** [Integration Scoping Checklist](../frameworks/integration_scoping_checklist.md) — §7 Failure Semantics (why a posted transaction that has crossed an interface cannot be corrected at the source).
