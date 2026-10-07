# Agent Skills

My custom AI agent skills in the OpenSkills `SKILL.md` format — job search, partnerships, content, and dev workflows.

## Install

Skills live at the repo root so they install individually with [OpenSkills](https://github.com/openskills):

```bash
npm install -g openskills
openskills install Mike-Demo/agent-skills/<skill-name>
```

For example:

```bash
openskills install Mike-Demo/agent-skills/resume-tailor
openskills install Mike-Demo/agent-skills/jobspipe
```

## Catalog

| Skill | Description |
|---|---|
| agent-chat-ux | Make agents communicate like great conversationalists: emoji language, status reactions, progress bars, and interactive cards/buttons. |
| adobe-firefly | Generate or edit images through Adobe Firefly's web app via the live browser on a paid Adobe account. |
| aikido | Use Aikido security scanning when the user asks to scan code for vulnerabilities or secrets, or to triage Aikido security issues. |
| application-form-filler | Fill out job application form fields with context-aware, tailored answers drawn from the candidate's CV and the job description. |
| beautiful-ai | Build Beautiful.ai presentations: list themes/folders/decks, create decks from an outline or template, export to PDF/PPTX. |
| browser-use | Use Browser Use when the user asks for Browser Use or this provider's API. |
| career-ops-followup | Track follow-up cadence for active job applications — flag overdue follow-ups, draft tailored follow-up emails/LinkedIn messages. |
| career-ops-patterns | Analyze job applications for rejection patterns and targeting insights — funnel stats, channel yield, ATS-vendor yield. |
| clera | Candidate dashboard for the Clera account via the authenticated MCP server (mcp.getclera.com). |
| cover-letter-generator | Create personalized, compelling cover letters from resume and job description. |
| dice | Search tech jobs on Dice via its public MCP server (mcp.dice.com/mcp). |
| firecrawl | Use Firecrawl when the user asks for Firecrawl or this provider's API. |
| firefly-first | Cost-saving router: try Adobe Firefly's web app first (free/unlimited on the paid Adobe account) before billable Pexo video generation. |
| github | Work with GitHub through the REST API (api.github.com) via a generic CLI proxy. |
| gptzero | Scan text with the GPTZero AI-detection API to check whether drafts read as human-written. |
| indeed | Indeed MCP skill — currently blocked: Indeed's MCP server is allowlisted to Claude's own connector clients. |
| job-description-analyzer | Analyze job postings, calculate match scores, identify gaps, and create application strategy. |
| job-feeds | Unified job search skill — three sources wired into one sweep, merged and deduped. |
| jobsdb | Free, unlimited manual job search against the public jobs_db Supabase (~178k US jobs). |
| jobspipe | Use Jobspipe when the user asks for Jobspipe or this provider's API. |
| lovable | Use Lovable when the user asks about Lovable projects, deployments, or publishing. |
| mental-health-support | Soften harsh wording in draining workflows (opt-in): neutral alternatives, notification batching, quiet hours; includes a job-search mode for status labels and digest summaries. |
| partnerships-career | Career strategy for experienced partnerships leaders across channel/alliance, affiliate, influencer/creator, and referral. |
| rebrandly | Use Rebrandly when the user asks for Rebrandly or this provider's API. |
| resume-ats-optimizer | Optimize resumes for Applicant Tracking Systems, check ATS compatibility, and analyze keyword match. |
| resume-tailor | Customize resume for specific job postings while maintaining truthfulness. |
| rezi | Use Rezi when the user asks for Rezi or this provider's API. |
| rssapi | Use Rssapi when the user asks for Rssapi or this provider's API. |
| superhuman-docs | Use Superhuman Docs when the user asks for Superhuman Docs or this provider's API. |
| tailored-resume-generator | Analyzes job descriptions and generates tailored resumes that highlight relevant experience, skills, and achievements. |
| uptimerobot | Use UptimeRobot when the user asks about site uptime, monitors, incidents, or status pages. |
| webkit-feature-flags | Safari WebKit Feature Flags (iOS/iPadOS 27): which flag gates which web feature, when to enable it, and which flags are internal engine switches to leave alone. Use when a developer asks which feature flag to toggle, what a flag does, or whether a flag is safe to change. |

## Attribution

`partnerships-career` is adapted from Maya-Beth Finotti's original skill; her authorship and the fork attribution are preserved in that skill's `SKILL.md`.

## License

MIT — see [LICENSE](LICENSE). Copyright (c) 2026 Mike Demopoulos.
