# 🤖 Ollama Scribe — Private locally hosted AI PR Summaries

[![Marketplace](https://img.shields.io/badge/Marketplace-Ollama%20Scribe-blue.svg?colorA=24292e&colorB=0366d6&style=flat&longCache=true&logo=github)](https://github.com/marketplace/actions/ollama-scribe)
![License](https://img.shields.io/badge/license-MIT-green)
![Category](https://img.shields.io/badge/Category-Utilities-orange)

**Ollama Scribe** is a high-performance GitHub Action that uses locally-hosted LLMs to automatically summarize Pull Request diffs. Keep your source code private and eliminate OpenAI/Anthropic API costs.

---



## 💡 Why Ollama Scribe?

Existing AI PR tools often send your sensitive code to 3rd-party servers or require expensive monthly subscriptions. **Ollama Scribe** bridges the gap between DevOps and Local AI:

* **🛡️ Privacy First:** Your code never leaves your infrastructure. Ideal for proprietary or sensitive projects.
* **💰 Zero Cost:** Leverage your own hardware (GPUs/iGPUs) with Ollama. No per-token fees.
* **🧠 Logic-Aware:** Optimized for code-centric models like `Qwen2.5-Coder` and `DeepSeek-R1`.
* **⚡ Optimized Performance:** Handles large diffs gracefully with intelligent truncation and `jq`-powered JSON sanitization.

---



## ⚙️ Configuration

| Name | Description | Required | Default |
| :--- | :--- | :--- | :--- |
| `github-token` | The `GITHUB_TOKEN` to post comments. | `true` | N/A |
| `ollama-url` | The URL of your Ollama server endpoint. | `true` | `http://localhost:11434` |
| `model-name` | The Ollama model to use for analysis. | `false` | `qwen2.5-coder:7b` |
| `max-diff-length` | Max characters to process (prevents context overflow). | `false` | `1500000` |
| `prompt-instructions`| The instructions for the model that prefix the diff. | `false` | `Summarize the following code changes for a PR description. Use bullet points:` |

---

## 🚀 Quick Start

### 1. Prerequisites
* A **Self-Hosted Runner** with network access to your Ollama instance.
* The following tools installed on the runner: `curl`, `jq`, and the `gh` (GitHub CLI).

### 2. Workflow Example
Create `.github/workflows/pr-summary.yml`:

```yaml
name: AI PR Summary

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  summarize:
    # IMPORTANT: Use 'self-hosted' if Ollama is running on your local network/machine
    runs-on: self-hosted 
    permissions:
      pull-requests: write
      contents: read

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Ollama Scribe - PR Summary
        uses: cjgsaunders/ollama-scribe@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          ollama-url: "http://localhost:11434" # Use a secret if your instance is public
          model-name: "qwen2.5-coder:14b"
