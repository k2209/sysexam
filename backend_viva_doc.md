# 🎓 Backend Viva Q&A — AI Voice Agent Platform

> **Project**: AI Voice Agent Platform (College Project)  
> **Backend Stack**: Python · FastAPI · MongoDB (Motor) · JWT · Sarvam AI · Gemini / Groq  
> **Command to run**: `cd backend && source venv/bin/activate && uvicorn main:app --reload`  
> **Server URL**: `http://localhost:8000`  
> **Docs URL**: `http://localhost:8000/docs` (Swagger auto-generated)

---

## 📌 Section 1 — Project Overview

### Q1. What is this project about?
**A.** This is an **AI Voice Agent Platform** where:
- **Users** can register, browse AI agents, and have **real-time voice conversations** with them.
- **Admins** can create/manage AI agents, manage users, and view conversation analytics.
- The system uses **Speech-to-Text (STT)**, **Large Language Models (LLMs)**, and **Text-to-Speech (TTS)** to enable full duplex voice chat.

### Q2. What is the tech stack used in the backend?
**A.**
| Layer | Technology |
|-------|-----------|
| Framework | **FastAPI** (Python) — modern, async web framework |
| Database | **MongoDB** (NoSQL) via **Motor** (async driver) |
| Authentication | **JWT** (JSON Web Tokens) via `python-jose` |
| Password Hashing | **bcrypt** via `passlib` |
| AI / LLM | **Google Gemini** (`google-genai` SDK) + **Groq** (LLaMA 3.3 70B) |
| Voice — STT | **Sarvam AI Saaras v3** (Speech-to-Text) |
| Voice — TTS | **Sarvam AI Bulbul v3** (Text-to-Speech) |
| Real-time | **WebSockets** (FastAPI native) |
| Email | **SMTP** (Gmail) for OTP delivery |
| Image Gen | **Google Imagen 4** for AI avatar generation |
| Server | **Uvicorn** (ASGI server) |

### Q3. Why did you choose FastAPI over Flask or Django?
**A.** FastAPI was chosen because:
1. **Async-first** — perfect for I/O-heavy operations (DB queries, external AI API calls, WebSockets).
2. **Automatic Swagger docs** — accessible at `/docs`, great for development and testing.
3. **Built-in WebSocket support** — essential for our real-time voice chat feature.
4. **Pydantic validation** — automatic request/response schema validation.
5. **High performance** — one of the fastest Python web frameworks.

### Q4. Why MongoDB instead of MySQL/PostgreSQL?
**A.** MongoDB was chosen because:
1. **Flexible schema** — agents, conversations, messages can have varying fields without migrations.
2. **Document model** — conversations with nested messages map naturally to MongoDB documents.
3. **TTL Indexes** — we use a TTL index on OTPs that auto-deletes expired OTPs (no cron job needed).
4. **Async driver (Motor)** — integrates perfectly with FastAPI's async architecture.

---

## 📌 Section 2 — Project Structure

### Q5. Explain the folder structure of the backend.

