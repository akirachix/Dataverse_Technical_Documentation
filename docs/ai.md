# MyKira AI Chatbot - Technical Documentation

## 1. Overview

### What Is the MyKira AI Chatbot?

The **MyKira AI Chatbot** is a smart assistant built into the MyKira digital health platform. It helps pregnant women in Zambia get answers to their health questions through a simple chat interface on their phones.

Think of it like a very knowledgeable pregnancy advisor that is available 24/7. When a user asks a question like "What foods should I avoid during pregnancy?" the chatbot reads through a collection of trusted medical documents, finds the relevant information, and gives a clear, helpful answer.

### What Makes It Special?

| Feature | What It Does | Why It Matters |
|---------|-------------|----------------|
| **Conversational Q&A** | Users can ask pregnancy-related questions in plain language | Makes health information accessible anytime, anywhere |
| **Conversation History** | Every chat is saved so users can look back at previous answers | Users don't have to remember everything; doctors can review past conversations |
| **Risk Correlation** | Chat messages can be linked to specific health risk assessments | If a user has a high-risk pregnancy, the chatbot can give more relevant advice |
| **Graceful Degradation** | If the AI system goes down, users get a helpful fallback message instead of an error | Users are never left confused or worried when technology fails |
| **Privacy & Security** | Users can only see their own chats; admins can help when needed | Protects sensitive health information |

### How It Works in Simple Terms

1. A pregnant woman opens the MyKira app and types a question
2. The app sends the question to our server
3. The server checks that she is who she says she is (login verification)
4. The server asks Google's Gemini AI to answer the question, but only after showing it a collection of trusted pregnancy health documents
5. The AI writes an answer based only on those trusted documents
6. The answer is saved in the database and sent back to the woman's phone
7. She can read it immediately, and it stays in her chat history for later

---

## 2. Models Used

### 2.1 The Brain: Generative Model (Gemini)

The chatbot's "brain" is a powerful AI model called **Gemini**, made by Google. We specifically use a version called `gemini-3-flash-preview-001`.

**What is a generative model?**
Think of it like a very advanced autocomplete. Just like your phone predicts the next word when you type a text message, Gemini predicts the next sentence when answering a question. But instead of just one word, it generates entire paragraphs that make sense and are helpful.

**Key Details:**

| Attribute | Explanation |
|-----------|-------------|
| **Model Name** | `gemini-3-flash-preview-001` - This is the specific version of Google's AI we use. We can change this by updating an environment variable called `GEMINI_MODEL_NAME`. |
| **Provider** | Google GenAI (Gemini API) - This is the service from Google that hosts the AI model. |
| **Client** | `google.genai.Client` - This is the software tool we use to talk to Google's servers. |
| **Context Window** | This is how much text the AI can "read" at once. Gemini can read very long documents, which is why we can give it entire medical files to reference. |

**Why Gemini?**
- It understands natural language very well (people can ask questions however they want)
- It can read and understand long documents
- It is fast enough for real-time chat
- It is cost-effective for a health startup

### 2.2 The Library: Knowledge Base

The chatbot doesn't just make up answers from what it learned during training. Instead, it reads from a special **knowledge base** - a collection of trusted pregnancy health documents.

**What is the knowledge base?**
Imagine a digital library folder on our server called `markdown_knowledge/`. Inside this folder are text files written in Markdown format (a simple way to format text). Each file contains trusted information about pregnancy health topics.

**How it works:**

| Step | What Happens |
|------|-------------|
| 1. **Storage** | All medical documents are saved as `.md` files in the `markdown_knowledge/` folder |
| 2. **Upload** | When the system starts, it uploads these files to Google's Gemini service using something called the "Files API" |
| 3. **Registry** | We keep a list called `gemini_registry.json` that remembers which files have been uploaded and where they are on Google's servers |
| 4. **Smart Updates** | When files change, the system only uploads the new or changed files - not everything again |
| 5. **Cleanup** | If a file is deleted from our folder, the system removes it from the registry too |

**Why a knowledge base matters:**
Without it, the AI might give outdated or incorrect medical advice based on what it learned years ago. With the knowledge base, we control exactly what information the AI uses. We can update it anytime by simply editing or adding text files.

### 2.3 Embeddings and Vector Search

