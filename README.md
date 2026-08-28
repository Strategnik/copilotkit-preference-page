# "What are you building?" — preference page (handoff)

One-question page that lets a contact self-select their use case with a single click.
Sets the HubSpot contact property `nurture_use_case`, which drives the Use-Case Nurture
workflow branching. **Bot-proof by design:** email security scanners follow links but do
not click buttons inside a page — so unlike raw email-link tracking (which produced 19/19
bot "selections" on the welcome send), every submission here is a human.

Styled to match copilotkit.ai: Plus Jakarta Sans, #EDEDF5 background, #120635 ink,
#6963ff accent, pill buttons, real logo asset.

## Files
- `index.html` — the whole page (self-contained; one CDN font link)
- `cpk-logo.svg` — CopilotKit logo (referenced by the page)

## Go-live checklist (CopilotKit team, ~10 min)
1. **Create the HubSpot form** (portal 45532593 → Marketing → Forms → embedded form):
   - Name: `Nurture · Use-Case Selection (preference page)`
   - Field 1: **Email** (required)
   - Field 2: contact property **"Nurture · Use Case"** (`nurture_use_case`) — hidden field
   - Turn OFF reCAPTCHA (submissions come via the Forms API; the button-click is the bot filter)
   - Copy the **form GUID** (in the form's embed code / URL)
2. **Set the GUID** in `index.html` → `const FORM_GUID = "…"`.
3. **Host** both files anywhere static (a `/what-are-you-building` path on the site,
   Vercel, Netlify — no build step, no server).
4. **Link to it from emails** as:
   `https://<host>/?email={{contact.email}}`
   The HubSpot personalization token identifies the recipient — no typing for them.
5. Test: open with `?email=your@email.com`, click a card, confirm the contact's
   `nurture_use_case` updates in HubSpot.

## Behavior details
- Click card → submits form (email + track) → thank-you state with a **track-matched
  Dojo deep link** (each selection lands on the live demo for that use case).
- `?demo=1` (or unset GUID) = demo mode: full UX, no submission — safe to preview.
- Forwarded emails: if `email` param is missing, the page still works (shows Dojo link)
  but records nothing — deliberate, prevents junk submissions.
- Downstream: setting `nurture_use_case` auto-enrolls the contact in the
  **Use-Case Nurture — Master (prime loader)** workflow *(note: its enrollment trigger
  currently needs re-pointing at this property — coordinate with whoever edited it)*.

## Alternative to step 1
Grant the `forms` scope to the private app used for this project and Nick's side can
create the form + set the GUID via API — zero manual steps.
