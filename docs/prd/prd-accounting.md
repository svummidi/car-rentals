# Accounting Module Product Requirements Document

## 1. Document Information and Version History

| Field | Value |
|---|---|
| Document | Accounting Module Product Requirements Document |
| Product | Car Rental Management Platform |
| Version | 1.0 |
| Status | Draft for stakeholder review |
| Date | 2026-11-01 |
| Source | Accounting requirements in the originating issue; no separate source requirements file was present in the repository |
| Owner | Product Management (to be assigned) |

| Version | Date | Change |
|---|---|---|
| 1.0 | 2026-11-01 | Initial PRD, with requirement IDs mapped to the 12 source modules |

This document records requirements for review; it does not represent stakeholder approval or establish unspecified policy.

## 2. Executive Summary

The Accounting Module will provide dedicated financial management for the company’s car-rental business, separate from its existing car-sales chart of accounts (CoA). It will support rental revenue recognition after payment reconciliation, cash-basis operating costs, vehicle depreciation and profitability, VAT configuration, reconciliation, controlled and immutable ledger adjustments, approvals, Oracle General Ledger (GL) CSV exchange, financial and operational reporting, and month-end close.

The source establishes several material boundaries: USD only; VAT only at one configurable rate; no accruals for in-progress rentals; costs other than depreciation recorded on a cash basis; Oracle integration through CSV only; and records retained for at least 10 years. It also specifies six measurable KPIs, including zero unresolved reconciliation mismatches and close by T+2. Decisions not supplied—such as the VAT rate, approval thresholds, and overhead allocation method—remain open rather than being assumed.

## 3. Product Overview and Vision

The Accounting Module is the financial system of record for rental-related transactions and reports. Its vision is to give authorized finance and operations users a traceable view of rental receipts, costs, tax, adjustments, reconciliation status, asset depreciation, and vehicle-level results, while preserving the distinction between rental accounting and car-sales accounting.

The anticipated technology context is React.js, TypeScript, and Next.js on the frontend; Node.js on the backend; and MongoDB. This PRD defines business and product requirements only. It does not prescribe implementation code, APIs, UI designs, or database schemas.

## 4. Business Background

The company is expanding from car sales into car rentals. Rental operations create distinct booking receipts, return-related charges, promotions, refunds, maintenance and fuel costs, vehicle depreciation, and reconciliation needs. The rental activity therefore needs a dedicated CoA branch and processes that can be audited independently while making appropriate financial information available to Oracle GL.

The only source requirements available for this PRD are those embedded in the originating issue. No separate supplied accounting requirements document was present in the repository. Source statements are represented by the module traceability labels in Section 8; ambiguities and missing policy decisions are recorded in Section 29.

## 5. Business Objectives

1. Capture accountable rental transactions in a dedicated, auditable ledger.
2. Recognize base and late-charge income only at their specified reconciliation and return milestones.
3. Make vehicle-level profitability visible using attributable revenue and associated costs.
4. Reconcile bank CSV activity daily (excluding holidays) and online gateway settlements in real time.
5. Apply approval hierarchy, segregation of duties, and immutable correction practices to sensitive financial activity.
6. Make rental financial information available to Oracle GL using CSV-only exchange.
7. Retain records for at least 10 years and close accounting periods within T+2 days.

## 6. Problem Statement

Without dedicated rental accounting processes, rental income and costs may be mixed with car-sales activity, recognized at the wrong event, or not reconciled to booking and settlement records. Finance also needs controlled correction workflows, depreciation, vehicle-level reporting, and timely period closure. The module must address these needs without inventing accounting policies where the source has left them undecided.

## 7. Stakeholders and User Personas

| Persona / stakeholder | Needs and responsibilities |
|---|---|
| Accounting staff | Maintain rental accounts and VAT configuration; reconcile receipts; record and review ledger activity; prepare reports and close periods. |
| Finance manager | Monitor financial accuracy, approvals, reconciliation exceptions, period-close progress, and Oracle exports. |
| Supervisor | Review or approve eligible adjustments, refunds, and chargebacks within configured authority and reporting-line rules. |
| Department head | Approve eligible escalated requests and oversee departmental financial control. |
| Director | Approve eligible escalated requests at the highest stated hierarchy level. |
| Marketing approver | Approve refunds above configured thresholds at the applicable supervisor, department-head, or director level. |
| Rental operations staff | Maintain booking, vehicle return, and operational information that supports financial events and reports. |
| Management | Review cash flow, profit and loss per vehicle, and rental business performance. |
| Solution architects, developers, and QA | Use testable business requirements while retaining the stated technology context and scope exclusions. |
| Oracle GL / integration stakeholders | Agree CSV mappings, transfer timing, validation, and error handling. |

An approver must be the poster’s direct reporting line. The organizational source for reporting relationships and the exact way it is maintained are open questions.

## 8. Functional Requirements

The IDs below are the authoritative requirement identifiers. Module labels preserve traceability to the 12 source functional modules. Priorities are proposed for stakeholder review based on whether the source describes a core capability; they do not add business policy.

### FR-ACC-001 — Dedicated Rental Chart of Accounts

- **Source traceability:** Module 1 — Dedicated Rental Chart of Accounts.
- **Description:** Maintain a rental CoA branch separate from the existing car-sales CoA. The branch must include base rental income, late charge income, promotion cost, maintenance cost, tax, fuel cost, vehicle installment, insurance, depreciation, manual adjustments, and chargebacks. It must allow future account extensions without restructuring existing accounts.
- **Business objective:** Keep rental activity distinct, consistently classified, and extensible.
- **Primary user/actor:** Accounting staff.
- **Preconditions:** Authorized accounting user; existing car-sales CoA is available as the separate reference branch.
- **Main workflow:** Accounting maintains the rental branch and its required accounts; rental transactions use the appropriate rental accounts; additional accounts can be added without reorganizing existing accounts.
- **Alternative workflows:** None specified.
- **Exception scenarios:** Required account missing or account extension conflicts with an existing account; handling must be reported for resolution and must not silently classify a transaction.
- **Business rules:** The rental branch remains separate from car-sales CoA. Customer-retention and cancellation-cost accounts are not explicitly listed and require confirmation.
- **Priority:** Must Have.
- **Dependencies:** Ledger, transaction classification, and accounting configuration.
- **Acceptance criteria:**
  - Given an authorized accounting user, when the rental CoA is reviewed, then it is separate from car-sales CoA and includes every account listed in this requirement.
  - Given an authorized user adds a future rental account, when it is saved, then existing accounts need not be restructured.
  - Given a transaction has no valid account mapping, when it is processed, then it is surfaced as an exception rather than silently assigned.

### FR-ACC-002 — Rental Revenue Recognition