**What are embeddings?**
Imagine you could turn a sentence into a list of numbers that captures its meaning. Two sentences about similar topics would have similar numbers. This is what an embedding model does — it turns text into numbers.

**Are we using them?**
**No, not yet.** Right now, we give the AI the entire documents to read. This is simpler but has limits:
- As we add more documents, the AI might not have enough "attention" to read them all at once
- It costs more because we send more text with every question
- It might be slower

**In the future**, we might use embeddings to:
1. Turn all our documents into number lists
2. When a user asks a question, turn their question into a number list too
3. Find which document sections have the most similar numbers
4. Only send those relevant sections to the AI, not everything

This is called **vector search** and would make the system faster and cheaper.

---

## 3. Training Approach

### 3.1 RAG: Teaching the AI to Read Our Books

**RAG stands for Retrieval-Augmented Generation.** Let's break that down:

- **Retrieval** = Finding the right information
- **Augmented** = Enhanced or improved
- **Generation** = Creating an answer

**In plain English:** Instead of the AI answering from memory, we teach it to first look up the right information in our documents, then use that information to write an answer.

**How RAG works in our system:**

```
User asks: "What vitamins do I need?"
         │
         ▼
System gives Gemini:
  - System instructions (rules for how to behave)
  - All knowledge base files (our trusted medical documents)
  - The user's question
         │
         ▼
Gemini reads the documents
finds information about vitamins
         │
         ▼
Gemini writes an answer
based ONLY on what it read
         │
         ▼
User gets: "During pregnancy, folic acid and iron are 
            especially important. Folic acid helps prevent 
            birth defects..."
```

**Why we chose RAG instead of Fine-Tuning:**

| Approach | What It Is | Why We Didn't Choose It (or Why We Did) |
|----------|-----------|----------------------------------------|
| **RAG (What we use)** | The AI reads our documents every time it answers | We can update medical info instantly by editing text files.  We know exactly what information the AI used.  Cheaper - no training costs.  Safer - the AI can't make up things outside our documents. |
| **Fine-Tuning** | Retraining the AI model itself on our data | Expensive - costs thousands of dollars and takes hours.  Slow to update - changing one sentence means retraining everything.  Hard to control - the model might still say things we don't want. |

### 3.2 Fine-Tuning

**What is fine-tuning?**
Fine-tuning is like sending the AI to a special school where it studies only your materials for weeks. Afterward, it "knows" that information deeply.

**Are we doing it?**
**No.** We are not fine-tuning the Gemini model. We use it "out of the box" but give it our documents to read each time. This is like letting a smart doctor use a medical textbook during an exam rather than memorizing the entire book.

---

## 4. Data Pipeline

### 4.1 The Journey of a Message

Here is exactly what happens when a user sends a message, step by step:

```
┌─────────────┐
│   User      │  1. Opens app and types:
│  (Phone)    │     "Is it safe to exercise?"
└──────┬──────┘
       │
       ▼
┌──────────────────┐
│  FastAPI Router  │  2. Receives the message at:
│  POST endpoint   │     /ai-responses/{user_id}
└────────┬─────────┘
         │
         ▼
┌─────────────────┐
│  Auth Check     │  3. Verifies:
│  (Security)     │     - Is this user logged in?
│                 │     - Is she trying to access her OWN data?
│                 │     - Or is an admin helping her?
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Service Layer   │  4. The "manager" that coordinates everything
│ send_message()  │     - It doesn't do the work itself
│                 │     - It tells other parts what to do
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
┌────────┐  ┌─────────────┐
│ Gemini │  │  Knowledge  │  5. The service asks:
│  API   │  │   Loader    │     - "Get me the latest medical documents"
│        │  │             │     - "Send them to Gemini with the user's question"
└───┬────┘  └─────────────┘
    │
    ▼
┌──────────┐
│  Gemini  │  6. Gemini reads the documents and the question,
│  thinks  │     then writes a helpful answer
└────┬─────┘
     │
     ▼
┌──────────┐
│  Answer  │  7. If Gemini fails or is slow, we send a
│  Text    │     friendly fallback message instead
└────┬─────┘
     │
     ▼
┌─────────────────┐
│   PostgreSQL    │  8. We save everything:
│   Database      │     - The user's original question
│                 │     - The AI's answer
│                 │     - The exact time it happened
│                 │     - Which user asked
│                 │     - Any linked health risk assessment
└────────┬────────┘
         │
         ▼
┌──────────────────┐
│  Response Sent   │  9. The answer goes back to the user's phone
│  to User's App   │     in a clean, structured format
└──────────────────┘
```

