# 🔐 LARAVEL AUTH PLAYBOOK — BCA SEM 6 (Gujarat University)

### DSC-C-364 P | Login & Registration Implementation in Laravel

> **Goal:** Walk in → Recognize pattern → Reproduce auth code → Pass viva → Maximum marks

---

## ⚡ PART 1: PATTERN ANALYSIS

### Top 8 Most Probable Auth Practicals

| Rank | Practical | Probability |
| --- | --- | --- |
| 1 | Registration Form + Validation + Store to DB | ⭐⭐⭐⭐⭐ |
| 2 | Login Form + Auth::attempt() + Session | ⭐⭐⭐⭐⭐ |
| 3 | Logout + Auth::logout() + Redirect | ⭐⭐⭐⭐⭐ |
| 4 | Protected Routes with `auth` Middleware | ⭐⭐⭐⭐⭐ |
| 5 | Dashboard Page (only visible after login) | ⭐⭐⭐⭐ |
| 6 | Show Logged-in User Name with Auth::user() | ⭐⭐⭐⭐ |
| 7 | Redirect if Already Logged In (guest middleware) | ⭐⭐⭐ |
| 8 | Password Hashing with bcrypt() / Hash::make() | ⭐⭐⭐⭐ |

---

### Must-Know Concepts (80/20 Rule)

**These 20% concepts appear in 80% of auth questions:**

1. **Auth Facade** → `Auth::attempt()`, `Auth::logout()`, `Auth::user()`, `Auth::id()`, `Auth::check()`
2. **Middleware** → `middleware('auth')` to protect routes, `middleware('guest')` to block logged-in users
3. **Password Hashing** → `Hash::make($password)` to store, `Hash::check()` to verify (or let Auth::attempt handle it)
4. **Validation** → `required`, `email`, `min:6`, `confirmed` (for password confirmation)
5. **Redirect** → `redirect()->route('dashboard')`, `redirect()->intended('dashboard')`
6. **Session** → Auth stores user in session automatically after `Auth::attempt()`
7. **User Model** → `app/Models/User.php` — already exists in Laravel, has `$fillable`
8. **Users Migration** → Already exists in Laravel — just run `php artisan migrate`

---

### Dependency Map (What Builds on What)

```
Step 1: Users Table (Migration already exists → just migrate)
    ↓
Step 2: User Model (already exists in app/Models/User.php)
    ↓
Step 3: AuthController (register, login, logout methods)
    ↓
Step 4: Routes (GET/POST for register, login, logout + protected dashboard)
    ↓
Step 5: Blade Views (register form, login form, dashboard)
    ↓
Step 6: Middleware (protect dashboard route with 'auth')
```

---

## ⚡ PART 2: MINIMUM VIABLE SETUP (Speed Strategy)

### Exam Day Commands — Copy These Exactly

```bash
# 1. Start server
php artisan serve

# 2. Create AuthController
php artisan make:controller AuthController

# 3. Run migrations (Users table already exists in fresh Laravel)
php artisan migrate

# 4. If users table already exists and you need to reset:
php artisan migrate:fresh
```

> ⚠️ **Do NOT use `php artisan ui` or `php artisan breeze:install`** unless the question specifically asks for it. Manual implementation shows more understanding and is exam-friendly.

---

### Fastest File Locations

| File | Path |
| --- | --- |
| Routes | `routes/web.php` |
| Controller | `app/Http/Controllers/AuthController.php` |
| User Model | `app/Models/User.php` |
| Users Migration | `database/migrations/xxxx_create_users_table.php` |
| Views | `resources/views/auth/` |
| .env | `.env` (root folder) |

### .env DB Setup (MySQL)

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=todoapp
DB_USERNAME=root
DB_PASSWORD=
```

---

## ⚡ PART 3: PRACTICAL TEMPLATES

---

### 🔹 TEMPLATE 1 — Routes (All Auth Routes)

**`routes/web.php`**

```php
<?php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthController;

