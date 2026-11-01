# Product Requirements Document: Car Rental Marketing Management System

## 1. Document Information and Version History

| Field | Value |
|---|---|
| Document | Car Rental Marketing Management System PRD |
| Version | 1.0 |
| Status | Draft for stakeholder review |
| Date | 2026-11-01 |
| Product owner | To be assigned |
| Business owner | Marketing Department |
| Source basis | Requirements in the originating issue |

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-11-01 | Product team | Initial structured PRD from the available business requirements |

**Source note:** The repository did not contain a separate marketing requirements document. This PRD organizes and clarifies the requirements supplied in the originating issue; stakeholders should compare it with the intended source document before approving it.

## 2. Executive Summary

The business is extending its existing car sales operation into car rentals and needs a centralized marketing management system. The product will help marketing staff understand fleet utilization and rental performance, manage customer segments and category-based pricing, monitor booking progress and competitor prices, approve and deliver promotions, and report on marketing and rental KPIs.

The initial release is limited to the confirmed capabilities in this PRD. It does not define rental prices, employee approval thresholds, external verification or messaging vendors, or automated marketing behavior. Those details require business decisions before implementation. The anticipated application context is React, TypeScript, and Next.js on the frontend; Node.js on the backend; and MongoDB for persistence. This is product guidance, not an architecture or implementation specification.

## 3. Product Vision and Objectives

**Vision:** Give the marketing department a reliable, role-appropriate view of rental demand, pricing, fleet performance, customers, and campaign outcomes.

**Objectives**

1. Make utilization, revenue, customer, and rental performance visible at daily, weekly, and monthly intervals.
2. Provide controlled category and term pricing, with seasonal adjustments and an auditable change history.
3. Give marketing staff usable customer segments and booking-funnel visibility without automating out-of-scope interventions.
4. Ensure promotions and rental approvals follow the required authority and consent rules.
5. Support approved promotions across email, SMS, and WhatsApp, with per-recipient and per-channel delivery outcomes.

## 4. Business Background

The company currently operates a car sales business and is expanding into rentals. Rental operations introduce new marketing needs: measuring how much of the fleet is earning rental income, defining rental rates by vehicle category and duration, responding to seasonal demand, attracting distinct customer groups, and understanding booking and campaign performance.

The platform is intended for the marketing department. It is not a rental operations, payment processing, vehicle telematics, or identity verification product unless a future scope decision explicitly adds those capabilities.

## 5. Problem Statement

Without a dedicated view of rental marketing and performance, teams may lack consistent answers to questions such as which vehicles are rented, how customers progress through booking, whether prices reflect configured seasonal periods, which promotions were approved and delivered, and how rental revenue and customer growth are trending. Pricing and approval decisions also need traceability and clear authority.

The system must centralize the required information while preserving unresolved business decisions rather than encoding invented policies.

## 6. Business Goals and Success Metrics

| KPI | Definition and calculation | Cadence / target |
|---|---|---|
| Weekly fleet utilization | Rented vehicles ÷ total fleet vehicles × 100 for the reporting period. The precise treatment of partial rentals, unavailable vehicles, and the denominator snapshot must be agreed. | Weekly; target at least 75%. Highlight results below 75%. |
| Month-over-month new-customer growth | (New customers this month − new customers last month) ÷ new customers last month × 100. Define “new customer” and handling of a zero prior-month baseline before release. | Monthly; trend visibility required. No numeric growth target supplied. |
| Month-over-month repeat-customer growth | Same growth formula applied to customers meeting the approved definition of “repeat.” The qualifying rental count and time window require confirmation. | Monthly; trend visibility required. No numeric growth target supplied. |
| On-time payment performance | Proposed measure: payments received on or before their due date ÷ payments due in the period × 100. Payment due-date rules, partial payments, and treatment of overdue unpaid invoices require confirmation. | Report as a KPI; target and final definition require approval. |
| Rental revenue trend | Total rental income by reporting period, compared with prior periods. Accounting treatment for taxes, refunds, discounts, deposits, and cancellations requires confirmation. | Daily, weekly, and monthly historical trend; no revenue target supplied. |
| Rental performance | Rental performance reporting must include the agreed rental measures and filters. The precise measure set, in addition to revenue and utilization, requires confirmation. | Daily, weekly, and monthly. |

No baseline or growth, payment, or revenue targets are supplied. Do not treat proposed formulas or targets as approved until stakeholders confirm them.

## 7. Stakeholder Identification

| Stakeholder | Interest / responsibility |
|---|---|
| Marketing Staff | Assign customer segments, manage permitted pricing and competitor observations, create promotions, and act within configured approval authority. |
| Marketing Supervisor | Review escalated rental approvals and perform other authorized marketing work. |
| Marketing Department Head | Approve promotions; review escalated rental approvals and authorized pricing or reports. |
| Marketing Director | Holds unlimited rental approval authority; other permissions require confirmation. |
| Marketing leadership / Product owner | Define targets, approve policy decisions, and accept reports and workflows. |
| Rental operations / Fleet management | Supply or validate fleet, availability, rental, and booking data. |
| Finance | Validate rental income and payment definitions and source data. |
| Engineering / Architecture | Assess fit with the anticipated technology context and external services. |
| QA | Verify requirements, role boundaries, calculation rules, and failure handling. |
| Customers | Provide booking information and explicit ID-verification consent; may receive approved promotions through selected channels. |

## 8. User Personas