**A.**
```
backend/
├── main.py            → App entry point, CORS config, router mounting
├── config.py          → All environment variables (DB, JWT, SMTP, AI keys)
├── database.py        → MongoDB connection (Motor async client)
├── requirements.txt   → Python dependencies
│
├── models/            → Data models (document builders + helpers)
│   ├── user.py        → User document creation & parsing
│   ├── admin.py       → Admin document creation & parsing
│   ├── agent.py       → AI Agent document creation & parsing
│   ├── category.py    → Agent category document
│   └── conversation.py → Conversation + message document
│
├── schemas/           → Pydantic schemas (request/response validation)
│   ├── auth.py        → Register, Login, OTP, Password schemas
│   ├── user.py        → User profile CRUD schemas
│   ├── admin.py       → Admin dashboard, user management schemas
│   ├── agent.py       → Agent CRUD schemas
│   └── conversation.py → Conversation list & detail schemas
│
├── routes/            → API route handlers (controllers)
│   ├── auth.py        → Register, Login, Forgot/Reset Password
│   ├── user.py        → User profile CRUD, dashboard stats
│   ├── admin.py       → Admin login, user management, dashboard
│   ├── agent.py       → Admin CRUD for agents & categories
│   ├── agents_public.py → User-facing agent listing
│   ├── chat.py        → WebSocket voice chat
│   ├── generate.py    → AI-powered agent config & image generation
│   └── history.py     → User conversation history
│
├── services/          → External service integrations
│   ├── llm.py         → Gemini + Groq LLM wrapper
│   └── sarvam.py      → Sarvam STT + TTS wrapper
│
├── utils/             → Utility functions
│   ├── auth.py        → JWT, password hashing, auth dependencies
│   └── email.py       → OTP generation, SMTP email sending
│
└── uploads/           → Static file storage (profile photos, agent images)
```

### Q6. What are "models", "schemas", "routes", "services", and "utils"?
**A.**
- **Models** (`models/`): Functions that create MongoDB documents and transform raw MongoDB documents into clean Python dicts. They define what data a document contains.
- **Schemas** (`schemas/`): Pydantic classes that validate incoming request data and define the response shape. Think of them as **contracts** for the API.
- **Routes** (`routes/`): The actual API endpoints — they receive requests, call the database/services, and return responses. These are the **controllers**.
- **Services** (`services/`): Wrappers around external APIs (Sarvam for voice, Gemini/Groq for AI). They isolate third-party logic.
- **Utils** (`utils/`): Helper functions used across routes (password hashing, JWT creation, email sending).

---

## 📌 Section 3 — Database & Connection

### Q7. How do you connect to MongoDB?
**A.** In `database.py`, we use **Motor** (async MongoDB driver):
```python
from motor.motor_asyncio import AsyncIOMotorClient

client = AsyncIOMotorClient(settings.MONGODB_URL)
db = client[settings.DB_NAME]  # database name: "college_project"
```
The connection is established in the **app lifespan** (on startup) and closed on shutdown:
```python
@asynccontextmanager
async def lifespan(app: FastAPI):
    await connect_to_mongodb()
    yield
    await disconnect_from_mongodb()
```

### Q8. What indexes are created on startup?
**A.** Three indexes are created:
1. `db.users.create_index("email", unique=True)` — ensures no duplicate user emails.
2. `db.admins.create_index("email", unique=True)` — ensures no duplicate admin emails.
3. `db.otps.create_index("expires_at", expireAfterSeconds=0)` — **TTL index** that automatically deletes OTP documents after their `expires_at` time passes.

### Q9. What is a TTL index?
**A.** TTL (Time-To-Live) index is a special MongoDB index on a date field. MongoDB automatically removes documents once the date passes. We use it on the `otps` collection — expired OTPs are automatically cleaned up without needing a scheduled job.

### Q10. What collections does the database have?
**A.**
| Collection | Purpose |
|------------|---------|
| `users` | Registered end-users |
| `admins` | Admin accounts |
| `agents` | AI agent configurations |
| `categories` | Agent categories (e.g., Education, Support) |
| `conversations` | Voice chat sessions with nested messages |
| `otps` | Temporary OTP codes for password reset (TTL-indexed) |

---

## 📌 Section 4 — Authentication & Security

### Q11. How does user authentication work?
**A.** We use **JWT (JSON Web Token) + bcrypt** authentication:
1. User sends email + password to `POST /api/auth/login`.
2. Server verifies password against the **bcrypt hash** stored in MongoDB.
3. Server creates a **JWT token** containing the user's ID and returns it.
4. For all subsequent requests, the client sends this token in the `Authorization: Bearer <token>` header.
5. Server decodes the token to identify the user on every request.

