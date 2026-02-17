# Notion Call Scorecards — Database Schema

Create a new **full-page database** in your Notion workspace (or use an existing "Call Scorecards" page). Add the following properties exactly as specified — the property names must match for the Zapier integration to work correctly.

## Database Properties

| Property Name | Type | Configuration | Notes |
|---------------|------|---------------|-------|
| **Call Title** | Title | — | Primary title property (default) |
| **Call Date** | Date | — | Date the call took place |
| **Recording Link** | URL | — | Link to the Grain recording |
| **Participants** | Rich Text | — | Attendees on the call |
| **Overall Score** | Number | Format: Number, 1 decimal | Average of all 5 VSPICED scores (1.0–5.0) |
| **Score Band** | Select | Options below | Visual indicator of call quality |
| **Situation Score** | Number | Format: Number, 0 decimals | 1–5 score |
| **Situation Notes** | Rich Text | — | AI-generated evidence and reasoning |
| **Pain Score** | Number | Format: Number, 0 decimals | 1–5 score |
| **Pain Notes** | Rich Text | — | AI-generated evidence and reasoning |
| **Impact Score** | Number | Format: Number, 0 decimals | 1–5 score |
| **Impact Notes** | Rich Text | — | AI-generated evidence and reasoning |
| **Critical Event Score** | Number | Format: Number, 0 decimals | 1–5 score |
| **Critical Event Notes** | Rich Text | — | AI-generated evidence and reasoning |
| **Decision Score** | Number | Format: Number, 0 decimals | 1–5 score |
| **Decision Notes** | Rich Text | — | AI-generated evidence and reasoning |
| **Overall Summary** | Rich Text | — | AI-generated 3-4 sentence call quality summary |
| **Key Next Steps** | Rich Text | — | AI-recommended follow-up actions |
| **Scored At** | Date | Include time | Timestamp when the scorecard was generated |

## Score Band Select Options

Configure these options for the **Score Band** property with the colors indicated:

| Option | Color | Criteria |
|--------|-------|----------|
| Excellent (4.5–5.0) | Green | Overall score >= 4.5 |
| Good (3.5–4.4) | Blue | Overall score >= 3.5 and < 4.5 |
| Adequate (2.5–3.4) | Yellow | Overall score >= 2.5 and < 3.5 |
| Weak (1.5–2.4) | Orange | Overall score >= 1.5 and < 2.5 |
| Missing (1.0–1.4) | Red | Overall score < 1.5 |

## How to Create the Database

1. Open your Notion workspace
2. Navigate to (or create) a page called **Call Scorecards**
3. Type `/database` and select **Database - Full page**
4. Rename the default "Name" property to **Call Title**
5. Add each property from the table above using the **+** button in the table header
6. For Select properties, click the property → add the options listed above with their colors

## Getting the Database ID

The Zapier Notion integration needs the database ID. To find it:

1. Open the database in Notion as a full page
2. Look at the URL — it will look like:
   ```
   https://www.notion.so/yourworkspace/abc123def456...?v=...
   ```
3. The database ID is the 32-character hex string before the `?v=` parameter
4. Format it with dashes: `abc123de-f456-7890-abcd-ef1234567890`

Alternatively, when you configure the Notion step in Zapier, you can select the database from a dropdown — Zapier will resolve the ID automatically.

## Recommended Views

### Default Table View
Show all properties in table format for a spreadsheet-like overview.

### Score Summary View (Board)
- Group by: **Score Band**
- Show: Call Title, Call Date, Overall Score, Participants
- This gives a kanban-style view of call quality distribution

### Timeline View
- Timeline by: **Call Date**
- Show: Call Title, Overall Score, Score Band
- Track scoring trends over time

### Filter: Recent Calls
- Filter: Call Date is within the past 7 days
- Sort: Call Date, Descending
- Quickly review the latest scored calls
