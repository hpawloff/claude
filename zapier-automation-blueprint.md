# Zapier Automation Blueprint — Grain VSPICED Call Scoring

This document describes the complete Zap configuration step-by-step. The Zap has **5 steps**:

```
┌─────────────────────────────────────────────────────────┐
│  Step 1: Grain — Recording Added (Trigger)              │
│  Step 2: Filter — Title contains "Intro" or             │
│          "Introduction"                                  │
│  Step 3: Grain — Get Recording (with transcript)        │
│  Step 4: AI by Zapier — Score with VSPICED prompt       │
│  Step 5: Notion — Create Database Item (scorecard)      │
└─────────────────────────────────────────────────────────┘
```

---

## Step 1: Trigger — Grain "Recording Added"

| Setting | Value |
|---------|-------|
| **App** | Grain |
| **Trigger Event** | Recording Added |
| **Account** | *(connect your Grain account)* |
| **Notes Filter** | *(leave blank to include all notes)* |

### What this does
Fires instantly every time a new recording finishes processing in Grain. Provides the recording title, date, URL, participants, and meeting summary.

### Test
Click **Test Trigger** to pull in a sample recording. Verify you can see fields like `Title`, `Recording URL`, `Date`, and `Participants`.

---

## Step 2: Filter — Only Continue for Intro Calls

| Setting | Value |
|---------|-------|
| **App** | Filter by Zapier *(built-in, free)* |
| **Filter Type** | Only continue if... |

### Filter Configuration

Set up the filter with an **OR** condition:

```
Field:  Step 1 → Title
Rule:   (Text) Contains
Value:  Introduction

         — OR —

Field:  Step 1 → Title
Rule:   (Text) Contains
Value:  Intro
```

> **Important:** Use "(Text) Contains" (case-insensitive) so it matches "intro", "Intro", "INTRO", "Introduction Call", "Intro Meeting", etc.

### What this does
Stops the Zap from continuing for calls that don't have "Introduction" or "Intro" in the title. Only matching calls proceed to scoring.

---

## Step 3: Action — Grain "Get Recording" (with transcript)

| Setting | Value |
|---------|-------|
| **App** | Grain |
| **Action Event** | API Request (Custom) |
| **Method** | GET |
| **URL** | `https://api.grain.com/_/public-api/recordings/{{step1_recording_id}}?include_transcript=true` |
| **Headers** | `Authorization: Bearer {{your_grain_api_key}}` |

### Alternative: Use Grain's built-in "Get Recording" action

If available in your Grain Zapier integration version:

| Setting | Value |
|---------|-------|
| **App** | Grain |
| **Action Event** | Get Recording |
| **Recording ID** | *(map from Step 1 → Recording ID)* |
| **Include Transcript** | Yes |
| **Include Notes** | Yes |

### What this does
Retrieves the full transcript and AI-generated notes for the recording. The transcript is the core input for VSPICED scoring.

### Output fields used downstream
- `transcript` — Full call transcript
- `title` — Recording title (backup if needed)
- `participants` — Attendee list
- `date` — Recording date

---

## Step 4: Action — AI by Zapier "Score the Call"

| Setting | Value |
|---------|-------|
| **App** | AI by Zapier |
| **Action Event** | Generate Text |
| **Model** | Claude (Anthropic) — *recommended* — or GPT-4 (OpenAI) |

### Prompt

Copy the full prompt from **`vspiced-scoring-prompt.md`** into the prompt field.

Replace the template variables with Zapier field mappings:

| Prompt Variable | Map to Zapier Field |
|----------------|---------------------|
| `{{title}}` | Step 1 → Title |
| `{{date}}` | Step 1 → Date |
| `{{participants}}` | Step 1 → Participants |
| `{{transcript}}` | Step 3 → Transcript |

### Output Fields

Define these output fields in the AI step so they can be individually mapped in Step 5:

| Field Name | Type |
|------------|------|
| `situation_score` | Number |
| `situation_notes` | String |
| `pain_score` | Number |
| `pain_notes` | String |
| `impact_score` | Number |
| `impact_notes` | String |
| `critical_event_score` | Number |
| `critical_event_notes` | String |
| `decision_score` | Number |
| `decision_notes` | String |
| `overall_score` | Number |
| `overall_summary` | String |
| `key_next_steps` | String |

### What this does
Sends the full transcript to AI with the VSPICED scoring rubric. Returns structured JSON with individual scores (1-5), evidence-based notes for each component, an overall score, summary, and recommended next steps.

