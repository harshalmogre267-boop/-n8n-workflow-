# ⚡ n8n Workflows Collection

A curated collection of production-ready, automated n8n workflows designed for productivity, education, and job hunting.

---

## 📂 Workflows Directory

| Workflow Name | Description | Key Services Used | Configuration Requirements | File |
| :--- | :--- | :--- | :--- | :--- |
| **🧠 AI Study Assistant** | Telegram bot that offers summaries, quizzes, flashcards & explanations using Claude AI. | Telegram, Anthropic (Claude Sonnet 3.5), n8n Code | Telegram Bot Token, Anthropic API Key | [ai_study_assistant_workflow.json](ai_study_assistant_workflow.json) |
| **🎯 InternHunt Job Scraper** | Automatic job board scraper (Internshala, RemoteOK, Adzuna) with Google Sheets logging, deduplication, and Slack/Gmail alerts. | Google Sheets, Gmail, Slack Webhooks, n8n Schedule | Google Sheets OAuth, Gmail API, Slack Webhook | [internhunt_n8n_workflow.json](internhunt_n8n_workflow.json) |

---

## 🧠 1. AI Study Assistant (Telegram + Claude)

An advanced n8n workflow that turns a Telegram bot into an interactive, AI-powered study assistant using the Claude (Anthropic) API. Students can query the bot for summaries, explanations, quiz questions, or flashcards on any academic topic.

### 🚀 Features
- **Multi-Mode Learning**:
  - 📖 **/summary `<topic>`**: Generates a structured, key-point summary of the topic (under 300 words).
  - 🧠 **/explain `<topic>`**: Provides a detailed explanation using analogies and examples.
  - ❓ **/quiz `<topic>`**: Generates exactly 5 multiple-choice questions with answers hidden behind a tip.
  - 🃏 **/flashcard `<topic>`**: Generates 5 flashcard QA pairs for study.
- **Dynamic Interaction**: Defaults to `/explain` if plain text is sent without a command.
- **Thinking Indicator**: Sends a typing/thinking message to the student while Claude processes the request.
- **Smart Follow-ups**: Suggests related learning commands after completing the response (e.g., prompting the user to take a quiz or generate flashcards on the same topic).
- **Error Handling**: Gracefully catches errors (such as API timeouts or bad input) and informs the user.

### 🛠️ How It Works (Node Pipeline)
1. **Telegram Trigger**: Listens for incoming messages.
2. **Parse Message**: Extracts command and topic.
3. **Is /start or /help?**: Handles welcome messages and instructions.
4. **Has Topic?**: Verifies user input.
5. **Send Thinking Message**: Sends thinking placeholder message.
6. **Build Prompt**: Tailors prompts for Claude based on the command.
7. **Call Claude API**: Posts to Anthropic Messages API (`claude-sonnet-4-20250514`).
8. **Extract Reply**: Formats and limits Claude's response.
9. **Send AI Reply**: Sends response back to Telegram.
10. **Send Follow-up Options**: Recommends other study commands.

### ⚙️ Setup & Installation

#### A. Import Workflow to n8n
1. Download the [ai_study_assistant_workflow.json](ai_study_assistant_workflow.json) file.
2. Open your n8n workspace.
3. Click on the menu (three dots in top right) -> **Import from File**.
4. Select `ai_study_assistant_workflow.json`.

#### B. Configure Credentials
1. **Telegram nodes**: Click on any Telegram node (e.g., `Telegram Trigger` or `Send Welcome Message`) and add/select your Telegram credentials (requires your Bot Token).
2. **Call Claude API node**: 
   - Open the node.
   - Under **Credential for Header Auth**, select/create a credential of type `Header Auth`.
   - Name the header: `x-api-key`.
   - Set the value to your Anthropic API key.
   - *Note:* The workflow is pre-configured to pass the `anthropic-version: 2023-06-01` header automatically.

---

## 🎯 2. InternHunt — Job Board Scraper

A robust automation that crawls leading job and internship boards every 6 hours, filters jobs based on target skills and keywords, logs new entries in a Google Sheet for tracking, and sends beautifully styled summaries to Slack and Gmail.

### 🚀 Features
- **Multi-Source Scraping**: Pulls listings from Internshala (HTML scraping with Cheerio), RemoteOK (JSON API), and Adzuna (JSON API).
- **Keyword & Quality Filtering**: Filters jobs based on tech stack keywords (Python, ML, React, etc.) and excludes senior/manager roles automatically.
- **Smart Deduplication**: Queries an existing Google Sheet to check if a job has already been logged, preventing duplicate notifications.
- **Rich Notifications**:
  - **Gmail**: Sends a professional HTML table summarizing new jobs.
  - **Slack**: Sends clean, interactive Slack Block Kit messages.
- **Error Alerts**: Sends email notifications if any node fails.

### 🛠️ How It Works (Node Pipeline)
1. **Every 6 Hours**: Triggers the crawler at schedule intervals.
2. **Fetch Nodes**: Triggers simultaneous GET requests to Internshala, RemoteOK, and Adzuna.
3. **Parse Nodes**: Code blocks parse HTML (using Cheerio) or JSON API responses into unified job objects.
4. **Merge All Sources**: Unifies parsed arrays.
5. **Keyword Filter**: Checks tags and titles against required and excluded terms.
6. **Read Seen Jobs**: Queries existing logs from Google Sheets.
7. **Deduplicate**: Compares IDs to find newly added jobs.
8. **Log to Google Sheets**: appends new items to Google Sheet.
9. **Format Notification & Send**: Dispatches Slack blocks and HTML Gmail message.

### ⚙️ Setup & Installation
1. Import [internhunt_n8n_workflow.json](internhunt_n8n_workflow.json) to your n8n instance.
2. Configure **Google Sheets credentials** and set your target `Spreadsheet ID`.
3. Set your **Slack Webhook URL** and **Gmail** configurations.
4. Replace `YOUR_APP_ID` and `YOUR_APP_KEY` in the Adzuna API node.
