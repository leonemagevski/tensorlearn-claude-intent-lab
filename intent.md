# Intent: results-ready notification

Sources: `inputs/01-approved-prd.md` (PRD N-17, approved 2026-09-18), `inputs/02-current-meeting.md` (discovery notes, 2026-09-20). `inputs/03-superseded-request.md` is superseded by PRD N-17: it is history, not approval, and nothing in this intent relies on it.

## Problem

- **P-1** The fictional clinic receives avoidable calls from patients checking whether their results are ready. These status-only calls load the support team and add no clinical value. (PRD: "receives avoidable calls from patients checking whether results are ready")

## Proposed outcome

- **O-1** The patient is notified when a result is ready for their review in the existing authenticated portal. (PRD: "Notify the patient when a result is ready for their review in the existing authenticated portal.")
- **O-2** "Ready" means a doctor has reviewed **and** released the result. A completed laboratory upload alone never makes a result ready or eligible for notification. (PRD constraint; discovery notes, product owner: "'Ready' must not mean that the laboratory upload merely finished.")
- **O-3** The notification only says that an update is available and directs the patient to sign in to the existing portal.

### How success will be observed

- **S-1** Results-status calls: status-only calls are counted separately from clinical questions and compared against a measured baseline. Success is a reduction against that baseline. (PRD; discovery notes, support lead)
- **S-2** Delivery failures: notification delivery failures are measured, and failures are handled without sending the patient duplicate messages. (PRD)
- **S-3** No numeric target is approved. The baseline and any numerical target will be agreed during discovery; until then success is observed as a direction of change, not against an invented number. (PRD: "none is approved yet")

## Affected users and systems

- **U-1** Patients: the only approved recipients of the notification.
- **U-2** Doctors who review and release results: their release is the trigger for eligibility.
- **U-3** Support team: receives the status calls and owns how they are counted.
- **U-4** Clinical, privacy, product and engineering owners: each owns a constraint or an open decision below.
- **Y-1** Existing authenticated patient portal and its existing result route, where the patient views the result.
- **Y-2** Laboratory result upload: an upstream event that does not by itself trigger anything.
- **Y-3** Notification sending, delivery-failure handling and notification/operational logs.

## Scope

In scope:

- **I-1** Notify the patient through the existing portal result route once a result is doctor-reviewed and released.
- **I-2** Duplicate prevention: at most one notification per released result version.
- **I-3** Delivery-failure handling without duplicate patient messages.

## Out of scope

Excluded from this intent (PRD scope; discovery notes):

- **X-1** Portal redesign.
- **X-2** A new or rebuilt patient dashboard (engineering lead: "a separate project").
- **X-3** Appointment scheduling.
- **X-4** Clinical interpretation of results.
- **X-5** Notifying third parties.
- **X-6** Everything only the superseded request asked for: notifying automatically on lab upload, putting the test result in an SMS, copying the referring doctor, rebuilding the patient dashboard and its fixed call-reduction target and deadline. None of these is approved.

## Constraints

- **C-1** Review before notification: a doctor must review and release the result before any notification is eligible. (PRD)
- **C-2** No clinical details: no diagnosis, test name or result, test value, clinical measurement or treatment detail in the message or in notification and operational logs. The message says only that an update is available. (PRD; privacy owner)
- **C-3** Access to the result stays authenticated in the existing portal. No attached results and no public result link. (PRD)
- **C-4** No duplicate notifications for the same released result version, including after delivery failures. (PRD)
- **C-5** Recipients: only the patient is approved. No other recipient may be added without the decision in OPEN-2. (PRD; clinical owner)

## Open questions and owners

- **OPEN-1 Channel** (SMS, email or portal push). Owners: product owner and privacy owner, after the consent and delivery review. Not decided. (PRD; privacy owner: "Consent and the actual channel are still under review.")
- **OPEN-2 Notification to the referring doctor.** Owners: clinical owner and privacy owner, who must decide recipient authorization. Not approved by the PRD, and approval must not be inferred from the superseded request. (PRD; clinical owner)

```json
{
  "notifyWhen": ["doctor-reviewed", "released"],
  "messageMayInclude": ["update-available"],
  "inScope": ["existing-portal-route", "duplicate-prevention", "delivery-failure-handling"],
  "openDecisions": [
    {"topic": "channel", "owners": ["product", "privacy"]},
    {"topic": "referring-doctor-notification", "owners": ["clinical", "privacy"]}
  ],
  "successMeasures": ["status-only-calls", "delivery-failures"],
  "numericTarget": null
}
```
