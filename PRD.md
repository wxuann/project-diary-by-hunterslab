# PRD · Field Journal

> Product requirements document for the hunterslab Field Journal.

---

## Problem statement

Solo founders, operators, and independent professionals lose value every day because:

1. Important context lives only in their head
2. Existing tools are built for teams (Notion, Linear) or for narrative (Day One) or for transactions (Todoist) — none for solo strategic thinking
3. Patterns across days, projects, and people are invisible until disaster reveals them

## Target user

A solo founder or operator running multiple workstreams in parallel. Examples:
- An independent consultant juggling clients and biz dev
- A founder pre-team-hire managing product + sales + fundraising alone
- A serial advisor / dealmaker tracking 20+ relationships at once

Not for: large teams, content creators, students.

## Core hypotheses

1. **Voice-to-text reduces friction enough to build a daily habit.** Typing detailed entries fails. Sub-60-second voice entries succeed.
2. **AI can extract structure without forcing the user to enter it.** Tags, todos, and relationships should emerge from natural writing, not be explicit form fields.
3. **The raw record is sacred. The summary is disposable.** Users should be able to re-render summaries with newer AI models against their unchanged history.

## Data model

### Entry
```
id          string (uuid)
date        date (YYYY-MM-DD)
content     text (markdown, raw user input)
source      enum: voice, text, import
audio_url   string?  (if voice)
projects    string[]  (auto-extracted #tags)
people      string[]  (auto-extracted @mentions)
todos       string[]  (auto-extracted TODO: lines)
ai_summary  text?     (regeneratable)
ai_details  text[]?   (structured key progress points)
created_at  timestamp
```

### Project
```
id            string
name          string
type          enum: long_term, deadline
deadline      date?
description   text
ai_status     text  (regenerated daily from recent entries)
status        enum: active, paused, done, killed
linked_count  integer  (count of entries tagging this project)
```

### Connection
```
id              string
name            string
alias           string?  (e.g. "Advisor", "Peer Founder")
avatar          string   (initial)
role            text     (one-line description)
strength        integer  (1-5)
status          enum: active, warming, cooling, dormant
resources       string[] (what they offer)
potential       string[] (potential collaborations)
private_note    text     (NEVER surfaced in AI outputs)
last_touched    date     (auto-computed from latest entry mention)
linked_entries  integer
```

## Core flows

### Daily entry
1. User opens app on phone or desktop
2. Taps record button OR types in compose area
3. Speaks or types one paragraph
4. Hits Save → entry stored as raw text + (optional) audio
5. Hits Analyze → AI extracts structure, generates summary, links to projects/people
6. User can edit the AI extraction if needed

### Project sense-making
1. Open Projects tab
2. See active projects grouped by urgency
3. Tap any project → see all linked entries, current AI status, todos
4. AI status regenerates daily from recent entries

### Relationship maintenance
1. Open Connections tab
2. See people grouped by relationship status
3. Tap any person → see their resources, potential collaborations, private note, linked entries
4. "Last touched" auto-updates from entry mentions
5. Status transitions (active → warming → cooling → dormant) flagged after N days no contact

## Non-goals

- Multi-user collaboration
- Public sharing of journal content
- Calendar integration (v1)
- Email integration (v1)
- Mobile native apps (PWA only)
- Real-time sync (eventual consistency is fine)

## Design principles

1. **Voice input is the default**, text is the fallback
2. **One screen, one decision** — never make the user choose between 5 templates
3. **AI is a render layer, not a database mutation** — preserve raw forever
4. **Dark UI by default** — built for evening review sessions
5. **Friction-free is a feature** — under-60-second entries always work

## Open questions

- Should connections support photos / contact info, or stay text-only?
- How do we handle entries that span multiple days (e.g. a 3-day trip)?
- Should AI insights be regenerated on a schedule, or only on demand?
- Multi-language support: ship English first, add others when there are 10+ active users?