// Guest routes (redirect to dashboard if already logged in)
Route::get('/register', [AuthController::class, 'showRegister'])->name('register')->middleware('guest');
Route::post('/register', [AuthController::class, 'register'])->middleware('guest');

Route::get('/login', [AuthController::class, 'showLogin'])->name('login')->middleware('guest');
Route::post('/login', [AuthController::class, 'login'])->middleware('guest');

// Logout
Route::post('/logout', [AuthController::class, 'logout'])->name('logout');

// Protected route — only logged-in users
Route::get('/dashboard', [AuthController::class, 'dashboard'])->name('dashboard')->middleware('auth');
```

**Common Mistakes:**
- Using `Route::get` for logout — always use `POST` for logout
- Forgetting `->middleware('auth')` on dashboard
- Not naming the login route `'login'` — Laravel's auth middleware redirects to this by default

---

### 🔹 TEMPLATE 2 — AuthController (Full)

**`app/Http/Controllers/AuthController.php`**

```php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use App\Models\User;

class AuthController extends Controller
{
    // Show registration form
    public function showRegister()
    {
        return view('auth.register');
    }

    // Handle registration
    public function register(Request $request)
    {
        $request->validate([
            'name'                  => 'required',
            'email'                 => 'required|email|unique:users',
            'password'              => 'required|min:6|confirmed',
        ]);

        User::create([
            'name'     => $request->name,
            'email'    => $request->email,
            'password' => Hash::make($request->password),
        ]);

        return redirect()->route('login')->with('success', 'Registration successful! Please login.');
    }

    // Show login form
    public function showLogin()
    {
        return view('auth.login');
    }

    // Handle login
    public function login(Request $request)
    {
        $request->validate([
            'email'    => 'required|email',
            'password' => 'required',
        ]);

        $credentials = $request->only('email', 'password');

        if (Auth::attempt($credentials)) {
            $request->session()->regenerate();
            return redirect()->route('dashboard')->with('success', 'Welcome back, ' . Auth::user()->name . '!');
        }

        return redirect()->back()->withErrors([
            'email' => 'Invalid email or password.',
        ])->withInput();
    }

    // Dashboard (protected)
    public function dashboard()
    {
        return view('auth.dashboard');
    }

