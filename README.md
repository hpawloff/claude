# Grain Call Scoring Automation

Automated VSPICED call scoring for Introduction/Intro calls recorded in Grain, with results populated to a Notion Call Scorecards database.

## Overview

This automation uses **Zapier** to:
1. **Trigger** on every new Grain recording with "Introduction" or "Intro" in the title
2. **Retrieve** the full call transcript from Grain
3. **Score** the call against the VSPICED framework using AI (Claude or OpenAI)
4. **Create** a scorecard entry in your Notion Call Scorecards database

## Files

| File | Purpose |
|------|---------|
| `zapier-automation-blueprint.md` | Complete Zapier Zap configuration — step-by-step |
| `vspiced-scoring-prompt.md` | The AI prompt used to score transcripts against VSPICED |
| `notion-database-schema.md` | Notion Call Scorecards database property definitions |
| `setup-guide.md` | End-to-end setup instructions with screenshots guidance |

## Quick Start

1. Read `setup-guide.md` for prerequisites (API keys, Notion database setup)
2. Create the Notion database using the schema in `notion-database-schema.md`
3. Follow `zapier-automation-blueprint.md` to build the Zap step-by-step
4. Copy the scoring prompt from `vspiced-scoring-prompt.md` into your AI step

## VSPICED Scoring Components

Each component is scored 1–5:

| Score | Label | Meaning |
|-------|-------|---------|
| 5 | Excellent | Asked relevant discovery questions AND captured specific, actionable answers |
| 4 | Good | Asked most key questions, got useful information with minor gaps |
| 3 | Adequate | Covered basics but missed opportunities to dig deeper |
| 2 | Weak | Superficial coverage, vague answers, missed key questions |
| 1 | Missing | Component not addressed or completely ineffective |

**Components:** Situation, Pain, Impact, Critical Event, Decision

## Environment Variables

These are configured in Zapier — no local `.env` file needed:

- `GRAIN_API_KEY` — Your Grain workspace API key (Business plan required)
- `NOTION_API_TOKEN` — Your Notion integration token
- `NOTION_DATABASE_ID` — The ID of your Call Scorecards database
- `ANTHROPIC_API_KEY` or `OPENAI_API_KEY` — For the AI scoring step
