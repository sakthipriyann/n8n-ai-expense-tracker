# Telegram Expense Tracker with AI — Voice, Image, PDF & Text to Google Sheets

An n8n workflow that automates personal expense tracking. Send a bill to your Telegram bot — as a photo, PDF, voice note, or typed text — and it gets parsed by AI and logged to Google Sheets automatically.

Built for Indian invoices. Recognizes vendors like Zomato, Zepto, BigBasket, Amazon and auto-categorizes items. Supports **multilingual input** — send invoices or speak in any language.

> A grocery bill with 50+ items that takes an hour to enter manually? Done in seconds.

---

## Features

- **4 input types** — PDF, photo, voice note, or typed text
- **AI-powered parsing** — OpenAI GPT-5 extracts vendor, date, items, categories, and prices
- **Image analysis** — OpenAI Vision (GPT-4o-mini) reads receipt photos directly
- **Voice support** — OpenAI Transcribe converts voice notes to text, then parses expenses
- **Multilingual** — Works with invoices and voice input in any language
- **Review before saving** — Bot sends a copyable code block summary for you to verify/edit
- **Payment method selection** — Inline keyboard buttons (Credit Card, UPI, Cash, etc.)
- **Monthly sheet routing** — Automatically writes to the correct tab (JAN, FEB, ... DEC)
- **Indian invoice optimized** — Picks final billed amount, ignores GST/tax breakdown rows

## How It Works

```
You (Telegram) → n8n Workflow → Google Sheets
```

1. **Send** a bill to your Telegram bot (photo, PDF, voice, or text)
2. **Routing** — Workflow detects input type automatically
   - Documents/Photos → Download → PDF text extraction or OpenAI Vision analysis
   - Voice notes → OpenAI Transcribe → AI text parsing
   - Typed text → Direct AI parsing
3. **AI parses** the expense into structured JSON (vendor, date, items, categories, prices)
4. **Review** — Bot replies with a copyable code block summary
5. **Confirm** — You copy, edit if needed, paste back
6. **Payment** — Select payment method via inline keyboard buttons
7. **Saved** — Expense is appended to the correct monthly tab in Google Sheets
8. **Done** — Confirmation message sent to Telegram

## Tech Stack

| Tool | Purpose |
|------|---------|
| [n8n](https://n8n.io) | Workflow automation |
| [Telegram Bot API](https://core.telegram.org/bots/api) | User interface (input + output) |
| [OpenAI GPT-5](https://openai.com) | Text parsing & expense extraction |
| [OpenAI GPT-4o-mini](https://openai.com) | Image/receipt analysis (Vision) |
| [OpenAI Transcribe](https://openai.com) | Voice note transcription |
| [Google Sheets](https://sheets.google.com) | Expense storage |

## Setup

### Prerequisites

- **n8n** instance (self-hosted or cloud)
- **Telegram Bot** — Create via [@BotFather](https://t.me/BotFather)
- **OpenAI API key** — From [platform.openai.com](https://platform.openai.com)
- **Google Sheets** — With OAuth2 credentials configured in n8n

### Step 1: Import the workflow

1. Download `Expense_Tracker.json` from this repo
2. Open your n8n instance
3. Go to **Workflows** → **Import from File**
4. Select the downloaded JSON file

### Step 2: Set up credentials in n8n

Create these credentials in n8n (**Settings → Credentials**):

| Credential | Type |
|-----------|------|
| Telegram account | Telegram API (Bot Token) |
| OpenAi account | OpenAI API Key |
| Google Sheets account | Google Sheets OAuth2 |

### Step 3: Replace placeholders

Search for `YOUR_` in the workflow and replace:

| Placeholder | Where to find your value |
|-------------|-------------------------|
| `YOUR_BOT_TOKEN` | From @BotFather when you created your bot |
| `YOUR_CHAT_ID` | Send a message to your bot, then visit `https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates` |
| `YOUR_GOOGLE_SHEET_ID` | The long ID in your Google Sheets URL: `docs.google.com/spreadsheets/d/<THIS_PART>/edit` |

These appear in:
- **Send Payment Options** node → URL field
- **Analyze Image** node → Image URL field
- **Store and Format** node → Code (chat_id)
- **Review** + **Final status** nodes → Chat ID parameter
- **Update sheet** node → Document ID

### Step 4: Create your Google Sheet

Create a Google Sheet with **12 tabs** named:

```
JAN  FEB  MAR  APR  MAY  JUN  JUL  AUG  SEP  OCT  NOV  DEC
```

Each tab should have these column headers in row 1:

| Date | Vendor | Item | Category | Quantity | Price | Payment Mode |
|------|--------|------|----------|----------|-------|-------------|

### Step 5: Activate

Toggle the workflow to **Active** and start sending bills to your Telegram bot!

## Input Examples

**Photo** — Take a picture of any receipt or invoice

**PDF** — Forward a digital invoice (Zomato, Amazon, Zepto, etc.)

**Voice** — Record a message like:
> "Spent 50 rupees on milk and 30 on bread at Zepto today"

**Text** — Type in this format:
```
Vendor: Burger King
Date: 18 Apr 2026
Food | Food | - | 82.96
```

## Customization

- **Payment methods** — Edit inline keyboard in the `Store and Format` code node
- **Categories** — Modify the OpenAI prompt to add custom categories (Fuel, Medicine, Subscriptions, etc.)
- **Vendor recognition** — Update the prompt with local vendor names for your region
- **Sheet layout** — Change column mappings in the `Update sheet` node
- **Monthly tab names** — Modify `getSheetName()` in `Handle Callback` if you prefer different naming (e.g., `April-2026`)

## License

MIT

## Author

Sakthi Priyan
