# 🎓 Frontend Viva Q&A — AI Voice Agent Platform

> **Project**: AI Voice Agent Platform (College Project)  
> **Frontend Stack**: Next.js 16 · React 19 · TypeScript · TailwindCSS v4  
> **Command to run**: `cd frontend && npm run dev`  
> **App URL**: `http://localhost:3000`  
> **Backend API URL**: `http://localhost:8000/api`

---

## 📌 Section 1 — Project Overview

### Q1. What is this frontend application?
**A.** This is the frontend for an **AI Voice Agent Platform**. It has three main areas:
1. **Landing Page** (`/`) — public marketing page with hero section, features, and workflow.
2. **User Dashboard** (`/dashboard`) — registered users browse AI agents, have voice conversations, view history, and manage their profile.
3. **Admin Panel** (`/admin/dashboard`) — administrators create/manage agents, manage users, view analytics.

### Q2. What is the tech stack used in the frontend?
**A.**
| Technology | Purpose |
|-----------|---------|
| **Next.js 16** | React framework with App Router, file-based routing, SSR |
| **React 19** | UI library (latest version) |
| **TypeScript** | Type-safe JavaScript |
| **TailwindCSS v4** | Utility-first CSS framework for styling |
| **WebSocket API** | Real-time voice chat communication |
| **Web Audio API** | Browser microphone recording & audio processing |
| **localStorage** | JWT token and user data persistence |

### Q3. Why Next.js instead of plain React (Vite)?
**A.** Next.js gives us:
1. **File-based routing** — folders automatically become URL paths (no React Router needed).
2. **App Router** — nested layouts, route groups, loading states out of the box.
3. **Server Components** — landing page is server-rendered for better SEO and performance.
4. **Built-in optimizations** — image optimization, code splitting, font optimization.

---

## 📌 Section 2 — Project Structure

### Q4. Explain the folder structure of the frontend.
**A.**
```
frontend/src/
├── app/                          → Pages (file-based routing)
│   ├── page.tsx                  → Landing page (/)
│   ├── layout.tsx                → Root layout (HTML, metadata, global CSS)
│   ├── globals.css               → Global CSS + Tailwind imports
│   │
│   ├── login/page.tsx            → /login
│   ├── register/page.tsx         → /register
│   ├── forgot-password/page.tsx  → /forgot-password
│   ├── reset-password/page.tsx   → /reset-password
│   ├── terms/page.tsx            → /terms
│   ├── privacy/page.tsx          → /privacy
│   │
│   ├── (dashboard)/              → Route Group (user dashboard)
│   │   ├── layout.tsx            → Dashboard layout (with sidebar)
│   │   └── dashboard/
│   │       ├── page.tsx              → /dashboard (user home)
│   │       ├── agents/page.tsx       → /dashboard/agents (browse agents)
│   │       ├── voice-chat/page.tsx   → /dashboard/voice-chat (live voice UI)
│   │       ├── history/page.tsx      → /dashboard/history
│   │       ├── profile/page.tsx      → /dashboard/profile
│   │       └── settings/page.tsx     → /dashboard/settings
│   │
│   └── admin/                    → Admin panel
│       ├── page.tsx              → /admin (admin login)
│       ├── layout.tsx            → Admin login layout (no sidebar)
│       └── dashboard/
│           ├── layout.tsx            → Admin dashboard layout (with sidebar)
│           ├── page.tsx              → /admin/dashboard (admin home)
│           ├── agents/               → Agent management pages
│           │   ├── page.tsx          → /admin/dashboard/agents (list)
│           │   ├── create/page.tsx   → /admin/dashboard/agents/create
│           │   └── edit/[id]/page.tsx → /admin/dashboard/agents/edit/[id]
│           ├── users/page.tsx        → /admin/dashboard/users
│           ├── logs/page.tsx         → /admin/dashboard/logs
│           └── settings/page.tsx     → /admin/dashboard/settings
│
├── components/                   → Reusable UI components
│   ├── ui/                       → Generic UI (Button, Input, Copyright)
│   ├── layout/                   → Layout pieces (Header, Footer, Container)
│   ├── landing/                  → Landing page sections (Hero, Features, Workflow)
│   ├── dashboard/                → User dashboard (Sidebar, DashboardHeader)
│   └── admin/                    → Admin panel (AdminSidebar, AdminHeader)
│
├── services/                     → API service layer
│   └── api.ts                    → Centralized API calls (all endpoints)
│
├── config/                       → Configuration
│   └── env.ts                    → Environment variables
│
└── styles/                       → CSS tokens and typography
    ├── tokens.css                → Design tokens (CSS variables)
    └── typography.css            → Font imports and text styles
```

