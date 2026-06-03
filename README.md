# AI Study Assistant — Telegram + Claude (n8n Workflow)

An advanced n8n workflow that turns a Telegram bot into an interactive, AI-powered study assistant using the Claude (Anthropic) API. Students can query the bot for summaries, explanations, quiz questions, or flashcards on any academic topic.

---

## 🚀 Features

- **Multi-Mode Learning**:
  - 📖 **/summary `<topic>`**: Generates a structured, key-point summary of the topic (under 300 words).
  - 🧠 **/explain `<topic>`**: Provides a detailed explanation using analogies and examples.
  - ❓ **/quiz `<topic>`**: Generates exactly 5 multiple-choice questions with answers hidden behind a tip.
  - 🃏 **/flashcard `<topic>`**: Generates 5 flashcard QA pairs for study.
- **Dynamic Interaction**: Defaults to the explanation mode if free text is sent without a command.
- **Thinking Indicator**: Sends a typing/thinking message to the student while Claude processes the request.
- **Smart Follow-ups**: Suggests related learning commands after completing the response (e.g., prompting the user to take a quiz or generate flashcards on the same topic).
- **Error Handling**: Gracefully catches errors (such as API timeouts or bad input) and informs the user.

---

## 🛠️ Workflow Architecture

The workflow consists of the following nodes in n8n:

1. **Telegram Trigger**: Listens for incoming messages from the Telegram bot.
2. **Parse Message** *(Code Node)*: Extracts message details and parses the command/topic.
3. **Is /start or /help?** *(IF Node)*: Directs users to instructions or continues to topic validation.
4. **Has Topic?** *(IF Node)*: Ensures a topic was provided. If not, asks the user to supply one.
5. **Send Thinking Message** *(Telegram Node)*: Alerts the user that their content is generating.
6. **Build Prompt** *(Code Node)*: Constructs customized system and user prompts depending on the selected mode.
7. **Call Claude API** *(HTTP Request)*: Sends the prompt to Anthropic's Messages API using `claude-sonnet-4-20250514`.
8. **Extract Reply** *(Code Node)*: Handles the API response, trims it to fit under Telegram's 4,000-character limit, and formats it.
9. **Send AI Reply** *(Telegram Node)*: Delivers the AI response to the Telegram chat.
10. **Send Follow-up Options** *(Telegram Node)*: Displays quick suggestions for other study tools on the same topic.
11. **Format/Send Error** *(Code & Telegram Nodes)*: Handles and displays errors gracefully.

---

## ⚙️ Setup & Installation

### Prerequisites
- **n8n** (Cloud or self-hosted)
- **Telegram Bot Token** (obtainable from [@BotFather](https://t.me/BotFather))
- **Anthropic API Key** (obtainable from the [Anthropic Console](https://console.anthropic.com/))

### 1. Import Workflow to n8n
1. Download the [ai_study_assistant_workflow.json](ai_study_assistant_workflow.json) file.
2. Open your n8n workspace.
3. Click on the menu (three dots in top right) -> **Import from File**.
4. Select `ai_study_assistant_workflow.json`.

### 2. Configure Credentials
1. **Telegram nodes**: Click on any Telegram node (e.g., `Telegram Trigger` or `Send Welcome Message`) and add/select your Telegram credentials (requires your Bot Token).
2. **Call Claude API node**: 
   - Open the node.
   - Under **Credential for Header Auth**, select/create a credential of type `Header Auth`.
   - Name the header: `x-api-key`.
   - Set the value to your Anthropic API key.
   - *Note:* The workflow is pre-configured to pass the `anthropic-version: 2023-06-01` header automatically.

---

## 📖 Usage Examples

Send these commands directly to your Telegram bot:

| Command | Action | Example |
| :--- | :--- | :--- |
| `/start` or `/help` | Shows welcome and setup help | `/start` |
| `/explain <topic>` | In-depth explanation with examples | `/explain Quantum Entanglement` |
| `/summary <topic>` | Clear point-by-point summary | `/summary Photosynthesis` |
| `/quiz <topic>` | 5 Multiple Choice Questions | `/quiz French Revolution` |
| `/flashcard <topic>` | 5 flashcard definitions | `/flashcard Mitochondria` |
| `<topic>` | Defaults to `/explain` mode | `Mitosis` |
