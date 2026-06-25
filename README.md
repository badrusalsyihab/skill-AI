# 🤖 skill-AI

A collection of custom **Claude Code Skills** for full-stack development workflows. Drop them into Claude Code and trigger powerful, opinionated automation for code review, security scanning, and architecture documentation.

> Skills are reusable instruction packs that extend Claude Code. Each one lives in its own folder with a `SKILL.md` file. [Learn more about Skills →](https://docs.claude.com/en/docs/claude-code/skills)

---

## 📦 Available Skills

| Skill | What it does | Trigger |
| :--- | :--- | :--- |
| **Fullstack Sentinel** | Full-stack QA scanner for Next.js & Laravel — finds missing `.env` vars, dead code, N+1 queries, React anti-patterns, security risks (XSS/SQLi/Mass Assignment), and dependency CVEs. | `/fullstack-sentinel` |
| **Project Arch Documenter** | Reverse-engineers a codebase to detect tech stack, database schema, third-party integrations, and features — then generates a self-contained interactive HTML dashboard with Mermaid.js flowcharts. | `/project-arch-documenter` |

---

## 🚀 Installation

Clone the repo and symlink (or copy) each skill into your Claude Code skills directory.

**User-level** (available in every project):

\```bash
# macOS / Linux
git clone git@github.com:badrusalsyihab/skill-AI.git
ln -s "$(pwd)/skill-AI/Fullstack Sentinel"      ~/.claude/skills/fullstack-sentinel
ln -s "$(pwd)/skill-AI/project-arch-documenter" ~/.claude/skills/project-arch-documenter
\```

\```powershell
# Windows (PowerShell)
git clone git@github.com:badrusalsyihab/skill-AI.git
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.claude\skills\fullstack-sentinel"      -Target ".\skill-AI\Fullstack Sentinel"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.claude\skills\project-arch-documenter" -Target ".\skill-AI\project-arch-documenter"
\```

**Project-level** (only inside one repo): copy the folders into `.claude/skills/` instead.

> ℹ️ The skill folder name must match the `name:` field in each `SKILL.md`.

After installing, restart Claude Code so the skills are picked up.

---

## 🧑‍💻 Usage

Inside Claude Code, invoke a skill with a slash command or a natural-language trigger phrase:

\```
/fullstack-sentinel
\```
> "Run a fullstack health check on this project"

\```
/project-arch-documenter
\```
> "Generate architecture documentation to HTML for this project"

Run them from the root of the project you want to analyze.

---

## 📂 Repository Structure

\```
skill-AI/
├── Fullstack Sentinel/
│   └── SKILL.md
├── project-arch-documenter/
│   └── SKILL.md
└── README.md
\```

---

## 🛠️ Tech Coverage

- **Frontend:** Next.js (App Router & Pages), React, TypeScript
- **Backend:** Laravel (PHP), Node (Express / NestJS)
- **Databases:** MySQL/Postgres, Prisma, MongoDB, Redis
- **Integrations:** Stripe, Midtrans, AWS, Firebase, Sentry, OpenAI, Anthropic, and more

---

## 🤝 Contributing

New skills are welcome! Add a new folder with a `SKILL.md` (include `name` and `description` frontmatter), then update the table above.

## 📄 License

MIT
