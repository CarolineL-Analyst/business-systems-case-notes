# Business Systems Case Notes

A growing collection of short, anonymized cases on how ambiguous enterprise-system problems are diagnosed, framed, and translated into practical system, data, process, or control decisions.

The cases are drawn from real enterprise-system scenarios, primarily in aviation MRO and parts trading. They are intentionally concise: the goal is not to document every implementation detail, but to make the reasoning visible — what had to be established, how the problem was localized, what evidence mattered, where ownership sat, and what principle could be reused elsewhere.

## Cases

### [Diagnosing Cross-System Failures Without Access to the Interface](case_blackbox_integration_diagnosis.md)

**Focus:** Integration diagnosis, source/target reasoning, evidence selection, root-cause isolation

When interface logs and the receiving system are unavailable, diagnosis has to proceed by inference rather than direct observation.

This case sets out a hypothesis-driven approach to separating source logic, mapping / reference resolution, transmission, and downstream treatment — and includes a real example where a previously successful exchange failed because a referenced account had been removed in the receiving system.

### [Correcting a Sales Order Raised Against the Wrong Customer — After Invoicing and Shipment](https://github.com/CarolineL-Analyst/business-systems-case-notes/blob/main/case_wrong_customer_invoiced_order.md)

**Focus:** Posted transaction correction, financial and audit traceability, airworthiness documentation

A sales order had been raised against the wrong customer, and by the time it was found the invoice had posted, crossed into the downstream financial system, and the goods had shipped.

The correction could not be an in-place edit. Each step of the reversal-and-reissue repairs a different layer of state — financial, commercial, revenue and cost attribution, audit linkage — and the parts certificate issued against the original order has to be cancelled and reissued as well.

## Frameworks

### [Integration Scoping Checklist — The Semantic Layer](integration_scoping_checklist.md)

The questions that have to be answered before an integration can be reliably scoped, estimated, or built: meaning, identity and matching keys, grain, status mapping, ownership, sequence, failure handling, and reconciliation. The focus is not transport technology, but whether data still means the same thing after it crosses a system boundary.

## Topics Being Added

Future cases will cover areas such as:

- inventory valuation and cost attribution,
- finance and operations timing differences,
- document and data semantics,
- process ownership and control design.

For KPI and reporting work — where the business definition matters more than the calculation — see [erp-bi-scoping-framework](https://github.com/CarolineL-Analyst/erp-bi-scoping-framework).

## About the Cases

Cases are anonymized and simplified where necessary. Customer identities, proprietary product details, transaction values, and implementation-specific information are omitted or generalized unless they are necessary to explain the reasoning.