### Q12. How is the password stored?
**A.** Passwords are **never stored in plain text**. We use **bcrypt** hashing via the `passlib` library:
```python
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(password: str) -> str:
    return pwd_context.hash(password)  # e.g., "$2b$12$..."

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)
```
Bcrypt is a one-way hash — you cannot reverse it to get the original password.

### Q13. What is JWT? How do you create and verify tokens?
**A.** JWT (JSON Web Token) is a compact, self-contained token for secure information transfer. Our token contains:
- `sub` — the user's MongoDB `_id` (subject)
- `role` — "user" or "admin"
- `exp` — expiration time (60 minutes by default)

**Creating a token:**
```python
def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.now(timezone.utc) + timedelta(minutes=60)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm="HS256")
```

**Verifying a token** (used as a FastAPI Dependency):
```python
async def get_current_user(token: str = Depends(oauth2_scheme)):
    payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
    user_id = payload.get("sub")
    # fetch user from DB...
```

### Q14. How do you protect routes? What is `Depends()`?
**A.** FastAPI's **Dependency Injection** system:
```python
@router.get("/me")
async def get_profile(current_user: dict = Depends(get_current_user)):
    return user_helper(current_user)
```
- `Depends(get_current_user)` automatically runs the `get_current_user` function before the route handler.
- It extracts the JWT from the `Authorization` header, decodes it, fetches the user from the DB, and passes it to the route.
- If the token is invalid/expired, it raises a `401 Unauthorized` error automatically.

### Q15. How is admin access restricted?
**A.** We have a two-level dependency chain:
```python
async def get_current_admin(current_user = Depends(get_current_user)):
    if current_user.get("role") != "admin":
        raise HTTPException(status_code=403, detail="Admin access required")
    return current_user
```
Admin-only routes use `Depends(get_current_admin)`, which first runs `get_current_user` (JWT check) and then verifies the role is "admin".

### Q16. What is CORS and why do you configure it?
**A.** CORS (Cross-Origin Resource Sharing) is a security mechanism. Since our frontend runs on `localhost:3000` and backend on `localhost:8000`, they are on different origins. Without CORS, the browser would block frontend requests to the backend.
```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000", "http://127.0.0.1:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## 📌 Section 5 — API Routes

### Q17. List all the API endpoints in the project.
**A.**

**Authentication (`/api/auth`)**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/auth/register` | Register new user |
| POST | `/auth/login` | User login → returns JWT |
| POST | `/auth/forgot-password` | Send OTP to email |
| POST | `/auth/verify-otp` | Verify OTP is valid |
| POST | `/auth/reset-password` | Reset password using OTP |

**User (`/api/users`)**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/users/me` | Get current user's profile |
| PUT | `/users/me` | Update profile (name, email, mobile) |
| POST | `/users/me/photo` | Upload profile photo |
| POST | `/users/me/change-password` | Change password |
| GET | `/users/me/dashboard-stats` | Dashboard statistics |

**Admin (`/api/admin`)**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/admin/register` | Register admin account |
| POST | `/admin/login` | Admin login → JWT |
| POST | `/admin/change-password` | Admin change password |
| GET | `/admin/dashboard/stats` | Dashboard analytics |
| GET | `/admin/users` | List users (paginated, searchable) |
| GET | `/admin/users/{id}` | Get single user |
| PATCH | `/admin/users/{id}/status` | Block/unblock user |
| GET | `/admin/users/{id}/conversations` | User's conversations |
| GET | `/admin/conversations` | All conversations globally |
| GET | `/admin/conversations/{id}` | Conversation detail |

**Agents — Admin (`/api/admin`)**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/admin/agents` | Create new agent (with image upload) |
| GET | `/admin/agents` | List agents (paginated, filterable) |
| GET | `/admin/agents/{id}` | Get single agent |
| PUT | `/admin/agents/{id}` | Update agent |
| PATCH | `/admin/agents/{id}/status` | Pause/activate agent |
| DELETE | `/admin/agents/{id}` | Delete agent |
| GET | `/admin/categories` | List agent categories |
| POST | `/admin/categories` | Create category |

**Agents — User (`/api/agents`)**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/agents` | List active agents (user-facing) |
| GET | `/agents/categories` | List categories |
| GET | `/agents/{id}` | Get agent details |

