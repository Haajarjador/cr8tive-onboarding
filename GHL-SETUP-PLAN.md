# GoHighLevel Setup Plan — CR8TIVE Gateway (Part 2)

This is the step-by-step for the GoHighLevel side. The pages (gateway, onboarding,
share-login) are already built. This covers what to create in GHL so leads get
tracked and no one stalls without a follow-up.

---

## ✅ BUILD STATUS (updated after Part 2B — your 6 audit decisions applied)

**Done automatically (via API, purely additive — proof in `ghl-snapshot-before.json` / `ghl-snapshot-after.json`):**
- Created these 7 new tags: `gw-gateway-opened`, `gw-path-credit-review`, `gw-path-direct-enrollment`, `gw-monitoring-shared`, `gw-contract-signed`, `gw-priority-urgent`, `gw-owner-conflict`. (68 existing tags untouched.)
- Switched the gateway's review booking to your **Credit Consultation** calendar (`No3aNZM7qY3wqPC1JCWG`) in the page code (decision #1).

**Decisions applied — overrides below where they differ from the original sections:**
- **#2 Owner tags:** REUSE your existing `owner-jador` / `owner-terrence` (do NOT make `gw-owner-` copies). `commas-purchase` reused as-is.
- **#3 Goal/timeline fields:** REUSE existing `What are you leveraging your credit for?` (as the goal) and `Preferred Timeline for Improvement` (as the timeline). Do NOT create `credit_goal` / `goal_timeline`.