- **Marketing Staff member:** Needs to classify customers, manage assigned marketing tasks, record competitor observations, create promotions, and see relevant rental performance. Must not exceed assigned permissions or approval authority.
- **Marketing Supervisor:** Needs a clear queue and context for rental decisions escalated from staff, plus access to authorized reports and marketing functions.
- **Marketing Department Head:** Needs to approve or reject promotions before activation and handle rental decisions escalated to this level.
- **Marketing Director:** Needs visibility and authority for escalated rental approvals without a configured monetary ceiling.
- **Marketing Analyst / Manager:** Needs consistent reporting on fleet utilization, income, customers, funnel progression, and rental performance. Access is subject to assigned permissions.
- **Rental customer:** Needs a transparent rental price breakdown before payment and must make an explicit choice about ID-verification consent before booking completion.

## 9. Functional Requirements

Priorities describe the requested initial scope: **Must Have** is required for the specified MVP, **Should Have** is important but may follow the minimum release, and **Could Have** is optional. No Should Have or Could Have capability is added to MVP unless confirmed below.

### FR-001 — Fleet totals and utilization

- **Module:** Fleet Utilization Tracking
- **Description:** Show total fleet size, rented-vehicle count, and rental utilization percentage for a selected reporting period.
- **Business justification:** Enables marketing to assess how effectively the fleet is being rented.
- **Priority:** Must Have
- **User story:** As a marketing user, I want to see fleet size, rented vehicles, and utilization so that I can assess rental performance.
- **Acceptance criteria:**
  - **Given** valid fleet and rental data for a reporting period, **when** an authorized user opens fleet utilization, **then** the system displays total fleet, rented vehicles, and the calculated utilization percentage for that period.
  - **Given** fleet data is unavailable or the denominator is zero, **when** utilization is requested, **then** the system identifies the result as unavailable rather than displaying a misleading percentage.
- **Dependencies:** Fleet and rental records; approved calculation and fleet-eligibility rules.

### FR-002 — Utilization periods and target alert

- **Module:** Fleet Utilization Tracking
- **Description:** Provide daily, weekly, and monthly utilization reporting and highlight weekly utilization below 75%.
- **Business justification:** Provides a consistent view of utilization and calls attention to performance below the business target.
- **Priority:** Must Have
- **User story:** As a marketing leader, I want utilization by day, week, and month, with a warning below the weekly target, so that I can identify underperformance.
- **Acceptance criteria:**
  - **Given** utilization data, **when** an authorized user selects daily, weekly, or monthly reporting, **then** the system displays results for the selected period.
  - **Given** a completed weekly utilization result is below 75%, **when** that result is displayed, **then** the system visibly highlights it as below target.
  - **Given** weekly utilization is exactly 75% or greater, **when** it is displayed, **then** it is not labeled below target.
- **Dependencies:** FR-001; approved period boundaries, timezone, and utilization calculation.

### FR-003 — Customer segmentation

- **Module:** Customer Segmentation
- **Description:** Allow authorized marketing employees to manually assign and view one of the defined segments: Local customers, Tourists, or Insurance replacement customers.
- **Business justification:** Supports tailored marketing based on the confirmed customer classifications.
- **Priority:** Must Have
- **User story:** As a marketing employee, I want to assign and view a customer segment so that marketing can address customers appropriately.
- **Acceptance criteria:**
  - **Given** an authorized employee is viewing an eligible customer, **when** they assign one of the three defined segments, **then** the selected segment is saved and visible to authorized users.
  - **Given** a user attempts to assign a value outside the defined segment list, **when** the change is submitted, **then** the system rejects it and preserves the existing segment.
  - **Given** no segment has been assigned, **when** the customer record is viewed, **then** the system indicates that the segment is unassigned rather than inferring one.
- **Dependencies:** Customer records and permissions; decision on whether exactly one segment is allowed per customer is open.

### FR-004 — Booking funnel visibility

- **Module:** Booking Funnel Analytics
- **Description:** Track Search → Vehicle Selection → Booking Confirmation → Payment and show the last completed stage for incomplete bookings.
- **Business justification:** Helps marketing understand where customers leave the booking journey.
- **Priority:** Must Have
- **User story:** As a marketing analyst, I want to see the last completed booking stage so that I can understand funnel progression.
- **Acceptance criteria:**
  - **Given** a booking has recorded journey events, **when** the funnel is viewed, **then** the stages appear in the specified order and the latest completed stage is shown for an incomplete booking.
  - **Given** a booking has no completed stage, **when** it is viewed, **then** the system indicates that no stage has been completed.
  - **Given** a booking is incomplete, **when** it appears in analytics, **then** the system does not automatically contact the customer to recover it.
- **Dependencies:** Booking journey event data and an agreed definition of stage completion. Automated abandoned-booking recovery is out of scope.

### FR-005 — Vehicle-category and term pricing

- **Module:** Vehicle Category-Based Pricing
- **Description:** Configure daily, weekly, and monthly rates for Small regular car, Medium regular car, and Medium luxury car. Do not supply default or actual prices.
- **Business justification:** Provides a consistent pricing structure by vehicle category and rental term.
- **Priority:** Must Have
- **User story:** As an authorized marketing user, I want to configure rates by category and rental term so that rental prices can be managed centrally.
- **Acceptance criteria:**
  - **Given** an authorized user is maintaining pricing, **when** they enter a rate for a supported category and term, **then** the system saves the rate with its category and term.
  - **Given** a category or term is outside the defined options, **when** a rate is submitted, **then** the system rejects it.
  - **Given** a pricing configuration is incomplete, **when** it is selected for a quote, **then** the system does not silently substitute an invented price.