    // Logout
    public function logout(Request $request)
    {
        Auth::logout();
        $request->session()->invalidate();
        $request->session()->regenerateToken();
        return redirect()->route('login')->with('success', 'Logged out successfully.');
    }
}
```

**Common Mistakes:**
- Forgetting `use Illuminate\Support\Facades\Auth;`
- Forgetting `use Illuminate\Support\Facades\Hash;`
- Forgetting `use App\Models\User;`
- Storing plain text password — always use `Hash::make()`
- Not calling `$request->session()->regenerate()` after login (security issue, but also asked in viva)

---

### 🔹 TEMPLATE 3 — User Model

**`app/Models/User.php`** (already exists — just verify `$fillable`)

```php
<?php
namespace App\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    protected $fillable = [
        'name',
        'email',
        'password',
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];
}
```

> ✅ In a fresh Laravel project, this file already exists and is correct. Just confirm `$fillable` has `name`, `email`, `password`.

---

### 🔹 TEMPLATE 4 — Registration View

**`resources/views/auth/register.blade.php`**

```php
<!DOCTYPE html>
<html>
<head><title>Register</title></head>
<body>
    <h2>Create Account</h2>

    {{-- Success Message --}}
    @if(session('success'))
        <p style="color:green;">{{ session('success') }}</p>
    @endif

    {{-- Validation Errors --}}
    @if($errors->any())
        <div style="color:red;">
            <ul>
                @foreach($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        </div>
    @endif

    <form action="/register" method="POST">
        @csrf

        <label>Name:</label><br>
        <input type="text" name="name" value="{{ old('name') }}"><br><br>

        @error('name')
            <div style="color:red;">{{ $message }}</div>
        @enderror

        <label>Email:</label><br>
        <input type="email" name="email" value="{{ old('email') }}"><br><br>

        @error('email')
            <div style="color:red;">{{ $message }}</div>
        @enderror

        <label>Password:</label><br>
        <input type="password" name="password"><br><br>

        @error('password')
            <div style="color:red;">{{ $message }}</div>
        @enderror

        <label>Confirm Password:</label><br>
        <input type="password" name="password_confirmation"><br><br>

        <input type="submit" value="Register">
    </form>

    <p>Already have an account? <a href="{{ route('login') }}">Login here</a></p>
</body>
</html>
```

**Common Mistakes:**
- Confirm password field MUST be named `password_confirmation` (not `confirm_password`) — Laravel's `confirmed` rule looks for this exact name
- Forgetting `@csrf`
- Using `old()` on password fields — never do this for security

---

### 🔹 TEMPLATE 5 — Login View

**`resources/views/auth/login.blade.php`**

```php
<!DOCTYPE html>
<html>
<head><title>Login</title></head>
<body>
    <h2>Login</h2>

    {{-- Success/Info Message --}}
    @if(session('success'))
        <p style="color:green;">{{ session('success') }}</p>
    @endif

    {{-- Login Error --}}
    @if($errors->has('email'))
        <p style="color:red;">{{ $errors->first('email') }}</p>
    @endif

    <form action="/login" method="POST">
        @csrf

        <label>Email:</label><br>
        <input type="email" name="email" value="{{ old('email') }}"><br><br>

        <label>Password:</label><br>
        <input type="password" name="password"><br><br>

        <input type="submit" value="Login">
    </form>

    <p>Don't have an account? <a href="{{ route('register') }}">Register here</a></p>
</body>
</html>
```

---

### 🔹 TEMPLATE 6 — Dashboard View (Protected Page)

**`resources/views/auth/dashboard.blade.php`**

```php
<!DOCTYPE html>
<html>
<head><title>Dashboard</title></head>
<body>
    <h2>Welcome, {{ Auth::user()->name }}!</h2>
    <p>You are logged in as: {{ Auth::user()->email }}</p>

    @if(session('success'))
        <p style="color:green;">{{ session('success') }}</p>
    @endif

    <hr>
    <p>This is your protected dashboard. Only logged-in users can see this.</p>

    <form action="{{ route('logout') }}" method="POST">
        @csrf
        <button type="submit">Logout</button>
    </form>
</body>
</html>
```

**Common Mistakes:**
- Using `Auth::user()->name` without importing `Auth` in Blade — not needed in Blade, the facade works directly
- Making logout a GET link (`<a href="/logout">`) — must be POST form with `@csrf`

---

## ⚡ PART 4: COMPLETE AUTH SYSTEM — Step-by-Step

> **This single flow solves every auth question. Memorize the order.**

---

### Step 1: Verify Users Migration Exists

```bash
# Check if migration file exists:
ls database/migrations/
# Look for: xxxx_create_users_table.php
# If yes → just run:
php artisan migrate
```

If not present (rare):

```php
// database/migrations/xxxx_create_users_table.php
public function up(): void
{
    Schema::create('users', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email')->unique();
        $table->string('password');
        $table->rememberToken();
        $table->timestamps();
    });
}
```

---

### Step 2: Confirm User Model

```php
// app/Models/User.php
protected $fillable = ['name', 'email', 'password'];
```

---

### Step 3: Create AuthController

```bash
php artisan make:controller AuthController
```

Paste the full controller from Template 2 above.

---

### Step 4: Define All Routes

Paste Template 1 routes into `routes/web.php`.

---

### Step 5: Create Views Folder + Files

```bash
mkdir resources/views/auth
```

Create these 3 files:
- `resources/views/auth/register.blade.php` → Template 4
- `resources/views/auth/login.blade.php` → Template 5
- `resources/views/auth/dashboard.blade.php` → Template 6

---

### Step 6: Test in Browser

```
http://localhost:8000/register   → Fill form → Submit → Redirects to login
http://localhost:8000/login      → Enter credentials → Redirects to dashboard
http://localhost:8000/dashboard  → Shows user name + logout button
                                 → If not logged in, redirects to /login automatically