**AI Generation (`/api/generate`)**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/generate/agent-config` | AI-generate agent config from prompt |
| POST | `/generate/agent-image` | AI-generate agent avatar |

**Chat (WebSocket)**
| Protocol | Endpoint | Description |
|----------|----------|-------------|
| WS | `/api/chat/{agent_id}?token=JWT` | Real-time voice chat |

**Conversation History (`/api/conversations`)**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/conversations` | List user's conversations |
| GET | `/conversations/{id}` | Get conversation with messages |

**Health**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/health` | Health check |

### Q18. How does the registration process work?
**A.**
1. Frontend sends `POST /api/auth/register` with `{ full_name, email, mobile, password }`.
2. Backend checks if email already exists → returns error if duplicate.
3. Backend checks if mobile already exists → returns error if duplicate.
4. Password is hashed using bcrypt.
5. A user document is created with `status: "active"`, timestamps, and inserted into MongoDB.
6. Returns `{ message: "Account created successfully" }`.

### Q19. How does the forgot password / OTP flow work?
**A.** Three-step process:
1. **`POST /auth/forgot-password`** — User enters email → server generates a random 6-digit OTP, stores it in the `otps` collection with a 10-minute expiry, and sends it via email (SMTP/Gmail).
2. **`POST /auth/verify-otp`** — User enters the OTP → server checks if it exists, is unused, and hasn't expired.
3. **`POST /auth/reset-password`** — User enters OTP + new password → server verifies OTP again, hashes the new password, updates the user document, and marks the OTP as used.

**Security measures:**
- OTPs expire in 10 minutes (both checked in code and auto-deleted via TTL index).
- Old unused OTPs are invalidated when a new one is requested.
- The forgot-password endpoint returns the same message whether the email exists or not (prevents email enumeration).

### Q20. How does pagination work?
**A.** We use **cursor-based MongoDB pagination** with `skip()` and `limit()`:
```python
@router.get("/users")
async def list_users(
    page: int = Query(1, ge=1),
    limit: int = Query(10, ge=1, le=100),
    search: str = Query(""),
):
    skip = (page - 1) * limit
    total = await db.users.count_documents(query)
    total_pages = ceil(total / limit)
    cursor = db.users.find(query).sort("created_at", -1).skip(skip).limit(limit)
```
Response includes: `{ users, total, page, limit, total_pages }`.

### Q21. How does search work?
**A.** We use **MongoDB regex** for case-insensitive search:
```python
if search.strip():
    query["$or"] = [
        {"full_name": {"$regex": escaped_search, "$options": "i"}},
        {"email": {"$regex": escaped_search, "$options": "i"}},
    ]
```
The `$options: "i"` flag makes the search case-insensitive. The search string is escaped with `re.escape()` to prevent regex injection.

---

## 📌 Section 6 — Voice Chat (WebSocket)

### Q22. How does the real-time voice chat work?
**A.** The voice chat uses a **WebSocket** connection. Here's the flow:

```
User (Browser) ←→ WebSocket ←→ FastAPI Server ←→ Sarvam AI + Gemini/Groq
```

1. **Connect**: Client opens `ws://localhost:8000/api/chat/{agent_id}?token=JWT`
2. **Authenticate**: Server validates the JWT token from the query parameter.
3. **Load Agent**: Server fetches the agent's system prompt, voice ID, languages from MongoDB.
4. **Create Conversation**: A new conversation document is created in MongoDB.
5. **Message Loop**:
   - Client sends audio (base64) → Server calls **Sarvam STT** → gets text
   - Server sends text to **Gemini/Groq LLM** with the agent's system prompt → gets AI response
   - Server calls **Sarvam TTS** → converts AI response to speech audio (base64)
   - Server sends back: `{ user_text, agent_text, audio }`
