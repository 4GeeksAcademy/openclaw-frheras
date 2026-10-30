# USER.md - User Model

Store stable user preferences and profile facts as directives that can guide future sessions.

Use one directive per entry:

```md
<!-- observed: YYYY-MM-DD | status: active -->

- Prefer concise progress updates during implementation work.
```

- Begin each directive with an imperative such as `Always`, `Never`, or `Prefer`.
- Record the observation date and either `active` or `superseded` on the metadata line.
- When a preference changes, mark the old entry `superseded` and rewrite the active directive in place. Never append a contradictory active directive.
- Keep stable communication style, relationships, and active-project context here. Put durable non-profile facts and decisions in `MEMORY.md`.
- Save this file at the workspace root as `USER.md`. It loads every session with a separate 4,000-character budget.

## Directives

Replace the example below with a real directive and a real observation date before you save this file. Never leave a placeholder directive `active`.

<!-- observed: 2026-10-30 | status: active -->

- Call the user Félix.

<!-- observed: 2026-10-30 | status: active -->

- Treat AI Engineering as Félix's primary learning project; treat AI-assisted application management and creation as secondary projects.

<!-- observed: 2026-10-30 | status: active -->

- Help Félix master practical AI use and apply it to build projects relevant to his work as a data analyst.

<!-- observed: 2026-10-30 | status: active -->

- When giving tool-specific help, account for Félix's daily use of Python, Claude, ChatGPT, and an ETL-creation app; ask for the app's name when it matters.

<!-- observed: 2026-10-30 | status: active -->

- Prefer Visual Studio Code as Félix's primary programming editor.

## Related

- [Agent workspace](/concepts/agent-workspace)

<!-- observed: 2026-10-30 | status: active -->

- When giving tool-specific guidance, account for Félix using Pentaho Spoon to create ETLs and learning n8n; this supersedes the earlier directive to ask which ETL-creation app he uses.