- **Source traceability:** Module 2 — Rental Revenue Recognition.
- **Description:** Recognize base rental income only after booking payment reconciliation. Recognize late charge income separately only after vehicle return and payment reconciliation. Do not accrue rentals that continue across reporting periods.
- **Business objective:** Apply the stated recognition milestones consistently and avoid unsupported in-progress rental accruals.
- **Primary user/actor:** Accounting staff; reconciliation process.
- **Preconditions:** Booking, payment, reconciliation, and (for late charges) return information are available.
- **Main workflow:** Reconcile the booking payment; recognize base rental income after successful reconciliation. For a late charge, confirm vehicle return and reconcile its payment before recognizing the separately classified income.
- **Alternative workflows:** None specified.
- **Exception scenarios:** Failed or pending reconciliation; vehicle not returned; late-charge payment not reconciled. Income recognition waits for the required event.
- **Business rules:** No accrual for rentals continuing across reporting periods. Base rental income and late charge income remain distinct.
- **Priority:** Must Have.
- **Dependencies:** FR-ACC-001, FR-ACC-008, booking, payment, and vehicle-return records.
- **Acceptance criteria:**
  - Given an unreconciled booking payment, when accounting processing runs, then base rental income is not recognized.
  - Given a reconciled booking payment, when accounting processing runs, then base rental income is recognized in the base rental income account.
  - Given a late charge whose vehicle has not been returned or whose payment is unreconciled, when processing runs, then late charge income is not recognized.
  - Given a rental spans a reporting period, when the period is processed, then no accrual is created for the in-progress rental.

### FR-ACC-003 — Discounts, Early Returns, No-Shows, and Cancellations

- **Source traceability:** Module 3 — Discounts, Early Returns, No-Shows and Cancellations.
- **Description:** Post discounts and promotions to promotion cost; record applicable partial early-return refunds as customer-retention costs; record no-shows as rental-cancellation costs; and preserve the distinction between revenue and cost accounts.
- **Business objective:** Make booking concessions and cancellation-related costs visible without conflating them with rental revenue.
- **Primary user/actor:** Accounting staff; rental operations staff provide event details.
- **Preconditions:** A booking and applicable promotion, early-return, no-show, or cancellation event are recorded.
- **Main workflow:** Identify the event and applicable amount; classify a discount or promotion to promotion cost, an applicable partial early-return refund to customer-retention cost, and a no-show to rental-cancellation cost.
- **Alternative workflows:** None specified.
- **Exception scenarios:** Eligibility, amount, and required CoA accounts are not fully defined; request confirmation rather than infer a treatment.
- **Business rules:** Applicable early-return refunds are partial; no-show costs are recorded as cancellation costs; costs and revenue remain separate. No unused prepaid package refunds are in scope.
- **Priority:** Must Have.
- **Dependencies:** FR-ACC-001, booking, refund, and approval records; customer-retention and cancellation account decisions.
- **Acceptance criteria:**
  - Given a discount or promotion, when it is recorded, then it is posted to promotion cost and not merged with a revenue account.
  - Given an applicable partial early-return refund, when it is recorded, then it is classified as a customer-retention cost.
  - Given a no-show, when its financial cost is recorded, then it is classified as a rental-cancellation cost.
  - Given a request to refund an unused prepaid package, when it is evaluated for this module, then it is identified as outside the stated scope.

### FR-ACC-004 — Vehicle Depreciation

- **Source traceability:** Module 4 — Vehicle Depreciation.
- **Description:** Apply straight-line depreciation using a fixed 60-month period. Monthly depreciation is purchase price divided by 60; stop depreciation after 60 months. Use the same schedule for tax and management reporting. Support annual asset revaluation and impairment.
- **Business objective:** Provide consistent vehicle depreciation records for tax and management reporting.
- **Primary user/actor:** Accounting staff.
- **Preconditions:** Vehicle asset and purchase price are recorded; commencement and disposal rules remain subject to confirmation.
- **Main workflow:** Calculate monthly depreciation as purchase price / 60 for the fixed 60-month period; make the schedule available to tax and management reporting; stop after month 60; record supported annual revaluation and impairment activity.
- **Alternative workflows:** Revaluation or impairment is recorded as applicable; detailed calculation and posting treatment are not specified.
- **Exception scenarios:** Missing purchase price, unclear commencement date, disposal, partial month, or revaluation method requires business confirmation.
- **Business rules:** Straight-line; fixed 60 months; no depreciation beyond 60 months; same schedule for tax and management; annual revaluation and impairment supported.
- **Priority:** Must Have.
- **Dependencies:** Vehicle asset records, ledger, and financial reports.
- **Acceptance criteria:**
  - Given a vehicle purchase price, when a monthly depreciation amount is calculated, then it equals purchase price divided by 60.
  - Given the 60-month depreciation period is complete, when a later month is processed, then no further depreciation is calculated.
  - Given tax and management reports use the depreciation schedule, when each is produced, then both use the same schedule.
  - Given annual revaluation or impairment is recorded, when the asset record is updated, then that event is supported without assuming an unspecified valuation method.

### FR-ACC-005 — Per-Vehicle Profitability

- **Source traceability:** Module 5 — Per-Vehicle Profitability.
- **Description:** Calculate individual vehicle profitability using attributable revenue and associated costs, including maintenance, fuel, vehicle installment, insurance, depreciation, and other applicable cash-basis costs. Allocate overhead monthly rather than per rental. Provide vehicle-level profitability reports.
- **Business objective:** Enable management to assess financial performance per vehicle.
- **Primary user/actor:** Accounting staff and management.
- **Preconditions:** Vehicle, revenue, cost, depreciation, and monthly overhead data are available.
- **Main workflow:** Associate applicable revenue and costs with a vehicle; include the specified cost categories; allocate overhead monthly; produce vehicle-level profitability.
- **Alternative workflows:** If a cost cannot be attributed or the monthly overhead method is not configured, flag it for confirmation rather than silently allocate it.
- **Exception scenarios:** Missing cost attribution, incomplete period data, and unspecified overhead allocation method.
- **Business rules:** Costs other than depreciation are cash-basis; overhead is monthly, not per rental. The overhead allocation formula is not specified.
- **Priority:** Must Have.
- **Dependencies:** FR-ACC-001, FR-ACC-004, transaction and expense records, monthly reporting.
- **Acceptance criteria:**
  - Given attributable vehicle revenue and associated costs, when the profitability report is produced, then the vehicle’s revenue and costs are shown in the calculation.
  - Given applicable maintenance, fuel, installment, insurance, depreciation, and other cash-basis costs, when vehicle profitability is calculated, then those categories are included.
  - Given overhead costs, when profitability is calculated, then overhead is allocated monthly rather than per rental.
  - Given the overhead method has not been confirmed, when a report is prepared, then the allocation is identified as requiring confirmation rather than using an invented formula.

### FR-ACC-006 — Configurable VAT Rules

