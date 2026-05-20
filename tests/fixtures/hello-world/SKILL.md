---
name: hello-world
description: Greets the user with a hello world message in a specified language. Use when you need a simple greeting or want to verify that basic output works.
license: MIT
compatibility: Works in any shell environment
allowed-tools: Bash(echo *)
metadata:
  author: aipcc-cicd
  version: "1.0.0"
---

# Hello World

Greets the user with a hello world message. This is a minimal example skill demonstrating the full Agent Skills spec format and is used as a CI test fixture for the skill-linter-action.

## Usage

Use this skill when you want to print a greeting. You can optionally specify a language.

## Steps

1. Ask the user for the target language (default: English)
2. Print the appropriate hello world message for that language
3. Confirm the greeting was displayed

## Gotchas

- If no language is specified, default to English
- Keep the message short — this is a greeting, not an essay

## Examples

```bash
echo "Hello, World!"
```

```bash
# Spanish
echo "¡Hola, Mundo!"
```
