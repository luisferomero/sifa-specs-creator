# Development Environment

- Primary OS: Windows 11.
- Primary shell: PowerShell 7.
- Codex runs natively on Windows.
- Use PowerShell syntax for local shell commands.
- Do not assume bash, zsh, chmod, sed, awk, `/tmp`, or Unix-only utilities are available.
- Prefer cross-platform package scripts where possible.
- When creating automation scripts for local development, prefer PowerShell (`.ps1`) or cross-platform Node/Python scripts.
- Bash `.sh` scripts may exist for CI/Linux environments, but do not use them for Windows-local workflows unless explicitly requested.
- Use Windows-compatible path handling.
- Prefer npm/pnpm scripts for development commands instead of OS-specific shell commands.
