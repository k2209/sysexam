# 🔥 LARAVEL EXAM PLAYBOOK — BCA SEM 6 (Gujarat University)
### DSC-C-364 P | Web Application Development Framework Using Laravel
> **Goal:** Walk in → Recognize pattern → Reproduce code → Pass viva → Maximum marks

---

## ⚡ PART 1: PATTERN ANALYSIS

### Top 10 Most Probable Practicals

| Rank | Practical | Phase | Probability |
|------|-----------|-------|-------------|
| 1 | Full CRUD (Create, Read, Update, Delete) with DB | V | ⭐⭐⭐⭐⭐ |
| 2 | Form + CSRF + Validation + Flash Message | III | ⭐⭐⭐⭐⭐ |
| 3 | Controller with index(), create(), store() | IV | ⭐⭐⭐⭐⭐ |
| 4 | Blade Master Layout (@extends, @yield, @section) | II | ⭐⭐⭐⭐ |
| 5 | Resource Controller + Route::resource() | IV | ⭐⭐⭐⭐ |
| 6 | Dynamic Routing with Parameters | I | ⭐⭐⭐⭐ |
| 7 | @foreach, @if, @unless in Blade | II | ⭐⭐⭐ |
| 8 | Migration + Model + Eloquent (store/fetch) | V | ⭐⭐⭐⭐⭐ |
| 9 | Session Flash Messages | III/IV | ⭐⭐⭐ |
| 10 | @include (reusable header/footer) | II | ⭐⭐⭐ |

---

### Must-Know Concepts (80/20 Rule)

**These 20% concepts appear in 80% of exam questions:**

1. **Route** → `Route::get()`, `Route::post()`, named routes, resource routes
2. **Controller** → `php artisan make:controller`, index/store/create/edit/update/destroy
3. **Blade** → `{{ }}`, `@foreach`, `@if`, `@extends`, `@yield`, `@section`, `@csrf`
4. **Validation** → `$request->validate()`, `@error`, `$errors->any()`
5. **Eloquent** → `Model::create()`, `Model::all()`, `Model::find($id)`, `->update()`, `->delete()`
6. **Migration** → `php artisan make:migration`, Schema::create, `php artisan migrate`
7. **Session** → `session()->flash('success', 'msg')`, `session('success')`
8. **Redirect** → `redirect()->back()`, `redirect()->route('name')`

---

### Dependency Map (What Builds on What)

```
Phase I: Routes
    ↓
Phase II: Blade Views (needs routes to pass data)
    ↓
Phase III: Forms + CSRF + Validation (needs routes + blade + controller)
    ↓
Phase IV: Controllers + Sessions (needs all above)
    ↓
Phase V: Database + Models + CRUD (needs all above + migration)
```

---

## ⚡ PART 2: MINIMUM VIABLE SETUP (Speed Strategy)

### Exam Day Commands — Copy These Exactly

```bash
# 1. Create project (if needed — usually pre-installed in lab)
composer create-project laravel/laravel todoapp
cd todoapp

# 2. Start server
php artisan serve

# 3. Create controller
php artisan make:controller TaskController

# 4. Create model + migration together
php artisan make:model Task -m

# 5. Run migration
php artisan migrate

# 6. Create resource controller (if asked)
php artisan make:controller TaskController --resource
```

### Fastest File Locations

| File | Path |
|------|------|
| Routes | `routes/web.php` |
| Controller | `app/Http/Controllers/TaskController.php` |
| Model | `app/Models/Task.php` |
| Migration | `database/migrations/xxxx_create_tasks_table.php` |
| Views | `resources/views/` |
| Layout | `resources/views/layouts/master.blade.php` |
| .env | `.env` (root folder) |

### .env DB Setup (MySQL)

```env
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

### 🔹 TEMPLATE 1 — Basic Routing (Phase I)
**Q: Create routes for `/welcome`, `/tasks`, and `/task/{name}`**
**Time: 5 min**

**`routes/web.php`**
```php
<?php
use Illuminate\Support\Facades\Route;

// Simple message
Route::get('/welcome', function () {
    return "Welcome to Laravel!";
});

// Hardcoded array
Route::get('/tasks', function () {
    $tasks = ['Task 1', 'Task 2', 'Task 3'];
    return $tasks;
});

// Dynamic parameter
Route::get('/task/{name}', function ($name) {
    return "Task: " . $name;
});

