# Spec Kit Partner Subagent

**Spec Kit Partner** is a next-generation, conversational, adaptive, and agentic subagent designed to help humans co-create rigorous, multi-perspective technical specs for GitHub Spec Kit projects. It leverages dynamic memory graphs, adaptive workflows, and explicit multi-role analysis to bridge human creativity and technical rigor. Yes, it is particularly useful for humans who are non-technical and/or not trained yet in [Spec Kit Methodology](https://github.com/github/spec-kit).

---

## Features

- **Conversational Orchestration:** Natural, empathic dialogue management tailored to user preferences.
- **Multi-Role Analysis:** Dynamically identifies and embodies relevant professional perspectives for thorough analysis.
- **Memory Graph:** Persists all insights, relationships, and synthesis steps in a queryable, file-based graph.
- **Adaptive Workflow:** Adjusts process phases and requirements in response to user input and context changes.
- **Transparent Logging:** All actions, analysis, and conversations are logged for auditability and recovery.
- **Extensible Design:** Add new engines, diagrams, or protocol enhancements as needed.

---

## Directory & File Structure

All files generated or managed by this subagent reside in a dedicated directory at your project root:

```
/project-root/
├── .claude/
│   └── agents/
│       └── spec-kit-partner.md       # Subagent definition (not runtime files)
│
├── spec-kit-partner/                 # All runtime files, code, data, and outputs
│   ├── src/                          # Engine code modules
│   ├── project-data/
│   │   ├── memory-graph.json
│   │   ├── user_profile.json
│   │   ├── workflow_state.json
│   │   ├── multi-role-analysis.json
│   │   ├── logs/
│   │   ├── spec/
│   │   └── diagrams/
│   └── .git/                         # (Optional: version control for agent state)
│
└── (other project files and folders…) # Unaffected by the agent
```

**To avoid committing agent-generated state and artifacts, add the following to your project’s `.gitignore`:**

```
/spec-kit-partner/
```

---

## Diagram Output Types

All diagrams produced by the agent are output in **Mermaid (`.mermaid`) format** by default, ensuring they are both human-readable and easily parsed or rendered by AI systems and tooling.  
If you require rendered versions, you may export `.svg` or `.png` from the `.mermaid` sources, but the canonical, AI-readable format is `.mermaid`.

---

## Initialization Protocol (Excerpt)

- On first run or when required files are missing:
    - **Check if each required directory exists; if not, create it before writing files inside.**
    - Generate all support code files and persistent data files as described in [Initialization Protocol](./spec-kit-partner.md).
    - Populate each `.json` file using the provided schemas and set initial values.
    - Log all steps and errors to `spec-kit-partner/project-data/logs/conversation.log`.

**See the full Initialization Protocol in [spec-kit-partner.md](./spec-kit-partner.md) for complete instructions.**

---

## Usage

- Place `spec-kit-partner.md` in `.claude/agents/`.
- On first invocation, the agent will create and initialize all required files and directories in `/spec-kit-partner/`.
- All runtime state, logs, diagrams, and outputs are self-contained within this directory.
- For more details on engine architecture, data schemas, and interaction protocols, refer to the main spec file.

---

## License

This project is licensed under the [MIT License](./LICENSE).

Copyright (c) [2025] [JCMRS]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

---

## Contact

For questions, contributions, or collaboration, open an issue or contact the repository maintainer.
