# GoHighLevel Audit — CR8TIVE (read-only, nothing changed)

Read-only snapshot taken of your live GHL (location `iBVrcJt8709FD5An6FE8`):
**5 pipelines, 53 workflows, 75 custom fields, 68 tags, 7 calendars.** I changed
nothing. Below is what you have, the gaps, the overlaps, numbered suggestions to
answer yes/no, and the likely onboarding-email cause.

---

## 1. What you have now (the parts that matter for the gateway)

**Keyword / onboarding-link workflows**
- `Start Text - Send onboarding Link` — **published** (updated Oct 30). Terrence's START keyword.
- `Enroll Text - Send onboarding Link` — **draft** (created Oct 30). Your ENROLL keyword, not live yet.
- `Onboarding Form Submission` — published. Fires when the onboarding form is submitted.
- `New Client Onboard Process` — published (v50, the big one).
- `new client purchase email onboarding` — published (Jul 2026). **This is almost certainly the "Commas purchase → onboarding email" workflow that has failed before.**
- `GHL Workflow Spec: Split-Pay Second Installment Reminder` — published.
- `Round 1`–`Round 5 Disputes`, `Monitoring Issue (LOGIN ERROR)`, `Monitoring Issue (IDIQ - ERROR)`, `Monitoring Issue Upgrade Plan`, `No Show Calendar > Nurture Second Appointment`, `No Results`, `Lead Re-Engagement Email` — all published and running.

**Pipelines**
- `Business Funding`, `Client Consulting Process` (your main credit-repair client pipeline with Round 1–5 + monitoring-issue stages), `Client Onboarding` (Text "Start" → Onboarding Link Sent → Form Submitted → Documents Under Review → Onboarding Complete → Active Client), plus two older "Limited Sale" pipelines.
- There is **no** "GW | Cr8tive Leads" pipeline yet, so the new one I proposed would not collide.

**Calendars (important — see the flag below)**
- `Existing Clients Update Report / More Service (Add On)` — id `4x1fxP5qWWWVP0xQ5MPm`. **This is the booking link currently in the gateway build.** Its real purpose is *existing clients needing another round*, not new-lead reviews.
- `Credit Consultation` — id `No3aNZM7qY3wqPC1JCWG`. A 15-min call that already covers "quick audit of your credit profile, goals and timeline, package fit." **This is the natural fit for the gateway's "Book My Credit Review."**
- Plus: Business Credit Consultation, No Show, Credit Restoration Masterclass, Haajar L's Personal Calendar.

**Fields and tags you already have that overlap the gateway plan** (see Overlaps).

---

## 2. Gaps (where a lead can stall with nobody assigned)

- **ENROLL keyword is a draft.** `Enroll Text - Send onboarding Link` is not published, so right now an ENROLL text may do nothing (or fall through to a different workflow). The gateway's whole "one owner per lead" start depends on this.
- **No owner-conflict handling.** You have `owner-jador` and `owner-terrence` tags, but nothing flags a contact who texts *both* keywords, so a lead can land in the wrong inbox silently.
- **No "login shared but call not booked" follow-up.** Nothing chases a review lead between sharing their monitoring login and booking the call.
- **No "ready to enroll, no payment" chaser** tied to the new gateway path (your hottest stall).
- **No same-day escalation for urgent (0–3 month) leads.**

## 3. Overlaps / conflicts (so we don't double up)

1. **Owner tags already exist.** `owner-jador`, `owner-terrence`, and `commas-purchase` are live. The gateway plan called for `gw-owner-jador` etc. We should **reuse your existing `owner-jador` / `owner-terrence`** instead of making `gw-` copies, to avoid two parallel owner systems. (Keep `gw-` only for the brand-new tags like `gw-gateway-opened`.)
2. **Goal / timeline fields already exist.** `What are you leveraging your credit for?` (Home, Vehicle, Personal Funding, Business Funding, Credit Cards) ≈ the proposed `credit_goal`; `When would you like begin services?` and `Preferred Timeline for Improvement` ≈ `goal_timeline`. We can **reuse these** rather than create new ones.
3. **Monitoring login fields already exist.** `Credit Monitoring` (Smart Credit / Identity IQ), `Identity IQ Username`, `Identity IQ Password`, `Credit Monitoring: Password (Case Sensitive)`, `Last 4 of SSN`. Your new short review form should write to **these same fields** so the data lands where your team already looks.
4. **Two keyword workflows could double-fire.** `Start Text` (published) and `Enroll Text` (draft) both "send onboarding link." If the new gateway ENROLL workflow is added without turning one off, a lead could get two links. We build a new copy beside it and you switch the old one off yourself.
5. **Several onboarding workflows run in parallel** (`new client purchase email onboarding`, `New Client Onboard Process`, `Onboarding Form Submission`). Worth confirming they don't step on each other after a purchase.

## 4. Efficiency suggestions (answer each yes / no / change)

1. **Point the gateway's "Book My Credit Review" at the `Credit Consultation` calendar** (`No3aNZM7qY3wqPC1JCWG`), not the "Existing Clients Add-On" calendar it's on now. The add-on calendar is for current clients; new review leads belong on Credit Consultation. *Saves:* new leads booking the wrong call type.
2. **Reuse `owner-jador` / `owner-terrence` instead of `gw-` owner tags.** *Saves:* one clean owner system instead of two.
3. **Reuse your existing goal/timeline fields** (`What are you leveraging your credit for?`, `Preferred Timeline for Improvement`) instead of creating `credit_goal` / `goal_timeline`. *Saves:* duplicate fields and reporting confusion.
4. **Publish/replace the ENROLL keyword workflow** so ENROLL reliably assigns Jador + sends the gateway link. I'll build a `GW` copy as a draft; you compare and switch. *Saves:* leads silently going nowhere.
5. **Add the missing stall follow-ups** (login-shared-not-booked, ready-to-enroll-no-payment, urgent same-day) as `GW` drafts. *Saves:* leads lost between steps.
6. **Add owner-conflict flagging** so a lead who texts both keywords is caught, not reassigned. *Saves:* wrong-inbox leads.

## 5. Known issue — onboarding email failing after some Commas checkouts (likely cause)

The onboarding email fires off the `commas-purchase` tag, applied by the Commas → Zapier → GHL Zap. Most likely causes, in order:
1. **The Zap didn't apply the exact tag** on some checkouts (a Zap error, or a tag-name/case mismatch), so `new client purchase email onboarding` never triggered. The tag must be exactly `commas-purchase`.
2. **Competing onboarding workflows** (`new client purchase email onboarding` vs `New Client Onboard Process`) — if one removes the tag or marks the contact done, the other can skip.
3. **Workflow "re-entry" off.** If the workflow only allows a contact through once, a re-purchase or a retag won't re-send.

To confirm, we'd check the Zap's run history for the failed buyers and open that one workflow's trigger. I can dig into the specific workflow if you want.

---

## What I need from you

Answer suggestions **1–6** above with yes / no / a change. I will build **only** what
you say yes to, as `GW`-prefixed **drafts** (nothing published), after taking the
before/after snapshot. Nothing has been created or changed yet.
