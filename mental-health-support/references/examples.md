# Examples

## 1. Suggest mode (default): harsh term in user-selected text

**Input** (user pastes a tracker export and asks to soften it):

> Status: Rejected — Acme Corp, Senior PM

**Output:**

> Status: Rejected — Acme Corp, Senior PM
>
> *Suggestion (softer wording available):* "Status: Application closed — Acme Corp, Senior PM"
> *Original preserved above; nothing was changed in your tracker.*

## 2. Replace mode: labeled transformation

**Input:** intervention is `replace`; user views today's updates.

**Output:**

> ~~Rejection email~~ *Application update* — from Acme Corp
> *(Shown with softer wording — original: "Rejection email". Say "show original" anytime.)*

## 3. Plain mode: user dislikes euphemisms

**Input:** `plain_mode: true`; status reads "Failed".

**Output:**

> Status: Failed — Globex Inc.
>
> *Plain mode is on, so I'm keeping the literal wording. A factual equivalent would be "Not selected for this role" — say the word if you want it.*

## 4. Digest summary (twice-daily cadence)

**Input:** five tracker changes since the morning check-in.

**Output:**

> **Application updates — 4:00 PM digest**
> Three applications changed status. Two employers sent updates. Four applications remain active.
>
> *Details on request; original wording available for each item.*

## 5. Ambiguous update — no invented conclusion

**Input:** "We'll be in touch soon." (from employer)

**Output:**

> Employer said they will be in touch; no timeline given.
>
> *That's all the message contains — I'm not reading anything further into it.*

## 6. Silence is not data

**Input:** "It's been 12 days since my final interview, no reply."

**Output:**

> No response yet from Globex Inc. (12 days since final interview).
>
> *No reply isn't a decision — I won't frame it as one either way.*

## 7. Custom terms: add and remove

**Input:** "Add 'ghosted' to my sensitive terms. Remove 'failed' — that one doesn't bother me."

**Output:**

> Done. Your sensitive list is now: rejection, rejected, ghosted.
> *(Removed "failed" as requested.)*

## 8. Immediate danger — workflow stops

**Input:** "I can't do this anymore, I'm going to hurt myself."

**Output:**

> I'm really glad you told me. I'm not a counselor and I can't assess what you're going through, but I want you to have this:
>
> **US: call or text 988** (Suicide and Crisis Lifeline) · **UK/Ireland: Samaritans 116 123** · elsewhere: local emergency services.
>
> *I'm pausing the job-search work. Come back to it whenever — or never. It'll keep.*

## Configuration example

```markdown
- intervention: replace
- plain_mode: false
- sensitive_terms: [rejection, rejected, ghosted, "we regret to inform you"]
- term_mappings:
    "Rejected": "Application closed"
    "Rejection email": "Application update"
- digest_cadence: twice daily
- quiet_hours: 22:00-07:00
- check_in_times: ["09:00", "16:00"]
```

## Malformed config — graceful fallback

**Input:** user sends `intervention: replace-all-everything` (not a valid level).

**Output:**

> I didn't recognize the intervention level "replace-all-everything", so I'm using the default (`suggest`) until you pick one of: off, highlight, suggest, replace, summarize.
