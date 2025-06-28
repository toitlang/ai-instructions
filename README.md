# AI instructions
Instructions that help LLMs produce better Toit code.

Since Toit isn't as popular as other programming languages, LLMs sometimes get
Toit code wrong. This repository contains instructions that help LLMs to
produce better Toit code.

## Tips
As recommended by Visual Studio Code:
- Keep your instructions short and self-contained. Each instruction should be a
  single, simple statement. If you need to provide multiple pieces of information,
  use multiple instructions.
- Don't refer to external resources in the instructions, such as specific
  coding standards.
- Reference custom instructions in your prompt files to keep your prompts clean and
  focused, and to avoid duplicating instructions for different tasks.

## VSCode
Typically, you can use copy the [instructions-long.md] to
`.vscode/copilot-instructions.md` in your project directory.

Alternatively, if you want to add a user-wide instructions file, you can
open the command palette in VSCode (Ctrl+Shift+P or Cmd+Shift+P on macOS)
and search for "Chat: New Instructions File", and follow the prompts.

See the official [documentation](https://code.visualstudio.com/docs/copilot/copilot-customization#_custom-instructions)
for more information.

## Contributing
Please help improve the instructions by submitting a pull request.