- **Dependencies:** Authorized roles; currency, tax, effective dates, and missing-rate behavior require confirmation.

### FR-006 — Longer-term rate relationship

- **Module:** Vehicle Category-Based Pricing
- **Description:** Ensure longer rental terms have lower per-day rates than shorter terms for the same category.
- **Business justification:** Preserves the stated business rule that longer rentals receive lower per-day pricing.
- **Priority:** Must Have
- **User story:** As a pricing manager, I want longer-term rates to have lower per-day cost so that term pricing follows the approved business rule.
- **Acceptance criteria:**
  - **Given** daily, weekly, and monthly prices are configured for the same category, **when** the rates are validated, **then** the derived per-day rates follow the approved comparison method and decrease as term length increases.
  - **Given** a proposed rate violates the required longer-term relationship, **when** it is saved, **then** the system prevents activation and explains the validation failure.
- **Dependencies:** FR-005; the exact per-day divisor, inclusive rental-day calculation, and treatment of partial periods must be confirmed.

### FR-007 — Seasonal and peak pricing

- **Module:** Seasonal and Peak Pricing
- **Description:** Let authorized marketing staff configure peak-period date ranges and adjusted prices for selected vehicle categories. Use standard pricing outside configured peak periods. Validate overlapping ranges.
- **Business justification:** Allows approved category pricing to reflect configured peak seasons.
- **Priority:** Must Have
- **User story:** As an authorized marketing user, I want to configure date-based peak pricing so that applicable rentals use the intended seasonal rates.
- **Acceptance criteria:**
  - **Given** a valid peak date range and one or more categories, **when** an authorized user saves adjusted pricing, **then** the configuration is associated with those dates and categories.
  - **Given** rental dates fall outside configured peak periods, **when** a price is calculated, **then** standard pricing is used.
  - **Given** a proposed peak period overlaps an existing period, **when** it is saved, **then** the system identifies the overlap and does not resolve it silently; the allowed overlap policy must be approved before release.
- **Dependencies:** FR-005; date inclusivity, effective date behavior, precedence, overlap policy, and whether a peak adjustment is an amount or rate require clarification.

### FR-008 — Booking price simulation and breakdown

- **Module:** Booking Price Simulation
- **Description:** Display estimated pricing and a clear breakdown on the booking confirmation page before payment, including applicable seasonal pricing.
- **Business justification:** Gives customers pricing transparency before committing to payment.
- **Priority:** Must Have
- **User story:** As a customer, I want to see the estimated rental price and its breakdown before payment so that I can make an informed booking decision.
- **Acceptance criteria:**
  - **Given** a selected vehicle, rental term, dates, and valid pricing, **when** the booking confirmation page is shown, **then** it displays an estimate and itemized breakdown before payment.
  - **Given** rental dates fall within an applicable configured peak period, **when** an estimate is calculated, **then** the breakdown reflects that seasonal pricing.
  - **Given** required pricing inputs or rules are unresolved or missing, **when** an estimate is requested, **then** the system does not present an invented or misleading total.
- **Dependencies:** FR-005, FR-007; taxes, fees, deposits, discounts, rounding, date boundaries, and estimate validity must be defined.

### FR-009 — Competitor price observations

- **Module:** Competitor Price Monitoring
- **Description:** Allow authorized users to record competitor name, vehicle category, observed price, and observation date, and compare historical observations.
- **Business justification:** Gives marketing a historical reference for competitor pricing.
- **Priority:** Must Have
- **User story:** As a marketing user, I want to record and compare competitor prices over time so that I can understand market changes.
- **Acceptance criteria:**
  - **Given** an authorized user enters a competitor observation with all required fields, **when** they save it, **then** the competitor, category, price, and observation date are retained.
  - **Given** historical observations exist, **when** an authorized user requests a comparison, **then** the system presents observations over time for the selected competitor and category.
  - **Given** a required field is missing or invalid, **when** the observation is submitted, **then** the system explains the validation error and does not save an incomplete record.
- **Dependencies:** Currency, rental term, source/provenance, and category mapping require clarification. Automated collection is out of scope.

### FR-010 — ID-verification consent

- **Module:** Customer ID Verification Consent
- **Description:** Capture explicit customer consent for ID verification and prevent booking completion when consent is not provided. This requirement does not imply an external verification integration.
- **Business justification:** Ensures the required customer consent is captured before booking completion.
- **Priority:** Must Have
- **User story:** As a customer, I want to make an explicit consent choice so that my booking can proceed only under the stated consent rule.
- **Acceptance criteria:**
  - **Given** a customer is completing a booking, **when** they explicitly provide consent, **then** the system records the consent decision with the booking.
  - **Given** consent is declined or not provided, **when** booking completion is attempted, **then** the system prevents completion and explains that consent is required.
  - **Given** consent is recorded, **when** an authorized user reviews the booking, **then** the recorded decision is available for audit.
- **Dependencies:** Consent wording, record metadata, retention, withdrawal handling, and legal basis require review. No external ID-verification service is assumed.

### FR-011 — Rental approval limits and escalation

