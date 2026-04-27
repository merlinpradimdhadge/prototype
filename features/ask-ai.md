# Ask AI — Feature PRD (Daily Log)

## Overview

Ask AI is a contextual AI assistant integrated into the Daily Log module. It allows construction managers and site supervisors to ask natural language questions about their project data and get instant, actionable answers — without manually navigating sections, filtering data, or generating reports.

The feature is accessed via the **Ask AI button** — a gradient pill button (`.btn-ask-ai`) positioned in the top bar of key screens. Tapping it opens a chat-style bottom sheet where users can type questions or pick from smart suggestions.

---

## Problem Statement

- Managers oversee multiple projects and need quick answers ("Which sites had safety incidents this week?", "How many workers were on-site yesterday?")
- Currently, answering such questions requires navigating into each project, opening individual sections, and mentally aggregating data
- Field supervisors want to quickly log or retrieve info hands-free or with minimal taps
- Weekly report generation requires manually reviewing all sections — AI can pre-summarize

## Goals

1. **Instant answers** — managers get data-driven responses in under 3 seconds
2. **Context-aware** — AI understands which screen the user is on (all projects vs. specific project) and scopes answers accordingly
3. **Actionable suggestions** — smart prompts guide users toward high-value queries
4. **Zero learning curve** — natural language input, no query syntax

---

## User Stories

| # | As a... | I want to... | So that... |
|---|---------|-------------|-----------|
| 1 | Project Manager | ask "Which projects had delays today?" from the project list | I can quickly identify problem sites without opening each one |
| 2 | Project Manager | ask "Summarize today's progress for Skyline Tower" from the project detail | I get a quick digest without scrolling through all sections |
| 3 | Site Supervisor | ask "How many workers clocked in today?" | I get headcount without navigating to the workforce section |
| 4 | Project Manager | ask "Generate a weekly summary for this project" | I can send a stakeholder update in one tap |
| 5 | Project Manager | ask "Compare worker hours this week vs last week" | I can spot staffing trends |
| 6 | Site Supervisor | ask "Any pending deliveries today?" | I know what to expect on-site without checking the deliveries section |

---

## Screen Placement

### Screen 1: Project List (All Projects)

- **Location**: Top bar, right side — after the existing report action button
- **Button style**: `.btn-ask-ai` — gradient pill (`#1F3688 → #365AD9`), white text, star icon, label "Ask AI"
- **Context scope**: Cross-project queries. AI has access to summary data for all projects in the current date range.
- **Example queries**:
  - "Which projects have open incidents?"
  - "Show me projects behind schedule"
  - "Total workforce across all sites today"
  - "Any missed deliveries today?"

### Screen 2: Project Detail

- **Location**: Detail header nav bar, between the back button and the report action button
- **Button style**: `.btn-ask-ai` — same gradient pill, but with semi-transparent white border to match the dark header
- **Context scope**: Single-project queries. AI has access to all data for the currently viewed project within the selected date range.
- **Example queries**:
  - "Summarize today's activity"
  - "How many hours of overtime were logged?"
  - "List all blocked tasks"
  - "What equipment is idle right now?"
  - "Generate a daily report"

---

## Ask AI Chat Sheet

### Trigger
Tapping the Ask AI button on either screen opens a bottom sheet (`.ask-ai-sheet`) that slides up, covering ~75% of the screen.

### Layout

```
┌─────────────────────────────────┐
│  ─── (handle)                   │
│  ✦ Ask AI          [×]          │
│  About: {context label}         │
│─────────────────────────────────│
│                                 │
│  Smart Suggestions (chips):     │
│  ┌─────────┐ ┌────────────────┐ │
│  │ Summary │ │ Delayed tasks  │ │
│  └─────────┘ └────────────────┘ │
│  ┌──────────────┐ ┌───────────┐ │
│  │ Worker hours  │ │ Incidents │ │
│  └──────────────┘ └───────────┘ │
│                                 │
│  ┌─ AI Response Area ─────────┐ │
│  │ (appears after query)      │ │
│  └────────────────────────────┘ │
│                                 │
│  ┌────────────────────────┐     │
│  │ Type your question...  │ [→] │
│  └────────────────────────┘     │
└─────────────────────────────────┘
```

### Components

1. **Header**
   - Star icon (gradient background matching `.btn-ask-ai`)
   - Title: "Ask AI"
   - Subtitle: context label — "All Projects" (from Screen 1) or project name (from Screen 2)
   - Close button (×)

