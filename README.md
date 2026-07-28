# Restore Your Glow — GoHighLevel Integration Spec

The landing page (`Restore Your Glow.dc.html`) POSTs a JSON lead payload to a
GoHighLevel **Inbound Webhook**. This file is the source of truth for the payload
shape and the GHL workflow mapping. If the page changes, this file is updated to
match — mirror these exact key names on the GHL side.

## Webhook URL

```
https://services.leadconnectorhq.com/hooks/x8a4MjwLyYluFTb3971o/webhook-trigger/b83b1280-6ebc-40e5-9f5c-7ed21884aa61
```

Set in the page's logic class as `GHL_WEBHOOK_URL`.

## Payload (flat, top-level keys — no nesting, no spaces in keys)

```json
{
  "firstName": "",
  "lastName": "",
  "fullName": "",
  "email": "",
  "phone": "",
  "primaryFrustration": "",
  "desiredOutcome": "",
  "costOfInaction": "",
  "pastObstacle": "",
  "readiness": "",
  "status": "complete",
  "tags": ["Restore Your Glow Lead", "RYG Questionnaire Completed"]
}
```

### `status`
The page fires the webhook **twice** by design:
- `"partial"`  — fired right after the contact step (abandoned-lead safety net).
  tags: `["Restore Your Glow Lead", "RYG Incomplete Application"]`
- `"complete"` — fired after all 5 questions are answered.
  tags: `["Restore Your Glow Lead", "RYG Questionnaire Completed"]`

## Merge-field references (for GHL actions/notifications)

| Payload key          | GHL merge field                                    |
|----------------------|----------------------------------------------------|
| firstName            | `{{inboundWebhookRequest.firstName}}`              |
| lastName             | `{{inboundWebhookRequest.lastName}}`               |
| fullName             | `{{inboundWebhookRequest.fullName}}`               |
| email                | `{{inboundWebhookRequest.email}}`                  |
| phone                | `{{inboundWebhookRequest.phone}}`                  |
| primaryFrustration   | `{{inboundWebhookRequest.primaryFrustration}}`     |
| desiredOutcome       | `{{inboundWebhookRequest.desiredOutcome}}`         |
| costOfInaction       | `{{inboundWebhookRequest.costOfInaction}}`         |
| pastObstacle         | `{{inboundWebhookRequest.pastObstacle}}`           |
| readiness            | `{{inboundWebhookRequest.readiness}}`              |
| status               | `{{inboundWebhookRequest.status}}`                 |

## Workflow to build/maintain in GHL

Workflow name: **Restore Your Glow – Landing Page Leads**

1. **Trigger:** Inbound Webhook (URL above). After any payload change, re-capture
   the mapping reference by submitting one test through the live form.
2. **Create/Update Contact**
   - First Name ← `firstName`, Last Name ← `lastName`, Email ← `email`, Phone ← `phone`
   - Custom fields (create if missing, then map):
     - `RYG Primary Frustration` ← `primaryFrustration`
     - `RYG Desired Outcome`     ← `desiredOutcome`
     - `RYG Cost of Inaction`    ← `costOfInaction`
     - `RYG Past Obstacle`       ← `pastObstacle`
     - `RYG Readiness`           ← `readiness`
3. **Add Tags:** always `Restore Your Glow Lead`;
   `RYG Questionnaire Completed` when `status = complete`;
   `RYG Incomplete Application` when `status = partial`.
4. **Create Opportunity:** pipeline **New Consultations**, stage **New Lead**,
   name = contact full name.
5. **Internal Notification (Email + SMS)** to the account owner, showing all five
   answers using the merge fields above. Optional filter: only notify when
   `status = complete`.
6. Verify every mapping resolves against the payload keys; fix blanks; publish.

## Questionnaire answer options (reference)

The five questions, in fixed order (pain → desired result → cost of inaction →
past obstacle → readiness). Answers are stored verbatim as the string the visitor
selected.

1. **primaryFrustration** — "Which statement best describes what has been most frustrating?"
2. **desiredOutcome** — "If this program worked well for you, what would matter most?"
3. **costOfInaction** — "What concerns you most about staying where you are for another year?"
4. **pastObstacle** — "What has made lasting progress hardest in the past?"
5. **readiness** — "If Restore Your Glow feels like the right fit, how soon would you be ready to begin?"

## Golden rule

The landing page is edited in the design tool (this project) — it is the single
source of truth for the page and the payload. Do **not** edit the page's HTML or
change payload key names on the Claude Code side; request page changes in the
design tool so this spec and the deployed file never drift.
