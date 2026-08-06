# Assignment 6 — Building an AI-Assisted Git Safety Net (PR Ready Check)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In Week 2 you built Claude Code hooks that block a dangerous action *before* it happens (`PreToolUse`), and a restricted skill that could look but not touch (`allowed-tools` without `Write`). In this assignment you will discover that Git has the exact same idea, decades older: a **pre-commit hook** that blocks a commit before it's created.

You will build both halves of a real "PR Ready" workflow:

1. A **Git hook that follows fixed rules** — scans staged changes for hardcoded secrets and oversized files and refuses the commit. No AI involved, no guessing, just a rule that gives the same answer every time.
2. A **restricted Claude Code skill** (`/pr-ready`) that reads your staged diff and drafts a Pull Request title, description, and a short list of things worth a second look — the kind of judgment a fixed rule can't make (mixed changes, missing context, unclear intent). The skill never commits, pushes, or opens the PR. You do that yourself, using its draft as a starting point.

This mirrors the Agentic Loop from Week 3's Linux triage assignment: **Gather → Analyze → Human Act → Verify**. The hook and the skill both gather and analyze; only you act.

---

# Task 0 — Confirm Your Fork and Create a Feature Branch

## Goal

Confirm you are working in your own fork, then create a dedicated branch for this assignment.

### Evidence

#### Screenshot 1 — Output of git remote -v and git branch showing the new branch

![output ](screenshots/A6Screenshot1.png)

---

### Notes

**1. Why create a dedicated branch instead of doing this work on main?**

A dedicated branch lets you work on new features or changes without affecting the main branch. It provides an separate workspace where you can develop, test, and make commits safely. Once the work is complete and verified, the branch can be merged into main.

---

# Task 1 — Stage a Change With Realistic Risk

## Goal