2. **Smart Suggestions**
   - 4–6 contextual chips that change based on which screen triggered the sheet
   - Tapping a chip auto-fills and submits the query
   - **From Project List**: "Projects with delays", "Missed deliveries", "Workforce summary", "Safety overview"
   - **From Project Detail**: "Summarize today", "Blocked tasks", "Worker overtime", "Pending deliveries", "Generate report"

3. **Response Area**
   - Initially empty with a subtle placeholder: "Ask me anything about your projects"
   - After query: shows AI response with formatted text (bold highlights, bullet points)
   - Response card has a light gradient background matching the AI summary cards used elsewhere
   - Typing indicator (three animated dots) shown while "processing"

4. **Input Bar**
   - Text input with placeholder "Type your question..."
   - Send button (arrow icon) — enabled only when input is non-empty
   - Input uses the standard `.merlin-text-field` styling adapted for inline use

### Behavior

- **Opening**: Sheet slides up with 0.3s ease-out transition
- **Backdrop**: Semi-transparent overlay (same as FAB overlay) — tapping dismisses the sheet
- **Submitting**: User types or taps a suggestion → brief typing animation (1.5s) → AI response appears
- **Multiple queries**: User can ask follow-up questions; previous responses remain visible (scrollable)
- **Closing**: Tap × or swipe down or tap backdrop

---

## Data Model (Prototype Scope)

For the prototype, AI responses are **pre-canned** based on keyword matching:

| Keyword(s) | Response |
|------------|----------|
| summary, summarize | Formatted summary of the day's key metrics |
| delay, behind, late | List of delayed items with reasons |
| worker, workforce, hours | Worker count and hours breakdown |
| incident, safety | Safety status and incident list |
| delivery, deliveries | Delivery status overview |
| equipment, idle | Equipment utilization summary |
| report | Generated report preview |
| blocked, stuck | Blocked tasks with blockers listed |

Fallback response: "I can help with project summaries, workforce data, deliveries, safety incidents, equipment status, and reports. Try asking about any of these!"

---

## Visual Specs

### Ask AI Button (`.btn-ask-ai`)
- **Height**: 36px
- **Border radius**: 32px (pill)
- **Background**: `linear-gradient(135deg, #1F3688, #365AD9)`
- **Text**: white, `labelXSmall` (12px, weight 500)
- **Icon**: 14×14 star SVG, white
- **Padding**: 0 14px
- **Gap** (icon to text): 6px
- **Shadow**: `0 2px 8px rgba(31, 54, 136, 0.3)`

### On dark header (Screen 2)
- Same gradient background
- Add `border: 1px solid rgba(255,255,255,0.2)`

### Ask AI Sheet
- Background: `var(--bg-white)`
- Border radius: `20px 20px 0 0`
- Max height: 75% of phone frame
- Shadow: `0 -4px 24px rgba(0,0,0,0.15)`
- Z-index: 60 (same level as quick-form sheets)

### Suggestion Chips
- Padding: `8px 14px`
- Border radius: `20px`
- Background: `linear-gradient(135deg, rgba(31,54,136,0.08), rgba(54,90,217,0.08))`
- Border: `1px solid rgba(54,90,217,0.15)`
- Text: `paragraphXSmall` (12px), color `#1F3688`
- On tap: filled gradient background, white text

### AI Response Card
- Background: `linear-gradient(135deg, #F6F1FA, #EBF0FF, #F6F1FA)`
- Border: `1px solid var(--brand-100)`
- Border radius: `12px`
- Padding: `14px`
- Text: `paragraphSmall` (14px), `var(--text-sub)`
- Bold highlights: `var(--text-strong)`

---

## Edge Cases

- **No data available**: "No data found for the selected date range. Try expanding the date filter."
- **Offline**: Button is visible but tapping shows toast: "AI requires an internet connection."
- **Long responses**: Response area scrolls independently within the sheet.

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Ask AI adoption rate | >40% of daily active users try it in first week |
| Avg queries per session | 2–3 |
| Time to answer vs manual navigation | 5× faster |
| User satisfaction (post-query) | >4.2/5 |

---

## Future Enhancements (Out of Scope)

- Voice input for hands-free queries on-site
- AI-generated daily log entries from photos
- Proactive AI alerts ("I noticed 3 projects have no logs today")
- Cross-module queries (linking to Supply Chain, Sales data)
- Export AI responses as formatted reports
