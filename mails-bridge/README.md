# Mails Bridge

Multi-account email access for Claude Code and Claude Desktop via IMAP/SMTP. Add Gmail, Outlook, Yahoo, iCloud, or any email provider — then search, read, send, and organize emails right from your conversation.

## Setup

### 1. Install the plugin

Add the marketplace in Claude Code:

```
https://github.com/pavankalyanm/claude-plugins.git
```

Then install **Mails Bridge** from the plugin list.

### 2. Create an App Password

| Provider | How to get an App Password |
|----------|---------------------------|
| **Gmail** | Enable 2FA → [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords) → Create for "Mail" |
| **Outlook / Hotmail** | [account.live.com/proofs/AppPassword](https://account.live.com/proofs/AppPassword) |
| **Yahoo** | [login.yahoo.com/account/security/app-passwords](https://login.yahoo.com/account/security/app-passwords) |
| **iCloud** | [appleid.apple.com](https://appleid.apple.com) → Sign-In and Security → App-Specific Passwords |
| **Zoho** | [accounts.zoho.com](https://accounts.zoho.com/home#security/security_apppassword) → Security → App Passwords |
| **Fastmail** | [fastmail.com/settings/security/devicekeys](https://www.fastmail.com/settings/security/devicekeys) |
| **ProtonMail** | Requires [ProtonMail Bridge](https://proton.me/mail/bridge) running locally |
| **Other** | Use your account password with custom IMAP/SMTP servers |

### 3. Add your account

Tell Claude:

```
add my personal email user@gmail.com xxxx xxxx xxxx xxxx
```

The server auto-detects IMAP/SMTP settings from the email domain. For custom providers:

```
add my work email user@company.com password123 with imap server mail.company.com and smtp server smtp.company.com
```

### 4. Verify

```
show my email profile
```

You'll see inbox total, unread count, and the detected provider.

## Commands

### Account management

| Command | What it does |
|---------|-------------|
| `email_add_account(alias, email, password)` | Add an account (auto-detects provider) |
| `email_add_account(alias, email, password, imap_host, imap_port, smtp_host, smtp_port)` | Add with custom servers |
| `email_remove_account(alias)` | Remove an account |
| `email_list_accounts()` | List all accounts and providers |
| `email_supported_providers()` | Show all auto-detected providers |

### Reading email

| Command | What it does |
|---------|-------------|
| `email_search(query, account, max_results, folder)` | Search via IMAP — `"UNSEEN"`, `"FROM \"boss@co.com\""`, `"SUBJECT \"meeting\""` |
| `email_read_message(uid, account, folder)` | Read full message by UID |
| `email_read_thread(subject, account)` | Read all messages in a thread |

### Composing

| Command | What it does |
|---------|-------------|
| `email_create_draft(to, subject, body, account)` | Save a draft |
| `email_send(to, subject, body, account)` | Send an email (always confirms first) |

### Organization

| Command | What it does |
|---------|-------------|
| `email_list_drafts(account)` | List draft emails |
| `email_list_folders(account)` | List all IMAP folders/labels |
| `email_move_message(uid, destination, account)` | Move to another folder |
| `email_mark_read(uid, account)` | Mark as read |
| `email_mark_unread(uid, account)` | Mark as unread |
| `email_star_message(uid, account)` | Star/flag a message |
| `email_get_profile(account)` | Inbox stats |

## Use case: Cowork daily briefing

In Claude's **Cowork** mode, Mails Bridge shines as part of multi-tool workflows. Here's a real example — a morning briefing agent that checks your email alongside your calendar and tasks:

**Prompt to Cowork:**

> "Every morning, check my unread emails across all accounts, summarize anything urgent, draft replies to messages that need a response, and flag anything related to my job search."

**What happens:**

1. Cowork calls `email_list_accounts()` to discover your accounts (personal, work, sde, job...)
2. Runs `email_search("UNSEEN")` in parallel across all accounts
3. Reads the full body of important messages with `email_read_message()`
4. Cross-references with your calendar (if connected) to surface meeting-related emails
5. Drafts replies with `email_create_draft()` for your review
6. Stars urgent messages with `email_star_message()`
7. Presents a clean summary table:

```
| Account  | From              | Subject                    | Action      |
|----------|-------------------|----------------------------|-------------|
| personal | recruiter@stripe  | Interview next steps       | Draft reply |
| work     | manager@company   | Q3 planning doc            | Starred     |
| job      | no-reply@lever    | Application received       | No action   |
| sde      | github@github.com | PR review requested        | Starred     |
```

**Other Cowork workflows:**

- **"Forward all job alerts from my job account to my personal account"** — searches, reads, and re-sends
- **"Find the thread with Sarah about the API migration and summarize it"** — uses `email_read_thread()` to pull the full conversation
- **"Clean up my inbox — archive anything older than 30 days that I've read"** — bulk `email_move_message()` with date filters
- **"Draft a follow-up to everyone I interviewed with last week"** — searches sent mail, reads threads, drafts personalized follow-ups

## Supported providers

| Provider | Domains | Auto-detected |
|----------|---------|:---:|
| Gmail | gmail.com, googlemail.com | Yes |
| Outlook | outlook.com, hotmail.com, live.com, msn.com | Yes |
| Yahoo | yahoo.com, ymail.com | Yes |
| iCloud | icloud.com, me.com, mac.com | Yes |
| AOL | aol.com | Yes |
| Zoho | zoho.com | Yes |
| Fastmail | fastmail.com | Yes |
| ProtonMail | protonmail.com, proton.me | Yes* |
| Custom | any domain | Pass imap_host/smtp_host |

*ProtonMail requires ProtonMail Bridge running locally.

## Requirements

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) package manager