```

---

## ⚡ PART 5: VIVA PREPARATION — 25 Auth Questions

### Authentication Basics

**Q1: What is authentication in Laravel?**
> Authentication is verifying who a user is — matching their email/password against the database. Laravel uses the `Auth` facade for this.

**Q2: What does `Auth::attempt()` do?**
> It takes an array of credentials (email, password), checks them against the `users` table, hashes the password for comparison, and logs the user in if they match. Returns `true` or `false`.

**Q3: What is the difference between Authentication and Authorization?**
> Authentication = verifying identity (who are you?). Authorization = verifying permissions (what can you do?). Login is authentication. Middleware restricting pages is authorization.

**Q4: Where does Laravel store the logged-in user after login?**
> In the **session**. `Auth::attempt()` automatically writes the user's ID to the session.

**Q5: What is `Auth::user()`?**
> Returns the currently authenticated User model object. You can use `Auth::user()->name`, `Auth::user()->email`, etc.

**Q6: What is `Auth::check()`?**
> Returns `true` if a user is logged in, `false` otherwise. Useful in Blade: `@if(Auth::check())`.

**Q7: What is `Auth::id()`?**
> Returns the ID of the currently logged-in user.

---

### Password Hashing

**Q8: Why do we use `Hash::make()` for passwords?**
> Passwords must never be stored as plain text. `Hash::make()` uses bcrypt to create a secure one-way hash. Even if the DB is leaked, passwords are unreadable.

**Q9: Does `Auth::attempt()` hash the password automatically?**
> Yes. You pass the plain text password to `Auth::attempt()` and it handles hashing and comparison internally using `Hash::check()`.

**Q10: What is `bcrypt()` vs `Hash::make()`?**
> Both do the same thing. `bcrypt($password)` is a helper function. `Hash::make($password)` uses the `Hash` facade. `Hash::make()` is preferred as it respects the configured hashing driver.

---

### Middleware

**Q11: What is middleware in Laravel?**
> Middleware is code that runs between the HTTP request and the controller. It can allow, block, or redirect requests.

**Q12: What does `middleware('auth')` do?**
> It checks if the user is logged in. If not, it redirects them to the named route `'login'`. That's why the login route MUST be named `'login'`.

**Q13: What does `middleware('guest')` do?**
> It blocks logged-in users from accessing that route (e.g., register/login pages). If already logged in, they're redirected to `/home` or `/dashboard`.

**Q14: How do you apply middleware to a route?**
> `Route::get('/dashboard', [AuthController::class, 'dashboard'])->middleware('auth');`

---

### Registration

**Q15: What validation rule ensures password and confirm password match?**
> `'password' => 'required|confirmed'`. The confirm field in the form MUST be named `password_confirmation`.

**Q16: What does `unique:users` do in validation?**
> `'email' => 'required|email|unique:users'` checks that the email doesn't already exist in the `users` table. If it does, validation fails with an error.

**Q17: What does `withInput()` do on a redirect?**
> `redirect()->back()->withInput()` sends the old form data back so `old('email')` can repopulate fields after a failed login.

---

### Logout

**Q18: How do you log a user out in Laravel?**
> Call `Auth::logout()`, then `$request->session()->invalidate()` to destroy the session, then `$request->session()->regenerateToken()` to reset the CSRF token.

**Q19: Why must logout be a POST request?**
> To prevent CSRF attacks. A malicious site could embed a GET link that logs the user out silently. POST requires a valid `@csrf` token.

---

### Session & Security

**Q20: Why call `$request->session()->regenerate()` after login?**
> To prevent **Session Fixation attacks** — where an attacker uses a known session ID to hijack a session after the user logs in.

**Q21: What is `redirect()->intended('dashboard')`?**
> If the user was redirected to login from a protected page, `intended()` sends them back to that original page after login. Falls back to `'dashboard'` if no intended URL exists.

---

### Blade Auth Helpers

**Q22: How do you show content only to logged-in users in Blade?**
```php
@auth
    <p>Welcome, {{ Auth::user()->name }}</p>
