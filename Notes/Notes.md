## Install uv
winget install -e --id astral-sh.uv

## install spec kit

 uvx --system-certs --from git+https://github.com/github/spec-kit.git specify init .


git checkout -b users/ufuoma_ikporo/hackathon-test

git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/UfuomaIkporoDBA/hackathon-team-2026.git
git push -u origin main

git remote add origin https://github.com/UfuomaIkporoDBA/hackathon-team-2026; 

git checkout -b develop; 
git add .; 
git commit -m "Initial Spec Kit scaffold"; git push -u origin develop
git checkout master
git pull
git checkout -b develop
git push -u origin develop

## ------------------ Spec Kit 

  Some agents may store credentials, auth tokens, or other identifying and private artifacts in  │
│  the agent folder within your project.                                                          │
│  Consider adding .github/ (or parts of it) to .gitignore to prevent accidental credential       │
│  leakage.                                                                                       │
│                                                                                                 │
╰─────────────────────────────────────────────────────────────────────────────────────────────────╯

╭────────────────────────────────────────── Next Steps ───────────────────────────────────────────╮
│                                                                                                 │
│  1. You're already in the project directory!                                                    │
│  2. Start using slash commands with your coding agent:                                          │
│     2.1 /speckit.constitution - Establish project principles                                    │
│     2.2 /speckit.specify - Create baseline specification                                        │
│     2.3 /speckit.plan - Create implementation plan                                              │
│     2.4 /speckit.tasks - Generate actionable tasks                                              │
│     2.5 /speckit.implement - Execute implementation                                             │
│                                                                                                 │
╰─────────────────────────────────────────────────────────────────────────────────────────────────╯

╭───────────────────────────────────── Enhancement Commands ──────────────────────────────────────╮
│                                                                                                 │
│  Optional commands that you can use for your specs (improve quality & confidence)               │
│                                                                                                 │
│  ○ /speckit.clarify (optional) - Ask structured questions to de-risk ambiguous areas before     │
│  planning (run before /speckit.plan if used)                                                    │
│  ○ /speckit.analyze (optional) - Cross-artifact consistency & alignment report (after           │
│  /speckit.tasks, before /speckit.implement)                                                     │
│  ○ /speckit.checklist (optional) - Generate quality checklists to validate requirements         │
│  completeness, clarity, and consistency (after /speckit.plan)                                   │
│                                                                                                 │
╰───────────────────────────────────────────────────────────────────────