// Multiple parameters
Route::get('/task/{name}/{priority}', function ($name, $priority) {
    echo "Task: " . $name . "<br>";
    echo "Priority: " . $priority;
});
```

**Common Mistakes:**
- Forgetting `use Illuminate\Support\Facades\Route;`
- Using `echo` inside return (use one or the other)

---

### 🔹 TEMPLATE 2 — Blade with Data (Phase II)
**Q: Create a Blade view to display a list of tasks passed from route**
**Time: 10 min**

**`routes/web.php`**
```php
Route::get('/tasklist', function () {
    $tasks = ['Study Laravel', 'Complete Assignment', 'Build TodoApp'];
    return view('tasklist', compact('tasks'));
});
```

**`resources/views/tasklist.blade.php`**
```html
<!DOCTYPE html>
<html>
<head><title>Task List</title></head>
<body>
    <h2>My Tasks</h2>
    <ul>
        @foreach($tasks as $task)
            <li>{{ $task }}</li>
        @endforeach
    </ul>

    @unless(count($tasks))
        <p style="color:red;">No tasks available.</p>
    @endunless
</body>
</html>
```

**Common Mistakes:**
- Using `<?php echo ?>` instead of `{{ }}`
- Forgetting `@endforeach`

---

### 🔹 TEMPLATE 3 — Master Layout (Phase II)
**Q: Create a master layout with @yield and use @extends in child views**
**Time: 12 min**

**`resources/views/layouts/master.blade.php`**
```html
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'My App')</title>
</head>
<body>
    <header>
        <h1>My Todo Application</h1>
        <nav><a href="/">Home</a> | <a href="/tasks">Tasks</a></nav>
        <hr>
    </header>

    <div>
        @yield('content')
    </div>

    <hr>
    <footer>
        <p>&copy; 2026 My Todo App</p>
    </footer>
</body>
</html>
```

**`resources/views/home.blade.php`**
```html
@extends('layouts.master')

@section('title', 'Home Page')

@section('content')
    <h2>Welcome to Home Page</h2>
    <p>This is my first Laravel application.</p>
@endsection
```

**Route:**
```php
Route::get('/home', function () {
    return view('home');
});
```

**Common Mistakes:**
- Wrong path: `@extends('layouts/master')` — use dot notation: `@extends('layouts.master')`
- Forgetting `@endsection`

---

### 🔹 TEMPLATE 4 — Form + CSRF + Validation (Phase III)
**Q: Create a form to add a task. Validate title as required. Show errors. Flash success.**
**Time: 20 min**

**`routes/web.php`**
```php
use App\Http\Controllers\TaskController;

Route::get('/tasks/create', [TaskController::class, 'create']);
Route::post('/tasks/store', [TaskController::class, 'store'])->name('tasks.store');
```

**`app/Http/Controllers/TaskController.php`**
```php
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;

class TaskController extends Controller
{
    public function create()
    {
        return view('tasks.create');
    }

    public function store(Request $request)
    {
        $request->validate([
            'title'       => 'required',
            'description' => 'required',
        ]);

        session()->flash('success', 'Task added successfully!');
        return redirect()->back();
    }
}
```

**`resources/views/tasks/create.blade.php`**
```html
<!DOCTYPE html>
<html>
<head><title>Create Task</title></head>
<body>
    <h3>Add Task</h3>

    {{-- Success Message --}}
    @if(session('success'))
        <div style="color:green; font-weight:bold;">
            {{ session('success') }}
        </div>
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

    <form action="{{ route('tasks.store') }}" method="POST">
        @csrf
        <label>Title:</label>
        <input type="text" name="title"><br><br>

        @error('title')
            <div style="color:red;">{{ $message }}</div>
        @enderror

        <label>Description:</label>
        <input type="text" name="description"><br><br>

        <input type="submit" value="Add Task">
    </form>
</body>
</html>
```

**Common Mistakes:**
- Forgetting `@csrf` → 419 error
- Route name mismatch in `route('tasks.store')`
- No `@error ... @enderror` closing tag

---

### 🔹 TEMPLATE 5 — Controller + Session (Phase IV)
**Q: Use store() to save task in session, show in index()**
**Time: 15 min**

**`app/Http/Controllers/TaskController.php`**
```php
public function index()
{
    $tasks = session('tasks', []);
    return view('tasks.index', compact('tasks'));
}