@endauth
```

**Q23: How do you show content only to guests (not logged in)?**
```php
@guest
    <a href="/login">Login</a>
@endguest
```

**Q24: Difference between `@auth` and `Auth::check()`?**
> `@auth` is a Blade directive (shorthand). `Auth::check()` is a PHP method — both check if the user is logged in. `@auth` is cleaner in Blade templates.

---

### Trap Questions ⚠️

**Q25 (TRAP): Why does my dashboard show even when not logged in?**
> You forgot `->middleware('auth')` on the dashboard route.

**TRAP: Why does login always fail even with correct credentials?**
> Password was stored as plain text (forgot `Hash::make()`). `Auth::attempt()` compares hashed values — it will always fail against plain text.

**TRAP: Why does the confirm password validation fail?**
> Your confirm field is named `confirm_password` instead of `password_confirmation`. Laravel's `confirmed` rule specifically looks for `{field}_confirmation`.

**TRAP: Why am I getting 419 error on logout?**
> Logout form is missing `@csrf`. Every POST form needs `@csrf`.

**TRAP: Why is middleware('auth') redirecting to `/login` and not my custom login page?**
> The route for your login page must be named exactly `'login'`. Change to `->name('login')`.

---

## ⚡ PART 6: 2-HOUR EXAM STRATEGY

### First 10 Minutes

1. Read the question — identify if it's: registration only, login only, or full auth system
2. Run `php artisan migrate` first — get the DB ready immediately
3. Create AuthController: `php artisan make:controller AuthController`
4. Create the `resources/views/auth/` folder
5. Start server: `php artisan serve`

### Time Allocation

| Task | Time |
| --- | --- |
| Read + Plan | 5 min |
| Routes in web.php | 5 min |
| AuthController (all methods) | 20 min |
| Register Blade View | 10 min |
| Login Blade View | 8 min |
| Dashboard Blade View | 5 min |
| Browser Testing | 7 min |
| **Total** | **60 min** |

### How to Avoid Getting Stuck

| Problem | Fix |
| --- | --- |
| Login always fails | Password stored as plain text — use `Hash::make()` |
| 419 error on any form | Add `@csrf` inside the form |
| Dashboard accessible without login | Add `->middleware('auth')` to the route |
| `Auth::user()` returns null | User is not logged in — check middleware |
| `unique:users` error on re-register | Use a different email, or `php artisan migrate:fresh` |
| Redirect goes to `/home` not `/dashboard` | Change `HOME` constant in `RouteServiceProvider` or use `redirect()->route('dashboard')` |
| `Class Auth not found` | Add `use Illuminate\Support\Facades\Auth;` at top of controller |

---

## ⚡ PART 7: LAST-MINUTE REVISION CHEAT SHEET

```
╔══════════════════════════════════════════════════════════════════════╗
║         LARAVEL AUTH CHEAT SHEET — BCA SEM 6                        ║
╠══════════════════════════════════════════════════════════════════════╣
║ COMMANDS                                                             ║
║  php artisan serve                          → Start server           ║
║  php artisan make:controller AuthController → Create controller      ║
║  php artisan migrate                        → Run users migration    ║
║  php artisan migrate:fresh                  → Reset all tables       ║
╠══════════════════════════════════════════════════════════════════════╣
║ ROUTES (web.php)                                                     ║
║  Route::get('/register', [AuthController::class, 'showRegister'])   ║
║        ->name('register')->middleware('guest');                      ║
║  Route::post('/register', [AuthController::class, 'register'])      ║
║        ->middleware('guest');                                        ║
║  Route::get('/login', [AuthController::class, 'showLogin'])         ║
║        ->name('login')->middleware('guest');                         ║
║  Route::post('/login', [AuthController::class, 'login'])            ║
║        ->middleware('guest');                                        ║
║  Route::post('/logout', [AuthController::class, 'logout'])          ║
║        ->name('logout');                                             ║
║  Route::get('/dashboard', [AuthController::class, 'dashboard'])     ║
║        ->name('dashboard')->middleware('auth');                      ║
╠══════════════════════════════════════════════════════════════════════╣
║ CONTROLLER — KEY LINES                                               ║
║  use Illuminate\Support\Facades\Auth;     → Import Auth             ║
║  use Illuminate\Support\Facades\Hash;     → Import Hash             ║
║  use App\Models\User;                     → Import User model        ║
║                                                                      ║
║  Auth::attempt($credentials)             → Login (true/false)       ║
║  Auth::logout()                          → Logout                   ║
║  Auth::user()                            → Get logged-in user       ║
║  Auth::check()                           → Is user logged in?       ║
║  Auth::id()                              → Get logged-in user ID    ║
║  Hash::make($password)                   → Hash password            ║
║  $request->session()->regenerate()       → After login              ║
║  $request->session()->invalidate()       → After logout             ║
╠══════════════════════════════════════════════════════════════════════╣
║ VALIDATION RULES                                                     ║
║  'name'     => 'required'                                            ║
║  'email'    => 'required|email|unique:users'    → Registration       ║
║  'email'    => 'required|email'                 → Login              ║
║  'password' => 'required|min:6|confirmed'       → Registration       ║
║  'password' => 'required'                       → Login              ║
║  Confirm field name MUST be: password_confirmation                   ║
╠══════════════════════════════════════════════════════════════════════╣
║ BLADE SYNTAX                                                         ║
║  @csrf                            → In EVERY form                    ║
║  @auth ... @endauth               → Show only to logged-in users     ║
║  @guest ... @endguest             → Show only to guests              ║
║  {{ Auth::user()->name }}         → Show user's name                 ║
║  {{ Auth::user()->email }}        → Show user's email                ║
║  @error('field') {{ $message }} @enderror  → Show field error       ║
║  @if(session('success')) ... @endif        → Show flash message      ║
║  old('email')                     → Repopulate input after error     ║
╠══════════════════════════════════════════════════════════════════════╣
║ MIDDLEWARE                                                            ║
║  middleware('auth')    → Redirect to login if not logged in          ║
║  middleware('guest')   → Redirect to dashboard if already logged in  ║
╠══════════════════════════════════════════════════════════════════════╣
║ COMMON ERRORS & FIXES                                                ║
║  Login always fails     → Password not hashed (use Hash::make)       ║
║  419 Error              → Missing @csrf in form                      ║
║  Dashboard open to all  → Missing ->middleware('auth') on route      ║
║  Confirm pass fails     → Field name must be password_confirmation   ║
║  Auth class not found   → Add: use Facades\Auth; in controller       ║
║  Redirect to /home      → Use redirect()->route('dashboard')         ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 📁 QUICK REFERENCE: FILE STRUCTURE FOR AUTH

```
todoapp/
├── routes/
│   └── web.php                          ← All auth routes here
├── app/
│   ├── Http/
│   │   └── Controllers/
│   │       └── AuthController.php       ← showRegister, register,
│   │                                       showLogin, login,
│   │                                       dashboard, logout
│   └── Models/
│       └── User.php                     ← Already exists, verify $fillable
├── database/
│   └── migrations/
│       └── xxxx_create_users_table.php  ← Already exists in Laravel
├── resources/
│   └── views/
│       └── auth/
│           ├── register.blade.php       ← Registration form
│           ├── login.blade.php          ← Login form
│           └── dashboard.blade.php      ← Protected page after login
└── .env                                 ← DB credentials
```

---

*Prepared for Gujarat University BCA Sem 6 — DSC-C-364 P*
*Companion to: LARAVEL\_EXAM\_PLAYBOOK.md*
*Focus: Manual Authentication (without Breeze/Jetstream/UI scaffolding)*