- **Source traceability:** Module 6 — Configurable VAT Rules.
- **Description:** Support VAT only and a single configurable VAT rate. Accounting must be able to modify VAT configuration without engineering involvement. Do not support location-based tax variations or exemptions. Handle cross-region pickup and drop-off through delivery/drop-off charges rather than differentiated tax rates.
- **Business objective:** Let Accounting manage the approved single VAT rate while keeping tax treatment within the stated scope.
- **Primary user/actor:** Accounting staff.
- **Preconditions:** An authorized accounting user and a business-approved VAT rate/effective date are available; the rate and date policy are open questions.
- **Main workflow:** Authorized Accounting user updates the single VAT configuration; applicable financial processing uses that configuration; cross-region pickup/drop-off is handled through delivery/drop-off charges rather than a different tax rate.
- **Alternative workflows:** None specified.
- **Exception scenarios:** Missing or invalid configuration or an attempted location-based variation/exemption is surfaced and not silently applied.
- **Business rules:** VAT only; one configurable rate; no location variations or exemptions. Exact rate, effective-date management, tax base, and rounding are open questions.
- **Priority:** Must Have.
- **Dependencies:** CoA tax account, accounting permissions, transaction processing and reports.
- **Acceptance criteria:**
  - Given an authorized accounting user, when the single VAT rate is changed, then the change can be made without engineering involvement.
  - Given transactions in different pickup/drop-off regions, when tax is processed, then no location-based rate variation or exemption is applied.
  - Given cross-region pickup/drop-off, when applicable charges are recorded, then delivery/drop-off charges—not differentiated tax rates—represent the cross-region handling.
  - Given a VAT configuration change, when the effective date or treatment is unspecified, then the system flags the decision for confirmation rather than inventing a rule.

### FR-ACC-007 — Immutable Manual Adjustments

- **Source traceability:** Module 7 — Immutable Manual Adjustments.
- **Description:** Record all manual adjustments under the manual-adjustment CoA. Require adjustment notes and capture creation date and responsible person. Maintain an append-only, immutable ledger. Correct or reverse transactions using new entries that reference original entries; never modify existing ledger entries.
- **Business objective:** Ensure manual accounting activity is explainable, attributable, and auditable.
- **Primary user/actor:** Authorized accounting poster; authorized approver where required.
- **Preconditions:** User is authorized; mandatory adjustment note is provided; applicable approval rules are satisfied.
- **Main workflow:** Submit adjustment with note; record creation date and responsible person; post it to the manual-adjustment account; for a correction or reversal, create a new entry referencing the original.
- **Alternative workflows:** Correct a prior entry through a new correcting entry; reverse through a new reversal entry.
- **Exception scenarios:** Missing note, unauthorized poster, invalid reference, or attempt to edit/delete an existing ledger entry is rejected or flagged.
- **Business rules:** Append-only immutable ledger. Existing entries are not modified. Approval thresholds and hierarchy are governed by FR-ACC-009.
- **Priority:** Must Have.
- **Dependencies:** FR-ACC-001, FR-ACC-009, identity/role permissions, audit records.
- **Acceptance criteria:**
  - Given a manual adjustment without a note, when it is submitted, then it is not posted.
  - Given a valid adjustment, when it is posted, then the note, creation date, responsible person, and manual-adjustment account are recorded.
  - Given an existing ledger entry needs correction, when a correction is posted, then a new entry references the original and the original remains unchanged.
  - Given a user attempts to edit or delete a posted ledger entry, when the request is made, then the operation is prevented.

### FR-ACC-008 — Payment Reconciliation

- **Source traceability:** Module 8 — Payment Reconciliation.
- **Description:** Support daily bank CSV reconciliation, excluding holidays, including upload and processing of bank CSV reports, matching booking transactions, detecting mismatches and delays, and automated email alerts. Reconcile online gateway settlements in real time and match settlement events to corresponding booking records.
- **Business objective:** Validate recorded receipts against bank and online settlement activity and surface exceptions promptly.
- **Primary user/actor:** Accounting staff; reconciliation process.
- **Preconditions:** Bank CSV or online settlement event is available; booking and payment records exist.
- **Main workflow:** For bank activity, upload and process the CSV, match booking transactions, identify mismatches/delays, and email alerts. For online gateway activity, match settlement events against booking records in real time.
- **Alternative workflows:** Unmatched or delayed transactions remain exceptions for investigation; duplicate handling is open.
- **Exception scenarios:** Invalid CSV, missing booking, amount mismatch, delayed settlement, duplicate event, or unavailable email delivery.
- **Business rules:** Bank reconciliation is daily, excluding holidays. Online gateway settlement reconciliation is real time. Discrepancy alert KPI is same day detected.
- **Priority:** Must Have.
- **Dependencies:** Booking, payment, settlement provider, email delivery, FR-ACC-002, FR-ACC-010.
- **Acceptance criteria:**
  - Given a valid bank CSV report, when it is uploaded and processed on a scheduled reconciliation day, then booking transactions are matched and mismatches or delays are detected.
  - Given a detected bank discrepancy, when it is identified, then an automated email alert is sent within the same day.
  - Given an online gateway settlement event, when it is received, then it is reconciled in real time against the corresponding booking record.
  - Given a payment amount mismatch or failed reconciliation, when processing completes, then the item remains identifiable as an exception and base income is not recognized until its required reconciliation succeeds.
  - Given a duplicate payment or reconciliation event, when it is received, then duplicate handling follows a business-confirmed rule; no duplicate-posting policy is assumed here.

### FR-ACC-009 — Hierarchical Financial Approval

- **Source traceability:** Module 9 — Hierarchical Financial Approval.
- **Description:** Provide threshold-based approvals for financial adjustments, refunds, and chargebacks. Use the hierarchy Supervisor → Department Head → Director. An approver must be the poster’s direct reporting line; maintain segregation of duties. Refunds above configured thresholds require approval from the appropriate Marketing supervisor, department head, or director. Chargebacks use the chargeback account and descriptive ledger notes; do not generate automated journal entries for chargeback fees.
- **Business objective:** Control sensitive financial activity through authority, traceability, and separation of posting and approval.
- **Primary user/actor:** Poster and designated supervisor, department head, or director; Marketing approver for relevant refunds.
- **Preconditions:** User/reporting-line relationships, thresholds, and approval permissions are configured. Threshold amounts are not supplied.
- **Main workflow:** Poster submits an adjustment, refund, or chargeback; system determines configured approval path and threshold; eligible direct-line approver reviews; approval/rejection is recorded; chargeback principal is posted to the chargeback account with descriptive notes.
- **Alternative workflows:** Escalate through the stated hierarchy when required by configured thresholds; exact escalation conditions require configuration.
- **Exception scenarios:** Missing direct-line approver, poster attempts own approval, missing chargeback note, or chargeback-fee journal automation request.
- **Business rules:** Segregation of duties; approver is poster’s direct reporting line. Refunds above configured thresholds require the appropriate Marketing approver. No automated journal entries for chargeback fees.
- **Priority:** Must Have.
- **Dependencies:** Identity, reporting hierarchy, configured thresholds, ledger, refund/chargeback records.
- **Acceptance criteria:**
  - Given an adjustment, refund, or chargeback subject to approval, when it is submitted, then the configured threshold-based approval workflow is invoked.
  - Given an approver who is not the poster’s direct reporting line, when they attempt approval, then the approval is not accepted.
  - Given a poster attempts to approve their own transaction, when approval is attempted, then segregation of duties prevents it.
  - Given a refund above a configured threshold, when approval is required, then the appropriate Marketing supervisor, department head, or director is selected according to the configured hierarchy.
  - Given a chargeback, when it is posted, then it uses the chargeback account and has descriptive ledger notes; no automated journal entry for chargeback fees is generated.

