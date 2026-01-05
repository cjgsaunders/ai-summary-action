# 🤖 Ollama Scribe Local — AI Pull Request Summaries & Reviews

[![Marketplace](https://img.shields.io/github/v/release/cjgsaunders/ollama-scribe-local?label=Marketplace)](https://github.com/marketplace/actions/ollama-scribe-local)
![License](https://img.shields.io/badge/license-MIT-green)
![Category](https://img.shields.io/badge/Category-Utilities-orange)

**Ollama Scribe Local** is a high-performance GitHub Action that uses locally hosted LLMs to perform two distinct jobs:
**1.** Automatically generate PR summaries so you can spend more time writing code not PRs
**2** Conduct in-depth reviews of Pull Request diffs. 
Keep your source code private and eliminate OpenAI/Anthropic API costs.



## ⚙️ Configuration

| Name | Description                                                                                 | Required | Default |
| :--- |:--------------------------------------------------------------------------------------------| :--- | :--- |
| `github-token` | The `GITHUB_TOKEN` to post comments.                                                        | `true` | N/A |
| `ollama-url` | The URL of your Ollama server endpoint.                                                     | `true` | N/A |
| `model-name` | The Ollama model to use for analysis.                                                       | `false` | `qwen2.5-coder:14b` |
| `mode` | Operation mode: `summary` (default) or `review`.                                            | `false` | `summary` |
| `max-diff-length` | Max characters to process (prevents context overflow).                                      | `false` | `1500000` |
| `prompt-instructions`| Custom instructions to override the default prompt for the selected mode set in the script. | `false` | `''` |

---

## 🚀 Quick Start

### 1. Prerequisites
*   A **Self-Hosted Runner** with network access to your Ollama instance.
*   The following tools installed on the runner: `curl`, `jq`, and the `gh` (GitHub CLI).

### 2. Workflow Example (PR Summary)
Create `.github/workflows/pr-summary.yml`:

```yaml
name: AI PR Summary

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  summarize:
    # IMPORTANT: Use 'self-hosted' if Ollama is running on your local network/machine.
    # Can use ubuntu-latest if your Ollama instance is hosted online somewhere.
    runs-on: self-hosted
    permissions:
      pull-requests: write
      contents: read

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Ollama Scribe - PR Summary
        uses: cjgsaunders/ollama-scribe-local@2.0.0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          ollama-url: "http://localhost:11434" # Use a secret if your instance is public
          model-name: "qwen2.5-coder:14b"
          mode: 'summary' # This is the default
```

### 3. Workflow Example (PR Review)
Create `.github/workflows/pr-review.yml`:

```yaml
name: AI PR Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: self-hosted
    permissions:
      pull-requests: write
      contents: read

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Ollama Scribe - PR Review
        uses: cjgsaunders/ollama-scribe-local@2.0.0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          ollama-url: "http://localhost:11434"
          model-name: "qwen2.5-coder:14b"
          mode: 'review'
```