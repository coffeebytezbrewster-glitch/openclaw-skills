# Technical Writer Skill

Write clear, helpful technical documentation.

## When to Use

Use this skill when:
- Writing or editing articles for the dev-notes project
- Creating documentation with code examples
- Explaining technical concepts to humans
- Writing README files or guides

## Guidelines

### Tone

- **Smart but approachable** — Assume readers are capable, not clueless
- **No fluff** — Skip "Great question!" and "Let's dive in!"
- **Opinionated** — It's okay to say what's good AND what's bad
- **Show, don't tell** — Code > descriptions

### Structure

Follow the dev-notes article template:

```
# [Topic] Explained for Humans

[One paragraph hook]

## The One-Sentence Pitch
**Bold summary.**

## Why Should You Care?
- Bullet points
- Specific use cases

## What Actually Is It?
Plain English explanation

## Let's See It In Action
### Installation
```bash
# Code
```
### Basic Usage
```bash
# Code
```
### Demo Code
```[language]
# Copy-pasteable example
```

## [Key Feature Deep Dive]

## Real-World Usage

## When NOT to Use This

## Getting Started

## The Bottom Line

---

## Resources
- **Official**: [link](url)
- **Documentation**: [link](url)
- **GitHub**: [link](url)

---

*Last updated: [Month Day, Year]*
```

### Screenshot Rules

- Desktop viewport: 1280x800
- Capture installation, running state, key features
- Save to `assets/screenshot-[description].png`
- Use `![Alt text](assets/screenshot-*.png)` in markdown

### Demo Code

- Simple, copy-pasteable
- Include comments
- Test before including

### File Naming

- Folder: `kebab-case` (e.g., `gitea-self-hosted-git`)
- Article: `[topic]-[topic].md` (e.g., `gitea-self-hosted-git.md`)
- Screenshots: `screenshot-[description].png`
- Demo files: `demo-[description].[ext]`

## Commands

Use the screenshot script for captures:

```bash
node screenshot.js <url> <output.png> [--width=1280] [--height=800]
```

## Example Output

See existing articles in `~/dev-notes/articles/`:
- `ollama-local-ai/`
- `openclaw-security/`
- `github-copilot-sdk/`
