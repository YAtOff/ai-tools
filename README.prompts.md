# 🎯 Reusable Prompts

Ready-to-use prompt templates for specific development scenarios and tasks, defining prompt text with a specific mode, model, and available set of tools.

### How to Use Reusable Prompts

**To Install:**
- Click the VS Code or VS Code Insiders install button for the prompt you want to use
- Download the `*.prompt.md` file and manually add it to your prompt collection

**To Run/Execute:**
- Use `/prompt-name` in VS Code chat after installation
- Run the `Chat: Run Prompt` command from the Command Palette
- Hit the run button while you have a prompt file open in VS Code

---

## Available Prompts

| Prompt | Description |
|--------|-------------|
| **Architecture Analyzer** [Install in VS Code](vscode:extension/GitHub.copilot-chat?install&promptFile=prompts/architecture-analyzer.prompt.md) [Install in VS Code Insiders](vscode-insiders:extension/GitHub.copilot-chat?install&promptFile=prompts/architecture-analyzer.prompt.md) | Analyze the structure, main components, and relationships in this software project. Create a detailed `docs/architecture.md` file in Markdown that explains the project's architecture with Mermaid diagrams. |
| **Review** [Install in VS Code](vscode:extension/GitHub.copilot-chat?install&promptFile=prompts/review.prompt.md) [Install in VS Code Insiders](vscode-insiders:extension/GitHub.copilot-chat?install&promptFile=prompts/review.prompt.md) | Perform a comprehensive, actionable code review as a senior expert software engineer. Focus on performance, security, code quality, testing, documentation, and architecture. |

---

## Prompt Details

### Architecture Analyzer

**Mode:** `agent`

Analyzes the structure, main components, and relationships in your software project to create comprehensive architectural documentation.

**What it does:**
1. **Overview**: Creates a brief summary of the software architecture and its main goals
2. **Main Components**: Lists and describes primary system components (UI, business logic, data access, database, etc.)
3. **Component Relationships**: Explains how components interact (data flow, control flow, APIs)
4. **Mermaid Diagrams**: Includes visual diagrams for each major section
5. **File Structure & Data Flow**: Optional diagrams depicting file/folder structure and main data flows

**Output:** Generates `docs/architecture.md` file with complete architectural documentation and Mermaid diagrams.

---

### Review

**Mode:** `agent`

**Role:** Senior expert software engineer with extensive experience in maintaining projects over a long time and ensuring clean code and best practices.

**Input:** Specify what code to review (e.g., "current PR", "last commit", "changed files", specific file paths, or folder paths)

**What it does:**
1. **Determines Code Source Type**: Automatically detects whether you want to review a PR, commit, changed files, or specific paths
2. **Retrieves Code Content**: Extracts the relevant code for analysis
3. **Performs Comprehensive Review** across six key areas:
   - **Performance**: Unnecessary allocations, sync-over-async, repeated network calls
   - **Security**: Input validation, error handling, sensitive data exposure, HTTP usage
   - **Code Quality**: Readability, maintainability, coding standards, naming conventions, modularity
   - **Testing**: Test coverage, meaningful test cases, mocks/stubs
   - **Documentation**: Comments, README updates, API documentation
   - **Architecture**: Scalability, design patterns, separation of concerns

**Output Format:**
- Summary with high-level assessment
- Critical Issues (must fix) with file locations, issues, proposed fixes, and rationale
- Suggestions (nice to have) with improvements and proposed changes
- Good Practices highlighting positive aspects
- Metadata including review date, files reviewed count, and focus area

**Review saved as:** `docs/YYYY-MM-DDTHH:MM:SS-review.md`

**Additional Feature:** Optional focus area parameter for targeted reviews (e.g., "HTTP error handling")

---

## Contributing

To add a new prompt to this collection:

1. Create a new `.prompt.md` file in the `prompts/` directory
2. Follow the prompt file format with frontmatter:
   ```markdown
   ---
   mode: agent
   description: 'Brief description of what the prompt does'
   ---

   [Your prompt content here]
   ```
3. Update this README.prompts.md file with the new prompt entry in the table and details section

---

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- [Awesome GitHub Copilot](https://github.com/github/awesome-copilot)