public function store(Request $request)
{
    $request->validate(['title' => 'required']);

    $tasks = session('tasks', []);
    $tasks[] = $request->title;
    session(['tasks' => $tasks]);

    session()->flash('success', 'Task added!');
    return redirect()->back();
}
```

---

### 🔹 TEMPLATE 6 — Resource Controller (Phase IV)
**Q: Define resourceful routes using Route::resource()**
**Time: 5 min (just routes + command)**

```bash
php artisan make:controller TaskController --resource
```

**`routes/web.php`**
```php
use App\Http\Controllers\TaskController;
Route::resource('tasks', TaskController::class);
```

**This auto-creates these routes:**
| Method | URI | Action |
|--------|-----|--------|
| GET | /tasks | index |
| GET | /tasks/create | create |
| POST | /tasks | store |
| GET | /tasks/{id} | show |
| GET | /tasks/{id}/edit | edit |
| PUT | /tasks/{id} | update |
| DELETE | /tasks/{id} | destroy |

---

## ⚡ PART 4: UNIVERSAL CRUD TEMPLATE

> **This single template solves 70% of exam questions. Memorize this.**

---

### Step 1: Commands
```bash
php artisan make:model Task -m
php artisan make:controller TaskController --resource
# Edit migration file, then:
php artisan migrate
```

---

### Step 2: Migration
**`database/migrations/xxxx_create_tasks_table.php`**
```php
public function up(): void
{
    Schema::create('tasks', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->string('description')->nullable();
        $table->string('status')->default('pending');
        $table->timestamps();
    });
}
```

---

### Step 3: Model
**`app/Models/Task.php`**
```php
<?php
namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class Task extends Model
{
    protected $fillable = ['title', 'description', 'status'];
}
```

---

### Step 4: Routes
**`routes/web.php`**
```php
use App\Http\Controllers\TaskController;
Route::resource('tasks', TaskController::class);
```

---

### Step 5: Controller (Full CRUD)
**`app/Http/Controllers/TaskController.php`**
```php
<?php
namespace App\Http\Controllers;
use App\Models\Task;
use Illuminate\Http\Request;

class TaskController extends Controller
{
    // READ ALL
    public function index()
    {
        $tasks = Task::all();
        return view('tasks.index', compact('tasks'));
    }

    // SHOW FORM
    public function create()
    {
        return view('tasks.create');
    }

    // SAVE TO DB
    public function store(Request $request)
    {
        $request->validate([
            'title'       => 'required',
            'description' => 'required',
        ]);

        Task::create([
            'title'       => $request->title,
            'description' => $request->description,
            'status'      => 'pending',
        ]);

        return redirect()->route('tasks.index')->with('success', 'Task created!');
    }

    // SHOW EDIT FORM
    public function edit(string $id)
    {
        $task = Task::find($id);
        return view('tasks.edit', compact('task'));
    }

    // UPDATE IN DB
    public function update(Request $request, string $id)
    {
        $task = Task::find($id);
        $task->update([
            'title'       => $request->title,
            'description' => $request->description,
            'status'      => $request->status,
        ]);
        return redirect()->route('tasks.index')->with('success', 'Task updated!');
    }

    // DELETE FROM DB
    public function destroy(string $id)
    {
        $task = Task::find($id);
        $task->delete();
        return redirect()->route('tasks.index')->with('success', 'Task deleted!');
    }
}
```

---

### Step 6: Blade Views

**`resources/views/tasks/index.blade.php`**
```html
<!DOCTYPE html>
<html>
<head><title>All Tasks</title></head>
<body>
    <h2>Task List</h2>
    <a href="{{ route('tasks.create') }}">+ Add Task</a>
    <br><br>

    @if(session('success'))
        <p style="color:green;">{{ session('success') }}</p>
    @endif

    <table border="1" cellpadding="8">
        <tr>
            <th>ID</th><th>Title</th><th>Description</th><th>Status</th><th>Actions</th>
        </tr>
        @foreach($tasks as $task)
        <tr>
            <td>{{ $task->id }}</td>
            <td>{{ $task->title }}</td>
            <td>{{ $task->description }}</td>
            <td>{{ $task->status }}</td>
            <td>
                <a href="{{ route('tasks.edit', $task->id) }}">Edit</a> |
                <form action="{{ route('tasks.destroy', $task->id) }}" method="POST" style="display:inline;">
                    @csrf
                    @method('DELETE')
                    <button type="submit" onclick="return confirm('Delete?')">Delete</button>
                </form>
            </td>
        </tr>
        @endforeach
    </table>
