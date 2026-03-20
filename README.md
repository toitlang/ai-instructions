# Toit AI tools

Skills and other tools for AI agents working with Toit.

## Installation

#### Project-Local Skills

In your repository, create a workspace skills directory: `.agent/skills/`, `.claude/skills/`, `.copilot/skills/`.

Place each skill from skills/ inside its own subdirectory under this folder:

```
for d in .agent .claude; do
  mkdir -p "$d/skills"
  cp -r $PATH_TO_AI_TOOLS/skills/* "$d/skills"
done
```

Restart your editor.