### FR-ACC-010 — Oracle General Ledger Synchronization

- **Source traceability:** Module 10 — Oracle General Ledger Synchronization.
- **Description:** Support CSV-based integration with Oracle GL only; do not introduce API integration. Make payment reconciliation entries available for real-time synchronization. Synchronize other transactions through end-of-day CSV exports. Document data mapping and error-handling requirements as business requirements.
- **Business objective:** Transfer rental financial information to Oracle GL with traceable mappings and failure handling.
- **Primary user/actor:** Accounting staff and Oracle/integration stakeholders.
- **Preconditions:** Oracle GL account mapping, CSV format, and transfer operations are agreed; details remain open.
- **Main workflow:** Make payment-reconciliation entries available for real-time synchronization; export other transactions in end-of-day CSV; validate output and report transfer or mapping errors for resolution.
- **Alternative workflows:** Failed files or records are retained for diagnosis and controlled retry; exact retry and correction rules require confirmation.
- **Exception scenarios:** Invalid mapping, malformed CSV, Oracle rejection, duplicate export, unavailable real-time transfer mechanism.
- **Business rules:** CSV-only integration; no API. Payment-reconciliation entries are described as available for real-time synchronization, while other transactions use end-of-day CSV exports. These statements leave the real-time mechanism ambiguous and must not be silently reconciled.
- **Priority:** Must Have.
- **Dependencies:** Ledger, payment reconciliation, agreed Oracle mapping and CSV exchange process.
- **Acceptance criteria:**
  - Given payment-reconciliation entries, when they become eligible for Oracle synchronization, then they are available for the specified real-time synchronization process without introducing an API integration.
  - Given other transactions, when end-of-day export runs, then they are provided through CSV.
  - Given an Oracle CSV mapping or transfer failure, when detected, then the affected records and error are reported for resolution and are not silently treated as successfully synchronized.
  - Given the real-time mechanism or detailed CSV format has not been agreed, when integration is specified, then the ambiguity remains an open decision.

### FR-ACC-011 — Financial and Statutory Reporting

- **Source traceability:** Module 11 — Financial and Statutory Reporting.
- **Description:** Support Rental Agreement/Contract Reports; Income and Tax Reports; Vehicle Maintenance and Safety Reports; Fleet and Vehicle Availability Reports; Accident/Damage Reports; and Driver’s License Verification Reports. Financial KPIs include cash flow (revenue per day) and profit and loss per vehicle.
- **Business objective:** Provide financial and supporting operational reports required for rental oversight and financial review.
- **Primary user/actor:** Accounting staff and management; operational users for supporting reports.
- **Preconditions:** Relevant booking, payment, tax, vehicle, maintenance, safety, incident, and license-verification data is maintained.
- **Main workflow:** Authorized user selects an available report; the module presents the requested records and financial KPIs from recorded data.
- **Alternative workflows:** Incomplete or unavailable source data is identified to the report consumer; no unstated reporting policy is assumed.
- **Exception scenarios:** Missing source information, inconsistent periods, or unauthorized report access.
- **Business rules:** Cash flow KPI is revenue per day; profitability KPI is profit and loss per vehicle. No IFRS/local GAAP dual reporting or fleet-utilization dashboard integration.
- **Priority:** Must Have.
- **Dependencies:** Booking, ledger, tax, vehicle, and operational records.
- **Acceptance criteria:**
  - Given authorized access and the required source data, when a user requests each listed report, then the report is available.
  - Given a cash-flow report, when it is produced, then cash flow is represented as revenue per day.
  - Given a vehicle profitability report, when it is produced, then profit and loss is available per vehicle.
  - Given a request for dual IFRS/local GAAP reporting or fleet-utilization dashboard integration, when scope is assessed, then it is identified as excluded.

### FR-ACC-012 — Financial Record Retention and Month-End Close

- **Source traceability:** Module 12 — Financial Record Retention and Month-End Close.
- **Description:** Retain financial records for at least 10 years; ensure transactions are reflected in accounting no later than T+2 days; complete accounting period closure within T+2 days; define operational requirements for monitoring and reporting close progress.
- **Business objective:** Preserve financial history and complete timely, observable accounting close.
- **Primary user/actor:** Accounting staff and finance manager.
- **Preconditions:** Period calendar, transaction timestamps, retention policy, and close ownership are established; T+2 day type and closure definition are open.
- **Main workflow:** Record transaction timing and status; monitor records against the T+2 accounting reflection requirement; track period-close tasks/progress; report progress and closure status; retain financial records for at least 10 years.
- **Alternative workflows:** Delayed or blocked items are surfaced with status and reason for close management.
- **Exception scenarios:** Missing close evidence, incomplete reconciliations, unreflected transactions, or retention deletion before the minimum period.
- **Business rules:** Transactions reflected in accounting no later than T+2 days; period closure completed within T+2 days; financial records retained at least 10 years. Calendar versus business days and closure criteria remain open.
- **Priority:** Must Have.
- **Dependencies:** Ledger, reconciliation, period calendar, operational ownership, records retention.
- **Acceptance criteria:**
  - Given an accountable transaction, when its accounting reflection is monitored, then it is flagged if not reflected by T+2 days.
  - Given an accounting period end, when close progress is monitored, then progress and outstanding blockers are reportable against the T+2 closure requirement.
  - Given records within the minimum retention period, when retention processing runs, then those financial records are not deleted.
  - Given a period-close request, when required close validation fails, then the period is not reported as closed and the validation failure is visible.

## 9. User Stories