</body>
</html>
```

**`resources/views/tasks/create.blade.php`**
```html
<!DOCTYPE html>
<html>
<head><title>Create Task</title></head>
<body>
    <h2>Add New Task</h2>

    @if($errors->any())
        <div style="color:red;">
            @foreach($errors->all() as $error)
                <p>{{ $error }}</p>
            @endforeach
        </div>
    @endif

    <form action="{{ route('tasks.store') }}" method="POST">
        @csrf
        <label>Title:</label><br>
        <input type="text" name="title" value="{{ old('title') }}"><br><br>

        <label>Description:</label><br>
        <input type="text" name="description" value="{{ old('description') }}"><br><br>

        <input type="submit" value="Save Task">
    </form>
    <a href="{{ route('tasks.index') }}">Back</a>
</body>
</html>
```

**`resources/views/tasks/edit.blade.php`**
```html
<!DOCTYPE html>
<html>
<head><title>Edit Task</title></head>
<body>
    <h2>Edit Task</h2>

    <form action="{{ route('tasks.update', $task->id) }}" method="POST">
        @csrf
        @method('PUT')

        <label>Title:</label><br>
        <input type="text" name="title" value="{{ $task->title }}"><br><br>

        <label>Description:</label><br>
        <input type="text" name="description" value="{{ $task->description }}"><br><br>

        <label>Status:</label><br>
        <select name="status">
            <option value="pending"   {{ $task->status == 'pending'   ? 'selected' : '' }}>Pending</option>
            <option value="completed" {{ $task->status == 'completed' ? 'selected' : '' }}>Completed</option>
        </select><br><br>

        <input type="submit" value="Update Task">
    </form>
    <a href="{{ route('tasks.index') }}">Back</a>
