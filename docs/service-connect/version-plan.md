# Version Plan — Service Connect App

## Version 0.1 — Lead Assignment MVP

### Goal

Build the minimum useful system for admin-curated lead assignment.

### Checklist

- Auth with roles: CUSTOMER, PROVIDER, ADMIN.
- Customer registration/login.
- Provider registration/login.
- Admin login.
- Customer creates service request.
- Admin lists pending requests.
- Admin verifies/rejects request.
- Admin sets lead credit cost.
- Admin assigns providers to verified lead.
- Provider lists assigned leads.
- Provider unlocks lead by spending credit.
- Max 3 providers can unlock a lead.
- Basic provider credit wallet.
- Admin grants initial/free credits manually.
- Basic audit fields and logs.

## Version 0.2 — Credit & Lead Quality

### Goal

Improve credit operations and lead quality control.

### Checklist

- Lead quality fields: urgency, budget range, preferred time, location details.
- Admin quality score/notes.
- Credit transaction history.
- Idempotency key for unlock API.
- Credit refund/manual adjustment by admin.
- Provider low-credit warning.
- Lead rejection reason.
- Better filtering for admin lead queue.

## Version 0.3 — Review, Trust & Provider Scoring

### Goal

Add trust signals and provider quality scoring.

### Checklist

- Customer can review provider after contact/completion.
- Provider rating summary.
- Admin review moderation.
- Provider trust score calculation baseline.
- Complaint/refund reason tracking.
- Lead outcome tracking: won, lost, no response, invalid.

## Version 0.4 — Notification & Automation

### Goal

Notify users and reduce manual follow-up.

### Checklist

- In-app notification table/API.
- Notify provider when lead assigned.
- Notify admin when provider credit is low.
- Notify customer when provider accepts lead.
- Email/SMS adapter abstraction.
- Scheduled jobs for expired leads and stale assignments.
- Admin dashboard alerts.

## Version 0.5 — Manual Payment / Credit Package

### Goal

Allow providers to request credit purchase packages while keeping payment manual.

### Checklist

- Credit package management by admin.
- Provider submits manual payment request.
- Upload/reference payment proof if needed.
- Admin approves/rejects payment request.
- Approved request grants credits.
- Payment request audit trail.

## Version 0.6 — Semi-Automated Matching

### Goal

Help admin find suitable providers faster without fully automatic marketplace behavior.

### Checklist

- Provider service categories.
- Provider service areas.
- Provider availability/basic capacity.
- Suggested provider list for lead.
- Matching score explanation.
- Admin still confirms final assignment.

## Version 0.7 — Provider Pro / Membership Packages

### Goal

Introduce provider membership plans and commercial segmentation.

### Checklist

- Provider membership package table.
- Provider plan status.
- Plan benefits: priority assignment, credit bonus, visibility boost.
- Admin-managed plan activation.
- Plan expiration and renewal reminders.

## Version 0.8 — Customer Tracking & Advanced Review

### Goal

Improve customer-side visibility and trust.

### Checklist

- Customer tracks providers that accepted their lead.
- Customer can mark contacted/completed.
- Advanced review criteria.
- Customer feedback on lead quality.
- Provider response time tracking.
- Customer request timeline.

## Version 1.0 — Stable Marketplace

### Goal

Make the platform stable enough for broader usage.

### Checklist

- Operational dashboards.
- Admin reporting.
- Stable permissions and audit logs.
- Lead conversion metrics.
- Provider quality metrics.
- Data cleanup/archive policies.
- Production monitoring baseline.
- Security hardening.

## Version 1.1+ — Scale

### Goal

Scale product, architecture, and monetization.

### Checklist

- Multi-service categories with richer taxonomy.
- Multi-location support.
- Online payment integration.
- Mobile app.
- Real-time notification.
- Microservice extraction by domain boundary.
- Message broker for domain events.
- Search/matching optimization.