| ID | User story | Requirement |
|---|---|---|
| US-ACC-001 | As Accounting staff, I want a separate, extensible rental CoA so that rental transactions remain distinct from car sales. | FR-ACC-001 |
| US-ACC-002 | As Accounting staff, I want rental income recognized at the required reconciliation and return milestones so that income follows the stated business rules. | FR-ACC-002 |
| US-ACC-003 | As Accounting staff, I want discounts, early-return refunds, no-shows, and cancellations classified separately from revenue so that concession and cancellation costs are visible. | FR-ACC-003 |
| US-ACC-004 | As Accounting staff, I want vehicles depreciated on the fixed straight-line schedule so that tax and management reporting use consistent depreciation. | FR-ACC-004 |
| US-ACC-005 | As a manager, I want vehicle-level profitability including attributable costs and monthly overhead so that I can review each vehicle’s financial result. | FR-ACC-005 |
| US-ACC-006 | As Accounting staff, I want to configure the single VAT rate without engineering support so that approved VAT configuration can be maintained. | FR-ACC-006 |
| US-ACC-007 | As Accounting staff, I want immutable, attributable adjustment and reversal entries so that corrections are auditable without changing history. | FR-ACC-007 |
| US-ACC-008 | As Accounting staff, I want bank and online settlements reconciled to bookings so that discrepancies and delays are detected. | FR-ACC-008 |
| US-ACC-009 | As a finance approver, I want threshold-based requests routed through the direct reporting hierarchy so that financial controls and segregation of duties are maintained. | FR-ACC-009 |
| US-ACC-010 | As Accounting staff, I want Oracle GL exchanges through mapped CSV processes so that rental financial activity can be transferred and errors handled. | FR-ACC-010 |
| US-ACC-011 | As management, I want rental financial and supporting operational reports so that I can monitor cash flow and per-vehicle results. | FR-ACC-011 |
| US-ACC-012 | As a finance manager, I want retention and close progress monitored against the stated timelines so that records are preserved and periods close on time. | FR-ACC-012 |

## 10. Acceptance Criteria

The Given/When/Then criteria in Section 8 are requirement-specific acceptance criteria. The following cross-cutting cases provide additional test coverage without adding unstated financial policy.

| Case | Given | When | Then |
|---|---|---|---|
| Successful financial transaction | A valid booking payment is received and reconciled | The accounting process handles it | Base rental income is recognized in its rental income account. |
| Failed payment reconciliation | A booking payment remains unreconciled | Income recognition is evaluated | Base rental income is not recognized, and the reconciliation exception remains visible. |
| Amount mismatch | A settlement amount differs from its booking transaction | Reconciliation runs | The mismatch is detected and reported; it is not silently treated as a match. |
| Duplicate transaction | A duplicate payment or reconciliation event is presented | The event is processed | The event is identified for handling under a confirmed duplicate policy; the PRD does not prescribe an unprovided posting rule. |
| Refund approval | A refund is above a configured threshold | The refund is submitted | It routes to the appropriate Marketing approver in the stated hierarchy and enforces direct-line approval and segregation of duties. |
| Unauthorized adjustment | A user without authority submits or approves an adjustment | The action is attempted | The unauthorized action is blocked; valid adjustments require a note and required approval. |
| Ledger correction/reversal | A posted entry needs correction or reversal | Accounting records the change | A new entry references the original; the original is unchanged. |
| Depreciation calculation | A vehicle has a recorded purchase price and is within the fixed depreciation term | Monthly depreciation is calculated | The monthly amount is purchase price divided by 60; calculation stops after 60 months. |
| VAT configuration change | An authorized Accounting user changes the single VAT rate | The configuration is saved | Accounting can maintain the rate without engineering involvement; effective-date behavior is not inferred. |
| Oracle CSV synchronization failure | A CSV export is rejected or a mapping fails | The transfer result is processed | The error and affected records are visible; the transfer is not marked successful without confirmation. |
| Period closure validation | A period has outstanding close validations | Closure is requested | The period is not reported closed; progress and blockers remain visible. |

## 11. Financial Business Rules

| Area | Rule |
|---|---|
| Rental income | Recognize base rental income only after booking payment reconciliation. |
| Late charges | Recognize separately from base income, only after vehicle return and payment reconciliation. |
| In-progress rentals | Do not accrue rentals continuing across reporting periods. |
| Promotions | Post discounts and promotions to promotion cost, distinct from revenue. |
| Early returns | Record applicable partial refunds as customer-retention costs; applicability and calculation require confirmation where unspecified. |
| No-shows/cancellations | Record no-shows as rental-cancellation costs; account setup requires confirmation. |
| Depreciation | Straight-line, fixed 60 months; monthly amount = purchase price / 60; stop after 60 months; same schedule for tax and management; annual revaluation and impairment supported. Commencement, disposal, partial month, and revaluation treatment are open. |
| Vehicle profitability | Include attributable revenue and associated maintenance, fuel, installment, insurance, depreciation, and other applicable cash-basis costs. Allocate overhead monthly, not per rental; formula is open. |
| VAT | VAT only, one configurable rate, no location-based variation or exemptions. Rate, effective dates, calculation base, and rounding are open. |
| Costs | Cash-basis recording for costs other than depreciation. Specific account treatment of vehicle installments and other cost components requires accounting confirmation where not specified. |
| Manual adjustments | Use manual-adjustment CoA; mandatory note; capture creation date and responsible person; ledger is append-only. Correct/reverse through new entries referencing the original. |
| Reconciliation | Bank CSV reconciliation daily excluding holidays; online gateway settlement reconciliation in real time. Mismatches and delays are identified; automated email alerts sent same day detected. |
| Refund authorization | Threshold-based. Refunds above configured thresholds require appropriate Marketing supervisor, department head, or director approval. Amounts and exact routing are open. |
| Chargebacks | Post to chargeback account with descriptive ledger notes. Do not generate automated journal entries for chargeback fees. |
| Record retention | Retain financial records at least 10 years. Supporting-document retention is open. |
| Accounting close | Reflect transactions in accounting no later than T+2 days and close period within T+2 days. Calendar/business-day basis and close definition are open. |

## 12. Chart of Accounts Requirements

The rental CoA is a separate branch from the car-sales CoA and must remain extensible without restructuring existing accounts.

| Required account category | Requirement |
|---|---|
| Base rental income | Separate base rental income recognition. |
| Late charge income | Separate from base rental income. |
| Promotion cost | Discounts and promotions post here. |
| Maintenance cost | Vehicle maintenance costs. |
| Tax | VAT-related tax account/category. |
| Fuel cost | Vehicle fuel costs. |
| Vehicle installment | Include as a vehicle profitability cost category; detailed GL treatment requires confirmation. |
| Insurance | Vehicle insurance costs. |
| Depreciation | Vehicle depreciation. |
| Manual adjustments | All manual adjustments post under this CoA category. |
| Chargebacks | Chargebacks post here with descriptive ledger notes. |
| Customer retention cost | Needed to represent applicable partial early-return refunds; exact account setup is an open question. |
| Rental cancellation cost | Needed to represent no-show costs; exact account setup is an open question. |

Account codes, parent-child structure, Oracle mappings, and any additional accounts are not specified and require Accounting confirmation. The table does not prescribe accounting codes or a complete ledger design.

