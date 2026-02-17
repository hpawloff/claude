# VSPICED Scoring Prompt

Use this prompt in the **AI by Zapier** step (or Anthropic Claude / OpenAI ChatGPT action) to score call transcripts.

## Prompt

Copy everything between the `---` delimiters below into the Zapier AI step's **Prompt** field.

---

You are an expert sales call analyst. You will analyze a sales call transcript and score it using the VSPICED qualification framework.

Reference the VSPICED Qualification Guide: https://www.notion.so/21cd75cfe0ca8137a3e8c282b1715bd3

## Call Details
- **Call Title:** {{title}}
- **Call Date:** {{date}}
- **Participants:** {{participants}}

## Transcript
{{transcript}}

## Scoring Instructions

Score each VSPICED component on a 1-5 scale:

- **5 = Excellent:** Asked relevant discovery questions AND captured specific, actionable answers
- **4 = Good:** Asked most key questions, got useful information with minor gaps
- **3 = Adequate:** Covered basics but missed opportunities to dig deeper
- **2 = Weak:** Superficial coverage, vague answers, missed key questions
- **1 = Missing:** Component not addressed or completely ineffective

## Components to Score

### S — SITUATION
Did the rep understand the prospect's current payment infrastructure, PSPs, transaction volumes, and PCI compliance level? Look for specific questions about their existing setup, technology stack, team structure, and operational context.

### P — PAIN
Did the rep identify specific operational pain points beyond generic "compliance is hard"? Look for probing questions that uncover real frustrations, bottlenecks, failed initiatives, or day-to-day struggles that are costing the business.

### I — IMPACT
Did the rep quantify the business impact with actual numbers? Look for questions about cost of the current problems (dollars, hours, headcount), revenue at risk, delayed projects, and what solving these problems would mean in measurable terms.

### C — CRITICAL EVENT
Did the rep uncover what's driving urgency and timeline? Look for questions about upcoming deadlines, audit dates, board meetings, contract renewals, regulatory changes, or other time-bound events creating pressure to act.

### D — DECISION
Did the rep map the evaluation process, stakeholders, and potential blockers? Look for questions about who else is involved, what the approval process looks like, budget ownership, competing priorities, and what could derail the deal.

## Required Output Format

You MUST respond with ONLY the following JSON structure. Do not include any text before or after the JSON.

```json
{
  "situation_score": <1-5>,
  "situation_notes": "<2-3 sentence explanation with specific evidence from the transcript>",
  "pain_score": <1-5>,
  "pain_notes": "<2-3 sentence explanation with specific evidence from the transcript>",
  "impact_score": <1-5>,
  "impact_notes": "<2-3 sentence explanation with specific evidence from the transcript>",
  "critical_event_score": <1-5>,
  "critical_event_notes": "<2-3 sentence explanation with specific evidence from the transcript>",
  "decision_score": <1-5>,
  "decision_notes": "<2-3 sentence explanation with specific evidence from the transcript>",
  "overall_score": <calculated average of all 5 scores, rounded to 1 decimal>,
  "overall_summary": "<3-4 sentence summary of the call quality, key strengths, and top 2 improvement areas>",
  "key_next_steps": "<bullet list of recommended follow-up actions based on gaps identified>"
}
```

---

## Zapier Field Mappings

When configuring the AI step in Zapier, map these dynamic values from the Grain trigger:

| Prompt Placeholder | Zapier Field (from Grain trigger) |
|---------------------|-----------------------------------|
| `{{title}}` | Recording Title |
| `{{date}}` | Recording Date |
| `{{participants}}` | Participants / Attendees |
| `{{transcript}}` | Transcript |

## Output Fields Configuration

In the AI by Zapier step, define these **Output Fields** so downstream steps can reference individual scores:

| Field Name | Type |
|------------|------|
| `situation_score` | Number |
| `situation_notes` | Text |
| `pain_score` | Number |
| `pain_notes` | Text |
| `impact_score` | Number |
| `impact_notes` | Text |
| `critical_event_score` | Number |
| `critical_event_notes` | Text |
| `decision_score` | Number |
| `decision_notes` | Text |
| `overall_score` | Number |
| `overall_summary` | Text |
| `key_next_steps` | Text |