6. **Disconnect**: On WebSocket close, the server saves `ended_at` and `duration_seconds` to the conversation document.

### Q23. What is the WebSocket message protocol?
**A.**

**Client → Server:**
```json
{ "type": "audio", "data": "<base64 audio>", "language": "hi-IN", "provider": "gemini" }
{ "type": "text", "text": "hello", "language": "en-IN", "provider": "groq" }
{ "type": "ping" }
```

**Server → Client:**
```json
{ "type": "connected", "agent_name": "...", "conversation_id": "..." }
{ "type": "status", "message": "Thinking..." }
{ "type": "response", "user_text": "...", "agent_text": "...", "audio": "<base64>" }
{ "type": "error", "message": "..." }
{ "type": "pong" }
```

### Q24. Why WebSocket instead of regular HTTP?
**A.** Regular HTTP is request-response — the server can't push data to the client. WebSocket maintains a **persistent, bidirectional connection**, which is essential for:
1. **Real-time feedback** — sending status updates ("Processing...", "Thinking...", "Speaking...") as the AI processes.
2. **Streaming audio** — sending audio back without the overhead of new HTTP connections.
3. **Conversation context** — the server maintains an in-memory conversation history for the LLM.

### Q25. How is the WebSocket authenticated?
**A.** WebSockets can't use the standard `Authorization` header (browser limitation), so we pass the JWT as a **query parameter**:
```
ws://localhost:8000/api/chat/{agent_id}?token=<JWT>
```
The server first accepts the connection, then validates the token. If invalid, it sends an error message and closes the connection with a policy violation code.

---

## 📌 Section 7 — AI Integration

### Q26. What LLM models do you use?
**A.** Two providers, selectable per conversation:
1. **Google Gemini 2.5 Flash** — via the `google-genai` SDK (default).
2. **Groq LLaMA 3.3 70B Versatile** — via the `groq` SDK (alternative, faster for some use cases).

### Q27. How does the LLM service work?
**A.** The `services/llm.py` provides a unified `generate_response()` function:
```python
async def generate_response(
    system_prompt: str,       # Agent's personality/instructions
    conversation_history: [],  # Previous messages for context
    user_text: str,           # Latest user message
    provider: str = "gemini", # "gemini" or "groq"
) -> str:
```
- Each AI agent has a **system prompt** that defines its personality and behavior.
- The full conversation history is sent to the LLM so it has context.
- The **language** is enforced via a dynamic addition to the system prompt: `"You MUST reply entirely in {language} script and language."`

### Q28. What is Sarvam AI? Why did you choose it?
**A.** **Sarvam AI** is an Indian AI company specializing in **Indian language processing**. We use two of their APIs:
1. **Saaras v3 (STT)** — converts speech audio to text, supports 10+ Indian languages.
2. **Bulbul v3 (TTS)** — converts text to natural-sounding speech with 38 different voice options (male/female, various styles).

We chose Sarvam because our project supports **multi-lingual voice chat** in Indian languages (Hindi, Gujarati, Marathi, Tamil, Telugu, Bengali, Kannada, Malayalam, Punjabi) — and Sarvam handles these better than most alternatives.

### Q29. How does STT (Speech-to-Text) work?
**A.** In `services/sarvam.py`:
```python
async def speech_to_text(audio_bytes, language_code="unknown"):
    response = await client.post(
        "https://api.sarvam.ai/speech-to-text",
        headers={"api-subscription-key": SARVAM_KEY},
        data={"language_code": language_code, "model": "saaras:v3"},
        files={"file": ("audio.wav", audio_bytes, "audio/wav")},
    )
    return {"transcript": data["transcript"], "language_code": data["language_code"]}
```
The audio is sent as a WAV file (16kHz, mono, 16-bit PCM).