## 13. Revenue Recognition Rules

1. Base rental income recognition is gated on successful booking payment reconciliation.
2. Late charge income is separate and gated on both vehicle return and payment reconciliation.
3. No accrual is created for a rental continuing across a reporting period.
4. Discounts and promotions remain costs in the promotion-cost account, not netted into a revenue account.
5. Applicable partial early-return refunds and no-show cancellation costs remain classified as costs, distinct from revenue.
6. Unspecified timing and calculation details are open questions, not assumed policy.

## 14. Depreciation and Asset Management

- Use straight-line depreciation over a fixed 60-month period.
- Monthly depreciation calculation: purchase price divided by 60.
- Stop depreciation after 60 months.
- Use the same depreciation schedule for tax and management reporting.
- Support annual asset revaluation and impairment.
- Depreciation commencement date, partial-month treatment, asset disposal, and revaluation/impairment measurement and posting are unresolved and require Accounting confirmation.
- No alternative useful life, salvage value, or tax-specific schedule is defined in the available requirements.

## 15. Payment Reconciliation Workflows

### Bank CSV

1. Accounting uploads a bank CSV report.
2. The system processes the report and identifies matching booking transactions.
3. Mismatches and delayed payments are detected and presented for investigation.
4. Automated discrepancy email alerts are sent within the same day the discrepancy is detected.
5. Reconciliation is performed daily, excluding holidays.

CSV format, upload validation, matching tolerances, holiday calendar source, retry behavior, and duplicate handling require confirmation.

### Online payment gateway

1. The system receives a gateway settlement event.
2. The event is matched against the corresponding booking record.
3. Reconciliation occurs in real time.
4. Failed, mismatched, delayed, or duplicate events are visible as exceptions; detailed resolution and idempotency policy require confirmation.

“Real time” is a source requirement but has no defined duration or service target. No numeric latency is asserted here.

## 16. Approval and Authorization Matrix

| Transaction | Approval rule | Hierarchy / notes |
|---|---|---|
| Financial adjustment | Threshold-based approval. | Supervisor → Department Head → Director; approver must be poster’s direct reporting line. |
| Refund | Threshold-based; refunds above configured thresholds require approval. | Appropriate Marketing supervisor, department head, or director; threshold amounts and selection rules are open. |
| Chargeback | Threshold-based approval. | Use chargeback account and descriptive ledger notes. Do not generate automated journal entries for chargeback fees. |

All approval flows must maintain segregation of duties. The poster must not be treated as an approver for their own transaction where that would violate segregation. This matrix does not invent monetary thresholds or resolve how multiple direct-reporting relationships are selected.

## 17. Tax and VAT Requirements

- Support VAT only.
- Support one configurable VAT rate.
- Allow Accounting to change VAT configuration without engineering involvement.
- Do not support location-based tax variations or exemptions.
- Use delivery/drop-off charges, rather than differentiated tax rates, for cross-region pickup/drop-off handling.
- The exact rate, effective-date management, tax base, rounding, configuration-change authorization, and historical transaction treatment are open questions.
- No tax rates, exemptions, or jurisdictional rules are established by this PRD.

## 18. Ledger and Audit Requirements

- Financial ledger entries must be traceable to their business transactions.
- Manual adjustments use the manual-adjustment CoA and require descriptive notes, creation date, and responsible person.
- Ledger is append-only and immutable; posted entries cannot be edited or deleted.
- Corrections and reversals are new entries referencing their original entries.
- Adjustment, refund, and chargeback approvals must be recorded sufficiently to evidence the approval path and decision.
- Chargebacks require descriptive ledger notes; chargeback fees must not generate automated journal entries.
- Financial records are retained for at least 10 years. Required retention for supporting documents is open.
- Audit-event detail, access to historical records, and any legal hold process are not specified.

## 19. Oracle General Ledger Integration

- Integration is CSV-only; do not introduce API integration.
- Payment reconciliation entries must be available for real-time synchronization.
- Other transactions synchronize through end-of-day CSV exports.
- Business requirements must define data mapping, validation, failure reporting, and error handling before integration acceptance.
- At minimum, stakeholders must agree how rental CoA accounts map to Oracle accounts, which business/ledger identifiers are required, CSV format and encoding, file/record validation, acknowledgment/rejection handling, retries, duplicate prevention, reconciliation of exported records, and ownership of error resolution.
- These are requirements to define, not assumed field mappings or integration design.
- **Ambiguity:** “Real-time synchronization” is required for payment-reconciliation entries, while the integration is expressly CSV-based and other transactions use end-of-day CSV. The source does not define whether real-time means immediate availability for an on-demand CSV, a continuously exchanged CSV, or another CSV-only transfer arrangement. Stakeholders must clarify the mechanism; an API is not an allowed interpretation.

## 20. Reporting and Analytics

### Required reports

- Rental Agreement/Contract Reports.
- Income and Tax Reports.
- Vehicle Maintenance and Safety Reports.
- Fleet and Vehicle Availability Reports.
- Accident/Damage Reports.
- Driver’s License Verification Reports.

### Financial KPIs

- Cash flow (revenue per day).
- Profit and loss per vehicle.

Reports must use available source records and the defined revenue and depreciation rules. Reporting periods, filters, formats, and statutory jurisdictional content are unspecified. Do not add dual IFRS/local GAAP reporting or fleet-utilization dashboard integration.

## 21. Non-Functional Requirements

Exact numeric service levels and performance targets were not supplied. They remain open for stakeholder definition rather than invented here.

| Quality area | Requirement |
|---|---|
| Financial data accuracy | Preserve amounts and classifications from source transactions; flag mismatches rather than silently treating them as reconciled. |
| Transaction consistency | Avoid representing failed reconciliation or failed Oracle export as successful; preserve traceability between related financial records. |
| Security and role-based permissions | Restrict accounting configuration, posting, approval, reporting, and export functions to authorized roles. |
| Auditability | Capture adjustment note, creation date, responsible person, and related correction/reversal reference; retain approval and reconciliation outcomes. |
| Ledger immutability | Posted entries are append-only and cannot be modified; corrections use new referenced entries. |
| System availability | Availability target and maintenance expectations are open questions. |
| Performance | Response-time, batch throughput, report-generation, and file-size targets are open questions. |
| Scalability | Capacity and growth targets are open questions; future CoA extension must not require existing accounts to be restructured. |
| Data retention | Retain financial records for at least 10 years; supporting-document retention is open. |
| Error handling | Surface validation, matching, approval, and Oracle CSV errors with enough context for investigation; no silent success. |
| Integration reliability | Preserve status and traceability for CSV exchanges and online settlement events; detailed retry/recovery targets are open. |
| Backup and recovery | Backup frequency, recovery-point objective, recovery-time objective, and restoration testing targets are open questions. |

## 22. Data Requirements