### Tips
- **Claude (Anthropic)** is recommended for longer transcripts — it handles larger context windows well
- If using **AI by Zapier** (not a direct Claude/OpenAI action), paste the entire prompt into the "Prompt" field and list the output fields
- If the transcript is very long, you may need to use the direct **Anthropic (Claude)** Zapier integration instead of "AI by Zapier" to avoid token limits

---

## Step 5: Action — Notion "Create Database Item"

| Setting | Value |
|---------|-------|
| **App** | Notion |
| **Action Event** | Create Database Item |
| **Account** | *(connect your Notion integration)* |
| **Database** | Call Scorecards *(select from dropdown)* |

### Property Mappings

Map each Notion database property to the corresponding Zapier field:

| Notion Property | Type | Map to Zapier Field |
|----------------|------|---------------------|
| **Call Title** | Title | Step 1 → Title |
| **Call Date** | Date | Step 1 → Date |
| **Recording Link** | URL | Step 1 → Recording URL |
| **Participants** | Rich Text | Step 1 → Participants |
| **Overall Score** | Number | Step 4 → overall_score |
| **Score Band** | Select | *(see formula below)* |
| **Situation Score** | Number | Step 4 → situation_score |
| **Situation Notes** | Rich Text | Step 4 → situation_notes |
| **Pain Score** | Number | Step 4 → pain_score |
| **Pain Notes** | Rich Text | Step 4 → pain_notes |
| **Impact Score** | Number | Step 4 → impact_score |
| **Impact Notes** | Rich Text | Step 4 → impact_notes |
| **Critical Event Score** | Number | Step 4 → critical_event_score |
| **Critical Event Notes** | Rich Text | Step 4 → critical_event_notes |
| **Decision Score** | Number | Step 4 → decision_score |
| **Decision Notes** | Rich Text | Step 4 → decision_notes |
| **Overall Summary** | Rich Text | Step 4 → overall_summary |
| **Key Next Steps** | Rich Text | Step 4 → key_next_steps |
| **Scored At** | Date | *(Use Zapier's built-in `{{zap_meta_human_now}}`)* |

### Score Band Mapping

For the **Score Band** select property, you have two options:

**Option A: Add a Formatter step** (insert between Steps 4 and 5)

Use a **Formatter by Zapier → Utilities → Lookup Table** action:

| When `overall_score` is... | Set Score Band to... |
|---------------------------|---------------------|
| 5.0, 4.9, 4.8, 4.7, 4.6, 4.5 | Excellent (4.5–5.0) |
| 4.4, 4.3, 4.2, 4.1, 4.0, 3.9, 3.8, 3.7, 3.6, 3.5 | Good (3.5–4.4) |
| 3.4, 3.3, 3.2, 3.1, 3.0, 2.9, 2.8, 2.7, 2.6, 2.5 | Adequate (2.5–3.4) |
| 2.4, 2.3, 2.2, 2.1, 2.0, 1.9, 1.8, 1.7, 1.6, 1.5 | Weak (1.5–2.4) |
| 1.4, 1.3, 1.2, 1.1, 1.0 | Missing (1.0–1.4) |

**Option B: Include score_band in the AI prompt output**

Add `"score_band"` to the AI output JSON and add this instruction to the prompt:

```
"score_band": "<one of: 'Excellent (4.5–5.0)', 'Good (3.5–4.4)', 'Adequate (2.5–3.4)', 'Weak (1.5–2.4)', 'Missing (1.0–1.4)' based on overall_score>"
```

Then map Step 4 → score_band directly to the Notion Score Band property. **This is the simpler approach.**

---

## Complete Zap Summary

```
TRIGGER:  Grain → Recording Added
  │
  ▼
FILTER:   Title contains "Introduction" OR "Intro"
  │
  ▼
ACTION:   Grain → Get Recording (include transcript)
  │
  ▼
ACTION:   AI by Zapier → VSPICED scoring prompt
  │
  ▼
ACTION:   Notion → Create Database Item (Call Scorecards)
```

## Error Handling

- **Zap History:** Monitor Zapier's Task History for failed runs. Common issues:
  - Grain API rate limits (rare for single-call processing)
  - AI step timeout on very long transcripts (>2 hours of call time)
  - Notion API errors if database properties don't match field names exactly
- **Auto-Replay:** Enable Zapier's auto-replay feature to automatically retry failed steps
- **Notification:** Set up a Zapier error notification to alert you via email or Slack when a run fails
