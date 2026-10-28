# Intent: Results-ready notification

Sources: `inputs/01-approved-prd.md` (PRD N-17, approved), `inputs/02-current-meeting.md`
(discovery notes), `inputs/03-superseded-request.md` (superseded, historical context only:
nothing below is approved by it alone).

## Problem

- **P-1.** The clinic receives avoidable calls from patients checking whether their results
  are ready. (PRD N-17: "receives avoidable calls from patients checking whether results are
  ready.")

## Proposed outcome

- **O-1.** When a result is ready for the patient's review, the patient is notified that an
  update is available and is directed to view it in the existing authenticated portal.
  (PRD N-17, Problem and outcome.)
- **I-1. Review-before-notification gate.** A notification is eligible only when all of the
  following are true: the lab upload is complete, a doctor has reviewed the result, and the
  doctor has released it. A finished lab upload on its own never counts as "ready". This
  applies to every released result, not only abnormal ones. (PRD N-17: "A doctor must review
  and release the result before any notification is eligible."; product owner: "'Ready' must
  not mean that the laboratory upload merely finished.")

### How success will be observed

- **M-1.** Success is observed as a reduction, compared against a measured baseline, in:
  - the number of status-only calls about results, counted separately from clinical
    questions (support lead), and
  - the number of notification delivery failures (PRD N-17: "Measure reduction in
    results-status calls and delivery failures.").
- All inbound calls are not the measure. No baseline value or numeric target is approved
  yet; both are to be agreed during discovery (PRD N-17). The target in the superseded
  request is not approved.

## Affected users and systems

- **Patients:** the only approved recipients of the notification.
- **Doctors:** they review and release results, which is the trigger for eligibility.
- **Support staff:** they handle the status calls and report the status-only call count.
- **Existing authenticated portal and its result route:** where the patient views the result.
- **Lab result upload and the doctor review/release workflow:** the source of the gate
  conditions.
- **Notification delivery and its logs:** these must carry no clinical detail.

## Scope

- **S-1.** In scope:
  - Use the existing portal result route (PRD N-17; engineering lead: "the existing portal
    path is adequate").
  - Prevent duplicate notifications for the same released result version.
  - Handle delivery failures without duplicate patient messages.

## Out of scope

- **X-1.** Excluded:
  - Portal redesign.
  - The patient dashboard (engineering lead: a separate project).
  - Appointment scheduling.
  - Clinical interpretation.
  - Notifying third parties (PRD N-17, Scope).
- Excluded because they come only from the superseded request: notifying as soon as the lab
  uploads, putting the test result in an SMS, copying the referring doctor, rebuilding the
  patient dashboard, and its call-reduction target.

## Constraints

- **C-1.** A doctor must review and release the result before any notification is eligible
  (see I-1).
- **C-2.** The message says only that an update is available and directs the patient to the
  existing authenticated portal. No diagnosis, test result, clinical measurement or treatment
  detail may appear in the message, the notification logs or operational logs. (PRD N-17;
  privacy owner.)
- **C-3.** Access to the result is authenticated in the existing portal. Results are not
  attached to the message, and no public result link is introduced. (PRD N-17.)
- **C-4.** The patient is the only approved recipient. No other recipient is approved; the
  referring doctor remains open (OPEN-2).
- **C-5.** No duplicate notifications for the same released result version, including when a
  delivery fails and is retried. (PRD N-17.)

## Open questions and owners

- **OPEN-1. Channel** (SMS, email or portal push). Owners: **product owner and privacy
  owner**. They decide after the consent and delivery review. No channel is approved yet; the
  superseded request's SMS does not count as approval. (PRD N-17; privacy owner.)
- **OPEN-2. Notifying the referring doctor.** Owners: **clinical owner and privacy owner**.
  They decide whether this recipient is authorized. It is not approved, and approval must not
  be inferred from the superseded request. (PRD N-17; clinical owner.)

## Decision record

```json
{
  "notifyWhen": ["lab-upload-complete", "doctor-reviewed", "released"],
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