### Q30. How does TTS (Text-to-Speech) work?
**A.** In `services/sarvam.py`:
```python
async def text_to_speech(text, target_language_code="en-IN", speaker="ishita"):
    response = await client.post(
        "https://api.sarvam.ai/text-to-speech",
        json={"text": text, "target_language_code": target_language_code,
              "speaker": speaker, "model": "bulbul:v3"},
    )
    return data["audios"][0]  # base64-encoded WAV audio
```

### Q31. How does the AI agent config generation work?
**A.** The `POST /api/generate/agent-config` endpoint uses **Gemini Structured Outputs**:
1. Admin provides a plain text prompt (e.g., "create a Hindi tutor agent").
2. We define a **Pydantic schema** (`AgentConfigSchema`) that specifies the exact JSON structure we want.
3. We pass this schema to Gemini with `response_schema=AgentConfigSchema`.
4. Gemini returns a perfectly structured JSON with: name, category, description, system_prompt, languages, voice_id, and image_prompt.

This is called **Structured Outputs** — the AI is constrained to return data that matches our schema exactly.

### Q32. How does avatar image generation work?
**A.** The `POST /api/generate/agent-image` endpoint uses **Google Imagen 4**:
1. Takes an `image_prompt` (from the agent config generation).
2. Enhances the prompt with professional avatar styling instructions.
3. Calls `client.models.generate_images(model='imagen-4.0-generate-001', ...)`.
4. Returns the generated image as a base64-encoded JPEG string.

---

## 📌 Section 8 — File Uploads

### Q33. How do you handle file uploads?
**A.** We use **FastAPI's `UploadFile`** with **`Form()` data** (multipart/form-data):
```python
@router.post("/agents")
async def create_agent(
    name: str = Form(...),
    category: str = Form(...),
    image: UploadFile = File(None),
):
```
- Files are validated for type (JPEG, PNG, WebP, GIF only) and size (max 2MB for agents, 5MB for profile photos).
- Files are saved with a **UUID filename** to prevent collisions: `{uuid4().hex}.jpg`.
- When updating, old files are deleted from disk.
- Files are served via FastAPI's `StaticFiles` mount: `app.mount("/uploads", StaticFiles(...))`.

---

## 📌 Section 9 — Admin Dashboard Features

### Q34. What statistics does the admin dashboard show?
**A.** The `GET /api/admin/dashboard/stats` endpoint returns:
- **Total Users** — count of user documents.
- **Total Agents** — count of agent documents.
- **Conversations Today** — count of conversations started today.
- **Top 5 Agents** — MongoDB aggregation pipeline grouping by `agent_name` and sorting by count.
- **Daily Conversations (7 days)** — aggregation pipeline formatting dates and counting per day.

### Q35. How does user blocking work?
**A.** Admins can `PATCH /api/admin/users/{id}/status` with `{ "status": "blocked" }`.
- Sets both `status: "blocked"` and `is_active: false` in the user document.
- The `get_current_user` dependency checks these fields — blocked users get a 401 error on any authenticated request.
- The login route also checks status — blocked users see: "Your account has been blocked."

---

## 📌 Section 10 — Pydantic Schemas & Validation

### Q36. What is Pydantic and why do you use it?
**A.** Pydantic is a data validation library. In FastAPI, it's used to:
1. **Validate request bodies** — if a required field is missing or wrong type, FastAPI auto-returns a 422 error with a clear message.
2. **Define response shapes** — ensures the API returns consistent JSON structure.
3. **Email validation** — using `EmailStr` type that validates email format.

Example:
```python
class RegisterRequest(BaseModel):
    full_name: str
    email: EmailStr      # auto-validates email format
    mobile: str
    password: str
```