### Q5. What is a "Route Group" and why is `(dashboard)` in parentheses?
**A.** In Next.js App Router, a folder name wrapped in parentheses like `(dashboard)` is a **Route Group**. It creates a **shared layout** without affecting the URL path.
- `(dashboard)/dashboard/page.tsx` → URL is `/dashboard` (not `/(dashboard)/dashboard`)
- The `(dashboard)/layout.tsx` adds the **Sidebar** to all user dashboard pages.
- The voice-chat page uses the same route group but conditionally hides the sidebar.

### Q6. How does the layout system work?
**A.** Next.js uses **nested layouts**:
```
Root Layout (app/layout.tsx)
  ├── Landing page — no sidebar, just Header
  ├── Login/Register — standalone, no sidebar
  ├── (dashboard)/layout.tsx — adds Sidebar for all user pages
  │   ├── /dashboard — user home
  │   ├── /dashboard/agents — browse agents
  │   └── /dashboard/voice-chat — voice chat (sidebar hidden conditionally)
  └── admin/dashboard/layout.tsx — adds AdminSidebar for admin pages
      ├── /admin/dashboard — admin home
      ├── /admin/dashboard/agents — agent management
      └── /admin/dashboard/users — user management
```
Each layout wraps its children — so the sidebar is added once and persists across page navigation.

### Q7. What is `"use client"` and why is it on most pages?
**A.** Next.js 16 uses **React Server Components** by default — they render on the server and can't use browser APIs, hooks, or event handlers.

`"use client"` is added at the top of a file to make it a **Client Component** — it runs in the browser and can use:
- `useState`, `useEffect`, `useRef` hooks
- `onClick`, `onChange` event handlers
- Browser APIs like `localStorage`, `WebSocket`, `navigator.mediaDevices`

Almost all our pages are client components because they use interactive forms, API calls, and state management.

---

## 📌 Section 3 — API Service Layer

### Q8. How does the frontend communicate with the backend?
**A.** All API calls go through a **centralized API service** in `services/api.ts`. It provides:
- `authApi` — login, register, forgot password, verify OTP, reset password
- `userApi` — profile CRUD, dashboard stats, photo upload
- `agentsApi` — public agent listing, categories
- `adminApi` — admin login, user/agent management, dashboard stats, AI generation
- `conversationsApi` — conversation history listing

### Q9. How does the API request function work?
**A.** There's a generic `request<T>()` wrapper function:
```typescript
async function request<T>(
    endpoint: string,
    options: RequestInit = {},
    tokenKey: string = "user_access_token"
): Promise<T> {
    const url = `${API_URL}${endpoint}`;  // e.g., http://localhost:8000/api/auth/login

    // Auto-attach JWT token from localStorage
    const token = localStorage.getItem(tokenKey);
    if (token) {
        config.headers = { ...config.headers, Authorization: `Bearer ${token}` };
    }

    const response = await fetch(url, config);

    // Auto-logout on 401 Unauthorized
    if (response.status === 401) {
        localStorage.removeItem(tokenKey);
        window.location.href = isAdmin ? "/admin" : "/login";
    }

    return response.json();
}
```

**Key features:**
1. **Auto-attaches JWT** from localStorage to every request.
2. **Auto-logout on 401** — if token expires, removes it and redirects to login.
3. **Generic typing** — `request<LoginResponse>("/auth/login", ...)` ensures type safety.
4. **Separate token keys** — user tokens use `"user_access_token"`, admin tokens use `"admin_access_token"`.

