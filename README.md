# Glupe Semantic Audit Action 🛡️

A GitHub Action that mathematically proves an AI-generated implementation matches its original architectural specification. 

Currently, the software industry is struggling with the "Omission Blindspot"—autonomous coding agents are decent at writing code, but terrible at realizing what constraints or features they *forgot* to implement from a specification. 

This action uses the **Glupe Semantic Compiler** to perform **Test-Driven Architecture (TDA)**. It reduces both your specification and the AI's pull request into Glupe Intermediate Representation (GIR) and applies Set Theory (Semantic Subtraction) to definitively catch missing logic and architectural hallucinations.

## How it Works

1. **Zero-Install Refinement**: The action temporarily installs the Glupe compiler and reverse-engineers the AI's generated source code (e.g., C++, Python, Rust) back into a semantic blueprint (`.glp`).
2. **Deep Semantic Subtraction**: It runs a 2-way AI diff between your `spec_file` and the refined implementation. 
3. **Automated Guardrail**: 
   - If the AI forgot to implement a business rule: `[!] MISSING CONTAINERS` (Exits with 1 ❌)
   - If the AI hallucinated unrequested logic: `[?] UNDOCUMENTED CONTAINERS` (Exits with 1 ❌)
   - If it perfectly aligns: `[SUCCESS] Audit passed` (Exits with 0 ✅)

## Usage

Add this to a `.github/workflows/audit.yml` file in your repository to automatically audit any Pull Request.

```yaml
name: Check AI Pull Request
on: [pull_request]

jobs:
  audit-architecture:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Run Glupe Gatekeeper
        uses: alonsovm44/glupe-audit-action@v1.0.0 # Replace with actual version tag
        with:
          spec_file: 'docs/architecture.glp'
          impl_file: 'src/main.cpp'
          api_key: ${{ secrets.OPENAI_API_KEY }}
          ignore_scaffold: 'true'
```

## Inputs

| Input | Description | Required | Default |
| --- | --- | --- | --- |
| `spec_file` | Path to the expected `.glp` specification blueprint. | **Yes** | |
| `impl_file` | Path to the generated source code (e.g., `src/main.cpp`). | **Yes** | |
| `api_key` | OpenAI or Google API Key for the Semantic Engine. We highly recommend passing this via GitHub Secrets (`${{ secrets.API_KEY }}`). | **Yes** | |
| `model` | The LLM model to use for the semantic math. | No | `gpt-4o` |
| `ignore_scaffold` | Set to `"true"` to ignore standard architectural boilerplate (like `#include` blocks, constants, or simple wrapper functions) during the audit to prevent false positives. | No | `true` |

## Example Output

If an AI agent attempts to merge a Pull Request where they forgot a requested system call, the action will fail the CI pipeline and output:

```text
[AUDIT] Performing Semantic Subtraction (cloud)...
   [AUDIT] Aligning container mappings semantically...
   [AUDIT] Filtering architectural scaffolding from extra containers...
   [AUDIT] Deep Semantic Subtraction on container: main...

=== SEMANTIC SUBTRACTION REPORT ===

[~] LOGIC MISMATCH IN CONTAINER: main (Code: MainStep)
[MISSING]
- Direct execution of the PowerShell command as specified in the expected specification.

[HALLUCINATED]
- Construction of a PowerShell command to execute an installation script.
- Call to ExecuteSystemCommand with the constructed command.

[FAIL] Audit rejected: Implementation diverges from specification.
```

## License

MIT