### Q37. What validations are done on the backend?
**A.**
- **Email**: Pydantic `EmailStr` validates format. Uniqueness checked in MongoDB.
- **Mobile**: Uniqueness checked in MongoDB during registration.
- **Password**: Minimum 8 characters (checked in reset-password and change-password routes).
- **File uploads**: Content type whitelist + file size limits.
- **Agent ID**: Validated using `ObjectId.is_valid()` before MongoDB queries.
- **Status values**: Whitelist check — only "active"/"blocked" for users, "active"/"paused" for agents.

---

## 📌 Section 11 — Configuration & Environment

### Q38. How do you manage configuration?
**A.** All configuration is in `config.py` using environment variables with sensible defaults:
```python
class Settings:
    MONGODB_URL = os.getenv("MONGODB_URL", "mongodb://localhost:27017")
    DB_NAME = os.getenv("DB_NAME", "college_project")
    SECRET_KEY = os.getenv("SECRET_KEY", "dev-secret-key-change-in-production")
    ALGORITHM = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES = 60
    GEMINI_API_KEY = os.getenv("GEMINI_API_KEY", "")
    GROQ_KEY = os.getenv("GROQ_KEY", "")
    SARVAM_KEY = os.getenv("SARVAM_KEY", "")
```
Environment variables are loaded from a `.env` file using `python-dotenv`.

### Q39. What are the key environment variables?
**A.**
| Variable | Purpose |
|----------|---------|
| `MONGODB_URL` | MongoDB connection string |
| `DB_NAME` | Database name |
| `SECRET_KEY` | JWT signing key |
| `SMTP_HOST/PORT/USER/PASSWORD` | Email for OTP |
| `GEMINI_API_KEY` | Google Gemini API key |
| `GROQ_KEY` | Groq API key |
| `SARVAM_KEY` | Sarvam AI API subscription key |

---

## 📌 Section 12 — Error Handling

### Q40. How do you handle errors in the backend?
**A.** We use **FastAPI's HTTPException** with appropriate HTTP status codes:
```python
raise HTTPException(
    status_code=status.HTTP_400_BAD_REQUEST,
    detail="An account with this email already exists",
)
```
Common codes used:
- `400` — Bad request (validation errors, duplicate data)
- `401` — Unauthorized (invalid/expired token)
- `403` — Forbidden (blocked user, non-admin accessing admin routes)
- `404` — Not found (user, agent, conversation)
- `500` — Server error (AI API failures)

