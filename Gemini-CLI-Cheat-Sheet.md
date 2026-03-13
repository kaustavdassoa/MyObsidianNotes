# Gemini CLI Cheat Sheet for Developers

This cheat sheet provides a quick reference for developers using the Gemini CLI, focusing on essential commands, features, and integration tips for Obsidian notes.

## 1. Essential CLI Flags

When running the `gemini` command, you can use various flags to control its behavior.

| Flag       | Description                                                                  | Example Usage                                |
| :--------- | :--------------------------------------------------------------------------- | :------------------------------------------- |
| `--model`  | Specify the Gemini model to use (e.g., `gemini-pro`, `gemini-1.5-flash`).    | `gemini --model gemini-1.5-flash "summarize this"` |
| `--system` | Provide a system instruction to guide the model's response.                 | `gemini --system "Act as a senior developer" "Explain React hooks"` |
| `--temp`   | Set the temperature for the model's response (0.0-1.0). Higher values mean more creativity. | `gemini --temp 0.8 "Write a short story"`    |
| `--json`   | Output the response in JSON format.                                          | `gemini --json "list 3 programming languages"` |
| `--stream` | Stream the model's response in real-time.                                  | `gemini --stream "Tell me a long joke"`      |
| `--output` | Save the response to a specified file.                                       | `gemini "Hello world" --output hello.txt`    |

## 2. Key Slash Commands

Slash commands provide interactive features within the Gemini CLI chat interface.

| Command     | Description                                                                  | Example Usage (within chat)                    |
| :---------- | :--------------------------------------------------------------------------- | :--------------------------------------------- |
| `/chat`     | Start an interactive chat session with the Gemini model.                     | `/chat`                                        |
| `/compress` | Compress files or directories. (Requires additional prompts for file/format) | `/compress`                                    |
| `/help`     | Display help information about the Gemini CLI.                               | `/help`                                        |
| `/history`  | View your chat history.                                                      | `/history`                                     |
| `/clear`    | Clear the current chat session.                                              | `/clear`                                       |
| `/exit`     | Exit the Gemini CLI.                                                         | `/exit`                                        |

## 3. Using the `@` Symbol for File Context

The `@` symbol allows you to easily reference local files or directories within your prompts, providing the Gemini model with immediate context.

**Syntax:**
- `@<file_path>`: Includes the content of a file.
- `@<directory_path>`: Includes a listing of the directory contents.

**Examples:**

- `gemini "Summarize the key points in this document: @./Notes/Langchain/0.0 Getting Started - Initial Setup.md"`
- `gemini "Help me refactor this code: @./src/main.py"`
- `gemini "Based on the files in this directory, what's the project's main purpose? @./src"`

## 4. Tips for Integrating into Obsidian Notes

Integrating the Gemini CLI with Obsidian can supercharge your note-taking and knowledge management for development.

1.  **Automate Content Generation:** Use the Gemini CLI to generate code snippets, explanations, or summaries directly into your Obsidian notes. For example, create a shell script that takes a prompt, calls `gemini`, and appends the output to a specific note.
    ```bash
    # Example script to append Gemini output to an Obsidian note
    gemini "$1" >> "E:/GitHub/MyObsidianNotes/WIP/AI Portfolio Projects/Generated Content.md"
    ```
2.  **Contextual Code Review/Explanation:** When working on a code block in Obsidian, use the `@` symbol to send that code block to Gemini for review, refactoring suggestions, or detailed explanations. You can copy the code, use the CLI, and paste the output back.
    ```bash
    gemini "Explain this Python function: @./my_project/utils.py"
    ```
3.  **Quick Research and Summaries:** For technical papers or long documentation you've saved locally, use `gemini "Summarize this PDF: @./Notes/Prompt-Engineering/gemini-for-google-workspace-prompting-guide-101.pdf"` to get quick insights directly within your workflow, then paste the summary into your research notes.