- **Module:** Role-Based Approval Management
- **Description:** Support Marketing Staff, Marketing Supervisor, Marketing Department Head, and Marketing Director. Configure limits X, Y, and Z for the first three roles; the Director has unlimited authority. Escalate rentals that exceed an employee's configured limit to an appropriate higher role.
- **Business justification:** Keeps rental approval decisions within defined authority and makes escalation possible.
- **Priority:** Must Have
- **User story:** As an approver, I want rental requests above my limit escalated to an authorized higher role so that approvals respect delegated authority.
- **Acceptance criteria:**
  - **Given** configured limits and a rental request amount, **when** an employee's approval authority is evaluated, **then** the system allows approval only when the request is within that employee's configured limit.
  - **Given** a request exceeds the current employee's limit, **when** escalation is required, **then** it is routed to an appropriate higher-authority role and cannot be approved by that employee.
  - **Given** a request reaches the Marketing Director, **when** the Director's authority is evaluated, **then** no monetary limit blocks approval.
  - **Given** a limit or escalation route is missing or ambiguous, **when** a request is evaluated, **then** the system does not silently grant approval.
- **Dependencies:** Values of X/Y/Z, meaning of “rental amount,” role hierarchy/routing, multiple approvers, rejection, reassignment, and escalation timing require approval.

### FR-012 — Pricing audit trail

- **Module:** Pricing Audit Trail
- **Description:** Record who changed a price, previous and new price, date and time, pricing category, and rental term; allow authorized review of historical changes.
- **Business justification:** Provides accountability and traceability for pricing decisions.
- **Priority:** Must Have
- **User story:** As an authorized reviewer, I want to inspect historical price changes so that I can understand when and by whom rates changed.
- **Acceptance criteria:**
  - **Given** an authorized user changes a price, **when** the change is saved, **then** the system records the user, old and new values, timestamp, category, and term.
  - **Given** pricing changes exist, **when** an authorized user views history, **then** changes are available in a comprehensible historical sequence.
  - **Given** a user lacks audit permission, **when** they request price history, **then** access is denied.
- **Dependencies:** FR-005 and role permissions; timestamp timezone and audit retention require confirmation.

### FR-013 — Promotion creation and approval

- **Module:** Promotion Approval
- **Description:** Allow marketing staff to create promotions and require Marketing Department Head approval before activation. Keep the workflow limited to the required approval.
- **Business justification:** Provides controlled promotion activation without unnecessary approval stages.
- **Priority:** Must Have
- **User story:** As marketing staff, I want to submit a promotion for Department Head approval so that it becomes active only after authorization.
- **Acceptance criteria:**
  - **Given** a Marketing Staff member has created a promotion, **when** it is submitted, **then** it is pending Marketing Department Head approval and is not active.
  - **Given** the Department Head approves a pending promotion, **when** activation conditions are met, **then** the promotion may become active.
  - **Given** a promotion is rejected or still pending, **when** delivery is attempted, **then** the system prevents activation and delivery.
  - **Given** the promotion workflow is used, **when** approvals are recorded, **then** no extra approval stage is introduced beyond the required Department Head approval.
- **Dependencies:** Promotion content, validity dates, approval record, rejection/edit/resubmission behavior, and whether higher roles may create promotions require clarification.

### FR-014 — Multi-channel promotion delivery

- **Module:** Multi-Channel Promotion Delivery
- **Description:** Support email, SMS, and WhatsApp; allow one or multiple channels; keep independent per-recipient/per-channel delivery status; show failures; attempt delivery only with valid contact details and after approval.
- **Business justification:** Enables approved promotions to reach customers through available channels and makes delivery outcomes visible.
- **Priority:** Must Have
- **User story:** As an authorized marketing user, I want to select promotion channels and see delivery outcomes by recipient and channel so that I can monitor campaign delivery.
- **Acceptance criteria:**
  - **Given** an approved promotion and valid customer contact details for a selected channel, **when** delivery is initiated, **then** the system attempts delivery through that channel and records the outcome independently for that recipient.
  - **Given** multiple channels are selected, **when** a delivery is initiated, **then** each recipient/channel pair has its own status.
  - **Given** a channel lacks valid contact details or a delivery fails, **when** delivery results are displayed, **then** the ineligible or failed delivery is clearly identified and is not shown as successful.
  - **Given** a promotion is unapproved or inactive, **when** delivery is requested, **then** the system does not attempt delivery.
- **Dependencies:** FR-013; customer channel consent, valid-contact definition, messaging providers, retry policy, delivery evidence, and jurisdictional marketing rules require confirmation.

### FR-015 — Marketing reports and KPI dashboard

- **Module:** Marketing Reports and Analytics
- **Description:** Provide KPI dashboards and daily, weekly, and monthly reports covering fleet utilization, total rental income, new customers, repeat customers, and rental performance, with historical reporting.
- **Business justification:** Enables leadership to monitor rental marketing and business performance over time.
- **Priority:** Must Have
- **User story:** As a marketing leader, I want dashboards and historical reports for rental and customer KPIs so that I can monitor results and trends.
- **Acceptance criteria:**
  - **Given** authorized access and available source data, **when** a user selects daily, weekly, or monthly reporting, **then** the report includes the required measures for the selected period.
  - **Given** historical source data exists, **when** a user views a KPI, **then** historical results are available using the same approved definitions.
  - **Given** a KPI cannot be calculated from available data, **when** it is reported, **then** it is marked unavailable and not represented as zero.
- **Dependencies:** Approved KPI definitions, source systems, period boundaries, timezone, and access permissions.

### FR-016 — Authentication and role authorization