### Q41. What happens if the AI API fails during voice chat?
**A.** Each step has individual error handling:
- **STT fails** → sends `{ "type": "error", "message": "Speech recognition failed" }` and `continue`s the loop (doesn't crash the session).
- **LLM fails** → sends an error and `continue`s.
- **TTS fails** → sends a text-only response (agent_text without audio). The user still gets the text response.
The WebSocket connection stays alive so the user can try again.

---

## 📌 Section 13 — Conversation Storage

### Q42. How are conversations stored?
**A.** Each voice chat session creates a **conversation document** in MongoDB:
```json
{
    "user_id": "...",
    "agent_id": "...",
    "agent_name": "Hindi Tutor",
    "messages": [
        { "role": "user", "content": "Hello", "timestamp": "..." },
        { "role": "assistant", "content": "Namaste!", "timestamp": "..." }
    ],
    "duration_seconds": 120,
    "started_at": "...",
    "ended_at": "..."
}
```
Messages are appended in real-time using `$push`:
```python
await db.conversations.update_one(
    {"_id": conv_id},
    {"$push": {"messages": user_msg}},
)
```

### Q43. How is the conversation duration calculated?
**A.** When the WebSocket disconnects (in the `finally` block):
```python
ended_at = datetime.now(timezone.utc)
duration_seconds = int((ended_at - conv_started_at).total_seconds())
await db.conversations.update_one(
    {"_id": conv_id},
    {"$set": {"ended_at": ended_at, "duration_seconds": duration_seconds}},
)
```

---

## 📌 Section 14 — Additional Technical Questions

### Q44. What is `asynccontextmanager` and why do you use it?
**A.** It's used for FastAPI's **lifespan** events — code that runs on startup and shutdown:
```python
@asynccontextmanager
async def lifespan(app):
    await connect_to_mongodb()  # runs on startup
    yield                       # app runs
    await disconnect_from_mongodb()  # runs on shutdown
```

### Q45. What is the difference between `find_one()` and `find()`?
**A.**
- `find_one()` returns a single document (or `None`). Used for fetching by ID or unique field.
- `find()` returns a **cursor** — you iterate over it with `async for`. Used for listing with pagination, sorting, etc.

### Q46. What is `ObjectId` and why do you validate it?
**A.** MongoDB uses `ObjectId` as the default `_id` type — a 12-byte identifier. When API endpoints receive IDs as strings (from URLs), we validate them with `ObjectId.is_valid()` before querying. Invalid IDs would crash the query, so we return a 400 error instead.

### Q47. How do you run the backend server?
**A.**
```bash
cd backend
source venv/bin/activate        # activate Python virtual environment
uvicorn main:app --reload       # start server with hot-reload
```
- `uvicorn` is the ASGI server that runs FastAPI.
- `--reload` enables hot-reload during development — the server restarts when code changes.
- The server runs on `http://localhost:8000` by default.

### Q48. What is a virtual environment (`venv`) and why do you use it?
**A.** A Python virtual environment is an isolated Python installation. It ensures:
- Project dependencies don't conflict with system Python or other projects.
- `requirements.txt` lists exact versions for reproducibility.
- Created with: `python -m venv venv`
- Activated with: `source venv/bin/activate`

### Q49. What Python libraries does the project use?
**A.**
| Library | Version | Purpose |
|---------|---------|---------|
| `fastapi` | 0.115.0 | Web framework |
| `uvicorn[standard]` | 0.30.6 | ASGI server |
| `motor` | 3.5.1 | Async MongoDB driver |
| `pymongo` | 4.8.0 | MongoDB base driver |
| `python-jose[cryptography]` | 3.3.0 | JWT encoding/decoding |
| `passlib[bcrypt]` | 1.7.4 | Password hashing |
| `bcrypt` | 4.1.3 | Bcrypt algorithm |
| `python-dotenv` | 1.0.1 | Load `.env` files |
| `pydantic[email]` | 2.9.1 | Data validation + EmailStr |
| `python-multipart` | 0.0.9 | Multipart form parsing (file uploads) |
| `groq` | 0.24.0 | Groq LLM SDK |
| `httpx` | 0.28.1 | Async HTTP client (for Sarvam API) |
| `google-genai` | 1.3.0 | Google Gemini SDK |

### Q50. What is the difference between `$set` and `$push` in MongoDB?
**A.**
- `$set` — **Updates** a field's value: `{"$set": {"status": "blocked"}}` changes the status.
- `$push` — **Appends** to an array: `{"$push": {"messages": new_msg}}` adds a message to the conversation.

---

## 📌 Quick Reference — What to Say if Asked...

| If they ask... | Say this... |
|---|---|
| "What framework?" | **FastAPI** — Python, async, auto-docs |
| "What database?" | **MongoDB** — NoSQL, document-based, async via Motor |
| "How is auth done?" | **JWT + bcrypt** — token in header, password hashed |
| "How does voice work?" | **WebSocket** → Sarvam STT → Gemini LLM → Sarvam TTS → audio back |
| "What AI models?" | **Gemini 2.5 Flash** (default) + **Groq LLaMA 3.3 70B** (alternative) |
| "How are files stored?" | Local disk under `/uploads/`, served via **StaticFiles** mount |
| "How are passwords forgotten?" | OTP sent via **SMTP email**, verified, then password reset |
| "How do you validate data?" | **Pydantic schemas** (automatic 422 on invalid data) |
| "Where are API docs?" | **Swagger UI** at `http://localhost:8000/docs` (auto-generated) |