### 4.2 Detailed Step Breakdown

**Step 1: The User Sends a Message**
- The user opens the MyKira mobile app
- She types her question and taps send
- The app sends an HTTP POST request to our server
- The request includes her user ID and her message

**Step 2: The Router Receives the Request**
- Our server uses FastAPI (a Python web framework)
- The router is like a receptionist - it knows which "department" should handle each request
- For chat messages, it routes to the `/ai-responses/{user_id}` endpoint
- It also checks that the data is properly formatted using Pydantic schemas

**Step 3: Security Check**
- Before doing anything, we verify the user's identity
- We check their JWT token (like a digital ID card)
- We make sure they can only access their own conversations
- Admins can access any conversation to help with support

**Step 4: The Service Layer Orchestrates**
- The service layer is like a project manager
- It doesn't do the actual work but coordinates who does what
- It calls the AI generation service and the database repository

**Step 5: Loading Knowledge and Generating a Reply**
- The system checks if our medical documents are already uploaded to Google's servers
- If not, it uploads them
- It sends the documents + the user's question to Gemini
- Gemini reads everything and writes an answer

**Step 6: Fallback Protection**
- Sometimes Google servers are busy or down
- If Gemini doesn't respond within a reasonable time, or returns nothing, we don't crash
- Instead, we send a pre-written friendly message:
  > "I'm having trouble connecting right now. Please try again in a moment. If this is urgent, please contact your clinic directly."
- This ensures users are never left hanging

**Step 7: Saving to the Database**
- Everything is stored in PostgreSQL, a reliable database
- We save:
  - `ai_response_id`: A unique ID for this specific message pair
  - `user_id`: Who asked the question
  - `risk_id`: If this is related to a specific health risk assessment (optional)
  - `user_message`: What the user asked
  - `ai_response`: What the AI answered
  - `response_time`: The exact timestamp

**Step 8: Sending the Response**
- The answer is formatted into a clean JSON structure
- It includes all the details the app needs to display the chat nicely
- The response is sent back to the user's phone

### 4.3 Getting Conversation History

When a user wants to see her past chats:

1. She opens the chat history screen in the app
2. The app sends a GET request to `/ai-responses/user/{user_id}`
3. We verify her identity again
4. We query the database for all messages where `user_id` matches
5. We sort them by time (oldest first) so the conversation reads naturally
6. We send the full list back to her phone

### 4.4 How the Knowledge Base Stays Updated

**The Knowledge File Lifecycle:**

```
[Our Server]
    │
    ├── markdown_knowledge/
    │   ├── nutrition.md
    │   ├── exercise.md
    │   ├── warning_signs.md
    │   └── ...
    │
    └── gemini_registry.json (keeps track of uploads)

When the system starts:
    │
    ▼
1. Check each .md file
    │
    ├── Is it in the registry? (Have we uploaded it before?)
    │   ├── YES → Check if Google still has it
    │   │   ├── YES → Use the existing copy
    │   │   └── NO  → Upload it again
    │   └── NO  → Upload it for the first time
    │
2. Update the registry with new file references
    │
3. Remove entries for deleted files
    │
4. Return the list of active files to the chat service
```

**Why this matters:**
- We don't waste time uploading the same files over and over
- If Google accidentally deletes a file, we detect it and re-upload
- If we delete a file from our server, we clean up the registry
- This keeps costs down and ensures the AI always has the latest information

---

## 5. Integration

### 5.1 How the Mobile App Talks to Our Server

**The Interface (API):**

| Aspect | Details | Simple Explanation |
|--------|---------|-------------------|
| **Protocol** | REST/HTTP | The standard way apps talk to servers over the internet |
| **Framework** | FastAPI | A modern, fast Python tool for building APIs |
| **Data Format** | JSON | A clean, readable way to structure data that both computers and humans understand |
| **Create Request** | `AIResponseCreate` | The "template" for what data must be included when sending a new message |
| **Read Response** | `AIResponseRead` | The "template" for what data comes back when you get a message or history |

**What the user sends (Create):**
```json
{
  "risk_id": "optional-uuid-here",
  "user_message": "What foods should I avoid?"
}
```

