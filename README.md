# # 🎙️ Aneesh AI Voice Assistant
Aneesh AI Voice Assistant is a test voice agent built with Retell AI and Twilio for inbound calls. It answers questions about my professional profile, collects emails, schedules meetings, and sends call summaries using POKU Labs and Make.com integrations. An AI-powered **voice assistant** built for inbound calls that can talk about my professional background, projects, and experience — and even schedule meetings in real time.


📞 **Call the Aneesh AI Assistant:**  
### **+1 (361) 282-4535**
*(Test number · limited usage)*

---

## 🧠 Technical Architecture & Integration Details

This voice agent is built using a tool-based, decision-driven architecture where the AI dynamically chooses how to fetch and respond with information during a live call.

---

## 🎯 Retell AI Agent Prompt Design

The Retell AI agent is configured using a structured system prompt that defines **objective, tone, behavior, and tool usage**.

### Agent Objective
- Act as a professional AI voice assistant
- Answer questions about my:
  - Professional summary
  - Projects
  - Education
  - Experience
- Allow callers to schedule meetings
- Collect user email politely during the call

### Tone Prompt
- Friendly, calm, and professional
- Human-like conversational flow
- Clear, concise, and confident
- Polite handling of unknown queries

### Speech Prompt
- Natural pacing and pauses
- No robotic responses
- Clear pronunciation
- Context-aware follow-up questions



---


<img width="913" height="645" alt="Screenshot 2026-01-10 192528" src="https://github.com/user-attachments/assets/57aaf712-fe4f-4263-9faf-2618743af15a" />



---
## 🧠 Knowledge Retrieval Logic

During a conversation, the agent follows this logic:

1. **Search Knowledge Hub**
   - Resume data
   - Professional experience
   - Projects
   - Education

2. **If information is NOT found**
   - Call a custom tool named `information`
   - Fetch data from external systems (SQL database)

---

## 🔧 Custom Tool: `information`

The `information` tool is a backend function defined in Retell AI.

### Tool Configuration
- **Tool Name:** `information`
- **Purpose:** Fetch structured or external data
- **Trigger Condition:** Knowledge Hub miss
- **Integration:** Webhook via Make.com
- **Input / Output:** JSON

### Example Tool Input (JSON)

'''{
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
          "link": {
            "type": "string"
          },
          "warranty": {
            "type": "string"
          },
          "delivery_days": {
            "type": "string"
          },
          "main_features": {
            "type": "string"
          },
          "product_name": {
            "type": "string"
          },
          "price": {
            "type": "string"
          }
        },
        "required": [
          "product_name",
          "price",
          "main_features"
        ]
      }
    }
  },
  "required": [
    "email",
    "products"
  ]
}'''

---

## 🔄 Make.com Automation Workflow

When the `information` tool is called:

1. Retell AI sends a **JSON payload** to a Make.com webhook  
2. Make.com parses the incoming request  
3. A **dynamic SQL query** is constructed  
4. The query is executed against the SQL database  
5. Query results are formatted into **JSON**  
6. The JSON response is sent back to Retell AI  
7. Retell AI converts the response into **natural spoken output**


<img width="1737" height="461" alt="Screenshot 2026-01-10 215242" src="https://github.com/user-attachments/assets/65f3dc31-4b23-4e6c-b609-58f6cce8773d" />

---

## 🗄️ SQL Database Integration

- Uses **dynamic SQL queries**
- Query parameters are filled using **user input**
- Keeps structured data **separate from the LLM**
- Enables **accurate, controlled, and deterministic responses**



<img width="926" height="435" alt="Screenshot 2026-01-10 192605" src="https://github.com/user-attachments/assets/66adfc02-9832-4fea-b149-219c07448e56" />



---

## 📩 POKU Labs – Human-in-the-Loop Integration

Before the call ends:

- **PKU Labs** is triggered mid-conversation  
- Collects the caller’s **email address**  
- Works as a **human-in-the-loop system**  
- Integrated with Retell AI using **MCP**  
- Data is passed instantly **without breaking conversation flow**

---

## 📝 Post-Call Detailed Summary Function

After the call ends, a custom function generates a **detailed call summary**.

### Summary Includes
- Caller phone number  
- Caller email address  
- Conversation summary  
- User questions  
- Meeting date & time (if scheduled)

### Output Format
- **Structured JSON**
- Sent via **Make.com email automation**
- Can be stored in a **database** if required

---

## 🔐 System Design Principles

- Tool-based reasoning to **avoid hallucinations**
- **Knowledge-first, database-second** approach
- Clear separation of **AI, automation, and data layers**
- Scalable design for **complex voice agents**
- **Cost-aware** testing setup

---

## ✨ Key Highlights

✅ AI-powered inbound voice agent  
✅ Answers questions about my **resume & projects**  
✅ Collects **email address** during the call  
✅ Schedules **meetings live on call**  
✅ Sends **detailed call summary via email**
✅ Integrated **Cal.com** for live calendar availability checks, meeting booking, rescheduling & cancellation

---

## 🧠 Tech Stack & Integrations

- **Retell AI** – Voice agent & conversation logic  
- **Twilio** – Inbound calling number  
- **Knowledge Hub** – Resume & professional data  
- **PKU Labs** – Mid-call data collection (email, meeting info)  
- **Make.com** – Automation, SQL DB & email workflows
- **Cal.com** – Calendar availability, meeting booking, rescheduling & cancellation

---

## 📩 Call Summary Includes

After each call, an automated email is generated with:
- Caller questions & intent
- Collected email address
- Scheduled meeting **date & time**
- Complete conversation summary

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

📞 **+1 (361) 282-4535**
