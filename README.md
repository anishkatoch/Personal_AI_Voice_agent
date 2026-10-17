# 🎙️ Aneesh AI Voice Assistant

[![Voice Engine](https://img.shields.io/badge/Voice-Retell%20AI-6C5CE7)](#-tech-stack--integrations)
[![Telephony](https://img.shields.io/badge/Telephony-Twilio-F22F46)](#-tech-stack--integrations)
[![Automation](https://img.shields.io/badge/Automation-Make.com-3A5CFF)](#-tech-stack--integrations)
[![Scheduling](https://img.shields.io/badge/Scheduling-Cal.com-292929)](#-tech-stack--integrations)
[![Status](https://img.shields.io/badge/Status-Demo%20%2F%20Limited%20Usage-orange)](#️-note)

An AI-powered **voice assistant** for inbound calls that talks about my professional background, projects, and experience — and can schedule meetings live, during the call.

Built with **Retell AI** + **Twilio** for the call layer, backed by a **Knowledge Hub → SQL fallback** reasoning chain, **Make.com** automation, **POKU Labs** for human-in-the-loop email capture, and **Cal.com** for real scheduling.

<div align="center">

### 📞 Call the Assistant: **+1 (361) 282-4535**
*(Test number · limited usage — see [Note](#️-note))*

</div>

---

## 📑 Table of Contents

- [How It Works](#-how-it-works-at-a-glance)
- [Retell AI Agent Prompt Design](#-retell-ai-agent-prompt-design)
- [Knowledge Retrieval Logic](#-knowledge-retrieval-logic)
- [Custom Tool: `information`](#-custom-tool-information)
- [Make.com Automation Workflow](#-makecom-automation-workflow)
- [SQL Database Integration](#️-sql-database-integration)
- [POKU Labs – Human-in-the-Loop](#-poku-labs--human-in-the-loop-integration)
- [Post-Call Summary Function](#-post-call-detailed-summary-function)
- [System Design Principles](#-system-design-principles)
- [Key Highlights](#-key-highlights)
- [Tech Stack & Integrations](#-tech-stack--integrations)
- [Why This Project?](#-why-this-project)
- [Note](#️-note)
- [Try It Now](#️-try-it-now)

---

## 🧭 How It Works, At a Glance

```mermaid
flowchart TD
    A[📞 Inbound Call via Twilio] --> B[🤖 Retell AI Agent]
    B --> C{Knowledge Hub Lookup}
    C -- Found --> D[🗣️ Spoken Answer]
    C -- Not Found --> E[🔧 information Tool]
    E --> F[🔗 Make.com Webhook]
    F --> G[🗄️ Dynamic SQL Query]
    G --> F
    F --> B
    B --> H[📅 Cal.com Scheduling]
    B --> I[📩 POKU Labs: Email Capture]
    B --> J[📝 Post-Call Summary]
    J --> K[✉️ Make.com Email Automation]
```

<details>
<summary><strong>Read the flow as a numbered sequence</strong></summary>

1. Caller dials the Twilio number → routed to the Retell AI agent.
2. The agent first checks the **Knowledge Hub** (resume, projects, education, experience).
3. If the answer isn't there, it calls the **`information`** custom tool.
4. That tool hits a **Make.com webhook**, which builds and runs a **dynamic SQL query**.
5. Results flow back through Make.com → Retell AI → spoken response.
6. At any point, the agent can route into **Cal.com** to check availability, book, reschedule, or cancel a meeting.
7. **POKU Labs** captures the caller's email mid-conversation without breaking the flow.
8. After the call, a **post-call summary function** fires, sending a structured recap via **Make.com email automation**.

</details>

---

## 🎯 Retell AI Agent Prompt Design

The Retell AI agent is configured using a structured system prompt that defines **objective, tone, behavior, and tool usage**.

<table>
<tr><th>Prompt Layer</th><th>What It Controls</th></tr>
<tr>
<td><strong>Objective</strong></td>
<td>

- Act as a professional AI voice assistant
- Answer questions about my **professional summary, projects, education, experience**
- Allow callers to **schedule meetings**
- **Collect user email** politely during the call

</td>
</tr>
<tr>
<td><strong>Tone</strong></td>
<td>

- Friendly, calm, and professional
- Human-like conversational flow
- Clear, concise, and confident
- Polite handling of unknown queries

</td>
</tr>
<tr>
<td><strong>Speech</strong></td>
<td>

- Natural pacing and pauses
- No robotic responses
- Clear pronunciation
- Context-aware follow-up questions

</td>
</tr>
</table>

<p align="center">
<img width="913" height="645" alt="Retell AI agent configuration" src="https://github.com/user-attachments/assets/57aaf712-fe4f-4263-9faf-2618743af15a" />
</p>

---

## 🧠 Knowledge Retrieval Logic

During a conversation, the agent follows this decision order:

1. **Search Knowledge Hub** — resume data, professional experience, projects, education.
2. **If information is NOT found** — call the custom tool `information` and fetch data from external systems (SQL database).

This keeps answers **fast and cheap** when the Knowledge Hub already has the answer, and falls back to **live, deterministic data** only when needed.

---

## 🔧 Custom Tool: `information`

The `information` tool is a backend function defined in Retell AI.

| Field | Value |
|---|---|
| **Tool Name** | `information` |
| **Purpose** | Fetch structured or external data |
| **Trigger Condition** | Knowledge Hub miss |
| **Integration** | Webhook via Make.com |
| **Input / Output** | JSON |

<details>
<summary><strong>📄 View example tool input schema (JSON)</strong></summary>

```json
{
  "type": "object",
  "properties": {
    "email": {
      "type": "string",
      "description": "The customer's email address, any domain allowed."
    },
    "products": {
      "type": "array",
      "description": "List of products the customer wants a quote for.",
      "items": {
        "type": "object",
        "properties": {
          "link": { "type": "string" },
          "warranty": { "type": "string" },
          "delivery_days": { "type": "string" },
          "main_features": { "type": "string" },
          "product_name": { "type": "string" },
          "price": { "type": "string" }
        },
        "required": ["product_name", "price", "main_features"]
      }
    }
  },
  "required": ["email", "products"]
}
```

</details>

---

## 🔄 Make.com Automation Workflow

When the `information` tool is called:

<details open>
<summary><strong>Step-by-step pipeline</strong></summary>

1. Retell AI sends a **JSON payload** to a Make.com webhook
2. Make.com parses the incoming request
3. A **dynamic SQL query** is constructed
4. The query is executed against the SQL database
5. Query results are formatted into **JSON**
6. The JSON response is sent back to Retell AI
7. Retell AI converts the response into **natural spoken output**

</details>

<p align="center">
<img width="1737" height="461" alt="Make.com automation workflow" src="https://github.com/user-attachments/assets/65f3dc31-4b23-4e6c-b609-58f6cce8773d" />
</p>

---

## 🗄️ SQL Database Integration

- Uses **dynamic SQL queries**
- Query parameters are filled using **user input**
- Keeps structured data **separate from the LLM**
- Enables **accurate, controlled, and deterministic responses**

<p align="center">
<img width="926" height="435" alt="SQL database integration" src="https://github.com/user-attachments/assets/66adfc02-9832-4fea-b149-219c07448e56" />
</p>

---

## 📩 POKU Labs – Human-in-the-Loop Integration

Before the call ends:

- **POKU Labs** is triggered mid-conversation
- Collects the caller's **email address**
- Works as a **human-in-the-loop system**
- Integrated with Retell AI using **MCP**
- Data is passed instantly **without breaking conversation flow**

---

## 📝 Post-Call Detailed Summary Function

After the call ends, a custom function generates a **detailed call summary**.

<details>
<summary><strong>What's in the summary</strong></summary>

- Caller phone number
- Caller email address
- Conversation summary
- User questions
- Meeting date & time (if scheduled)

</details>

**Output format:** Structured JSON → sent via **Make.com email automation** → can be stored in a **database** if required.

---

## 🔐 System Design Principles

- Tool-based reasoning to **avoid hallucinations**
- **Knowledge-first, database-second** approach
- Clear separation of **AI, automation, and data layers**
- Scalable design for **complex voice agents**
- **Cost-aware** testing setup

---

## ✨ Key Highlights

| | |
|---|---|
| ✅ | AI-powered inbound voice agent |
| ✅ | Answers questions about my **resume & projects** |
| ✅ | Collects **email address** during the call |
| ✅ | Schedules **meetings live on call** |
| ✅ | Sends **detailed call summary via email** |
| ✅ | Integrated **Cal.com** for live calendar availability checks, meeting booking, rescheduling & cancellation |

---

## 🧠 Tech Stack & Integrations

| Component | Role |
|---|---|
| **Retell AI** | Voice agent & conversation logic |
| **Twilio** | Inbound calling number |
| **Knowledge Hub** | Resume & professional data |
| **POKU Labs** | Mid-call data collection (email, meeting info) |
| **Make.com** | Automation, SQL DB & email workflows |
| **Cal.com** | Calendar availability, meeting booking, rescheduling & cancellation |

---

## 🚀 Why This Project?

This voice agent acts as a:

- 🤖 Personal AI assistant
- 📄 Voice-based professional portfolio
- 📅 Meeting scheduler
- 📊 Lead & interaction tracker

---

## ⚠️ Note

This is a **demo/testing voice agent**.
Usage is limited due to operational costs, but the architecture supports **complex, scalable voice agents**.

---

## ☎️ Try It Now

Call the number and ask about:

- My professional experience
- My projects
- My education
- Scheduling a meeting

<div align="center">

### 📞 **+1 (361) 282-4535**

</div>