**What the user receives (Read):**
```json
{
  "ai_response_id": "123e4567-e89b-12d3-a456-426614174000",
  "risk_id": null,
  "user_id": "123e4567-e89b-12d3-a456-426614174001",
  "user_message": "What foods should I avoid?",
  "ai_response": "During pregnancy, avoid unpasteurized dairy...",
  "response_time": "2026-08-28T14:30:00+03:00"
}
```

**Input Rules:**
- `user_message`: Optional, but if provided, maximum 2,000 characters (about one page of text)
- `risk_id`: Optional. If included, it links this chat to a specific health assessment
- `user_id`: Comes from the web address (URL), not the message body, for security

### 5.2 Security and Authorization

**How We Protect User Data:**

| Layer | What It Does | Analogy |
|-------|-------------|---------|
| **Authentication** | `get_current_user` checks the JWT token | Like checking a driver's license to prove who you are |
| **Authorization** | `require_self_or_admin` checks permissions | Like checking if your name is on the guest list |
| **Enforcement** | Applied to every chat endpoint | Like a bouncer at every door, not just the front one |

**The Security Rules:**
1. **Users can only see their own chats** — This is like having a private diary that no one else can read
2. **Admins can help anyone** — Support staff can access conversations to help users who are having trouble
3. **No one else gets in** — Not even other users of the app

### 5.3 Data Privacy and Anonymization

**Current State:**
Right now, we store messages as plain text. This means:
- If someone has database access, they can read what users asked
- We don't automatically remove names, phone numbers, or other personal details from messages

**What we store:**