On your own fork of this repository (the one you've been submitting your DMI work in since onboarding), create a new branch and stage a change that a real reviewer should catch: a hardcoded-looking secret and a leftover debug statement.

### Evidence

#### Screenshot 1 — Output of  `git status` showing the staged file on feature/ai-pr-ready

![output ](screenshots/A6Screenshot2.png)

---

### Notes

**1. Why does this assignment use an obviously fake key instead of a real one?**

A fake SSH key is used to prevent exposing real credentials. A real private key grants access to a repository or server, so sharing it publicly could lead to serious security risks. Using a fake SSH key allows users to practice the process safely without risking unauthorized access or compromising sensitive systems.

---

# Task 2 — Write a Real Git Pre-Commit Hook

## Goal

Create a tracked, shareable pre-commit hook that blocks a commit containing secret-like patterns or files over 1MB.

### Evidence

#### Screenshot 2 — `hooks/pre-commit` open in VS Code showing the full script

![output ](screenshots/A6Screenshot2.1.png)

---

#### Screenshot 3 — Output of `git config core.hooksPath` confirming it points to `hooks`

![output ](screenshots/A6Screenshot3.png)

---

### Notes

**1. Why is `hooks/pre-commit` tracked in the repo instead of living only in `.git/hooks/`?**

A hooks/pre-commit file is tracked in the repository so it can be shared with everyone working on the project. Files inside .git/hooks/ are local to each developer's machine and are not tracked by Git. By keeping the hook in the repository, everyone can use the same pre-commit checks.

---

**2. Compare this to `PreToolUse` from Week 2 Assignment 6. What does each one intercept, and what do they have in common?**

The Git pre-commit hook intercepts a commit before Git creates it, allowing checks or scripts to run before the commit is saved.

The PreToolUse hook intercepts tool execution before an AI assistant is allowed to use a tool, giving it a chance to validate or block the action.

---

# Task 3 — Prove the Hook Blocks the Risky Commit

## Goal

Attempt to commit the staged file from Task 1 and show the hook rejecting it.

### Evidence

#### Screenshot 4 — Terminal showing `git commit` rejected with the hook's "BLOCKED" message naming the exact file

![output ](screenshots/A6Screenshot4.png)

---

### Notes

**1. Which line in `hooks/pre-commit` matched your fake key, and why did it match?**

The line below matched the fake key:

- if git diff --cached -- "$file" | grep -qE 'AKIA[0-9A-Z]{16}|-----BEGIN (RSA|OPENSSH|PRIVATE) KEY-----'; then

It matched because the grep command searches staged changes for patterns that look like secrets. My fake key started with the AKIA prefix, which matches the regular expression AKIA[0-9A-Z]{16}. As a result, the hook detected it as a possible secret and blocked the commit.

---

**2. Could this hook have caught a poorly-named variable that stores a secret without the `AKIA` prefix? What does that tell you about the limits of a fixed rule like this?**

No. This hook only detects secrets that match the specific patterns defined in its regular expression. If a secret were stored in a variable without the AKIA prefix or another matching pattern, the hook would not detect it. This shows that fixed-rule detection has limitations—it is effective for known patterns but can miss secrets that use different formats or names.

---

# Task 4 — Build the `/pr-ready` Skill

## Goal

Create a manually invoked Claude Code skill that reads your staged changes and produces a PR-readiness report and a draft PR description — without writing, committing, or pushing anything itself.

### Evidence

#### Screenshot 5 — `SKILL.md` frontmatter showing `allowed-tools: Bash, Read, Grep` (no `Write`) and `disable-model-invocation: true`

![output ](screenshots/A6Screenshot5.png)

---

#### Screenshot 6 — `/pr-ready` output while the risky file is still staged, showing it flagged the secret and/or debug statement

![output ](screenshots/A6Screenshot6.png)

---

### Notes

**1. Why does `/pr-ready` have `Bash` and `Read` but not `Write`?**

`/pr-ready` only needs `Bash` to run Git commands and `Read` to inspect the staged changes and repository files. It does not need `Write` because its purpose is to review the code and determine whether it is ready for a pull request, not to modify any files. This follows the principle of least privilege by granting only the permissions required for the task.

---

**2. The pre-commit hook and `/pr-ready` both looked at the same staged diff. Did they flag the same things? What did one catch that the other didn't?**

No, they did not flag the same things.

The pre-commit hook checked for specific issues, such as secrets that matched known patterns (like an AKIA key) and files larger than 1 MB. It automatically blocked the commit if it found these problems.

The `/pr-ready` command reviewed the staged changes more broadly to determine whether they were ready for a pull request. It could identify issues related to code quality, completeness, or readiness that the pre-commit hook would not detect. In contrast, the pre-commit hook focused only on the predefined rules for secrets and file size.

---

# Task 5 — Fix the Issues and Re-Verify

## Goal

Remove the secret and debug statement, then prove both gates now pass clean.

### Evidence

#### Screenshot 7 — `git commit` succeeding after the fix (no BLOCKED message)

![output ](screenshots/A6Screenshot7.png)

---

#### Screenshot 8 — Second `/pr-ready` run showing a clean risk report and a drafted PR title + description

![output ](screenshots/A6Screenshot8.png)

---

### Notes

**1. What exactly did you change to satisfy the pre-commit hook?**

To satisfy the pre-commit hook, I removed or replaced the credential-shaped string `(AWS_ACCESS_KEY_ID=AKIAABCDEFGHIJKLMNOP)` and removed the debug statement that printed the AWS access key. These changes ensured there were no secret-like patterns or exposed credentials in the staged files, allowing the commit to pass the pre-commit checks successfully.

---

# Task 6 — Push and Open a Pull Request Using the AI Draft

## Goal

Push your branch and open a real Pull Request, using `/pr-ready`'s drafted title and description as your starting point — read it critically and edit before you use it.

**Important:** Open this Pull Request with base repository set to **your own fork** — not the shared upstream `pravinmishraaws/devops-micro-internship-pravinmishra` repository. This assignment's hook and skill files are your own practice work, not a change meant for the shared class repo.

### Evidence

#### Screenshot 9 — Your Pull Request showing the base repository is your own fork, plus the title and description, with the `/pr-ready` draft visible for comparison (paste it in the PR conversation or your notes below)

![output ](screenshots/A6Screenshot9.png)

---

#### PR Link

`https://github.com/pravinmishraaws/devops-micro-internship-interviews/pull/375`
---

### Notes

**1. What, if anything, did you edit in the AI's drafted PR description before using it? Why?**

I removed references to AWS integration because the script only contained a fake credential for testing the pre-commit hook and did not actually integrate with AWS. I also updated the description to accurately reflect the purpose of the script so that the PR clearly described the actual changes being submitted.

---

**2. If you had blindly copy-pasted the AI's draft without reading it, what could go wrong?**

The PR could contain incorrect or misleading information about the changes. This could confuse reviewers, slow down the review process, or make it seem like the code does something it does not. AI-generated content should always be reviewed for accuracy before it is used.

---

**3. Why does this PR need to target your own fork instead of the shared upstream repository?**

The PR should target my own fork because I do not have permission to push directly to the shared upstream repository. Using a fork allows me to develop and submit my changes safely without affecting the original project until they are reviewed and approved.

---

# Task 7 — Map the Workflow to the Agentic Loop

## Goal

Explain this assignment's workflow using the same Gather → Analyze → Human Act → Verify structure from Week 3.

### Notes

**1. Which step(s) represent Gather?**

The Gather step includes staging the changes, running the pre-commit hook, and using /pr-ready to inspect the staged files. These steps collect information about the code, identify potential issues, and determine whether the changes are ready for review.

---

**2. Which step(s) represent Analyze?**

The Analyze step is when the pre-commit hook checks for secrets and oversized files, and /pr-ready reviews the staged changes, identifies potential problems, and generates recommendations for improving the pull request.

---

**3. Which step is Human Act, and why must a human — not Claude — run `git commit`, `git push`, and open the PR?**

The Human Act step is when I review the AI's suggestions, make any necessary changes, and then run git commit, git push, and open the pull request. These actions affect the repository and its history, so they require human approval to ensure the changes are intentional and correct.

---

**4. Which step is Verify?**

The Verify step is rerunning the pre-commit hook and /pr-ready after making changes to confirm that all issues have been resolved and that the code is ready to be committed and submitted as a pull request.

---

**5. In one or two sentences: why do you need *both* the fixed-rule pre-commit hook and the AI skill? Isn't one enough?**

The pre-commit hook quickly enforces fixed rules, such as detecting secrets and oversized files, while the AI skill provides broader feedback on code quality, pull request readiness, and best practices. Using both creates a more reliable review process because each catches issues the other might miss.

---

# Task 8 — LinkedIn Post

## Goal

Publish a LinkedIn post summarizing what you built and what you learned about combining fixed-rule safety checks with AI-assisted review.

### Evidence

#### LinkedIn Post URL

`https://www.linkedin.com/posts/kenneth-boyi-6b4a353a6_devops-git-github-share-7486262012965130240-pysp/?utm_source=social_share_send&utm_medium=member_desktop_web&rcm=ACoAAGN28KgBcLsxZ_9wccn46X5xb7tSMc1PKWE`

---

## Key Learnings

Add 3-5 bullet points on what you learned this week.

- Learned how to use Git branches to isolate changes and keep the main branch stable until work is reviewed and ready to merge.
- Understood how Git pre-commit hooks automatically enforce security rules by detecting secrets and oversized files before a commit is created.
- Explored how an AI-powered /pr-ready review complements pre-commit hooks by providing feedback on code quality, PR readiness, and documentation.

---

# Submission Instructions

- Ensure `hooks/pre-commit` and `.claude/skills/pr-ready/SKILL.md` are committed to your GitHub repository
- Add all required screenshots to your submission
- All written answers must be in your own words
- Do not use a real secret or credential anywhere in your submission — the fake key in Task 1 is intentional and must stay clearly fake
- Open your Pull Request against your own fork, not the shared upstream repository
- Push your final changes to your forked repository
- Include your PR link and LinkedIn post URL

---

## GitHub Repository URL

Paste your forked repository URL here:

`https://github.com/Weblife007/devops-micro-internship-interviews/tree/feature/ai-pr-ready`

---

# Completion Checklist

- [✅] Branch `feature/ai-pr-ready` created with a staged file containing a fake secret and a debug statement
- [✅] `hooks/pre-commit` created and tracked in the repo (not only in `.git/hooks/`)
- [✅] `core.hooksPath` configured to point at `hooks/`
- [✅] Pre-commit hook shown blocking the risky commit
- [✅] `.claude/skills/pr-ready/SKILL.md` created with correct `allowed-tools` (no `Write`) and `disable-model-invocation: true`
- [✅] `/pr-ready` run against the risky diff and shown flagging issues
- [✅] Risky file fixed; `git commit` succeeds cleanly
- [✅] `/pr-ready` re-run showing a clean report and drafted PR title/description
- [✅] Pull Request opened using the AI draft as a starting point, with your own fork as the base repository (not upstream), PR link included
- [✅] Agentic Loop mapping (Task 7) completed in your own words
- [✅] LinkedIn post published and URL submitted
- [✅] All required screenshots added
- [✅] GitHub repository URL provided

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
