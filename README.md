# n8n-paper-trader

An internal team demo for learning the basics of [n8n](https://n8n.io/) workflow automation. The workflows here are intentionally straightforward so you can see how common building blocks fit together, then extend or combine them in your own projects.

## What this repo contains

Two exported n8n workflows:

| Workflow | File | Purpose |
|----------|------|---------|
| **Paper Trader** | `Paper Trader.json` | End-to-end automation: form input → external APIs → human approval → conditional action |
| **Webhook Example** | `Webhook Example.json` | Minimal webhook trigger that sends a notification |

Import either file in n8n via **Workflows → Import from File**.

## Paper Trader workflow

A simulated stock order flow using Alpaca's **paper trading** API (no real money). Someone submits a trade request through an n8n form; the workflow gathers account context, runs an AI risk check, asks for approval in Telegram, and only places the order if approved.

### Flow

```
Form submission → Get account (HTTP) → AI risk check (HTTP) → Telegram approval
                                                                    ↓
                                                          Approved? (IF)
                                                           ↙         ↘
                                                    Place order    Rejection message
                                                    (HTTP)         (Form)
                                                         ↓
                                                  Approval message
                                                  (Form)
```

### n8n concepts demonstrated

- **Form Trigger** — Collects user input (name, ticker, limit price) and starts the workflow.
- **HTTP Request** — Calls external REST APIs (Alpaca account and order endpoints).
- **HTTP Request (AI)** — Sends a structured prompt to an LLM via OpenRouter and uses the response in later steps.
- **Telegram (Send and Wait)** — Human-in-the-loop approval: the workflow pauses until someone approves or rejects in Telegram.
- **IF** — Branches execution based on whether the approval was granted.
- **Form (Completion)** — Shows a success or rejection message back to the person who submitted the form.

### Credentials you will need

Replace placeholders in the workflow after import:

- **Alpaca** — Paper trading API key and secret (`APCA-API-KEY-ID`, `APCA-API-SECRET-KEY`). Sign up at [Alpaca](https://alpaca.markets/) and use the paper trading environment.
- **OpenRouter** — API key for the AI risk-check step. Create one at [OpenRouter](https://openrouter.ai/).
- **Telegram** — Bot token and chat ID for the approval step. Create a bot via [@BotFather](https://t.me/BotFather) and use your chat ID for notifications.

> **Note:** The exported JSON uses placeholder values for secrets. Configure credentials in n8n (or update the nodes) before activating the workflow. Do not commit real API keys to this repo.

## Webhook Example workflow

A small workflow that listens for an incoming HTTP request and sends a Telegram message when triggered.

### Why it exists

This workflow is meant to be used **alongside another demo** (a separate app or service). That companion demo calls the webhook URL when something happens—for example, a user action or an event in another system. Together they show why webhooks matter: instead of polling or manual checks, an external system can push events into n8n and automation runs immediately.

If you are running the related demo, point its webhook URL at this workflow's production webhook URL in n8n.

### n8n concepts demonstrated

- **Webhook** — Exposes an HTTP endpoint that starts the workflow when called.
- **Telegram** — Sends a notification when the webhook fires.

## Getting started

### Hosting

This demo is hosted on **[Elest.io](https://elest.io/)** for the team—a managed option that avoids standing up infrastructure yourself. A **local n8n install** works equally well for learning: use [n8n's self-hosting docs](https://docs.n8n.io/hosting/) (Docker, npm, etc.) and import the same workflow files.

### Suggested learning path

1. Import **Webhook Example**, activate it, and trigger it with a simple HTTP client (`curl` or Postman) to see a webhook fire end-to-end.
2. Import **Paper Trader**, configure credentials, and walk through each node to see how data passes between steps.
3. Submit a test form, approve or reject in Telegram, and follow the IF branch to the outcome.
4. Experiment: add a node, change the form fields, swap the notification channel, or connect the webhook workflow to your own trigger.

The goal is to understand triggers, HTTP integrations, branching, and human approval—not to build production trading logic. Once these patterns click, you can apply them to internal tools, alerts, data sync, and more complex automations.

## Resources

- [n8n documentation](https://docs.n8n.io/)
- [n8n community](https://community.n8n.io/)
- [Alpaca API docs](https://docs.alpaca.markets/) (paper trading)