### Q10. Where is the JWT token stored?
**A.** In the browser's **localStorage**:
```typescript
// On login
localStorage.setItem("user_access_token", response.access_token);
localStorage.setItem("user", JSON.stringify(response.user));

// On logout
localStorage.removeItem("user_access_token");
localStorage.removeItem("user");
```

### Q11. What is the `API_URL` and how is it configured?
**A.** Defined in `services/api.ts`:
```typescript
const API_URL = process.env.NEXT_PUBLIC_API_URL || "http://localhost:8000/api";
```
- `NEXT_PUBLIC_` prefix makes the env variable available in the browser.
- Default is `http://localhost:8000/api` for local development.
- Configured in `.env.local` file.

---

## 📌 Section 4 — Form Validation

### Q12. How is form validation done in the frontend?
**A.** We use **client-side validation** with React state. Here's the pattern used across all forms:

```typescript
const [errors, setErrors] = useState({ email: "", password: "" });

const validateForm = () => {
    let isValid = true;
    const newErrors = { email: "", password: "" };

    // Email validation
    if (!formData.email) {
        newErrors.email = "Email is required";
        isValid = false;
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
        newErrors.email = "Please enter a valid email";
        isValid = false;
    }

    // Password validation
    if (!formData.password) {
        newErrors.password = "Password is required";
        isValid = false;
    }

    setErrors(newErrors);
    return isValid;
};
```

### Q13. What validations are done on the registration form?
**A.**
| Field | Validation Rules |
|-------|-----------------|
| Full Name | Required |
| Email | Required + regex format check (`/\S+@\S+\.\S+/`) |
| Mobile | Required + exactly 10 digits + only numeric characters allowed |
| Password | Required + minimum 8 characters |
| Confirm Password | Required + must match password |
| Terms Checkbox | Must be checked |

**Mobile number special handling:**
```typescript
if (field === "mobile") {
    const numericValue = value.replace(/[^0-9]/g, "");  // strips non-numeric chars
    finalValue = numericValue;
}
```

### Q14. How are validation errors displayed?
**A.** Each `<Input>` component receives an `error` prop:
```tsx
<Input
    label="Email"
    type="email"
    value={formData.email}
    onChange={(e) => { setFormData({...formData, email: e.target.value}); }}
    error={errors.email}  // ← Error message shown below the input
/>
```
The `Input` component renders the error text in red below the field. Errors are **cleared live** as the user types (via the `onChange` handler).

### Q15. How do you handle API errors (e.g., "email already exists")?
**A.** We have separate state for API errors:
```typescript
const [apiError, setApiError] = useState("");

try {
    await authApi.register(data);
    router.push("/login?registered=true");
} catch (error: any) {
    setApiError(error.message || "Registration failed");
}
```
API errors are displayed at the top of the form in a red banner and cleared when the user starts typing.

### Q16. What does `noValidate` on the form mean?
**A.** `<form noValidate>` disables the browser's built-in HTML5 validation (the default tooltips). We do this because we have our **own custom validation** with better error messages and real-time feedback.

---

## 📌 Section 5 — Voice Chat Page

### Q17. How does the voice chat interface work?
**A.** The voice chat page (`/dashboard/voice-chat`) is the **most complex page** in the application. It:
1. Opens a **WebSocket** connection to the backend.
2. Records audio from the user's microphone using the **Web Audio API**.
3. Converts the recording to a **WAV file** (16kHz, mono, 16-bit PCM).
4. Sends the audio to the backend as **base64** over the WebSocket.
5. Receives the AI's text response + audio response back.
6. Plays the audio response automatically.
7. Displays the full transcript in a chat-like UI.

### Q18. How does WebSocket connection work in the frontend?
**A.**
```typescript
const connectWebSocket = () => {
    const token = localStorage.getItem("user_access_token");
    const wsUrl = "http://localhost:8000/api"
        .replace("http://", "ws://")  // Convert to WebSocket URL
        .replace("https://", "wss://");

    const ws = new WebSocket(`${wsUrl}/chat/${agentId}?token=${token}`);

    ws.onopen = () => setConnectionStatus("connected");

    ws.onmessage = (event) => {
        const data = JSON.parse(event.data);
        switch (data.type) {
            case "connected":   // Agent info received
            case "status":      // "Thinking...", "Processing..."
            case "response":    // AI response with text + audio
            case "error":       // Error message
        }
    };

    ws.onclose = () => setConnectionStatus("disconnected");
};
```