- **Module:** Role-Based Access Control
- **Description:** Authenticate users and enforce role-appropriate access for marketing features and approval actions.
- **Business justification:** Protects customer, pricing, approval, and campaign data and prevents unauthorized actions.
- **Priority:** Must Have
- **User story:** As a system owner, I want authenticated users restricted by assigned permissions so that only authorized people can perform protected actions.
- **Acceptance criteria:**
  - **Given** an unauthenticated user requests a protected function, **when** access is evaluated, **then** access is denied.
  - **Given** an authenticated user lacks permission for an action, **when** they attempt it, **then** the system denies the action.
  - **Given** an authenticated user has the required permission, **when** they perform an authorized action, **then** the action is allowed and recorded where an audit record is required.
- **Dependencies:** Identity provider, permission matrix, account lifecycle, and role assignment governance require confirmation.

### FR-017 — Reporting periods and historical consistency

- **Module:** Reporting and Analytics
- **Description:** Use consistent approved period boundaries for daily, weekly, and monthly metrics and retain historical results/data needed for comparison.
- **Business justification:** Prevents inconsistent interpretation of performance across views.
- **Priority:** Must Have
- **User story:** As an analyst, I want consistent reporting periods so that I can compare rental metrics reliably.
- **Acceptance criteria:**
  - **Given** a reporting period and approved calendar/timezone rules, **when** metrics are calculated, **then** the same source events are assigned consistently to that period across dashboards and reports.
  - **Given** a user changes the reporting period, **when** the report refreshes, **then** all displayed metrics use the selected period.
  - **Given** source data is corrected, **when** historical metrics are recalculated or retained, **then** the system follows an approved correction and restatement policy.
- **Dependencies:** Calendar, timezone, data correction, and historical retention policy.

## 10. User Stories

The requirement-specific user stories in Section 9 are authoritative and testable through their acceptance criteria. The end-to-end themes are:

1. As a marketing employee, I can classify customers and review booking-funnel progress without triggering automated recovery.
2. As a pricing user, I can manage category and term rates, apply configured seasonal rates, and review an audit trail.
3. As a customer, I can see an estimate and breakdown before payment and must provide the specified ID-verification consent to complete a booking.
4. As a marketing approver, I can review rental requests within my limit and route requests above it to the appropriate higher authority.
5. As promotion staff, I can request Department Head approval and, after approval, deliver through selected supported channels with individual delivery outcomes.
6. As a marketing leader, I can compare utilization, income, customers, and rental performance across reporting periods.

## 11. Acceptance Criteria

The Given/When/Then criteria attached to each functional requirement in Section 9 are the acceptance baseline. Before development sign-off, product and business owners must resolve dependencies and open questions that prevent a deterministic test. QA should verify role boundaries, positive and negative paths, missing data, invalid configurations, date boundaries, and delivery failures, in addition to the stated happy paths.

## 12. User Journeys

### 12.1 Customer booking and consent

1. Customer searches for a rental and selects a vehicle/category.
2. System uses configured standard pricing and any applicable configured peak pricing to prepare an estimate.
3. Customer reviews the estimated amount and breakdown on the confirmation page before payment.
4. Customer makes an explicit ID-verification consent choice.
5. Booking completion is allowed only if required consent is provided; payment then follows the separately defined payment process.

### 12.2 Pricing maintenance

1. Authorized user selects one of the three supported vehicle categories and daily, weekly, or monthly term.
2. User enters or updates pricing.
3. System validates the longer-term per-day relationship and applicable peak-date rules.
4. On successful save, system records the required audit details.
5. Authorized users can review the pricing history.

### 12.3 Rental approval escalation

1. Rental request is presented for approval.
2. System evaluates the request against the current approver's configured limit.
3. Within-limit requests may be decided by that approver; over-limit requests are escalated to an appropriate higher role.
4. Marketing Director can approve without a monetary ceiling.
5. Exact thresholds, routing, and rejection handling must be configured from approved policy.

### 12.4 Promotion approval and delivery

1. Marketing staff create and submit a promotion.
2. It remains inactive until Marketing Department Head approval.
3. Once approved and eligible for activation, staff select email, SMS, WhatsApp, or multiple channels.
4. The system attempts each eligible recipient/channel delivery independently.
5. Staff review per-recipient/per-channel outcomes, including failures.

### 12.5 Marketing performance review

1. Authorized user chooses daily, weekly, or monthly reporting.
2. Dashboard presents fleet utilization, rental income, new and repeat customers, and rental performance for the selected period.
3. Weekly utilization below 75% is highlighted.
4. Historical periods can be compared using the same approved KPI definitions.

## 13. Business Rules

### Pricing calculation

- Pricing is configured for three categories and three terms only, unless a later scope change is approved.
- Longer terms must result in lower per-day rates for the same category. The per-day divisor and rental-duration convention remain to be defined.
- Use standard pricing outside configured peak periods. Use adjusted pricing for selected categories within applicable configured peak periods.
- Display estimates and a clear breakdown before payment.
- Do not invent prices, fees, taxes, deposits, discounts, or rounding rules.

### Rental duration

- Rental duration determines which term rate applies, but exact pickup/return inclusivity, partial-day handling, term selection, and mixed-term calculation are unspecified and require stakeholder clarification.

### Seasonal precedence and date validation

- Configured date ranges may adjust prices for selected categories.
- Date-range overlap must be validated explicitly. Whether overlaps are forbidden or resolved by a precedence rule is undecided; do not silently choose a winner.
- Confirm inclusive/exclusive boundaries, timezone, and treatment of a rental spanning both standard and peak dates.

