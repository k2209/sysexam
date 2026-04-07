# SEC-BCA-356 — Web Skill Development Tools
## Exam Question & Answer Guide
**Course:** Web Skill Development Tools (Git, Github, Google Docs, Google Sheet)  
**Course Code:** SEC-BCA-356 | **Credits:** 2

---

## Unit 1 — Git & GitHub

---

### Git Basics

---

**Q1. What is Git? What is a Version Control System (VCS)?**

**Git** is a free, open-source distributed version control system created by Linus Torvalds in 2005. It tracks changes in source code during software development.

**Version Control System (VCS)** is a system that records changes to files over time so you can recall specific versions later. It allows multiple developers to work on the same project without conflict.

---

**Q2. What is the difference between Local and Remote version control?**

- **Local VCS:** Tracks changes only on your own machine. No collaboration possible. Example: keeping backup copies manually.
- **Remote VCS:** A central server stores the repository. Multiple users can collaborate, push and pull changes. Example: GitHub, GitLab.

Git supports both — you work locally and sync with a remote (like GitHub) when needed.

---

**Q3. List and explain the core Git commands.**

| Command | Description |
|---|---|
| `git init` | Initializes a new Git repository in a folder. Creates a hidden `.git` directory. |
| `git add <file>` | Stages changes (moves files to the staging area before committing). |
| `git commit -m "message"` | Saves staged changes to the local repository with a descriptive message. |
| `git status` | Shows the state of the working directory and staging area (untracked, modified, staged files). |
| `git log` | Displays the commit history (author, date, message, hash). |

---

**Q4. What is a repository in Git? What is the staging area?**

**Repository (Repo):** A storage location where your project's files and history of changes are kept. It can be:
- **Local** — on your own machine
- **Remote** — hosted online (e.g., GitHub)

**Staging Area (Index):** An intermediate area where you prepare changes before committing them.
- Use `git add` to move changes to the staging area
- Use `git commit` to permanently save them to the repository

---

**Q5. What are the key features of Git?**

- Distributed — every developer has a full copy of the repository
- Fast performance — most operations are local
- Data integrity — uses SHA-1 hashing to track content
- Supports branching and merging
- Free and open source
- Supports non-linear development (parallel branches)

---

**Q6. How do you install and set up Git?**