### Q19. How does microphone recording work?
**A.** We use the **Web Audio API** (not MediaRecorder):
```typescript
const startRecording = async () => {
    // 1. Get microphone access
    const stream = await navigator.mediaDevices.getUserMedia({
        audio: { channelCount: 1, echoCancellation: true, noiseSuppression: true }
    });

    // 2. Create audio processing pipeline
    const audioContext = new AudioContext();  // usually 44100 or 48000 Hz
    const source = audioContext.createMediaStreamSource(stream);
    const processor = audioContext.createScriptProcessor(4096, 1, 1);

    // 3. Collect raw PCM audio data
    processor.onaudioprocess = (e) => {
        const channelData = e.inputBuffer.getChannelData(0);
        pcmChunksRef.current.push(new Float32Array(channelData));
    };

    source.connect(processor);
    processor.connect(audioContext.destination);
};
```

### Q20. Why do you downsample the audio to 16kHz?
**A.** The browser records at the device's native sample rate (usually **44,100 Hz or 48,000 Hz**), but the Sarvam STT API expects **16,000 Hz** (16kHz). So we downsample using linear interpolation:
```typescript
function downsample(buffer: Float32Array, fromRate: number, toRate: number): Float32Array {
    const ratio = fromRate / toRate;  // e.g., 48000/16000 = 3
    const newLength = Math.round(buffer.length / ratio);
    const result = new Float32Array(newLength);
    for (let i = 0; i < newLength; i++) {
        const srcIndex = i * ratio;
        const lower = Math.floor(srcIndex);
        const upper = Math.min(lower + 1, buffer.length - 1);
        const frac = srcIndex - lower;
        result[i] = buffer[lower] * (1 - frac) + buffer[upper] * frac;
    }
    return result;
}
```

### Q21. How do you create a WAV file from raw audio samples?
**A.** We manually write the **WAV file header** (44 bytes) followed by the PCM audio data:
```typescript
function encodeWav(samples: Float32Array, sampleRate: number): ArrayBuffer {
    const buffer = new ArrayBuffer(44 + samples.length * 2);
    const view = new DataView(buffer);

    // Write WAV header: "RIFF", file size, "WAVE", "fmt ", PCM format,
    //                    mono channel, sample rate, "data", data size
    writeStr(0, "RIFF");
    view.setUint32(4, 36 + samples.length * 2, true);
    writeStr(8, "WAVE");
    // ... (44 bytes of header)

    // Write 16-bit PCM samples
    for (let i = 0; i < samples.length; i++) {
        const s = Math.max(-1, Math.min(1, samples[i]));
        view.setInt16(44 + i * 2, s < 0 ? s * 0x8000 : s * 0x7FFF, true);
    }
    return buffer;
}
```

### Q22. How does the audio playback work?
**A.** When the server sends back a base64-encoded audio response:
```typescript
// 1. Convert base64 to a Blob URL
function base64ToAudioUrl(base64: string): string {
    const binary = atob(base64);
    const bytes = new Uint8Array(binary.length);
    for (let i = 0; i < binary.length; i++)
        bytes[i] = binary.charCodeAt(i);
    const blob = new Blob([bytes], { type: "audio/wav" });
    return URL.createObjectURL(blob);
}

// 2. Play using HTML5 Audio
const audio = new Audio(audioUrl);
audio.onended = () => setAgentStatus("idle");
audio.play();
```

### Q23. What are the different states of the voice chat?
**A.**
| State | UI Indicator | Meaning |
|-------|-------------|---------|
| `idle` | Blue mic button | Ready to record |
| `recording` | Red pulsing mic + waveform bars | User is speaking |
| `processing` | Bouncing dots + "Thinking..." | STT + LLM processing |
| `speaking` | "Speaking..." status | Playing AI audio response |
| `disconnected` | "Reconnect" button | WebSocket closed |