### Approval escalation

- Marketing Staff, Marketing Supervisor, and Marketing Department Head have configurable limits X, Y, and Z respectively. Marketing Director authority is unlimited.
- A request above the current employee's approved limit must be escalated to the appropriate higher role.
- Threshold values, comparison currency/amount, hierarchy routing, and approver delegation remain undecided.

### Customer consent

- Explicit ID-verification consent is required to complete a booking.
- No consent means booking completion is blocked.
- Consent collection does not imply an external verification integration or a verification result.

### Promotions

- Marketing Department Head approval is required before promotion activation.
- No delivery can occur before approval and activation.
- Delivery is attempted only when valid customer contact details are available for the selected channel.
- Status is tracked independently for each recipient and channel.

### Customer segmentation

- Segmentation is manual and limited to Local customers, Tourists, and Insurance replacement customers.
- No automated inference or classification is included.
- Whether a customer can have one or multiple simultaneous segments requires confirmation.

### Reporting

- Required reporting cadence: daily, weekly, and monthly.
- Weekly utilization target is at least 75%; results below 75% must be highlighted.
- Metric formulas and source-of-truth data need approval where this PRD identifies ambiguity. Missing/invalid source data must not be presented as a fabricated zero.

## 14. Role-Based Access Control

The following is the minimum required authority model. It is not a complete permission matrix; the product owner must approve the final action-by-role mapping.

| Role | Required authority |
|---|---|
| Marketing Staff | Assign/view customer segments and create promotions; may approve rentals only within configured limit X if authorized. |
| Marketing Supervisor | May review escalated rentals within configured limit Y if authorized. |
| Marketing Department Head | Must approve promotions before activation; may review escalated rentals within configured limit Z if authorized. |
| Marketing Director | Unlimited rental approval authority. |

All roles require authenticated access. Access to pricing changes, audit history, customer details, reporting, promotion delivery, and administration must be explicitly assigned. The system must enforce authorization at the action boundary, not merely hide unavailable controls. Role assignment and changes should be auditable. No role is assumed to have access to all customer data by default.

## 15. Non-Functional Requirements

All numeric service targets below are **proposed targets requiring stakeholder approval**, not confirmed business requirements.

| Area | Requirement |
|---|---|
| Application performance | Proposed: 95% of routine interactive reads complete within 2 seconds and report queries within 5 seconds under an agreed representative load. Benchmark and workload must be agreed. |
| Scalability | Support growth in customers, rentals, fleet, audit events, and campaign delivery without loss of data or material degradation; expected volumes and growth rates are unknown and need sizing. |
| Availability | Proposed: 99.5% monthly availability, excluding agreed maintenance. Business-hours coverage, recovery objectives, and exclusions require approval. |
| Authentication and authorization | Require authentication for protected data and functions; enforce least-privilege, role-based authorization for sensitive actions and approvals. |
| Data protection | Protect customer contact, booking, consent, pricing, and approval data in transit and at rest using organization-approved safeguards. Data classification and key-management standards require confirmation. |
| Audit logging | Preserve pricing changes and approval/consent events as required by their functional requirements; logs must identify actor and time and be access-controlled. Retention and tamper-resistance requirements need confirmation. |
| API reliability | Proposed: define service-level targets for API availability and latency; use bounded timeouts and safe handling of transient failures. Exact targets and retry/idempotency behavior require approval. |
| Error handling | Present understandable, non-sensitive errors; preserve valid user input where safe; do not report failed pricing, approval, or message operations as successful. |
| Accessibility | Target WCAG 2.2 AA for customer and staff web experiences, subject to accessibility review and stakeholder approval. |
| Maintainability | Keep product rules and configuration understandable and testable; document approved calculation rules and integration contracts. The anticipated stack is context, not a mandated architecture. |
| Data retention | Retain customer, booking, consent, campaign, pricing, and audit data only for an approved business/legal period. Periods, deletion, archival, and legal holds are open decisions. |

## 16. Data Requirements

The following are conceptual data needs, not a database schema:

| Information group | Minimum information needed |
|---|---|
| Fleet and rentals | Vehicle identifier, supported category, fleet eligibility/status, rental dates, rental status, and reporting-period facts needed for utilization. |
| Customer | Customer identifier, contact details where provided, assigned segment, and customer/rental history needed for new/repeat reporting. |
| Booking funnel | Booking/session identifier, customer association where available, stage events, event times, and completion state for each of the four funnel stages. |
| Pricing | Category, rental term, configured rate, effective period, status, and applicable peak-period/category adjustment. Currency and other price components require decision. |
| Pricing audit | Actor, previous and new prices, timestamp, category, and term. |
| Consent | Booking/customer association, explicit decision, capture time, and approved consent-text/version reference. Additional metadata is subject to privacy review. |
| Competitor observations | Competitor name, vehicle category, observed price, observation date; term, currency, and source are open. |
| Approval | Request, amount and currency, requesting user, evaluating limit, assigned approver/role, decision, time, and escalation history as required by approved policy. |
| Promotion and delivery | Promotion content/validity, approval decision, selected channels, recipient/channel pair, contact eligibility, delivery status, provider reference where available, and failure details subject to privacy policy. |
| Reporting | Source facts and approved definitions needed to reproduce historical utilization, income, customer, payment, and rental performance metrics. |

Data ownership, source systems, identifiers, synchronization frequency, data quality controls, correction history, retention, and deletion rules must be established before implementation.

