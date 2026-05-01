# 🎓 Database Viva Q&A — AI Voice Agent Platform

> **Database**: MongoDB (NoSQL, Document-based)  
> **Driver**: Motor (Async Python driver for MongoDB)  
> **Database Name**: `college_project`  
> **Connection**: `mongodb://localhost:27017`

---

## 📌 Section 1 — Database Overview

### Q1. What database are you using and why?
**A.** We use **MongoDB** — a NoSQL, document-based database. Reasons:
1. **Flexible Schema** — No rigid table structures. Agents can have different fields (some have voice, some don't) without needing ALTER TABLE.
2. **Document Model** — A conversation with nested messages is a single document, not 3 separate tables joined together.
3. **TTL Indexes** — OTPs auto-delete after expiry. No cron jobs needed.
4. **Async Support** — Motor driver integrates perfectly with FastAPI's async architecture.
5. **JSON-native** — MongoDB stores BSON (Binary JSON), and our API already speaks JSON. No ORM translation layer needed.

### Q2. What is MongoDB? How is it different from MySQL?
**A.**

| Feature | MongoDB (NoSQL) | MySQL (SQL) |
|---------|----------------|-------------|
| Data Model | **Documents** (JSON-like objects) | **Tables** with rows and columns |
| Schema | **Flexible** — documents in the same collection can have different fields | **Fixed** — every row must match the table schema |
| Query Language | MongoDB Query Language (MQL) | SQL |
| Joins | **Embedded documents** (no joins needed in most cases) | **JOINs** across tables |
| Scaling | Horizontal (sharding) | Vertical (bigger server) |
| Best For | Unstructured/semi-structured data, rapid prototyping | Structured data, complex relationships |

**Example — Conversation in MySQL vs MongoDB:**

**MySQL** (3 tables + JOIN):
```sql
-- conversations table
| id | user_id | agent_id | started_at |
-- messages table
| id | conversation_id | role | content | timestamp |
-- To get a conversation with messages: JOIN needed
```

**MongoDB** (1 document, no join):
```json
{
    "_id": ObjectId("..."),
    "user_id": "...",
    "agent_id": "...",
    "messages": [
        { "role": "user", "content": "Hello", "timestamp": "..." },
        { "role": "assistant", "content": "Hi!", "timestamp": "..." }
    ],
    "started_at": "..."
}
```

### Q3. What is Motor? Why not use PyMongo directly?
**A.** **Motor** is the **asynchronous** wrapper around PyMongo. 
- **PyMongo** is synchronous — it blocks the server thread while waiting for MongoDB. 
- **Motor** is asynchronous — it uses `async/await`, allowing FastAPI to handle other requests while waiting for the DB response.
- Since FastAPI is an **async framework**, using a synchronous driver would defeat the purpose and create bottlenecks.

```python
# Motor (async) — what we use ✅
user = await db.users.find_one({"email": "test@test.com"})

# PyMongo (sync) — would block ❌
user = db.users.find_one({"email": "test@test.com"})
```

### Q4. How do you connect to MongoDB?
**A.** In `database.py`:
```python
from motor.motor_asyncio import AsyncIOMotorClient
from config import settings

client: AsyncIOMotorClient = None
db = None

async def connect_to_mongodb():
    global client, db
    client = AsyncIOMotorClient(settings.MONGODB_URL)  # "mongodb://localhost:27017"
    db = client[settings.DB_NAME]                       # "college_project"

    # Create indexes on startup
    await db.users.create_index("email", unique=True)
    await db.admins.create_index("email", unique=True)
    await db.otps.create_index("expires_at", expireAfterSeconds=0)

    print(f"Connected to MongoDB: {settings.DB_NAME}")

async def disconnect_from_mongodb():
    global client
    if client:
        client.close()
```

The connection is managed via FastAPI's **lifespan**:
```python
@asynccontextmanager
async def lifespan(app: FastAPI):
    await connect_to_mongodb()   # ← runs on server startup
    yield
    await disconnect_from_mongodb()  # ← runs on server shutdown
```

---

## 📌 Section 2 — Collections (Tables)

### Q5. What collections does the database have?
**A.** The `college_project` database has **6 collections**:

| # | Collection | Purpose | Approx. Document Count |
|---|------------|---------|----------------------|
| 1 | `users` | Registered end-users | Grows with signups |
| 2 | `admins` | Admin accounts | Small (1–5) |
| 3 | `agents` | AI agent configurations | 10–50 |
| 4 | `categories` | Agent categories (Education, Support, etc.) | 5–15 |
| 5 | `conversations` | Voice chat sessions with embedded messages | Grows rapidly |
| 6 | `otps` | Temporary OTP codes for password reset | Ephemeral (TTL auto-delete) |

### Q6. What is a "collection" in MongoDB?
**A.** A **collection** in MongoDB is equivalent to a **table** in MySQL. It stores **documents** (equivalent to rows). Unlike SQL tables, a MongoDB collection does not enforce a fixed schema — each document can have different fields.

### Q7. What is a "document" in MongoDB?
**A.** A **document** is a single record in a collection. It's stored as **BSON** (Binary JSON) and looks like a JSON object:
```json
{
    "_id": ObjectId("661f3a2b..."),
    "full_name": "John Doe",
    "email": "john@example.com",
    "mobile": "9876543210",
    "hashed_password": "$2b$12$...",
    "status": "active",
    "created_at": ISODate("2026-03-26T10:30:00Z")
}
```

---

## 📌 Section 3 — Collection Schemas (Document Structure)

### Q8. What is the structure of the `users` collection?
**A.**
```json
{
    "_id": ObjectId("..."),          // Auto-generated unique ID
    "full_name": "Krunal Patel",     // String — user's display name
    "email": "krunal@example.com",   // String — unique, lowercase, trimmed
    "mobile": "9876543210",          // String — phone number
    "hashed_password": "$2b$12$...", // String — bcrypt hash (never plain text)
    "role": "user",                  // String — always "user" for this collection
    "is_active": true,               // Boolean — legacy active flag
    "status": "active",              // String — "active" or "blocked"
    "profile_photo": "/uploads/profile_photos/abc123.jpg",  // String or null
    "created_at": ISODate("2026-03-26T10:30:00Z"),  // UTC timestamp
    "updated_at": ISODate("2026-04-10T14:00:00Z")   // UTC timestamp
}
```

**Code that creates this** (`models/user.py`):
```python
def new_user_doc(full_name, email, mobile, hashed_password, role="user"):
    now = datetime.now(timezone.utc)
    return {
        "full_name": full_name,
        "email": email.lower().strip(),
        "mobile": mobile,
        "hashed_password": hashed_password,
        "role": role,
        "is_active": True,
        "status": "active",
        "created_at": now,
        "updated_at": now,
    }
```

### Q9. What is the structure of the `admins` collection?
**A.**
```json
{
    "_id": ObjectId("..."),
    "full_name": "Admin User",
    "email": "admin@example.com",
    "mobile": "9999999999",
    "hashed_password": "$2b$12$...",
    "is_active": true,
    "created_at": ISODate("..."),
    "updated_at": ISODate("...")
}
```
> **Note:** Admins are stored in a **separate collection** from users — not mixed together. This gives cleaner separation of concerns and prevents accidental admin privilege escalation.

### Q10. What is the structure of the `agents` collection?
**A.**
```json
{
    "_id": ObjectId("..."),
    "name": "Hindi Tutor",                         // Agent display name
    "category": "Education",                       // Category for filtering
    "description": "A friendly Hindi language tutor",  // Short description
    "profile_image": "/uploads/agent_images/xyz.jpg",  // Avatar image path or null
    "languages": ["Hindi", "English (US)"],        // Array of supported languages
    "voice_enabled": true,                         // Whether voice is active
    "voice_id": "ishita",                          // Sarvam TTS voice identifier
    "system_prompt": "You are a Hindi tutor...",   // LLM personality instructions
    "status": "active",                            // "active" or "paused"
    "created_at": ISODate("..."),
    "updated_at": ISODate("...")
}
```

**Key points:**
- `languages` is an **array** — MongoDB natively supports arrays (no separate join table needed like in SQL).
- `system_prompt` is the core of the agent — it defines how the AI behaves.
- `voice_id` maps to one of 38 Sarvam Bulbul voices.

### Q11. What is the structure of the `categories` collection?
**A.**
```json
{
    "_id": ObjectId("..."),
    "name": "Education",
    "created_at": ISODate("...")
}
```
Simple collection used to populate category dropdowns in the UI.

### Q12. What is the structure of the `conversations` collection?
**A.**
```json
{
    "_id": ObjectId("..."),
    "user_id": "661f3a2b...",                  // String — user's _id
    "agent_id": "662a1b3c...",                 // String — agent's _id
    "agent_name": "Hindi Tutor",               // Denormalized for quick display
    "messages": [                              // Embedded array of messages
        {
            "role": "user",
            "content": "Mujhe Hindi sikhao",
            "timestamp": ISODate("2026-04-14T10:05:00Z")
        },
        {
            "role": "assistant",
            "content": "Zaroor! Chaliye shuru karte hain...",
            "timestamp": ISODate("2026-04-14T10:05:03Z")
        }
    ],
    "duration_seconds": 300,                   // Total session duration (5 min)
    "started_at": ISODate("2026-04-14T10:00:00Z"),
    "ended_at": ISODate("2026-04-14T10:05:00Z")
}
```

**Key design decisions:**
- **Messages are embedded** (not in a separate collection) — avoids joins, atomic reads.
- `agent_name` is **denormalized** — stored here even though it exists in agents collection: avoids a lookup every time we list conversations.
- `duration_seconds` is calculated on WebSocket disconnect: `ended_at - started_at`.

### Q13. What is the structure of the `otps` collection?
**A.**
```json
{
    "_id": ObjectId("..."),
    "email": "krunal@example.com",       // User's email (lowercase)
    "otp_code": "482910",                // Random 6-digit code
    "expires_at": ISODate("2026-04-14T10:10:00Z"),  // Expiry time (10 min from creation)
    "is_used": false,                    // Whether this OTP has been used
    "created_at": ISODate("2026-04-14T10:00:00Z")
}
```

**This collection has a TTL index** — MongoDB automatically deletes documents after `expires_at` passes. Even if the code doesn't check expiry, the data is physically removed.

---

## 📌 Section 4 — Indexes

### Q14. What are database indexes and why do you use them?
**A.** An **index** is a data structure that speeds up queries. Without an index, MongoDB must scan every document in a collection (like reading every page of a book). With an index, it can jump directly to the matching document (like a book's index).

### Q15. What indexes are created in this project?
**A.** Three indexes are created at startup in `database.py`:

| # | Collection | Field | Type | Purpose |
|---|------------|-------|------|---------|
| 1 | `users` | `email` | **Unique** | Prevents duplicate emails + speeds up login lookups |
| 2 | `admins` | `email` | **Unique** | Prevents duplicate admin emails + speeds up admin login |
| 3 | `otps` | `expires_at` | **TTL** | Auto-deletes expired OTPs after their expiry time |

```python
await db.users.create_index("email", unique=True)
await db.admins.create_index("email", unique=True)
await db.otps.create_index("expires_at", expireAfterSeconds=0)
```

### Q16. What is a Unique Index?
**A.** A unique index ensures that **no two documents** in a collection can have the same value for that field. If you try to insert a duplicate, MongoDB throws a `DuplicateKeyError`.
- `users.email` is unique — guarantees no two users can register with the same email.
- We also check in code before inserting (`find_one` first), but the index is a **database-level safety net**.

### Q17. What is a TTL Index?
**A.** TTL (Time-To-Live) index automatically **deletes documents** when a specified date field passes.
```python
await db.otps.create_index("expires_at", expireAfterSeconds=0)
```
- `expireAfterSeconds=0` means: delete the document as soon as `expires_at` is in the past.
- MongoDB runs a background thread every 60 seconds that checks and removes expired documents.
- **Use case**: OTPs are valid for 10 minutes — after that, they're automatically cleaned up. No cron job or manual cleanup needed.

### Q18. What is the `_id` field? What is ObjectId?
**A.**
- Every MongoDB document gets an automatic `_id` field as its **primary key**.
- By default, `_id` is an **ObjectId** — a 12-byte unique identifier.
- ObjectId structure: `4 bytes (timestamp) + 5 bytes (random) + 3 bytes (counter)`.
- It's sortable by creation time and globally unique (no collisions even across different servers).
- In our code, we convert it to a string when sending to the frontend: `str(user["_id"])`.

---

## 📌 Section 5 — CRUD Operations

### Q19. How do you INSERT a document?
**A.** Using `insert_one()`:
```python
# Create user
user_doc = new_user_doc(full_name="John", email="john@test.com", ...)
result = await db.users.insert_one(user_doc)
# result.inserted_id → ObjectId of the new document
```

### Q20. How do you READ/FIND documents?
**A.**

**Find one document:**
```python
user = await db.users.find_one({"email": "john@test.com"})
# Returns a dict or None
```

**Find multiple documents (with cursor):**
```python
cursor = db.users.find({"status": "active"}).sort("created_at", -1).skip(0).limit(10)
users = []
async for user_doc in cursor:
    users.append(user_helper(user_doc))
```

**Count documents:**
```python
total = await db.users.count_documents({"status": "active"})
```

**Find distinct values:**
```python
distinct_agents = await db.conversations.distinct("agent_id", {"user_id": user_id})
# Returns a list of unique agent_ids
```

### Q21. How do you UPDATE documents?
**A.**

**Update one document:**
```python
await db.users.update_one(
    {"_id": ObjectId(user_id)},           # Filter — which document
    {"$set": {"status": "blocked",         # Update — what to change
              "updated_at": datetime.now(timezone.utc)}}
)
```

**Update many:**
```python
# Invalidate all unused OTPs for this email
await db.otps.update_many(
    {"email": "john@test.com", "is_used": False},
    {"$set": {"is_used": True}}
)
```

**Append to an array ($push):**
```python
# Add a message to a conversation
await db.conversations.update_one(
    {"_id": conv_id},
    {"$push": {"messages": {"role": "user", "content": "Hello", "timestamp": now}}}
)
```

### Q22. How do you DELETE documents?
**A.**
```python
# Delete one agent
await db.agents.delete_one({"_id": ObjectId(agent_id)})
```

### Q23. What is the difference between `$set` and `$push`?
**A.**
| Operator | Purpose | Example |
|----------|---------|---------|
| `$set` | **Replace** a field's value | `{"$set": {"status": "blocked"}}` → changes status |
| `$push` | **Append** an item to an array | `{"$push": {"messages": msg}}` → adds a message to the list |

### Q24. What is the difference between `find_one()` and `find()`?
**A.**
| Method | Returns | Use Case |
|--------|---------|----------|
| `find_one(filter)` | A single document dict or `None` | Fetch by ID, check if email exists |
| `find(filter)` | An **async cursor** (iterate with `async for`) | List users, list agents with pagination |

`find()` supports chaining: `.sort()`, `.skip()`, `.limit()` for pagination and ordering.

---

## 📌 Section 6 — Aggregation Pipelines

### Q25. What is a MongoDB aggregation pipeline?
**A.** An aggregation pipeline is a sequence of **stages** that process and transform documents. It's like SQL's `GROUP BY`, `COUNT`, `SUM` but more powerful. Each stage takes input documents and outputs transformed documents to the next stage.

### Q26. Where do you use aggregation pipelines?
**A.** We use them in **5 places**:

#### Pipeline 1: Top 5 Most Used Agents (Admin Dashboard)
```python
pipeline = [
    {"$group": {"_id": "$agent_name", "count": {"$sum": 1}}},
    {"$sort": {"count": -1}},
    {"$limit": 5}
]
```
**In SQL equivalent:**
```sql
SELECT agent_name, COUNT(*) as count
FROM conversations
GROUP BY agent_name
ORDER BY count DESC
LIMIT 5;
```

#### Pipeline 2: Daily Conversations (Last 7 Days — Admin Dashboard)
```python
pipeline = [
    {"$match": {"started_at": {"$gte": seven_days_ago}}},
    {"$group": {
        "_id": {"$dateToString": {"format": "%Y-%m-%d", "date": "$started_at"}},
        "count": {"$sum": 1}
    }}
]
```
**In SQL equivalent:**
```sql
SELECT DATE(started_at) as date, COUNT(*) as count
FROM conversations
WHERE started_at >= NOW() - INTERVAL 7 DAY
GROUP BY DATE(started_at);
```

#### Pipeline 3: Total Voice Minutes (User Dashboard)
```python
pipeline = [
    {"$match": {"user_id": user_id}},
    {"$group": {"_id": None, "total_seconds": {"$sum": "$duration_seconds"}}}
]
```
**In SQL equivalent:**
```sql
SELECT SUM(duration_seconds) as total_seconds
FROM conversations
WHERE user_id = ?;
```

#### Pipeline 4: Top Agents for a User (User Dashboard)
```python
pipeline = [
    {"$match": {"user_id": user_id}},
    {"$group": {"_id": "$agent_name", "count": {"$sum": 1}}},
    {"$sort": {"count": -1}},
    {"$limit": 4}
]
```

#### Pipeline 5: Count Unique Agents Interacted With
```python
distinct_agents = await db.conversations.distinct("agent_id", {"user_id": user_id})
active_agents = len(distinct_agents)
```
*(This uses `distinct()` instead of a full pipeline — simpler for unique value counting)*

### Q27. Explain the aggregation stages used.
**A.**

| Stage | Purpose | Example |
|-------|---------|---------|
| `$match` | **Filter** — like SQL `WHERE` | `{"$match": {"user_id": "abc"}}` |
| `$group` | **Group** — like SQL `GROUP BY` | `{"$group": {"_id": "$agent_name", "count": {"$sum": 1}}}` |
| `$sort` | **Sort** — like SQL `ORDER BY` | `{"$sort": {"count": -1}}` (descending) |
| `$limit` | **Limit** — like SQL `LIMIT` | `{"$limit": 5}` |
| `$sum` | **Accumulator** — sum values | `{"$sum": "$duration_seconds"}` or `{"$sum": 1}` (count) |
| `$dateToString` | **Format dates** — for grouping by day | `{"$dateToString": {"format": "%Y-%m-%d", "date": "$started_at"}}` |

---

## 📌 Section 7 — Query Patterns

### Q28. How does search work in MongoDB?
**A.** We use **regex queries** for case-insensitive text search:
```python
if search.strip():
    escaped = re.escape(search.strip())  # Prevent regex injection
    query["$or"] = [
        {"full_name": {"$regex": escaped, "$options": "i"}},
        {"email": {"$regex": escaped, "$options": "i"}},
    ]
```
- `$regex` — performs a regular expression match.
- `$options: "i"` — case-insensitive flag.
- `$or` — matches documents where **either** field matches (like SQL `WHERE name LIKE '%search%' OR email LIKE '%search%'`).
- `re.escape()` — escapes special regex characters to prevent injection attacks.

### Q29. How does pagination work?
**A.** Using MongoDB's `skip()` and `limit()`:
```python
page = 2       # requested page
limit = 10     # items per page
skip = (page - 1) * limit  # skip = 10 (skip first 10 items)

total = await db.users.count_documents(query)          # total matching documents
total_pages = ceil(total / limit)                       # e.g., ceil(45/10) = 5

cursor = db.users.find(query)
    .sort("created_at", -1)    # newest first
    .skip(skip)                # skip first N
    .limit(limit)              # return only N
```

**Response format:**
```json
{
    "users": [...],        // 10 users for this page
    "total": 45,           // total users matching filter
    "page": 2,             // current page
    "limit": 10,           // items per page
    "total_pages": 5       // total pages
}
```

### Q30. How does sorting work?
**A.**
```python
.sort("created_at", -1)   # -1 = descending (newest first)
.sort("name", 1)           #  1 = ascending (A → Z)
```
We sort:
- Users, agents, conversations by `created_at` descending (newest first).
- Categories by `name` ascending (alphabetical).

### Q31. How do you filter by category?
**A.** Case-insensitive exact match using anchored regex:
```python
if category:
    query["category"] = {"$regex": f"^{category}$", "$options": "i"}
```
- `^...$` anchors ensure **exact match** (not partial match).
- `$options: "i"` makes it case-insensitive ("Education" matches "education").

---

## 📌 Section 8 — Data Models (Helper Functions)

### Q32. What are "helper" functions in the models?
**A.** Helper functions convert raw MongoDB documents (which have `_id` as ObjectId) into clean Python dicts for the API response:
```python
def user_helper(user: dict) -> dict:
    return {
        "id": str(user["_id"]),           # ObjectId → string
        "full_name": user.get("full_name", ""),
        "email": user.get("email", ""),
        "mobile": user.get("mobile", ""),
        "role": user.get("role", "user"),
        "profile_photo": user.get("profile_photo", None),
        "is_active": user.get("is_active", True),
        "status": status,                 # Derived from "status" or "is_active"
        "created_at": user.get("created_at", ""),
        "updated_at": user.get("updated_at", ""),
    }
```

**Why?**
- `_id` → `id`: Frontend expects a string ID, not a MongoDB ObjectId.
- `user.get("field", default)`: Safe access — returns default if field is missing (prevents KeyError).
- **Omit sensitive fields**: `hashed_password` is never included in the response.

### Q33. What are "doc" functions in the models?
**A.** Doc functions create new documents for insertion:
```python
def new_user_doc(full_name, email, mobile, hashed_password, role="user"):
    now = datetime.now(timezone.utc)
    return {
        "full_name": full_name,
        "email": email.lower().strip(),    # normalized
        "mobile": mobile,
        "hashed_password": hashed_password, # bcrypt hash
        "role": role,
        "is_active": True,
        "status": "active",
        "created_at": now,                  # UTC timestamp
        "updated_at": now,
    }
```

### Q34. Why is there both `is_active` and `status` on the user document?
**A.** This is a **migration pattern**. Initially the project only had `is_active` (boolean: true/false). Later, `status` was added as a more expressive string field ("active", "blocked"). Both are kept for **backward compatibility**:
```python
# Derive status: use explicit field if present, else fall back to is_active
raw_status = user.get("status")
if raw_status:
    status = raw_status
else:
    status = "active" if user.get("is_active", True) else "blocked"
```

### Q35. Why is `agent_name` stored in conversations (denormalization)?
**A.** **Denormalization** means storing a copy of data that technically belongs to another collection. We store `agent_name` directly in the conversation document because:
1. **Listing conversations** — We need to show the agent name in every list item. Without denormalization, we'd have to do a separate DB call for each conversation to fetch the agent name.
2. **Performance** — One read instead of N+1 reads.
3. **History accuracy** — Even if the agent is renamed later, the conversation retains the name it was originally with.

**Trade-off**: If an agent is renamed, old conversations still show the old name. This is acceptable for our use case.

---

## 📌 Section 9 — Relationships Between Collections

### Q36. Draw the Entity Relationship Diagram (ERD).
**A.**

```
┌──────────────────┐          ┌──────────────────┐
│     admins       │          │    categories     │
├──────────────────┤          ├──────────────────┤
│ _id (ObjectId)   │          │ _id (ObjectId)   │
│ full_name        │          │ name             │
│ email (unique)   │          │ created_at       │
│ mobile           │          └──────────────────┘
│ hashed_password  │                  │
│ is_active        │                  │ (agent.category = categories.name)
│ created_at       │                  ▼
│ updated_at       │          ┌──────────────────┐
└──────────────────┘          │     agents       │
                              ├──────────────────┤
┌──────────────────┐          │ _id (ObjectId)   │
│      users       │          │ name             │
├──────────────────┤          │ category         │◄── references categories.name
│ _id (ObjectId)   │          │ description      │
│ full_name        │          │ profile_image    │
│ email (unique)   │          │ languages []     │
│ mobile           │          │ voice_enabled    │
│ hashed_password  │          │ voice_id         │
│ role             │          │ system_prompt    │
│ is_active        │          │ status           │
│ status           │          │ created_at       │
│ profile_photo    │          │ updated_at       │
│ created_at       │          └──────────────────┘
│ updated_at       │                  │
└──────────────────┘                  │
        │                             │
        │  (conv.user_id = users._id) │  (conv.agent_id = agents._id)
        │                             │
        ▼                             ▼
┌─────────────────────────────────────────┐
│           conversations                  │
├─────────────────────────────────────────┤
│ _id (ObjectId)                          │
│ user_id          ◄── references users._id (as string)
│ agent_id         ◄── references agents._id (as string)
│ agent_name       (denormalized from agents.name)
│ messages []      ◄── embedded array                    │
│   ├─ role        ("user" | "assistant")                │
│   ├─ content     (message text)                        │
│   └─ timestamp   (UTC datetime)                        │
│ duration_seconds                                       │
│ started_at                                             │
│ ended_at                                               │
└─────────────────────────────────────────┘

┌──────────────────┐
│      otps        │
├──────────────────┤
│ _id (ObjectId)   │
│ email            │◄── references users.email
│ otp_code         │
│ expires_at       │◄── TTL index (auto-delete)
│ is_used          │
│ created_at       │
└──────────────────┘
```

### Q37. What type of relationships exist?
**A.**

| Relationship | Type | Implementation |
|---|---|---|
| User → Conversations | **One-to-Many** | `conversations.user_id` stores the user's `_id` as a string |
| Agent → Conversations | **One-to-Many** | `conversations.agent_id` stores the agent's `_id` as a string |
| Category → Agents | **One-to-Many** | `agents.category` stores the category name (string, not _id) |
| User → OTPs | **One-to-Many** | `otps.email` stores the user's email |
| Conversation → Messages | **Embedded (One-to-Many)** | `messages` is an array inside the conversation document |

> **Note:** MongoDB doesn't enforce foreign keys like SQL. These relationships are maintained by application logic.

---

## 📌 Section 10 — Security & Data Protection

### Q38. How is sensitive data protected?
**A.**
| Data | Protection |
|------|-----------|
| **Passwords** | Bcrypt hashed — never stored in plain text |
| **JWT Secret** | Stored in `.env` file, not committed to Git |
| **API Keys** (Gemini, Groq, Sarvam) | Stored in `.env` file |
| **Passwords in responses** | `hashed_password` is **never** included in API responses (excluded by helper functions) |
| **OTPs** | Auto-expire in 10 min + TTL auto-delete from DB |
| **Email enumeration** | Forgot-password returns same message whether email exists or not |

### Q39. How do you prevent duplicate records?
**A.** Two layers of protection:
1. **Application level** — `find_one()` check before `insert_one()`:
   ```python
   existing = await db.users.find_one({"email": request.email.lower()})
   if existing:
       raise HTTPException(400, "Email already exists")
   ```
2. **Database level** — Unique index on `email`:
   ```python
   await db.users.create_index("email", unique=True)
   ```
   Even if two requests arrive simultaneously and pass the application check, the index prevents the duplicate at the DB level.

---

## 📌 Section 11 — Data Flow Examples

### Q40. Trace the data flow for: "User registers"
**A.**
```
1. Frontend sends POST /api/auth/register
   Body: { "full_name": "John", "email": "john@test.com", "mobile": "9876543210", "password": "mypassword123" }

2. Backend (routes/auth.py):
   a. Check db.users.find_one({"email": "john@test.com"})     → None (doesn't exist ✅)
   b. Check db.users.find_one({"mobile": "9876543210"})       → None (doesn't exist ✅)
   c. hash_password("mypassword123")                          → "$2b$12$..."
   d. new_user_doc("John", "john@test.com", "9876543210", "$2b$12$...")
      → Creates: { full_name, email, mobile, hashed_password, status: "active", ... }
   e. db.users.insert_one(user_doc)                          → Inserted ✅

3. Response: { "message": "Account created successfully" }
```

### Q41. Trace the data flow for: "User starts a voice chat"
**A.**
```
1. Frontend opens WebSocket: ws://localhost:8000/api/chat/{agent_id}?token=JWT

2. Backend (routes/chat.py):
   a. Decode JWT → get user_id                           → "661f3a2b..."
   b. db.users.find_one({"_id": ObjectId(user_id)})     → User found ✅
   c. db.agents.find_one({"_id": ObjectId(agent_id)})   → Agent found ✅
   d. new_conversation_doc(user_id, agent_id, agent_name)
      → Creates: { user_id, agent_id, agent_name, messages: [], started_at: now }
   e. db.conversations.insert_one(conv_doc)              → Conversation created
   f. Send "connected" message to client

3. Client sends audio → STT → LLM → TTS

4. For each message exchange:
   a. db.conversations.update_one({"_id": conv_id}, {"$push": {"messages": user_msg}})
   b. db.conversations.update_one({"_id": conv_id}, {"$push": {"messages": agent_msg}})

5. On disconnect:
   a. db.conversations.update_one({"_id": conv_id},
        {"$set": {"ended_at": now, "duration_seconds": 300}})
```

### Q42. Trace the data flow for: "Admin blocks a user"
**A.**
```
1. Frontend sends PATCH /api/admin/users/{user_id}/status
   Body: { "status": "blocked" }
   Header: Authorization: Bearer <admin_jwt_token>

2. Backend (routes/admin.py):
   a. Decode JWT → verify role == "admin"               → Admin confirmed ✅
   b. Validate ObjectId(user_id)                        → Valid ✅
   c. db.users.find_one({"_id": ObjectId(user_id)})    → User found ✅
   d. db.users.update_one(
        {"_id": ObjectId(user_id)},
        {"$set": {"status": "blocked", "is_active": false, "updated_at": now}}
      )

3. Next time the blocked user tries to:
   - Login → 403 "Your account has been blocked"
   - Use any API → 401 (get_current_user checks status)
   - Connect to WebSocket → Error + connection closed
```

---

## 📌 Section 12 — MongoDB vs SQL Comparison

### Q43. Show how key queries would look in SQL vs MongoDB.
**A.**

#### Find a user by email
```sql
-- SQL
SELECT * FROM users WHERE email = 'john@test.com' LIMIT 1;
```
```python
# MongoDB
user = await db.users.find_one({"email": "john@test.com"})
```

#### List users with pagination
```sql
-- SQL
SELECT * FROM users ORDER BY created_at DESC LIMIT 10 OFFSET 20;
```
```python
# MongoDB
cursor = db.users.find().sort("created_at", -1).skip(20).limit(10)
```

#### Update user status
```sql
-- SQL
UPDATE users SET status = 'blocked', is_active = false WHERE id = 123;
```
```python
# MongoDB
await db.users.update_one(
    {"_id": ObjectId("123")},
    {"$set": {"status": "blocked", "is_active": False}}
)
```

#### Count conversations today
```sql
-- SQL
SELECT COUNT(*) FROM conversations WHERE started_at >= CURDATE();
```
```python
# MongoDB
count = await db.conversations.count_documents({"started_at": {"$gte": start_of_today}})
```

#### Group by agent and count
```sql
-- SQL
SELECT agent_name, COUNT(*) as count FROM conversations GROUP BY agent_name ORDER BY count DESC LIMIT 5;
```
```python
# MongoDB
pipeline = [
    {"$group": {"_id": "$agent_name", "count": {"$sum": 1}}},
    {"$sort": {"count": -1}},
    {"$limit": 5}
]
```

---

## 📌 Section 13 — Common Viva Follow-ups

### Q44. Can a user be both a user and an admin?
**A.** No. Users and admins are stored in **separate collections** (`users` and `admins`). They have **separate login endpoints** (`/api/auth/login` vs `/api/admin/login`) and **separate JWT tokens** (with different `role` claims). This is a security design choice.

### Q45. What happens if the database goes down?
**A.** Motor will raise a connection error. The application will respond with 500 Internal Server Error. In production, you would:
1. Use a **MongoDB replica set** (3+ servers) for high availability.
2. Add retry logic for transient failures.
3. Use a connection pool (Motor supports this natively).

### Q46. Why store timestamps in UTC?
**A.** UTC (Coordinated Universal Time) is timezone-agnostic. By storing in UTC:
- All timestamps are consistent regardless of server location.
- The frontend can convert to the user's local timezone for display.
- Avoids daylight saving time issues.
```python
datetime.now(timezone.utc)  # Always UTC
```

### Q47. What is BSON?
**A.** BSON (Binary JSON) is MongoDB's storage format. It extends JSON with:
- **Date** type (ISODate) — JSON only has strings.
- **ObjectId** type — efficient 12-byte unique ID.
- **Binary** type — for binary data.
- **More efficient** storage and traversal than plain JSON text.

### Q48. How much data can a MongoDB document hold?
**A.** Maximum document size is **16 MB**. For our conversations collection, each message is ~200 bytes. A 16 MB document could hold ~80,000 messages — far more than any realistic voice conversation.

### Q49. What would you change for production deployment?
**A.**
1. **MongoDB Atlas** — cloud-hosted, managed MongoDB with backups and monitoring.
2. **Replica Set** — 3+ node cluster for high availability.
3. **Additional indexes** — on `conversations.user_id`, `conversations.agent_id`, `conversations.started_at` for faster queries.
4. **Connection pooling** — Motor handles this, but pool size should be tuned.
5. **Data validation** — MongoDB schema validation rules for extra safety.
6. **Backups** — automated daily backups with point-in-time recovery.

### Q50. What is the MongoDB shell command to see your data?
**A.**
```bash
mongosh                              # Start MongoDB shell
use college_project                  # Switch to your database
db.users.find().pretty()             # List all users
db.users.find({"email": "test@test.com"})  # Find specific user
db.agents.countDocuments()            # Count agents
db.conversations.find().sort({started_at: -1}).limit(5)  # Last 5 conversations
show collections                      # List all collections
db.users.getIndexes()                 # Show indexes on users
```

---

## 📌 Quick Reference — What to Say if Asked...

| If they ask... | Say this... |
|---|---|
| "What database?" | **MongoDB** — NoSQL, document-based |
| "Why not MySQL?" | Flexible schema, embedded documents (no joins for messages), TTL indexes, async driver for FastAPI |
| "What driver?" | **Motor** — async Python driver for MongoDB |
| "Database name?" | `college_project` |
| "How many collections?" | **6** — users, admins, agents, categories, conversations, otps |
| "What indexes?" | Unique index on `users.email`, `admins.email`; TTL index on `otps.expires_at` |
| "What is TTL index?" | Auto-deletes documents after a date field expires (used for OTPs) |
| "How are relations done?" | Store IDs as strings (user_id, agent_id); messages **embedded** in conversations |
| "Any joins?" | No JOIN needed — we use **embedding** (messages inside conversations) and **denormalization** (agent_name in conversations) |
| "How are passwords stored?" | **Bcrypt hashed** — never plain text, never returned in API responses |
| "How does pagination work?" | `skip()` + `limit()` + `count_documents()` + `sort()` |
| "How does search work?" | **Regex** with `$options: "i"` for case-insensitive |
| "Aggregation?" | Used for dashboard stats — `$group`, `$match`, `$sort`, `$sum`, `$limit` |
| "What is ObjectId?" | 12-byte unique identifier, auto-generated as `_id` for every document |