The following are business entities and their purposes, not database schemas. Key information is descriptive and does not prescribe a physical data model.

| Entity | Business purpose and key information | Relationships / ownership |
|---|---|---|
| Chart of Accounts | Rental account classification, separate branch, account category and status. | Accounting owns; referenced by ledger entries and Oracle mapping. |
| Financial Transaction | Business event and financial amount, date, classification, source, and processing state. | Linked to bookings, payments, expenses, ledger entries, and periods; Accounting ownership. |
| Ledger Entry | Posted financial record, account, amount, date, note, responsible person, and reference to original when correcting/reversing. | Belongs to a financial transaction/period and CoA account; Accounting ownership. |
| Rental Booking | Rental agreement/booking identity and booking-related financial context. | Linked to vehicle, payments, reconciliation, and revenue; operations owns source details. |
| Payment | Customer/gateway payment amount, date, source, and status. | Linked to booking, reconciliation, refund, or chargeback as applicable; Finance/Accounting ownership of financial status. |
| Payment Reconciliation | Matching outcome, source bank CSV/gateway event, booking/payment link, discrepancy/delay status, and alert status. | Linked to payment and booking; Accounting owns reconciliation outcome. |
| Refund | Refund amount, reason/event, booking, status, and approval information. | Linked to payment, booking, approval request, and ledger; Accounting owns financial record. |
| Chargeback | Chargeback amount, descriptive ledger notes, source, approval, and account posting. | Linked to payment/booking and ledger; Accounting owns financial record. |
| Manual Adjustment | Adjustment amount, mandatory note, creation date, responsible person, approval and correction/reversal linkage. | Linked to manual-adjustment CoA account and ledger; Accounting owns. |
| Vehicle Asset | Vehicle identity and purchase price required for depreciation and profitability. | Linked to booking, expenses, depreciation, and reports; asset/operations ownership to confirm. |
| Depreciation Record | Monthly depreciation amount and schedule position for the fixed 60-month period. | Linked to vehicle asset, ledger, and tax/management reports; Accounting owns. |
| Maintenance Expense | Maintenance cost and vehicle attribution. | Linked to vehicle and financial transaction; operations supplies details, Accounting owns financial record. |
| Fuel Expense | Fuel cost and vehicle attribution. | Linked to vehicle and financial transaction; operations supplies details, Accounting owns financial record. |
| Insurance Expense | Insurance cost and vehicle attribution. | Linked to vehicle and financial transaction; Accounting owns financial record. |
| Tax Configuration | Single VAT rate and approved configuration/effective-date information. | Owned by authorized Accounting; referenced by tax processing and reports. |
| Approval Request | Transaction type, threshold result, approver, reporting-line relationship, decision, and status. | Linked to adjustment, refund, or chargeback; management/Accounting ownership. |
| Financial Period | Period boundaries, close status, progress, and blockers. | Contains financial activity and close reporting; Accounting owns. |
| Financial Report | Report type, period, and generated results/status. | Derived from underlying records; report access controlled by role. |
| Oracle GL Export Record | Exported transaction/entry reference, mapping, CSV batch, status, and error information. | Linked to ledger/payment reconciliation and Oracle transfer; Accounting/integration ownership. |

Ownership boundaries and exact required fields beyond those implied by these business purposes require stakeholder confirmation.

## 23. Security and Financial Controls

- Apply role-based access to accounting configuration, posting, approval, reconciliation, reports, and Oracle exports.
- Require authorized Accounting access to modify the VAT configuration.
- Enforce direct-reporting-line approval and segregation of duties for threshold-based workflows.
- Require adjustment notes and responsible-person attribution.
- Prevent editing or deletion of posted ledger entries; use referenced correcting or reversing entries.
- Record reconciliation outcomes and Oracle export failures so that financial status is not silently overstated.
- Restrict access to retained financial and supporting records according to an authorization policy; specific role matrix and supporting-document rules are open.

## 24. Dependencies and Integrations

- Existing car-sales CoA, kept distinct from rental CoA.
- Rental booking, return, vehicle, payment, and expense source records.
- Online payment gateway settlement events.
- Bank CSV reports and a method to upload them.
- Automated email delivery for discrepancy alerts.
- Organizational reporting-line data for approvals.
- Oracle GL CSV exchange and agreed account/data mappings.
- Accounting-approved VAT configuration and period calendar.

The exact systems, file contracts, ownership, and availability targets for these dependencies have not been specified.

## 25. MVP Scope

The MVP includes the 12 confirmed modules and their stated business constraints: rental CoA; revenue milestones; discount/early-return/no-show/cancellation classification; 60-month depreciation and annual revaluation/impairment support; per-vehicle profitability with monthly overhead allocation; single-rate VAT configuration; immutable adjustments; bank and online reconciliation; hierarchical approvals; Oracle CSV exchange; listed reports and KPIs; and 10-year minimum retention with T+2 monitoring and close.

The MVP financial and product boundaries are:

- Single currency: USD.
- VAT only, with one configurable rate.
- No multi-currency or FX handling.
- No deposit liability tracking.
- No accrual accounting for in-progress rentals, expected damages, or insurance claims.
- Cash-basis recording for costs other than depreciation.
- No delayed or corporate payment terms.
- No 30/60/90-day aging reports.
- B2C simplified invoices only.
- No itemized tax/surcharge or environmental fee handling.
- No invoice version history.
- Oracle integration through CSV only.
- No split billing.
- No mid-rental vehicle swaps.
- No unused prepaid package refunds.
- No automated doubtful-account provisioning.
- No dual IFRS/local GAAP reporting.
- No rolling forecasts or scenario modeling.

Excluded capabilities must not be treated as MVP requirements. No additional tax, accounting, or approval policy is implied by listing these scope boundaries.

## 26. Future Enhancements

No future enhancement is committed by the source. Potential candidates for separate discovery and approval may include capabilities explicitly outside this MVP, such as multi-currency/FX, invoice history, or additional reporting. They are not approved requirements, must not be included in MVP, and require future business and accounting review before planning.

## 27. Business Risks and Mitigation

| Risk | Potential impact | Mitigation / decision needed |
|---|---|---|
| VAT rate and effective-date rules remain undefined | Incorrect tax calculation or historical reporting. | Accounting confirms rate, effective dating, base, rounding, and history policy before configuration is accepted. |
| Approval thresholds/reporting line not defined | Unauthorized or misrouted approval. | Define amounts and authoritative reporting-line source before enabling approval workflows. |
| Oracle real-time/CSV ambiguity | Payment entries may not synchronize as intended. | Agree a CSV-only real-time availability/exchange mechanism and validate error flow. |
| Cost account treatment incomplete | Inconsistent customer-retention/cancellation or installment classification. | Confirm required accounts and detailed accounting treatment with Accounting. |
| Reconciliation duplicates/matching rules unresolved | Duplicate or incorrect financial status. | Confirm matching keys, tolerances, duplicate policy, and controlled exception handling. |
| T+2 and close criteria undefined | Teams may measure timeliness differently. | Confirm calendar/business-day basis, time zone, transaction anchor, and period closure checklist. |
| Asset timing and disposal rules incomplete | Incorrect depreciation in first/last period or after disposal. | Confirm commencement, partial-month, disposal, impairment, and revaluation treatment. |
| Supporting-document retention unclear | Audit evidence may not be retained long enough. | Set supporting-document retention and access requirements. |