## 17. Reporting and Analytics Requirements

- Provide daily, weekly, and monthly views and historical reporting.
- Include fleet utilization percentage, total rental income, new customers, repeat customers, and rental performance.
- Provide KPI dashboards and highlight weekly utilization below 75%.
- Show booking funnel progression through Search, Vehicle Selection, Booking Confirmation, and Payment, including the last completed stage for incomplete bookings.
- Support comparisons over historical competitor price observations.
- Display metric definitions, reporting dates, and unavailable data clearly. Do not treat missing data as zero.
- Filters, exports, access levels, refresh frequency, calendar, and timezone require stakeholder decisions.

## 18. External Integration Requirements

No external integrations are confirmed by the requirements. The system may require future interfaces to:

- Customer, fleet, booking, rental, and payment data sources.
- Email, SMS, and WhatsApp delivery providers.
- Authentication/identity management.
- An identity-verification provider, only if separately approved.

Provider selection, credentials and consent handling, API contracts, delivery callbacks, reliability targets, costs, and legal/compliance review must be determined before any integration is committed. ID-verification consent must work as a consent capture rule even if no verification provider exists. Competitor prices are manually entered; automated monitoring is not in scope.

## 19. Dependencies and Constraints

- Business owners must supply approved X/Y/Z approval limits and define escalation routing.
- Fleet, booking, customer, payment, and rental source data must be available and sufficiently reliable.
- Finance must approve income and payment KPI definitions.
- Pricing policy owners must specify currency, amounts, taxes/fees, duration conventions, seasonal adjustment model, and overlap behavior.
- Legal/privacy stakeholders must approve consent wording, contact-channel eligibility, marketing permissions, data retention, and applicable jurisdictional controls.
- Message delivery depends on approved providers and valid customer contact details.
- Anticipated technology context: React, TypeScript, Next.js, Node.js, MongoDB, and Vite or Webpack where appropriate. Final architecture and build tooling remain engineering decisions.
- This PRD specifies product outcomes and constraints; it does not authorize implementation of application code, databases, APIs, or UI as part of this document deliverable.

## 20. Security and Data Privacy

- Authenticate users and enforce least-privilege permissions for all protected functions.
- Protect customer contact information, booking records, consent evidence, pricing, approval decisions, and campaign data against unauthorized access or disclosure.
- Collect only data necessary for defined rental and marketing purposes. Define access, retention, deletion, correction, and legal-hold policies with privacy/legal stakeholders.
- Record explicit ID-verification consent and prevent booking completion without it. Define consent text/version, evidence, withdrawal behavior, and retention before launch.
- Do not infer consent for email, SMS, or WhatsApp from ID-verification consent. Confirm channel-specific marketing permissions and opt-out/suppression requirements before sending.
- Restrict pricing audit and approval history to authorized reviewers. Audit logs should not expose secrets or unnecessary personal data.
- External identity or messaging providers must undergo security, privacy, and compliance review before integration.
- Apply approved transport and storage protection, secure credential management, incident handling, and environment access controls.

## 21. MVP Scope

The MVP comprises the confirmed requirements in Section 9:

- Fleet totals, utilization, daily/weekly/monthly reporting, and below-75% weekly highlight.
- Manual customer assignment/viewing for the three specified segments.
- Booking funnel visibility and last completed stage, without recovery automation.
- Pricing by the three categories and daily/weekly/monthly terms, with lower per-day longer terms.
- Configurable peak pricing, validation of overlapping dates, and standard pricing outside peak periods, subject to resolving overlap policy.
- Booking estimate and price breakdown before payment.
- Manual competitor price observations and historical comparison.
- Explicit ID-verification consent as a booking-completion prerequisite, without assuming a verification service.
- The four roles and configurable X/Y/Z rental approval limits, Director unlimited authority, and escalation.
- Pricing change history with required audit fields.
- Promotion creation, Department Head approval before activation, and no additional workflow stages.
- Email, SMS, WhatsApp selection and per-recipient/channel status, gated by approval, activation, and valid contact details.
- Daily/weekly/monthly KPI dashboards and historical rental/marketing reporting.

MVP release readiness depends on resolving the blocking policy and data questions identified in this PRD. Where a requirement's business rule is still ambiguous, it must not be silently implemented using an assumed value.

## 22. Future Enhancements

These are possibilities only, not MVP commitments:

- Automated customer segmentation.
- Dynamic or demand-based pricing.
- Automated competitor price collection.
- Abandoned booking recovery.
- Loyalty programs.
- Advanced campaign attribution.
- Additional customer cohorts, vehicle categories, and channels.
- Expanded reporting, predictive insights, and configurable KPI targets.

Each enhancement requires a separate prioritization, privacy, and business-rule review.

## 23. Risks and Mitigation

| Risk | Impact | Mitigation |
|---|---|---|
| Intended source marketing document is absent from the repository | Requirements may omit existing rules or nuances. | Reconcile this draft against the authoritative source before approval. |
| Pricing duration, fees, seasonal overlaps, and date boundaries remain undefined | Incorrect or inconsistent customer quotes. | Obtain written pricing decisions and validate examples before implementing calculation behavior. |
| X/Y/Z and escalation routing are undefined | Unauthorized or blocked rental approvals. | Approve a threshold matrix and escalation paths; test boundaries and missing configuration. |
| Fleet, booking, customer, and payment data are unavailable or inconsistent | Misleading utilization and business KPIs. | Name source-of-truth owners, profile data, and define data-quality handling before delivery. |
| Income, customer, and payment KPI definitions are unresolved | Reports may not match finance or business expectations. | Approve formulas and reconciliation examples with Finance and Marketing. |
| Message channels lack selected providers or approved consent rules | Promotions may not be deliverable or lawful. | Complete provider, consent, opt-out, and jurisdiction review before activating delivery. |
| Customer and consent data is retained or accessed inappropriately | Privacy or regulatory exposure. | Approve minimization, access, retention, and deletion policies and verify them before launch. |
| Proposed NFR targets are mistaken for commitments | Unplanned cost or unmet expectations. | Mark proposed targets as pending and obtain explicit service-level approval. |

