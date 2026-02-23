# AI_EMAIL_SUMMAEIZER_AGENT
Agentic AI Email Summarizer (n8n Workflow)  An Agentic AI-powered email automation workflow built in n8n.

<img width="1528" height="483" alt="image" src="https://github.com/user-attachments/assets/1a9250ed-a180-4ac6-8fda-c4f968ed18e0" />



This workflow automatically:

* 📩 Listens for new emails (Gmail Trigger)
* 🧠 Summarizes content using LLM (Groq primary model)
* 🔁 Automatically falls back to Mistral if primary fails
* 🚨 Detects HIGH importance emails
* 📤 Forwards urgent emails to manager
* 📊 Logs processed emails into Google Sheets

---

# 🏗 Architecture Overview

```
Gmail Trigger
      ↓
Edit Fields (Clean Email Data)
      ↓
Basic LLM Chain
   ↙             ↘
Groq Model     Mistral Fallback
      ↓
IF (Check Importance)
   ↙            ↘
Send Email     Log to Sheets
```

---

# ⚙️ Tech Stack

* n8n (Workflow Automation)
* Gmail API
* Groq Chat Model (Primary LLM)
* Mistral Cloud Chat Model (Fallback LLM)
* Google Sheets (Logging)

---

# 🚀 Features

| Capability                 | Description                            |
| -------------------------- | -------------------------------------- |
| Autonomous decision making | Uses IF node to detect HIGH importance |
| Primary + Fallback AI      | Groq → Mistral automatic fallback      |
| Email forwarding           | Sends summary to manager               |
| Structured summarization   | Standardized AI output format          |
| Logging                    | Stores processed data in Google Sheets |
| Retry logic                | Built-in retry for LLM node            |
| Agentic behavior           | AI reasons → decides → acts            |

---

# 🔧 Workflow Components

## 1️⃣ Gmail Trigger

* Watches for new incoming emails
* Simplified output enabled

---

## 2️⃣ Edit Fields Node

Extracts and standardizes:

```js
email_text → {{$json.textPlain || $json.snippet}}
subject → {{$json.subject}}
sender → {{$json.from}}
```

Keep Only Set: ✅ Enabled

---

## 3️⃣ Basic LLM Chain

### Prompt Used:

```
You are an AI email assistant.

Analyze this email and return:

Summary: <short summary>
Importance: HIGH or MEDIUM or LOW

Email:
{{ $json.email_text }}
```

---

## 4️⃣ Primary Model – Groq Chat Model

Used for:

* Fast summarization
* Low latency production usage

---

## 5️⃣ Fallback Model – Mistral Cloud Chat

Used automatically if Groq fails.

Recommended model:

```
mistral-small-latest
```

API endpoint:

```
https://api.mistral.ai/v1/chat/completions
```

---

## 6️⃣ IF Node (Importance Detection)

Condition:

```
{{ $json.text.toLowerCase() }} contains "high"
```

TRUE → Forward to manager
FALSE → Log only

---

## 7️⃣ Send Email Node (Gmail)

Subject:

```
AI Summary: {{$node["Edit Fields"].json["subject"]}}
```

Body:

```
Original Sender: {{$node["Edit Fields"].json["sender"]}}

{{$json.text}}
```

---

## 8️⃣ Google Sheets Logging

Appends:

| Timestamp | Sender | Subject | Summary | Status      |
| --------- | ------ | ------- | ------- | ----------- |
| {{$now}}  | sender | subject | summary | Sent/Logged |

---

# 🛡 Error Handling Strategy

1. Retry enabled on LLM node (3 attempts)
2. Automatic fallback to Mistral
3. Logging of processed emails
4. Non-blocking failure handling

---

# 🧠 Agentic Capabilities

| Agent Skill | Implemented    |
| ----------- | -------------- |
| Perception  | Gmail Trigger  |
| Reasoning   | LLM Analysis   |
| Decision    | IF Node        |
| Action      | Send Email     |
| Memory      | Google Sheets  |
| Resilience  | Fallback Model |

---

# 📥 Installation

1. Install n8n (Cloud or Self-hosted)
2. Import workflow JSON
3. Configure credentials:

   * Gmail
   * Groq API Key
   * Mistral API Key
   * Google Sheets
4. Activate workflow

---

# 🔑 Required Environment Variables

```
GROQ_API_KEY=
MISTRAL_API_KEY=
```

---

# 📌 Production Recommendations

* Add Gmail label "AI-Processed"
* Add Slack notification for HIGH alerts
* Add importance scoring (1–10 scale)
* Add database logging (Postgres)
* Add rate limiting for API safety
* Add monitoring dashboard

---

# 🧪 Example Use Case

Incoming Email:

```
Subject: URGENT – Production Server Down
Body: Website is not responding. Immediate action required.
```

AI Output:

```
Summary: Production server is down and requires urgent attention.
Importance: HIGH
```

Action:

✔ Forwarded to manager
✔ Logged in Google Sheets