1. Download from **git-scm.com** and install.
2. Configure your identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@email.com"
```

These details appear in every commit you make.

---

### Branching & Merging

---

**Q7. What is branching in Git? Why is it useful?**

A **branch** is an independent line of development. The default branch is called **main** (or master).

Branching allows developers to work on new features or bug fixes without affecting the main codebase.

```bash
git branch feature-x        # create a new branch
git checkout feature-x      # switch to that branch
git checkout -b feature-x   # create and switch in one step
```

---

**Q8. What is merging in Git? What is a merge conflict?**

**Merging** combines changes from one branch into another:

```bash
git merge feature-x
```

A **merge conflict** happens when two branches edit the same part of a file differently. Git marks the conflict in the file and the developer must manually resolve it, then commit the result.

---

### GitHub

---

**Q9. What is GitHub? How is it different from Git?**

| Git | GitHub |
|---|---|
| Local version control tool | Cloud-based hosting platform for Git repositories |
| Runs on your computer | Runs on the internet |
| Tracks changes locally | Adds collaboration features (PRs, Issues, etc.) |

**Simply:** Git is the tool, GitHub is the service that hosts Git repositories online.

---

**Q10. What are the key features of GitHub?**

- Cloud hosting of repositories
- Pull Requests for code review and merging
- Issues for bug tracking and task management
- Forking — copy someone else's repo to your account
- GitHub Actions for CI/CD automation
- Collaboration with teams using access permissions
- GitHub Pages for hosting websites

---

**Q11. What is the difference between Cloning and Forking?**

| Cloning | Forking |
|---|---|
| Creates a local copy on your machine | Creates a copy in your own GitHub account |
| Used for repos you have access to | Used to contribute to others' projects |
| `git clone <url>` | Done via GitHub's "Fork" button |
| Changes pushed to original repo (if permitted) | Changes proposed via Pull Requests |

---

**Q12. What is a Pull Request (PR)? What are Issues in GitHub?**

**Pull Request:** A way to propose changes to a repository. You make changes in your branch/fork, then open a PR so others can review and merge your code into the main branch.

**Issues:** A built-in tracking system for reporting bugs, requesting features, or discussing tasks. Each issue can be assigned, labeled, and linked to commits or PRs.

---

**Q13. How do you create and manage a repository on GitHub?**

1. Log in to GitHub → click **"New repository"**
2. Enter repo name, description, choose public/private
3. Optionally initialize with README, .gitignore, license
4. Click **"Create repository"**
5. Clone it locally:
   ```bash
   git clone <url>
   ```
6. Push changes:
   ```bash
   git push origin main
   ```

---

## Unit 2 — Google Docs & Google Sheets

---

### Google Docs

---

**Q14. What is Google Docs? List its main features.**

**Google Docs** is a free, web-based word processor by Google (part of Google Workspace). It runs in the browser — no software installation needed.

Key features:
- Real-time collaborative editing
- Auto-save to Google Drive
- Commenting and suggestion mode
- Sharing with view/comment/edit permissions
- Built-in templates
- Version history
- Works offline

---

**Q15. How do you share a Google Doc? What are the permission levels?**

Click **Share** (top right) → enter email addresses or generate a shareable link.

| Permission | What they can do |
|---|---|
| Viewer | Can only read the document |
| Commenter | Can add comments but not edit content |
| Editor | Can fully edit the document |

You can also set link access to **"Anyone with the link"** for public sharing.

---

**Q16. What is real-time collaboration in Google Docs?**

Multiple users can work on the same document simultaneously. You can see each collaborator's cursor in a different color, and changes appear instantly for everyone. This eliminates the need to email documents back and forth.

The **comment feature** allows users to leave notes at specific text locations, and others can reply or resolve them.

---

**Q17. What is the difference between Editing, Suggesting, and Viewing mode in Google Docs?**

| Mode | Description |
|---|---|
| Editing | Changes are made directly to the document |
| Suggesting | Changes appear as tracked suggestions (shown in a different color). The owner accepts or rejects them. |
| Viewing | Read-only. No changes can be made. |

---

**Q18. What are Templates in Google Docs? Give examples.**

Templates are pre-designed document layouts that save time. Access via **File → New → From Template**.

Examples:
- Resume / CV
- Business Letter
- Meeting Notes
- Project Proposal
- Brochure
- Newsletter
- Invoice

---

### Google Sheets

---

**Q19. What is Google Sheets? What are its basic components?**

**Google Sheets** is a free, online spreadsheet application by Google — the cloud-based alternative to Microsoft Excel.

| Component | Description |
|---|---|
| Cell | Individual box at intersection of row and column (e.g., A1) |
| Row | Horizontal arrangement (numbered 1, 2, 3...) |
| Column | Vertical arrangement (labeled A, B, C...) |
| Sheet/Tab | Individual spreadsheet within a file |
| Range | Group of cells (e.g., A1:C10) |

---

**Q20. Explain the SUM, IF, and VLOOKUP functions with examples.**

**SUM** — Adds values:
```
=SUM(A1:A10)
```
Adds all values from A1 to A10.

**IF** — Logical test:
```
=IF(B2>50, "Pass", "Fail")
```
Returns "Pass" if B2 is greater than 50, else returns "Fail".

**VLOOKUP** — Searches a column and returns a related value:
```
=VLOOKUP("Alice", A2:C10, 3, FALSE)
```
Looks up "Alice" in column A and returns the value from the 3rd column of the range.

---

**Q21. What is Conditional Formatting in Google Sheets?**

Conditional formatting automatically changes the appearance (color, bold, etc.) of cells based on their values or a formula.

**Example:** Highlight all cells in column B that are less than 50 in red.

Steps: **Format → Conditional Formatting** → set the rule → choose a color.

Useful for quickly spotting patterns, outliers, or important data.

---

**Q22. What are Charts and Graphs in Google Sheets? How do you create one?**

Charts visually represent data for easier understanding.

Steps to create a chart:
1. Select the data range
2. Click **Insert → Chart**
3. Choose chart type: Bar, Line, Pie, Column, etc.
4. Customize title, colors, and labels in the Chart Editor

Charts update automatically when the underlying data changes.

---

**Q23. What is Data Validation in Google Sheets?**

**Data Validation** restricts what type of data can be entered in a cell, preventing input errors.

Examples of rules:
- Allow only numbers between 1–100
- Allow only dates
- Create a dropdown list of options (e.g., Yes/No, Department names)

**How to set it:** **Data → Data Validation** → set condition → save.

---

**Q24. How can Google Sheets be used collaboratively and linked with other Google tools?**

- **Sharing:** Share with view/comment/edit permissions (same as Docs)
- **Link with Google Forms:** Form responses automatically populate into a Sheet
- **Link with Google Docs:** Charts from Sheets can be inserted and linked into Docs — they update when Sheet data changes
- **Google Drive:** All Sheets are stored and accessible from Drive
- **Real-time collaboration:** Multiple users can edit simultaneously with live cursors

---

**Q25. What is Data Filtering in Google Sheets?**

**Filtering** lets you temporarily hide rows that don't match your criteria, making it easier to focus on specific data.

Steps:
1. Click on the data range
2. Go to **Data → Create a Filter**
3. Dropdown arrows appear on column headers
4. Click dropdown → filter by value, condition, or color

**Filter Views** allow each collaborator to apply their own filter without affecting others' views.

---

## Quick Revision Summary

| Topic | Key Points |
|---|---|
| Git | Distributed VCS, tracks changes, free & open source |
| Core commands | init, add, commit, status, log |
| Branching | Independent line of development, `git branch`, `git checkout` |
| Merging | Combines branches, conflicts need manual resolution |
| Git vs GitHub | Git = local tool, GitHub = cloud hosting platform |
| Cloning vs Forking | Clone = local copy, Fork = copy on your GitHub account |
| Pull Request | Propose changes for review before merging |
| Google Docs | Web-based word processor, real-time collaboration, permission levels |
| Google Sheets | Online spreadsheet, formulas (SUM, IF, VLOOKUP), charts, data validation |
| Conditional Formatting | Auto-format cells based on rules/values |
| Data Validation | Restrict input type in cells, create dropdowns |

---

*Course Code: SEC-BCA-356 | Credits: 2 | Units: 2 (15 hours each)*
