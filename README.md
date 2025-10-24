# 📧 Team of Email AI Agents (n8n Workflow)

An **AI-powered multi-agent email management system** built with **n8n** and **OpenAI** to automatically classify, respond, and manage different types of emails — from client inquiries to support requests and follow-ups.  

This workflow turns your Gmail inbox into an intelligent email assistant team that handles communication professionally, consistently, and 24/7.

---

## 🚀 Overview

The **Team of Email AI Agents** workflow automates **email classification and response generation** using a set of specialized AI agents.  
Each incoming email is analyzed, categorized, and routed to the appropriate AI agent for an automated yet personalized reply.

### 🧠 AI Agents
1. **Classifier Agent** → Detects the email type.  
2. **Support Agent** → Handles customer complaints and issue reports.  
3. **Meeting Agent** → Manages meeting scheduling and confirmations.  
4. **Inquiry Agent** → Responds to client inquiries and product/service questions.  
5. **Follow-Up Agent** → Handles delayed responses and re-engagement emails.  

---

## 🧩 Workflow Architecture

### 1. **Trigger: Gmail Trigger**
- **Node:** `Gmail Trigger`
- **Type:** `n8n-nodes-base.gmailTrigger`
- **Purpose:** Monitors your Gmail inbox for new messages every hour.
- **Options:**
  - Downloads email attachments.
  - Prefixes attachment names for tracking.
- **Credentials Required:** Gmail OAuth2

---

### 2. **AI Email Classification**
- **Node:** `Classifier Agent`
- **Type:** `@n8n/n8n-nodes-langchain.agent`
- **Connected Model:** `OpenAI Chat Model`
- **Task:** Analyzes incoming email text and categorizes it into one of:
  - Follow-Up Email  
  - Client Inquiry  
  - Support  
  - Meeting Request  
  - Introduction Email

**Example Output:**
```text
Client Inquiry
```

---

### 3. **Automated Routing Based on Category**

Once classified, emails are automatically routed to the appropriate AI agent for response.  

| Email Category | Responsible AI Agent | Gmail Label | Description |
|----------------|----------------------|--------------|-------------|
| Support | **Support Agent** | `Support` | Responds to issue reports, complaints, or refund requests |
| Meeting Request | **Meeting Agent** | `Meetings` | Suggests or confirms meeting times via Google Calendar |
| Client Inquiry | **Inquiry Agent** | `Inquiries` | Handles product/service-related questions |
| Follow-Up Email | **Follow-Up Agent** | `Follow Ups` | Sends polite follow-up reminders |
| Introduction Email | (Optional Extension) | `Introductions` | Greets or routes new contacts |

---

## 🤖 Specialized AI Agents

### 🧾 1. Support Agent
- **Node:** `Support Agent`
- **Type:** `@n8n/n8n-nodes-langchain.agent`
- **Role:** Manages customer support issues including technical problems, refund requests, or assistance queries.
- **Tone:** Empathetic, professional, and problem-solving.

**Example Response:**
```json
{
  "subject": "Re: Technical Issue - Assistance Required",
  "email_body": "Dear [Customer’s Name],\n\nThank you for reaching out. We’re sorry to hear that you’re experiencing issues with [Product Name]. Please try restarting the app and clearing your cache..."
}
```

---

### 📅 2. Meeting Agent
- **Node:** `Meeting Agent`
- **Integrations:** Google Calendar (`Get Events`)
- **Task:** Processes meeting requests, retrieves available calendar slots, and generates structured replies.
- **Features:**
  - Suggests available time slots.
  - Confirms or reschedules meetings.
  - Includes Google Calendar integration for event retrieval.

**Example Response:**
```json
{
  "subject": "Re: Scheduling a Meeting",
  "email_body": "Dear [Name],\n\nThank you for reaching out! Based on your request, here are my available slots:\n- Tuesday, 11:00 AM – 1:00 PM\n- Thursday, 3:00 PM – 5:00 PM\n\nPlease confirm which time works best."
}
```

---

### 💬 3. Inquiry Agent
- **Node:** `Inquiry Agent`
- **Purpose:** Responds to client or prospect questions regarding services, pricing, or products.
- **Capabilities:**
  - Crafts tailored professional responses.
  - Provides pricing information or next steps.
  - Requests clarification if needed.