### Q24. How does the language selection work?
**A.** When the WebSocket connects, the server sends the agent's supported languages. The user can switch languages via a dropdown. The selected language is sent with every audio message:
```typescript
const LANG_MAP = {
    "English (US)": "en-IN",
    "Hindi": "hi-IN",
    "Gujarati": "gu-IN",
    "Marathi": "mr-IN",
    // ... 10 Indian languages
};

ws.send(JSON.stringify({
    type: "audio",
    data: base64Audio,
    language: selectedLanguage,  // e.g., "hi-IN"
    provider: llmProvider,       // "gemini" or "groq"
}));
```

### Q25. Can users also type text messages?
**A.** Yes. There's a text input at the bottom that sends messages directly (skipping STT):
```typescript
ws.send(JSON.stringify({
    type: "text",
    text: "Hello, how are you?",
    language: "en-IN",
    provider: "gemini",
}));
```
This is useful for debugging or when the user prefers typing.

---

## 📌 Section 6 — Admin Panel

### Q26. How does the admin login work?
**A.** Admin login is separate from user login:
- Admin login page is at `/admin`.
- Calls `POST /api/admin/login` (different endpoint from user login).
- Token is stored under a **different key**: `admin_access_token` (vs `user_access_token`).
- This ensures admin and user sessions are completely independent.

### Q27. What can the admin do?
**A.**
1. **Dashboard** — View total users, agents, conversations today, top agents chart, daily conversations chart.
2. **Agent Management** — Create, edit, delete, pause/activate AI agents.
3. **User Management** — List users (with search + pagination), view user details, block/unblock users, view user's conversations.
4. **Conversation Logs** — View all conversations globally, read full conversation transcripts.
5. **Settings** — Change admin password.

### Q28. How does agent creation work with AI generation?
**A.** The admin has two options to create an agent:
1. **Manual** — Fill in all fields (name, category, system prompt, etc.) manually.
2. **AI-Generated** — Enter a simple prompt like "Create a Hindi language tutor":
   - Calls `POST /api/generate/agent-config` → Gemini returns a complete agent config (name, category, description, system_prompt, languages, voice_id, image_prompt).
   - Then calls `POST /api/generate/agent-image` → Imagen 4 generates a professional avatar.
   - All fields are auto-filled, admin can review and edit before saving.

### Q29. How does the agent edit page work with dynamic routes?
**A.** The edit page uses **Next.js dynamic routes**:
- File: `admin/dashboard/agents/edit/[id]/page.tsx`
- URL: `/admin/dashboard/agents/edit/abc123`
- `[id]` in the folder name becomes a route parameter.
- The page fetches the agent data on mount using this ID:
```typescript
const params = useParams();
const agentId = params.id as string;

useEffect(() => {
    adminApi.getAgent(agentId).then(setAgent);
}, [agentId]);
```

### Q30. How does multi-part form data work for agent creation?
**A.** Agent creation includes an **image upload**, so we can't use JSON. We use **FormData**:
```typescript
const formData = new FormData();
formData.append("name", data.name);
formData.append("category", data.category);
formData.append("languages", JSON.stringify(data.languages)); // array → JSON string
formData.append("voice_enabled", String(data.voice_enabled)); // boolean → string
if (data.image) formData.append("image", data.image);         // File object

const response = await fetch(`${API_URL}/admin/agents`, {
    method: "POST",
    headers: { Authorization: `Bearer ${token}` },
    body: formData,  // No Content-Type header — browser sets it with boundary
});
```
**Important**: We do NOT set `Content-Type: application/json` for FormData — the browser automatically sets the correct `multipart/form-data` header with the boundary.

---

## 📌 Section 7 — User Dashboard

### Q31. What does the user dashboard show?
**A.** The `/dashboard` page displays:
1. **Stats Cards** — Total Conversations, Active Agents, Voice Minutes.
2. **Recent Activity** — Last 5 conversations with agent name, last message, time ago.
3. **Top Agents** — Most frequently used agents.

All data comes from `GET /api/users/me/dashboard-stats`.

### Q32. How does the agent browsing page work?
**A.** The `/dashboard/agents` page:
- Lists all **active** agents (paused agents are hidden from users).
- Supports **search** by name.
- Supports **category filter** via dropdown.
- Supports **language filter**.
- Uses **pagination** with page controls.
- Each agent card shows name, category, description, supported languages.
- Clicking an agent navigates to the voice chat page: `/dashboard/voice-chat?agent_id=...`