## 28. Success Metrics and KPIs

| KPI | Target | Measurement method |
|---|---:|---|
| Accountable transaction capture | 100% | Count accountable rental transactions represented in the ledger divided by total accountable rental transactions from agreed source records for the measurement period. Define source population and exclusions before reporting. |
| Unresolved payment reconciliation mismatches | Zero | Count open/unresolved mismatch exceptions at the agreed measurement time; report both current count and period trend. |
| Automatic depreciation for active vehicles | 100% each month | Active vehicles with the required month’s automatically calculated depreciation divided by all active vehicles eligible for that month. Eligibility timing requires confirmation. |
| Manual adjustment/correction entries | Below 1% of total ledger entries | Count manual adjustment and correction entries divided by total ledger entries for the same agreed period. Define treatment of reversals in the count. |
| Period close timeliness | Close by T+2 days after period end | Compare recorded period closure date/time with the period-end anchor plus two days; day type and time zone require confirmation. |
| Reconciliation discrepancy email timeliness | Same day detected | For each detected discrepancy, compare detection date and alert-delivery date; measure the proportion delivered on the same day, with target that all are. |

These are established source KPIs; denominator, measurement period, timestamp, and edge-case conventions must be agreed where not explicit.

## 29. Assumptions and Open Questions

No assumptions below resolve a source ambiguity. The business impact explains why the decision is needed.

| ID | Open question / ambiguity | Business impact |
|---|---|---|
| OQ-01 | What is the exact VAT rate, and how are effective dates, configuration history, tax base, rounding, and changes applied to existing transactions? | Determines correct tax calculation, audit history, and reports. |
| OQ-02 | What are the approval threshold amounts at each hierarchy level, and how do thresholds escalate? | Required to route and authorize adjustments, refunds, and chargebacks. |
| OQ-03 | How is the direct reporting-line hierarchy maintained and validated, including changes and missing managers? | Required to select eligible approvers and enforce segregation of duties. |
| OQ-04 | When does depreciation commence, and how are asset disposal and partial months treated? | Changes first/last depreciation amounts and active-vehicle KPI eligibility. |
| OQ-05 | What method allocates monthly overhead across vehicles? | Required for reproducible per-vehicle profitability. |
| OQ-06 | What is the detailed Oracle CSV format, mapping, acknowledgment/error contract, and permitted real-time synchronization mechanism for payment-reconciliation entries? | Required to implement and test the stated CSV-only integration without contradicting “real time.” |
| OQ-07 | How are duplicate payment and reconciliation events identified and handled? | Prevents ambiguous matching and duplicate financial outcomes. |
| OQ-08 | Does T+2 mean calendar days or business days, and what timestamp/time zone starts the clock? | Required for consistent transaction timeliness and close measurement. |
| OQ-09 | What constitutes period closure, which validations are mandatory, and who owns blockers and close reporting? | Needed to determine whether closure is complete and to monitor progress. |
| OQ-10 | Which CoA accounts are required for customer-retention and rental-cancellation costs, and what detailed accounting treatment applies? | Required to classify early-return refunds and no-show costs consistently. |
| OQ-11 | What retention period and controls apply to supporting documents? | Financial records must be kept at least 10 years, but supporting evidence retention is unspecified. |
| OQ-12 | What matching keys/tolerances, holiday calendar, delayed-payment definition, alert recipients, and retry process apply to bank and gateway reconciliation? | Required for deterministic reconciliation and actionable discrepancy handling. |
| OQ-13 | What are the calculation and posting details for annual asset revaluation and impairment? | The source requires support but does not define valuation inputs or ledger treatment. |
| OQ-14 | How are vehicle installments treated in the ledger, and which other cost types qualify as applicable cash-basis costs? | They are listed as vehicle profitability costs, while detailed accounting classification is unspecified. |
| OQ-15 | What availability, performance, scalability, backup, recovery, and report access targets are required? | Needed for measurable non-functional acceptance without inventing SLAs. |
| OQ-16 | What report periods, filters, formats, and statutory jurisdictional content are required? | Needed to validate report completeness and statutory suitability. |
| OQ-17 | Are there additional rental CoA accounts, codes, or Oracle mappings beyond those listed? | Needed to finalize account configuration and integration mapping. |

### Source ambiguity and consistency notes

- The issue refers to a supplied Accounting requirements document, but none was present in the repository. This PRD uses the embedded requirements as its source; comparison against any separate source document remains outstanding.
- “Real-time synchronization” of payment reconciliation entries is ambiguous alongside the CSV-only Oracle integration requirement. This PRD preserves both requirements and records the mechanism as an open question rather than introducing an API.
- Customer-retention and rental-cancellation costs are required, but their CoA accounts are not in the original required account list. Their account setup is identified as an open question.
- Chargebacks require threshold-based approval, but the stated approval details also separately specify refund approvals and the direct reporting line. Exact threshold routing and chargeback approval specifics need confirmation.
- The source calls reports “financial and statutory,” but does not specify jurisdictions or statutory report formats. This PRD lists only the six named report types.
- T+2 is specified for accounting reflection and period close without defining day type, anchor, time zone, or close checklist.

## 30. Glossary

| Term | Definition |
|---|---|
| Accounting period close | Completion of accounting period closure; exact required validations and meaning require confirmation. |
| Append-only ledger | Ledger where posted entries are not edited or deleted; corrections use new entries. |
| Bank CSV reconciliation | Matching uploaded and processed bank CSV activity with booking transactions. |
| Chargeback | A payment-related chargeback recorded to the chargeback account with descriptive ledger notes. |
| Chart of Accounts (CoA) | The set of accounts used to classify financial activity. |
| Direct reporting line | The poster’s direct reporting relationship used to determine eligible approvers; source of hierarchy is open. |
| General Ledger (GL) | The ledger receiving financial entries, including the Oracle GL integration destination. |
| Manual adjustment | A manually posted financial adjustment using the manual-adjustment CoA, mandatory note, and responsible-person/date attribution. |
| Month-end close | Operational process to close an accounting period within the specified T+2 requirement; exact definition is open. |
| Reconciliation | Comparison and matching of payment/settlement activity to booking records. |
| T+2 | Two days after a business event or period end; calendar/business-day meaning and time anchor are open. |
| VAT | Value-added tax; this product supports VAT only and a single configurable rate. |