**Example Response:**
```json
{
  "subject": "Re: Pricing Information for Our Services",
  "email_body": "Dear [Name],\n\nThank you for your interest! Here’s a quick overview of our pricing plans..."
}
```

---

### 🔁 4. Follow-Up Agent
- **Node:** `Follow Up Agent`
- **Function:** Handles follow-ups for unanswered messages or delayed responses.
- **Use Cases:**
  - Re-engagement after no reply.
  - Checking in after proposals or meetings.
  - Polite reminders for pending responses.

**Example Response:**
```json
{
  "subject": "Re: Just Following Up",
  "email_body": "Dear [Name],\n\nI wanted to follow up on my previous message regarding [topic]. Please let me know if you had a chance to review it."
}
```

---

### 🧠 5. Classifier Agent (Coordinator)
- **Node:** `Classifier Agent`
- **Purpose:** Routes emails to the correct AI agent.
- **Input:** Raw email text from Gmail.
- **Output:** Category string (e.g., “Client Inquiry”).
- **Connection:** Linked to `OpenAI Chat Model` for context-aware classification.

---

## 🧩 Workflow Logic

1. **Gmail Trigger** detects new emails.  
2. **Classifier Agent** categorizes the email type.  
3. **Conditional Filters** route to respective agents (Support, Meeting, Inquiry, Follow-Up).  
4. **Each Agent** generates a JSON-formatted response (subject + body).  
5. **Gmail Draft Node** creates a draft response ready for review or auto-send.  
6. **Optional Labels** organize processed emails in Gmail.  

---

## ⚙️ Integrations

| Integration | Purpose |
|--------------|----------|
| **Gmail API** | Email retrieval, labeling, and drafting replies |
| **OpenAI API** | Natural language classification and response generation |
| **Google Calendar API** | Meeting scheduling and time slot checking |
| **n8n LangChain Nodes** | Orchestrates AI reasoning and structured output parsing |

---

## 🧠 Output Schema

Each AI agent uses a **Structured Output Parser** to maintain consistent formatting:

```json
{
  "subject": "Re: [Context-based Subject]",
  "email_body": "Dear [Recipient],\n\n[Body Content]\n\nBest Regards,\n[Your Name]\n[Company Name]"
}
```

---

## 🧰 Setup Instructions

### Requirements
- n8n instance (Cloud or Self-hosted)
- Gmail account with API access
- OpenAI API Key
- Google Calendar access (for meeting management)

### Steps to Deploy
1. Import the `Team_of_Email_AI_Agents.json` into your n8n workspace.
2. Configure the following credentials:
   - Gmail OAuth2
   - OpenAI API key
   - Google Calendar OAuth2 (optional for meetings)
3. Customize prompt templates in each AI agent to match your business tone.
4. Test with sample emails before activation.
5. Enable the workflow.

---

## 🧩 Data Flow Summary

```text
[ Gmail Trigger ]
        ↓
[ Classifier Agent ]
        ↓
 ┌─────────────┬──────────────┬───────────────┬──────────────┐
 │ Support     │ Meeting      │ Inquiry       │ Follow-Up    │
 │ Agent       │ Agent        │ Agent         │ Agent        │
 └─────────────┴──────────────┴───────────────┴──────────────┘
        ↓
[ Gmail Draft Node ]
        ↓
[ Email Sent / Labeled ]
```

---

## 🧾 Example Use Case

### Scenario:
Your business receives 100+ daily emails from customers, clients, and partners.

### Automation Flow:
1. n8n detects new emails automatically.
2. The **Classifier Agent** identifies the type (e.g., Meeting Request).
3. The appropriate **AI Agent** drafts a contextual response.
4. A Gmail draft is created automatically for review or direct sending.
5. All emails are labeled and categorized for audit purposes.

**Result:** 80% reduction in manual email handling time, improved response consistency, and round-the-clock service.

---

## 🧩 Author
**Created by:** Roqeeb Dauda  
**Project Name:** Team of Email AI Agents  
**Version:** 1.0.0  
**License:** MIT  
**Built with:** n8n + OpenAI + Gmail + Google Calendar

---