### Q33. How does the profile page work?
**A.** The `/dashboard/profile` page allows users to:
1. **View** their profile info (name, email, mobile, profile photo).
2. **Edit** name, email, mobile — calls `PUT /api/users/me`.
3. **Upload profile photo** — calls `POST /api/users/me/photo` with FormData.
4. **Change password** — requires current password + new password → calls `POST /api/users/me/change-password`.

### Q34. How does the conversation history page work?
**A.** The `/dashboard/history` page:
- Lists all past conversations with agent name, date, message count, duration.
- Supports search by agent name and pagination.
- Clicking a conversation opens its full transcript (all messages with timestamps).

---

## 📌 Section 8 — Components

### Q35. What reusable components are there?
**A.**
| Component | File | Purpose |
|-----------|------|---------|
| `Button` | `ui/Button.tsx` | Styled button with variants and sizes |
| `Input` | `ui/Input.tsx` | Form input with label, error display, show/hide password |
| `Copyright` | `ui/Copyright.tsx` | Footer copyright text |
| `Header` | `layout/Header.tsx` | Landing page navigation bar |
| `Footer` | `layout/Footer.tsx` | Landing page footer |
| `Container` | `layout/Container.tsx` | Content width container |
| `Sidebar` | `dashboard/Sidebar.tsx` | User dashboard navigation sidebar |
| `AdminSidebar` | `admin/AdminSidebar.tsx` | Admin panel navigation sidebar |
| `Hero` | `landing/Hero.tsx` | Landing page hero section |
| `Features` | `landing/Features.tsx` | Landing page features section |
| `Workflow` | `landing/Workflow.tsx` | Landing page workflow section |

### Q36. How does the custom Input component handle password visibility?
**A.** The `Input` component has built-in show/hide toggle for password fields:
```tsx
{type === "password" && (
    <button onClick={() => setShowPassword(!showPassword)}>
        {showPassword ? <EyeOffIcon /> : <EyeIcon />}
    </button>
)}
<input type={showPassword ? "text" : "password"} ... />
```

### Q37. How does the Sidebar work?
**A.** The Sidebar component:
- Renders navigation links (Dashboard, Agents, Voice Chat, History, Profile, Settings).
- Highlights the **active page** using `usePathname()`:
```typescript
const pathname = usePathname();
const isActive = pathname === item.href;
// Active link gets blue background, others are gray
```
- Has a **logout button** that clears localStorage and redirects to `/login`.
- Is conditionally hidden on the voice-chat page (full-screen experience).

---

## 📌 Section 9 — Routing & Navigation

### Q38. How does navigation work?
**A.** Next.js provides two ways:
1. **`<Link href="/dashboard">`** — for declarative navigation (renders `<a>` tag with client-side navigation).
2. **`useRouter().push("/login")`** — for programmatic navigation (after form submission, logout, etc.):
```typescript
const router = useRouter();
await authApi.login(data);
router.push("/dashboard");  // Navigate after successful login
```

### Q39. How do you handle protected routes?
**A.** Currently done at the API level — if a user tries to access a protected page without a token:
1. The API call returns `401 Unauthorized`.
2. The `request()` function auto-clears the token and redirects to `/login`.
3. The sidebar checks for token presence and shows/hides accordingly.

### Q40. What are the public vs protected pages?
**A.**
| Public (no login needed) | Protected (login required) |
|---|---|
| `/` (landing) | `/dashboard` (user home) |
| `/login` | `/dashboard/agents` |
| `/register` | `/dashboard/voice-chat` |
| `/forgot-password` | `/dashboard/history` |
| `/reset-password` | `/dashboard/profile` |
| `/terms` | `/dashboard/settings` |
| `/privacy` | `/admin/dashboard/*` |

---

## 📌 Section 10 — Styling & Design

