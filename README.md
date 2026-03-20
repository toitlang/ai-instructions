# Toit AI tools

Skills and other tools for AI agents working with Toit.

## Installation

#### Project-Local Skills

In your project, or home directory:

```
for d in .agent .claude; do
  mkdir -p "$d/skills"
  cp -r $PATH_TO_AI_TOOLS/skills/* "$d/skills"
done
```

Restart your editor.