| Data | How It's Stored | Privacy Level |
|------|----------------|---------------|
| `user_id` | UUID (random numbers/letters) | Safe - doesn't reveal identity by itself |
| `user_message` | Plain text in database |  Could contain personal info |
| `ai_response` | Plain text in database |  Generally safe (it's our content) |
| `risk_id` | UUID linking to health records | Sensitive - connects chat to medical data |

**Data Deletion:**
- If a user deletes her account, all her chat history is automatically deleted (`ondelete="CASCADE"`)
- If a risk assessment is deleted, linked chats are also cleaned up
- Timestamps are set by the server, not the user's phone, so they can't be faked

**What we should improve:**
- Add automatic detection and removal of personal information (names, phone numbers, addresses) from messages before storing them
- This is called **PII redaction** and is very important for health apps

---

## 6. Evaluation Method

### 6.1 How Do We Know the Chatbot Is Good?

**Right now:** We don't have an automatic system to check quality. We need to build one.

**Why evaluation matters:**
Imagine a doctor who never gets feedback on whether their advice helped patients. They might keep making the same mistakes. We need to regularly check if our chatbot is:
- Giving correct information
- Being safe (not suggesting dangerous things)
- Being helpful (actually answering the question)
- Being fast (not making users wait too long)

### 6.2 The Evaluation Framework We Should Build

| What We Check | How We Check It | What "Good" Looks Like |
|--------------|----------------|----------------------|
| **Safety** | Have real doctors review random chatbot answers | 100% pass rate — never suggests medication dosages or makes definitive diagnoses |
| **Groundedness** | Use another AI to check if the answer came from our documents | 90%+ of claims should be traceable to our knowledge base |
| **Helpfulness** | Ask users to rate answers (thumbs up/down) | 80%+ positive ratings |
| **Speed** | Measure how long each response takes | Most answers under 3 seconds |
| **Reliability** | Count how often we have to use the fallback message | Less than 2% of the time |
| **Medical Accuracy** | Have Zambian obstetricians (pregnancy doctors) review answers | High accuracy score against real clinical guidelines |

### 6.3 How to Implement This

1. **Add a feedback button** - After each answer, let users tap 👍 or 👎
2. **Better logging** - Instead of just printing errors to the screen, save them properly so we can analyze patterns
3. **Regular safety tests** - Every month, have someone try to "trick" the chatbot into giving dangerous advice. If it refuses, it passes.
4. **Red teaming** - Hire experts to deliberately ask tricky or harmful questions to find weaknesses before real users do

---

## 7. Accuracy Results

### 7.1 Current Status

We don't have formal accuracy measurements yet. This is normal for a new system, but we need to start measuring soon.

### 7.2 Targets We Should Aim For

| Metric | Target | What It Means |
|--------|--------|--------------|
| **Response Grounding** | 90%+ | When the chatbot says something, it should be able to point to which document it came from |
| **Safety Compliance** | 100% | Zero tolerance for giving specific medication doses or saying "you definitely have X disease" |
| **User Satisfaction** | 80%+ | Most users find the answers helpful |
| **System Uptime** | 99.5%+ | The chatbot should work almost all the time |
| **Fallback Rate** | Under 2% | Only rarely should we have to say "I'm having trouble" |
| **Response Speed** | Under 3 seconds (95% of the time) | Users shouldn't have to wait long |

### 7.3 How to Measure These

- **Grounding**: Have a human or AI reviewer check 100 random answers against the knowledge base
- **Safety**: Automated tests that ask for dosages and diagnoses; the chatbot should refuse
- **Satisfaction**: In-app feedback collection after every 5th message or so
- **Uptime**: Monitoring tools that ping our server every minute
- **Fallback Rate**: Log every time the fallback message is triggered
- **Speed**: Track the time from "user sends message" to "answer appears on screen"

---

## 8. Known Limitations

### 8.1 System Design Limits

| Limitation | What It Means | Why It's a Problem | How Bad Is It |
|------------|--------------|-------------------|---------------|
| **No Conversation Memory** | The AI treats every message as if it's the first one ever | If a user asks "What about exercise?" after asking about diet, the AI doesn't know they mean "exercise during pregnancy" | 🔴 High - Makes chat feel robotic |
| **No Smart Search** | We send ALL documents to the AI every time | As we add more documents, the AI might get overwhelmed or slow | 🟡 Medium - Will become a problem as we grow |
| **Only One AI Provider** | If Google goes down, we have no backup | Users get fallback messages instead of answers | 🔴 High - Single point of failure |
| **No Caching** | Even if 100 people ask the same question, we call Google 100 times | Wastes money and slows things down | 🟡 Medium - Costs more than needed |
| **Synchronous Processing** | The server waits for Google to respond before doing anything else | If Google is slow, our server can't help other users | 🔴 High - Can cause traffic jams |

### 8.2 Safety and Privacy Limits

| Limitation | What It Means | Why It's a Problem | How Bad Is It |
|------------|--------------|-------------------|---------------|
| **No Personal Info Filtering** | If a user types "My name is Mary and I live on Main Street," we save that exactly | Health data + personal info = very sensitive. If hackers get in, they know who said what | 🔴 Critical - Must fix soon |
| **No Rate Limiting** | A user (or hacker) could send 1,000 messages per minute | Costs us a fortune in API fees; could crash the system | 🔴 High - Financial risk |
| **No Content Filtering** | Someone could send hate speech or try to trick the AI | Bad user experience; potential legal issues | 🟡 Medium - Should address |
| **Fixed Instructions** | The AI always behaves exactly the same way | We can't easily test different approaches to see what works better | 🟢 Low - Nice to have, not urgent |

### 8.3 Operational Limits

| Limitation | What It Means | Why It's a Problem | How Bad Is It |
|------------|--------------|-------------------|---------------|
| **Files Must Be on Our Server** | To update medical info, someone must edit files on the computer running the app | Non-technical staff can't update info; requires developer time | 🟡 Medium - Operational bottleneck |
| **Registry Lives on One Server** | The file that tracks uploads is stored locally | If we have multiple servers, they might get confused about what's uploaded | 🔴 High - Blocks scaling |
| **Weak Retry Logic** | If Google says "404 Not Found," we try once more. But if Google says "I'm too busy" (429) or "Error" (500), we give up immediately | Temporary problems become permanent failures for users | 🟡 Medium - Annoying for users |
| **Poor Logging** | Error messages just print to the console | When something breaks, we can't easily find out why or see patterns | 🟡 Medium - Makes debugging hard |

---

## 9. Future Improvements

### 9.1 Short-Term (Next 3 Months)

These are the most important things to fix first:

| Improvement | What It Means | Why It Matters | Priority |
|-------------|--------------|---------------|----------|
| **Conversation Memory** | Let the AI remember the last 5-10 messages in a chat | Users can have natural back-and-forth conversations | P0 - Most requested feature |
| **Async Processing** | Don't make the server wait for Google. Say "I'll check" and come back later | Server can handle many users at once without slowing down | P0 - Critical for scaling |
| **Personal Info Filtering** | Automatically detect and remove names, addresses, phone numbers from stored messages | Protects user privacy; required by health data laws | P0 - Legal requirement |
| **Rate Limiting** | Limit users to, say, 20 messages per hour | Prevents abuse and controls costs | P1 - Important for budget |
| **Better Logging** | Save errors in a proper system (like Sentry or CloudWatch) instead of just printing them | We can actually fix problems when they happen | P1 - Essential for maintenance |

### 9.2 Medium-Term (3–6 Months)

Once the urgent things are fixed:

| Improvement | What It Means | Why It Matters | Priority |
|-------------|--------------|---------------|----------|
| **Smart Document Search** | Use embeddings to find only the most relevant document sections, not all documents | Faster, cheaper, and more accurate answers | P1 - Major cost savings |
| **Backup AI Providers** | If Google fails, automatically try OpenAI, Azure, or others | Users always get answers, even during outages | P1 - Reliability boost |
| **Answer Caching** | Save answers to common questions so we don't call Google every time | Dramatically reduces API costs | P2 - Nice cost savings |
| **A/B Testing** | Try different AI instructions for different users to see what works best | We can scientifically improve the chatbot | P2 - Data-driven improvement |
| **User Feedback** | Add thumbs up/down buttons after answers | We know what's working and what isn't | P2 - Direct user input |

### 9.3 Long-Term (6–12 Months)

Big vision items:

| Improvement | What It Means | Why It Matters | Priority |
|-------------|--------------|---------------|----------|
| **Safety Pre-Checker** | A small AI that reads Gemini's answers BEFORE sending them to users, checking for policy violations | Extra safety net for medical advice | P2 - Peace of mind |
| **Multiple Languages** | Support Nyanja, Bemba, and other Zambian languages, not just English | Reaches more mothers who don't speak English fluently | P3 - Inclusion and reach |
| **Automatic Testing** | Every night, automatically test 100 questions and alert us if accuracy drops | Catches problems before users do | P3 - Proactive quality |
| **Content Management System** | A simple web page where doctors can upload/update medical info without needing a developer | Faster updates, less technical dependency | P3 - Operational efficiency |
| **FHIR Integration** | Connect our `risk_id` to the international standard for health data (FHIR) | Allows sharing data with hospitals and other systems securely | P3 - Interoperability |

---

## Appendix: Database Schema

### The Table Structure

This is how the chat history is stored in our PostgreSQL database:

```sql
CREATE TABLE ai_responses (
    -- A unique ID for every single message pair (question + answer)
    ai_response_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    -- Who asked the question (links to the users table)
    user_id UUID NOT NULL REFERENCES users(user_id) ON DELETE CASCADE,

    -- Optional: links to a specific health risk assessment
    risk_id UUID REFERENCES risk_evaluations(risk_id) ON DELETE CASCADE,

    -- What the user asked (can be empty if they just open the chat)
    user_message TEXT,

    -- What the AI answered (must always have something)
    ai_response TEXT NOT NULL,

    -- Exact time the answer was generated (set automatically by the server)
    response_time TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**What each part means:**
- `UUID`: A random string like `123e4567-e89b-12d3-a456-426614174000` that uniquely identifies something
- `PRIMARY KEY`: The main way to find a specific row
- `NOT NULL`: This field MUST have a value - it can't be empty
- `REFERENCES`: This creates a link to another table
- `ON DELETE CASCADE`: If the linked user or risk assessment is deleted, automatically delete this chat too
- `DEFAULT gen_random_uuid()`: If no ID is provided, make one up automatically
- `DEFAULT NOW()`: If no time is provided, use the current server time

### Recommended Speed Improvements (Indexes)

Without indexes, the database has to read every single row to find what it needs. Indexes are like the index at the back of a book - they make finding things much faster.

```sql
-- Find all chats for a specific user quickly
CREATE INDEX idx_ai_responses_user_id ON ai_responses(user_id);

-- Sort chats by time quickly (for showing conversation history)
CREATE INDEX idx_ai_responses_response_time ON ai_responses(response_time);

-- Find chats linked to a specific risk assessment quickly
CREATE INDEX idx_ai_responses_risk_id ON ai_responses(risk_id) WHERE risk_id IS NOT NULL;
```

---