### Q41. How is the UI styled?
**A.** We use **TailwindCSS v4** — a utility-first CSS framework. Instead of writing custom CSS classes, you compose styles directly in the HTML:
```tsx
<div className="flex min-h-screen items-center justify-center bg-gray-50/50 p-4">
    <div className="w-full max-w-md rounded-2xl bg-white p-8 shadow-lg ring-1 ring-gray-900/5">
```
- `flex`, `items-center` → Flexbox centering
- `bg-gray-50/50` → Gray background with 50% opacity
- `rounded-2xl` → Large border radius
- `shadow-lg` → Large box shadow
- `ring-1 ring-gray-900/5` → Subtle border ring

### Q42. What design patterns are used?
**A.**
- **Glassmorphism** — `backdrop-blur-md bg-white/80` on headers for frosted glass effect.
- **Card-based layout** — content wrapped in white cards with shadow and rounded corners.
- **Responsive design** — `max-w-md`, `max-w-3xl` containers that adapt to screen size.
- **Micro-animations** — `animate-pulse` for recording indicator, `animate-bounce` for loading dots, `animate-spin` for loading spinners.
- **Color-coded status** — green for active/online, red for blocked/recording, yellow for paused.

---

## 📌 Section 11 — State Management

### Q43. How do you manage state?
**A.** We use **React's built-in state management** (no Redux or external libraries):
- `useState` — for local component state (form data, errors, loading flags).
- `useRef` — for values that don't need re-renders (WebSocket ref, audio ref, MediaStream ref).
- `useEffect` — for side effects (API calls on mount, WebSocket connection, auto-scroll).
- `useCallback` — for memoizing functions (WebSocket connect function).

### Q44. Give an example of useRef usage.
**A.** In the voice chat page, we use refs for values that persist across renders without triggering re-renders:
```typescript
const wsRef = useRef<WebSocket | null>(null);         // WebSocket connection
const streamRef = useRef<MediaStream | null>(null);   // Microphone stream
const audioContextRef = useRef<AudioContext | null>(null); // Audio processing
const pcmChunksRef = useRef<Float32Array[]>([]);      // Recorded audio chunks
const chatEndRef = useRef<HTMLDivElement>(null);       // Auto-scroll target
const audioRef = useRef<HTMLAudioElement | null>(null); // Currently playing audio
```
If these were `useState`, every audio chunk would cause a re-render — making the UI laggy.

---

## 📌 Section 12 — TypeScript Interfaces

### Q45. What TypeScript interfaces are defined?
**A.** In `services/api.ts`:
```typescript
interface LoginResponse {
    access_token: string;
    token_type: string;
    user: { id, full_name, email, mobile, ... };
}

interface UserProfile { id, full_name, email, mobile, role, profile_photo, ... }
interface AdminUser { id, full_name, email, status, ... }
interface Agent { id, name, category, description, languages, voice_id, system_prompt, ... }
interface Category { id, name, created_at }
interface ConversationListItem { id, agent_name, message_count, duration_seconds, ... }
interface ConversationDetail { id, agent_name, messages: ConversationMessage[], ... }
interface DashboardStatsResponse { total_users, total_agents, conversations_today, ... }
interface UserDashboardStatsResponse { total_conversations, active_agents, voice_minutes, ... }
```
These match the Pydantic schemas on the backend — ensuring type consistency.

### Q46. Why use TypeScript?
**A.** TypeScript adds **static type checking** to JavaScript:
1. Catches errors at compile time (before running the code).
2. Better IDE auto-completion and documentation.
3. API response types ensure we don't access undefined properties.
4. Makes the code more readable and maintainable.

---

## 📌 Section 13 — Forgot Password Flow (Frontend)

### Q47. How does the forgot password flow work on the frontend?
**A.** Three pages, three steps:

1. **`/forgot-password`** — User enters their email.
   - Calls `authApi.forgotPassword(email)`.
   - On success, navigates to `/reset-password?email=user@example.com`.

2. **`/reset-password`** — User enters the OTP + new password.
   - First calls `authApi.verifyOtp({ email, otp })` to check if OTP is valid.
   - Then calls `authApi.resetPassword({ email, otp, new_password })` to reset.
   - On success, navigates to `/login`.

The email is passed between pages via **URL query parameters**.

---