## 24. Development Milestones

Milestones are ordered by dependency and do not imply dates or team commitments.

1. **Discovery and policy sign-off:** Reconcile the source requirements document; approve KPI formulas, pricing rules, role permissions, and privacy policy.
2. **Data and integration readiness:** Confirm source systems, identifiers, data ownership, and messaging/authentication integration decisions.
3. **Product and architecture design:** Validate user journeys, access model, reporting definitions, and approved service targets.
4. **MVP implementation:** Deliver approved functional requirements in dependency order: access/data foundations; fleet, customer and funnel visibility; pricing and audit; approval/consent; promotions and delivery; dashboards.
5. **Verification and stakeholder acceptance:** Test functional criteria, role/security boundaries, calculation edge cases, reporting reconciliation, accessibility, and failure paths.
6. **Controlled launch and review:** Monitor approved service and business KPIs, reconcile reported measures, and prioritize follow-up work.

Dates, owners, release gates, and rollout strategy remain to be determined.

## 25. Open Questions and Assumptions

### Open questions requiring decisions

1. Where is the referenced source marketing requirements document, and does it add or override any issue requirements?
2. What currency, tax/fee/deposit/discount components, rounding, and price-effective-date rules apply?
3. How are rental days counted? Are pickup and return dates inclusive? How are partial days, durations between terms, extensions, cancellations, and refunds priced?
4. What exact divisor and comparison method define per-day daily, weekly, and monthly rates?
5. Is a seasonal adjustment a fixed amount, percentage, or replacement rate? How are overlapping periods handled? What if a rental spans standard and peak dates?
6. What date-boundary convention and business timezone define a day/week/month and a peak range?
7. What are approval limits X, Y, and Z, their currency, equality behavior, meaning of rental amount, hierarchy, and escalation path? How are rejection, delegation, and unavailable approvers handled?
8. Can one customer have multiple segments? Who can change a segment, and should changes be audited?
9. How are “new customer,” “repeat customer,” “total rental income,” “rental performance,” “on-time payment,” and payment due date defined? How are zero baselines, partial payments, refunds, taxes, and deposits treated?
10. Which fleet vehicles count in utilization, and how are unavailable, maintenance, reserved, and partial-day vehicles treated?
11. What sources provide fleet, rental, customer, booking-stage, and payment data? What are refresh, correction, and reconciliation rules?
12. What consent wording, version evidence, legal basis, withdrawal behavior, and retention apply to ID verification?
13. What customer permission/opt-out rules apply separately to email, SMS, and WhatsApp? How is contact validity defined?
14. Which providers and retry policies will support channel delivery? What statuses and delivery evidence are available?
15. What promotion content, start/end conditions, rejection/edit/resubmission process, and activation behavior are required?
16. What permissions apply to each role beyond the explicitly stated approval authority and promotion approval?
17. What reporting filters, export needs, refresh expectations, timezone, and historical retention are required?
18. What are the approved performance, availability, recovery, and data-retention targets?
19. What jurisdiction(s), privacy requirements, and marketing communication regulations apply?

### Assumptions explicitly not treated as confirmed rules

- This PRD uses only the issue statement because no separate source document was found in the repository.
- Proposed formulas for customer growth and on-time payment are illustrative until approved.
- Non-functional numeric targets are proposals for stakeholder approval.
- The anticipated technology stack is context, not a fixed implementation decision.
- No external ID verification, messaging, customer, payment, fleet, or analytics integration is assumed to exist.
- The defined list of segments, categories, terms, channels, and roles is treated as the initial required set; extensibility is not a confirmed MVP requirement.

## 26. Glossary

| Term | Definition |
|---|---|
| Booking funnel | The journey stages Search → Vehicle Selection → Booking Confirmation → Payment. |
| Booking completion | The event that finalizes a booking; exact system boundary and payment relationship require confirmation. |
| Fleet utilization | Rented vehicles divided by the approved fleet denominator for a reporting period, expressed as a percentage. |
| Local customer | One of the three manually assigned customer segments. |
| Tourist | One of the three manually assigned customer segments. |
| Insurance replacement customer | One of the three manually assigned customer segments. |
| Peak period | A marketing-configured date range during which adjusted category pricing may apply. |
| Rental term | Daily, weekly, or monthly duration pricing option. |
| Rental income | Rental-related monetary measure; accounting inclusions and exclusions require Finance approval. |
| Approval limit | Configured monetary authority threshold for Marketing Staff (X), Supervisor (Y), or Department Head (Z). |
| Promotion activation | Making an approved promotion eligible for delivery or customer use; exact activation conditions require confirmation. |
| Delivery status | Outcome recorded for an individual recipient and individual channel, independently of other channel deliveries. |
| MVP | The minimum product release limited to confirmed requirements and policy decisions documented in this PRD. |