</body>
</html>
```

---

## ⚡ PART 5: VIVA PREPARATION — 30 Questions

### MVC & Architecture

**Q1: What is MVC in Laravel?**
> MVC = Model-View-Controller. Model handles database, View handles UI, Controller handles logic between them.

**Q2: What is the role of a Controller?**
> Controller receives HTTP requests, processes them (calls model if needed), and returns a view or redirect.

**Q3: Where are routes defined in Laravel?**
> In `routes/web.php` for web routes.

**Q4: What is the default folder structure of Laravel?**
> `app/` (logic), `resources/views/` (blade), `routes/` (routes), `database/migrations/` (DB schema), `public/` (entry point).

---

### Routing

**Q5: What is a named route and why use it?**
> `->name('tasks.store')`. Used to generate URLs: `route('tasks.store')`. Avoids hardcoding URLs.

**Q6: Difference between Route::get() and Route::post()?**
> `GET` is for reading/displaying data. `POST` is for submitting form data.

**Q7: What is Route::resource()?**
> Automatically creates all 7 RESTful routes (index, create, store, show, edit, update, destroy) for a controller.

**Q8: How to pass multiple parameters in a route?**
> `Route::get('/task/{name}/{priority}', function($name, $priority) { ... });`

---

### Blade

**Q9: What is `{{ }}` in Blade?**
> Echoes and escapes output. Equivalent to `<?= htmlspecialchars($var) ?>`. Prevents XSS.

**Q10: Difference between `{{ }}` and `{!! !!}`?**
> `{{ }}` escapes HTML (safe). `{!! !!}` outputs raw HTML (unsafe, only for trusted content).

**Q11: What is @yield and @section?**
> `@yield('slot')` in master layout defines a placeholder. `@section('slot') ... @endsection` in child view fills it.

**Q12: What is @extends?**
> `@extends('layouts.master')` tells a blade view to inherit from the master layout.

**Q13: What is @include?**
> `@include('partial.header')` inserts another blade file's content at that point.

**Q14: What is @csrf?**
> Generates a hidden `_token` input field to protect forms from Cross-Site Request Forgery attacks. Laravel validates this on every POST.

---

### CSRF & Security

**Q15: What happens if you forget @csrf in a form?**
> Laravel returns a `419 | Page Expired` error.

**Q16: What is CSRF?**
> Cross-Site Request Forgery — an attack where a malicious site tricks a logged-in user into submitting unauthorized requests. `@csrf` prevents this.

---

### Validation

**Q17: How do you validate form input in Laravel?**
> `$request->validate(['title' => 'required', 'description' => 'required|min:5']);`

**Q18: How do you show validation errors in Blade?**
> Using `@error('fieldname') {{ $message }} @enderror` or `$errors->all()`.

**Q19: What is `old()` in Blade?**
> `old('title')` repopulates a form field with the previously submitted value after validation fails.

---

### Eloquent & Database

**Q20: What is Eloquent ORM?**
> Laravel's built-in ORM that maps database tables to PHP model classes. `Task` model → `tasks` table.

**Q21: What is `$fillable` in a model?**
> An array of column names that are mass-assignable. Protects against mass assignment vulnerabilities.

**Q22: What is a migration?**
> A PHP file that defines database table structure. Run with `php artisan migrate`.

**Q23: Difference between `Task::create()` and `new Task()→save()`?**
> `Task::create()` is shorter (needs `$fillable`). `new Task(); $task->title = '...'; $task->save();` is explicit.

**Q24: How to fetch all records?**
> `Task::all()` — returns a collection of all rows.

**Q25: How to find a record by ID?**
> `Task::find($id)` — returns single model or null.

---

### Sessions & Redirects

**Q26: What is a flash message?**
> A session value that exists only for the next request. `session()->flash('success', 'Done!')` — shown once, then gone.

**Q27: Difference between `redirect()->back()` and `redirect()->route('name')`?**
> `back()` redirects to previous URL. `route('name')` redirects to a named route.

---

### Relationships

**Q28: What is hasMany relationship?**
> One User has many Tasks. Defined in `User` model: `return $this->hasMany(Task::class);`

**Q29: What is belongsTo relationship?**
> Each Task belongs to one User. Defined in `Task` model: `return $this->belongsTo(User::class);`

---

### Trap Questions ⚠️

**Q30 (TRAP): Can you use `Route::resource()` and also define individual routes for the same controller?**
> Yes, but the individual routes must be defined BEFORE `Route::resource()` to avoid conflicts.

**TRAP: Why is my POST form returning 405 error?**
> Your route is defined as `GET` but form uses `method="POST"`. Match them.

**TRAP: Why does @method('PUT') / @method('DELETE') exist?**
> HTML forms only support GET and POST. `@method('PUT')` adds a hidden `_method` field so Laravel can simulate PUT/DELETE.

**TRAP: What if `Task::create()` says "Add [title] to fillable property"?**
> You forgot to add the column to `$fillable` array in the model.

---

## ⚡ PART 6: 2-HOUR EXAM STRATEGY

### First 10 Minutes
1. **Read BOTH questions** fully before coding anything
2. Identify which phase each question belongs to (I–V)
3. Pick the question you are more confident about — do it first
4. Open these files immediately: `web.php`, controller, blade view
5. Run `php artisan serve` and keep browser open at `localhost:8000`

### Question Selection Strategy
- If one question is **CRUD with DB** → do it (it's the most marks-rich)
- If one question is **Blade/Routing** → it's faster, do it first to warm up
- Avoid spending more than **90 min on Q1** — always attempt Q2

### Time Allocation
| Task | Time |
|------|------|
| Read + Plan | 5 min |
| Routes | 5 min |
| Controller | 15 min |
| Migration + Model | 10 min |
| Views (Blade) | 20 min |
| Testing in browser | 5 min |
| **Total** | **60 min per question** |

### How to Avoid Getting Stuck
- **CSRF error (419)?** → Add `@csrf` inside form
- **Class not found?** → Check `use App\Models\Task;` at top of controller
- **View not found?** → Check dot notation: `'tasks.create'` = `resources/views/tasks/create.blade.php`
- **Column not in table?** → Add to migration AND `$fillable`, re-run migrate
- **Route not found?** → Run `php artisan route:list` to see all routes

### Fast Debugging
```bash
php artisan route:list          # See all routes
php artisan migrate:fresh       # Drop all tables + re-migrate (use when stuck on DB)
php artisan cache:clear         # Clear config cache
```

---

## ⚡ PART 7: LAST-MINUTE REVISION CHEAT SHEET

```
╔══════════════════════════════════════════════════════════════════════╗
║           LARAVEL EXAM CHEAT SHEET — BCA SEM 6                      ║
╠══════════════════════════════════════════════════════════════════════╣
║ COMMANDS                                                             ║
║  php artisan serve                        → Start server             ║
║  php artisan make:controller TaskController → Create controller      ║
║  php artisan make:model Task -m           → Model + migration        ║
║  php artisan migrate                      → Run migrations           ║
║  php artisan migrate:fresh                → Drop all + re-migrate    ║
║  php artisan route:list                   → See all routes           ║
╠══════════════════════════════════════════════════════════════════════╣
║ ROUTES (web.php)                                                     ║
║  Route::get('/path', fn() => view('name'));                          ║
║  Route::get('/path', [Controller::class, 'method']);                 ║
║  Route::post('/path', [Controller::class, 'store'])->name('x');     ║
║  Route::resource('tasks', TaskController::class);                   ║
║  Route::get('/task/{id}', [Controller::class, 'show']);             ║
╠══════════════════════════════════════════════════════════════════════╣
║ CONTROLLER                                                           ║
║  use App\Models\Task;                     → Import model             ║
║  Task::all()                              → Fetch all                ║
║  Task::find($id)                          → Fetch one                ║
║  Task::create([...])                      → Insert (needs fillable)  ║
║  $task->update([...])                     → Update                   ║
║  $task->delete()                          → Delete                   ║
║  return view('tasks.index', compact('tasks'));  → Pass to view       ║
║  return redirect()->route('tasks.index'); → Redirect                 ║
║  ->with('success', 'Done!')               → Flash message            ║
║  $request->validate(['title'=>'required']); → Validate               ║
╠══════════════════════════════════════════════════════════════════════╣
║ BLADE SYNTAX                                                         ║
║  {{ $var }}          → Echo (escaped)                                ║
║  @foreach($items as $item) ... @endforeach                          ║
║  @if($x) ... @else ... @endif                                       ║
║  @unless($x) ... @endunless                                         ║
║  @extends('layouts.master')                                         ║
║  @section('content') ... @endsection                                ║
║  @yield('content')                                                  ║
║  @include('partial.header')                                         ║
║  @csrf            → In every POST form                               ║
║  @method('PUT')   → For update forms                                 ║
║  @method('DELETE') → For delete forms                               ║
║  @error('field') {{ $message }} @enderror                           ║
║  @if($errors->any()) ... @endif                                     ║
║  @if(session('success')) {{ session('success') }} @endif            ║
╠══════════════════════════════════════════════════════════════════════╣
║ MODEL (app/Models/Task.php)                                          ║
║  protected $fillable = ['title', 'description', 'status'];          ║
║  public function user() { return $this->belongsTo(User::class); }   ║
╠══════════════════════════════════════════════════════════════════════╣
║ MIGRATION (up() method)                                              ║
║  $table->id();                                                       ║
║  $table->string('title');                                            ║
║  $table->text('description')->nullable();                            ║
║  $table->string('status')->default('pending');                       ║
║  $table->foreignId('user_id')->constrained()->onDelete('cascade');   ║
║  $table->timestamps();                                               ║
╠══════════════════════════════════════════════════════════════════════╣
║ COMMON ERRORS & FIXES                                                ║
║  419 Error          → Add @csrf to form                              ║
║  405 Error          → Route method mismatch (GET vs POST)            ║
║  View not found     → Check path (dot notation)                      ║
║  Mass assignment    → Add column to $fillable                        ║
║  Class not found    → Add use App\Models\Task; in controller         ║
╠══════════════════════════════════════════════════════════════════════╣
║ .env DB CONFIG                                                       ║
║  DB_CONNECTION=mysql  DB_HOST=127.0.0.1  DB_PORT=3306               ║
║  DB_DATABASE=todoapp  DB_USERNAME=root   DB_PASSWORD=                ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 📁 QUICK REFERENCE: FILE STRUCTURE FOR CRUD

```
todoapp/
├── routes/
│   └── web.php                          ← All routes here
├── app/
│   ├── Http/
│   │   └── Controllers/
│   │       └── TaskController.php       ← Controller logic
│   └── Models/
│       └── Task.php                     ← Eloquent model
├── database/
│   └── migrations/
│       └── xxxx_create_tasks_table.php  ← DB schema
├── resources/
│   └── views/
│       ├── layouts/
│       │   └── master.blade.php         ← Master layout
│       └── tasks/
│           ├── index.blade.php          ← List all tasks
│           ├── create.blade.php         ← Add task form
│           └── edit.blade.php           ← Edit task form
└── .env                                 ← DB credentials
```

---

*Prepared for Gujarat University BCA Sem 6 — DSC-C-364 P*
*Based on Practical Syllabus: Phase I–V (50 Practicals)*