## 📌 Section 14 — Landing Page

### Q48. What sections does the landing page have?
**A.**
1. **Header** — Navigation bar with logo, links, Login/Register buttons.
2. **Hero** — Main headline, description, CTA (call-to-action) buttons.
3. **Features** — Feature cards showing platform capabilities (Voice AI, Multi-language, Analytics, etc.).
4. **Workflow** — Step-by-step how-it-works section.

### Q49. Is the landing page server-rendered?
**A.** Yes. The landing page (`app/page.tsx`) is a **Server Component** — it doesn't have `"use client"` at the top. This means:
- It renders on the server for better SEO (search engines can read the content).
- Faster initial page load (no JavaScript needed to render).
- The Header component uses `"use client"` only for the mobile menu toggle.

---

## 📌 Section 15 — Deployment & Environment

### Q50. How do you run the frontend?
**A.**
```bash
cd frontend
npm install          # Install dependencies (first time)
npm run dev          # Start development server at http://localhost:3000
```
For production:
```bash
npm run build        # Creates optimized production build
npm run start        # Starts production server
```

### Q51. What are the environment variables?
**A.** In `.env.local`:
```
NEXT_PUBLIC_API_URL=http://localhost:8000/api
```
The `NEXT_PUBLIC_` prefix is required for environment variables that need to be available in the browser.

### Q52. What dependencies does the frontend have?
**A.**
| Package | Version | Purpose |
|---------|---------|---------|
| `next` | 16.1.6 | React framework |
| `react` | 19.2.3 | UI library |
| `react-dom` | 19.2.3 | DOM rendering |
| `tailwindcss` | v4 | CSS framework |
| `typescript` | v5 | Type checking |
| `eslint` | v9 | Code linting |

---

## 📌 Section 16 — Additional Technical Questions

### Q53. What is `useSearchParams` and where is it used?
**A.** It's a Next.js hook that reads URL query parameters. Used in:
- **Voice Chat**: `const agentId = useSearchParams().get("agent_id");` — gets the agent to chat with from the URL.
- **Reset Password**: Gets the email from `?email=...` URL parameter.

It requires wrapping in `<Suspense>` because it triggers client-side rendering.

### Q54. What is `Suspense` and why is it used on the voice chat page?
**A.** `<Suspense>` is a React component that shows a **fallback UI** while its children are loading:
```tsx
<Suspense fallback={<LoadingSpinner />}>
    <VoiceChatInner />
</Suspense>
```
It's required because `useSearchParams()` makes the component need to read the URL on the client side—Next.js requires Suspense boundary for this.

### Q55. What happens when the user logs out?
**A.**
```typescript
const handleLogout = () => {
    localStorage.removeItem("user_access_token");
    localStorage.removeItem("user");
    window.location.href = "/login";
};
```
Clears all stored data and redirects to login. For admin: removes `admin_access_token` and redirects to `/admin`.

---

## 📌 Quick Reference — What to Say if Asked...

| If they ask... | Say this... |
|---|---|
| "What frontend framework?" | **Next.js 16** with App Router + **React 19** + **TypeScript** |
| "How is it styled?" | **TailwindCSS v4** — utility-first CSS classes |
| "How does routing work?" | **File-based routing** — folders become URLs |
| "How does auth work frontend-side?" | **JWT stored in localStorage**, auto-attached to every API request |
| "How does the voice chat work?" | **WebSocket + Web Audio API → WAV encoding → base64 → backend → audio back** |
| "Where are all API calls?" | Centralized in **`services/api.ts`** with typed interfaces |
| "How is validation done?" | **Client-side with React state** — regex for email, length checks for password |
| "How do layouts work?" | **Nested layouts** in the App Router — sidebar added once, wraps all child pages |
| "What is a route group?" | Folders in parentheses `(dashboard)` — shared layout without affecting the URL |
| "How do you handle errors?" | **try/catch** with `apiError` state displayed in red banners |
| "Is it responsive?" | Yes — uses `max-w-md`, `max-w-3xl` containers and Tailwind responsive utilities |
| "Server vs Client components?" | Landing page = **Server Component** (SEO), Dashboard pages = **Client Components** (interactivity) |