**Still to do BY HAND in the GHL UI (the API can't build these):**
1. **3 new custom fields** (Section A) — the create-field API can't set dropdown options, so add these in the UI as dropdowns: `GW Gateway Path` (review, direct), `GW Recommended Package` (membership, starter, silver, gold), `GW Consult Outcome` (Ready, Price, Later Date, Thinking, No-Show).
2. **Pipeline** `GW | Cr8tive Leads` (Section C).
3. **Trigger links** (Section D).
4. **All workflows** (Section F–G) as DRAFTS — GHL has no API to build automations. Use the recipes, and in them use `owner-jador` (not `gw-owner-jador`).
5. **Review calendar questions** — add the goal + timeline questions to the Credit Consultation booking form if they aren't already there. It is Jador's own consultation calendar; if adding them would disturb anything shared, tell me and we decide.

**Scope:** Jador's **ENROLL** leads only. Do NOT touch the START keyword or any of
Terrence's workflows.

---

## Ground rules (do not skip)

1. **ADD ONLY.** Never edit, rename, pause, or delete anything that already exists.
   If something new needs an existing item changed, build a NEW copy beside it and
   tell Jador which old one to switch off herself.
2. **Prefix everything you create with `GW`** (workflows) / `gw-` (tags), and put
   new workflows in a folder named **"Gateway Build"**.
3. **Snapshot first.** Before any change, save a read-only list of existing
   workflows, pipelines, fields, tags, and calendars as `ghl-snapshot-before.json`.
   Snapshot again after as `ghl-snapshot-after.json` and confirm nothing old changed.
4. **Workflows = DRAFTS only.** Never publish. Jador tests each with a fake lead and
   publishes it herself. If the Workflows API is off (403
   `WORKFLOW_API_NOT_ENABLED_FOR_LOCATION`), don't force it — follow the recipes in
   Section F by hand.
5. **No em dashes, no promised results** (no guaranteed scores, approvals, or
   deletions) in any message. Respect STOP/opt-outs.

---

## A. Custom fields (contact)

| Field | Type | Values |
|---|---|---|
| `gateway_path` | Dropdown | review, direct |
| `credit_goal` | Dropdown | home, car, rent, business, cards, improve |
| `goal_timeline` | Dropdown | 0-3, 3-6, 6-12, none |
| `recommended_package` | Dropdown | membership, starter, silver, gold |
| `consult_outcome` | Dropdown | Ready, Price, Later Date, Thinking, No-Show |

## B. Tags (reuse `commas-purchase` as-is, do not recreate it)

`gw-owner-jador`, `gw-owner-conflict`, `gw-gateway-opened`,
`gw-path-credit-review`, `gw-path-direct-enrollment`, `gw-monitoring-shared`,
`gw-contract-signed`, `gw-priority-urgent`

## C. Pipeline — NEW, named **"GW | Cr8tive Leads"**

Stages, in order:
`Gateway Sent → Gateway Opened → Review Path → Review Call Pending →
Review Call Booked → Ready to Enroll → Agreement Signed → Enrolled`

Only a **verified payment** (`commas-purchase`) moves a contact to **Enrolled**. A
click never does.

## D. Trigger Links (Phase 1 tracking — no webhooks; the plan has no webhook action)

Create these GHL Trigger Links and give Jador the list:

| Trigger Link | Points to | On click |
|---|---|---|
| Gateway | the gateway URL | tag `gw-gateway-opened`, stage → Gateway Opened |
| Credit Review (Card A) | gateway `#review` | tag `gw-path-credit-review`, field `gateway_path=review`, stage → Review Path |
| Start Services (Card B) | gateway `#program` | tag `gw-path-direct-enrollment`, field `gateway_path=direct`, stage → Ready to Enroll |

`gw-monitoring-shared` is set by the **Credit Review form submission** (the short
form `25Sd8GOFIqxR898uDLJk`), and moves the stage to **Review Call Pending**.

## E. Review calendar

Add `credit_goal` and `goal_timeline` as **required questions** on the review
calendar's booking form so the gateway's prefill lands in them.
**If adding them means editing the existing shared calendar, DO NOT edit it** —
tell Jador, and she decides whether to add the questions herself or make a separate
review calendar. Offer review times within 1 to 2 days.

Also build a saved Smart List **"Urgent Reviews"**: `goal_timeline = 0-3`, sorted by
call time.

---

## F. Keyword + follow-up workflows (DRAFTS) — Step 2C checklist

Each row = one automation. Build each as a draft; Jador publishes after testing.
`{{...}}` = GHL merge field / your trigger link.

### F0. Keyword: ENROLL (owner assignment)

Trigger: inbound SMS keyword **ENROLL**. In order:
1. If contact has another owner tag (e.g. a Terrence tag): add `gw-owner-conflict`,
   notify both owners, STOP. Otherwise add `gw-owner-jador` and set Assigned User =
   Jador (or her Operations Manager).
2. Assign the **conversation** to that user (so it shows in their "Assigned to me" inbox).
3. Send the gateway Trigger Link.
4. Stage → **Gateway Sent**.

### F1. Gateway sent, not opened
- Trigger: stage = Gateway Sent, no `gw-gateway-opened` after **24 hrs**.
- Action: **SMS** — "Hi {{contact.first_name}}, it's CR8TIVE. Here's your link to get started: {{gateway_link}}. Reply STOP to opt out."
- At **72 hrs** still not opened: **call task** to owner, "Call {{contact.first_name}}, gateway not opened."
- Stops when: `gw-gateway-opened` is added.

### F2. Credit Review chosen, login not shared
- Trigger: `gw-path-credit-review`, no `gw-monitoring-shared` after **24 hrs**.
- SMS — "Hi {{contact.first_name}}, to set up your free credit review, sign up for monitoring and share your login securely here: {{share_login_link}}. Reply STOP to opt out."
- At **72 hrs**: call task to owner.
- Stops when: `gw-monitoring-shared` is added.

### F3. Login shared, call not booked
- Trigger: `gw-monitoring-shared`, no booking after **24 hrs**.
- SMS — "Hi {{contact.first_name}}, we have your monitoring access. Book your credit review and we'll go over your report together: {{booking_link}}."
- **Same-day call task** (not 72 hrs) if `goal_timeline = 0-3`; also add `gw-priority-urgent`. Otherwise call task at **72 hrs**.
- Stops when: a review call is booked (stage → Review Call Booked).

### F4. Review call booked
- Trigger: booking created on the review calendar.
- Stage → **Review Call Booked**. Create reviewer task: "Pull {{contact.first_name}}'s 3-bureau report before the call."
- Owner text alert on every booking: name, `credit_goal`, `goal_timeline`, call time. Add "URGENT" if `goal_timeline = 0-3`.

### F5. Review call outcome (owner/Operations Manager records `consult_outcome`)
- **Ready:** task "Confirm {{contact.first_name}}'s payment within 2 business days." Pricing is sent to them separately, NOT the gateway package page. Once paid, the normal `commas-purchase` onboarding flow (Section G) takes over.
- **Price:** after **1 day**, SMS the membership option — "Hi {{contact.first_name}}, here's our monthly option, $199.99 to start then $129 each month: {{membership_link}}."
- **Later Date:** store the date in `start_date`; reminder **3 days before** — "Hi {{contact.first_name}}, you asked us to check back around now about starting. Ready when you are."
- **Thinking:** short check-ins at **2, 5, and 10 days**, then monthly nurture. Keep it light, no promised results.
- **No-Show:** after **1 hr** SMS a rebook link — "Hi {{contact.first_name}}, sorry we missed you for your credit review. Rebook here: {{booking_link}}." Call task **next day**.

### F6. Ready to Enroll, no payment (hottest stall)
- Trigger: `gw-path-direct-enrollment` / stage Ready to Enroll, no `commas-purchase`.
- After **2 hrs**: SMS — "Hi {{contact.first_name}}, need any help finishing your enrollment? We're here: {{checkout_link}}."
- At **24 hrs**: call task to owner (**highest priority**).
- Stops when: `commas-purchase` is added.

### F7. Owner conflict
- Trigger: `gw-owner-conflict` added. Notify **both** owners. **No reassignment.** A
  named person resolves it daily (write that person's name in the inbox setup).

---

## G. Onboarding workflow (after payment)

Trigger: **`commas-purchase`** tag (existing Commas → Zapier → GHL automation).
1. Move opportunity to **Enrolled**.
2. Send the onboarding email + SMS with the correct link:
   - Review-path client (`gateway_path = review`): `https://onboarding.cr8tivebusiness.com/?from=review`
   - Everyone else: `https://onboarding.cr8tivebusiness.com/`
   - SMS — "Welcome to CR8TIVE, {{contact.first_name}}! Finish your quick setup here: {{onboarding_link}}."
3. Task to the **Dispute Supervisor**: "Confirm {{contact.first_name}}'s onboarding is complete," due in **2 business days**.
4. **Stop** all gateway and nurture reminders for this contact.

### Onboarding stall rules
| Condition | Wait | Action | Owner |
|---|---|---|---|
| Credentials form not submitted | 48 hrs | SMS form link ({{credentials_link}}) | — |
| Still not submitted | 96 hrs | Call task | Dispute Supervisor |
| Client portal never opened | 7 days | SMS — "Hi {{contact.first_name}}, your client portal is where all your dispute updates post. Open it here: {{portal_link}}." | — |

### Known issue to fix BEFORE launch
The onboarding email has failed after some Commas checkouts before. Check: the
`commas-purchase` **tag spelling** (exact match, the Zap must apply this exact tag),
the **Zap** (Commas → Zapier → GHL) is on and mapping correctly, and the workflow
trigger is the tag (not a different event). **Run 3 test purchases** and confirm each
gets the tag, the email, AND the text before going live.

---

## H. Inbox setup

- Jador (and her Operations Manager) use the **"Assigned to me"** inbox filter.
- Daily: any conversation with no assigned user or with `gw-owner-conflict` goes to
  one named person to resolve. **Name that person here: ____________.**

---

## Link reference (fill in the real Trigger Link URLs when created)

| Name | URL |
|---|---|
| `{{gateway_link}}` | gateway Trigger Link |
| `{{share_login_link}}` | https://onboarding.cr8tivebusiness.com/share-login.html (or its Trigger Link) |
| `{{booking_link}}` | https://api.leadconnectorhq.com/widget/booking/4x1fxP5qWWWVP0xQ5MPm |
| `{{checkout_link}}` | https://credit.cr8tivebusiness.com/enroll |
| `{{membership_link}}` | https://credit.cr8tivebusiness.com/enroll?package=membership |
| `{{credentials_link}}` | https://api.leadconnectorhq.com/widget/form/yRCQRUWgoA2BjXyyXpcd |
| `{{portal_link}}` | https://clientportal.scorexer.com |
| `{{onboarding_link}}` | https://onboarding.cr8tivebusiness.com/ (add `?from=review` for review clients) |

---

## What still needs YOUR GoHighLevel connection (can't be done this session)

Everything in Sections A–H requires live GHL access (the GoHighLevel connection was
offline this session). When it's back, this plan is the build order. Also still your
call before launch: attorney sign-off on payment timing (D5), SmartCredit trial
confirmation (D8, trial stays off until then), Spanish copy review, and the 3 test
purchases above.
