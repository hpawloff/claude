# Setup Guide — Grain VSPICED Call Scoring Automation

End-to-end instructions for getting the automation running.

---

## Prerequisites

Before you begin, ensure you have:

- [ ] **Grain** account on the **Business plan** (required for API access)
- [ ] **Zapier** account (Professional plan recommended for multi-step Zaps with filters)
- [ ] **Notion** workspace with API integration enabled
- [ ] **Anthropic (Claude) API key** or **OpenAI API key** for the scoring step

---

## Part 1: Set Up Grain

### 1.1 Verify API Access

1. Log in to your Grain workspace at [grain.com](https://grain.com)
2. Go to **Settings → Workspace → API** (Business plan feature)
3. Generate or copy your **API key**
4. Test it with a quick curl:
   ```bash
   curl -H "Authorization: Bearer YOUR_GRAIN_API_KEY" \
     https://api.grain.com/_/public-api/me
   ```
5. You should get a JSON response with your workspace details

### 1.2 Ensure Recordings Are Processing

- Confirm that your Grain bot is joining calls and creating recordings
- Make sure AI notes and transcripts are being generated (this is on by default)

---

## Part 2: Set Up Notion

### 2.1 Create a Notion Integration

1. Go to [notion.so/my-integrations](https://www.notion.so/my-integrations)
2. Click **+ New integration**
3. Name it: `Grain Call Scorer`
4. Select your workspace
5. Under **Capabilities**, ensure these are checked:
   - Read content
   - Insert content
   - Update content
6. Click **Submit** and copy the **Internal Integration Secret** (starts with `ntn_`)

### 2.2 Create the Call Scorecards Database

1. Open your Notion workspace
2. Create a new page called **Call Scorecards** (or navigate to your existing one)
3. Add a **Database - Full page** block
4. Configure all properties as defined in **`notion-database-schema.md`**

### 2.3 Connect the Integration to the Database

1. Open the Call Scorecards database page
2. Click the **...** menu (top right) → **Connections** → **Connect to** → select `Grain Call Scorer`
3. Confirm the integration has access

> **This step is critical.** Without it, the Notion API will return a 404 error when Zapier tries to create pages.

---

## Part 3: Set Up Zapier

### 3.1 Connect Your Accounts

1. Log in to [zapier.com](https://zapier.com)
2. Go to **My Apps** and connect:
   - **Grain** — OAuth flow, log in with your Grain credentials
   - **Notion** — Use the Internal Integration Secret from Part 2.1
   - **AI by Zapier** — No account needed (built-in), OR connect **Anthropic (Claude)** directly

### 3.2 Create the Zap

Follow the 5-step blueprint in **`zapier-automation-blueprint.md`**:

#### Step 1: Trigger
- App: **Grain**
- Event: **Recording Added**
- Connect your Grain account
- Test trigger to pull in a sample recording

#### Step 2: Filter
- App: **Filter by Zapier**
- Condition: Title **(Text) Contains** `Introduction` **OR** Title **(Text) Contains** `Intro`

#### Step 3: Get Full Transcript
- App: **Grain**
- Event: **Get Recording** or **API Request**
- Map the Recording ID from Step 1
- Enable: Include Transcript = Yes

#### Step 4: AI Scoring
- App: **AI by Zapier** (or **Anthropic Claude**)
- Event: **Generate Text**
- Paste the prompt from **`vspiced-scoring-prompt.md`**
- Map the dynamic fields (title, date, participants, transcript)
- Define output fields for each score and notes field

#### Step 5: Create Notion Page
- App: **Notion**
- Event: **Create Database Item**
- Select: **Call Scorecards** database
- Map every property from Step 4's outputs (see blueprint for full mapping table)

### 3.3 Test the Zap

1. Click **Test** on each step individually to verify data flows correctly
2. After testing all steps, check your Notion Call Scorecards database for the new entry
3. Verify all scores, notes, and metadata populated correctly

### 3.4 Turn On the Zap

1. Click **Publish** (or **Turn on Zap**)
2. The Zap is now live — every new Grain recording with "Intro" or "Introduction" in the title will be automatically scored

---

## Part 4: Verification

### Run a Test Call

1. Schedule a test call and ensure Grain records it
2. Name the meeting something like **"Test Introduction Call"**
3. Have a brief conversation covering VSPICED topics
4. Wait for Grain to finish processing (usually 5-10 minutes after call ends)
5. Check Zapier's **Zap History** to see the run
6. Check your Notion **Call Scorecards** database for the new scorecard

### What to Look For

- All 5 VSPICED component scores are present (1-5 each)
- Notes reference specific moments from the transcript
- Overall score is calculated correctly
- Score Band matches the overall score
- Recording link opens the correct Grain recording

---

## Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| Zap doesn't trigger | Recording title doesn't contain "Intro" or "Introduction" | Check the exact title in Grain; filter is case-insensitive |
| Transcript is empty | Grain hasn't finished processing | Wait for Grain to complete transcription; check recording status |
| AI step fails | Transcript too long for token limit | Switch to direct Anthropic Claude action (200K context) instead of AI by Zapier |
| Notion 404 error | Integration not connected to database | See Part 2.3 — connect integration via the ... menu |
| Scores are null/wrong | AI output doesn't match expected JSON format | Verify the prompt exactly matches `vspiced-scoring-prompt.md`; test in AI playground first |
| Notion property mismatch | Property names don't match exactly | Ensure Notion property names match the blueprint exactly (case-sensitive) |
| Zap runs but skips | Filter blocks the recording | Check Zapier's Zap History → filter step to see why it was filtered out |

---

## Maintenance

- **Review scores weekly** to calibrate — AI scoring may need prompt adjustments based on your team's specific patterns
- **Update the prompt** if your VSPICED rubric evolves (changes to questions, new areas of focus)
- **Monitor Zapier usage** — each run consumes Zapier tasks; a Professional plan is recommended for multi-step Zaps
- **Grain API changes** — check Grain's [release notes](https://grain.com/release-notes) periodically for API updates
