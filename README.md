# Chronosphere Record

A self-contained agent skill that captures ordered code-change checkpoints, concise explanations, and command results for replay and PR review in Chronosphere.

## Install

Give your coding agent this instruction:

> Install the chronosphere-record skill from https://github.com/trezm/chronosphere-record. Install the complete skill directory, including its scripts, lib, and references folders, not just SKILL.md.

You can also [download the complete bundle](https://github.com/trezm/chronosphere-record/archive/refs/heads/main.zip), extract it, and place its contents in a `chronosphere-record` directory under your agent's skill installation directory. Keep the directory structure intact. This repository is public; no access to the private Chronosphere application repository is required.

Requires **Node 22.13+ and Git**. No npm install, API key, or remote MCP service is needed for recording. GitHub access is needed separately when reviewing a private PR in Chronosphere.

## Use

Ask your agent to use `chronosphere-record` while making code changes. It starts a local session before editing, records checkpoints after coherent changes, captures check results, and finishes the recording.

To share the result with a PR, ask the agent to export the finished log to **`.chronosphere/replay.jsonl`** at the target repository root and commit it with the changes. You can instead attach the `.jsonl` manually when opening the PR in Chronosphere.

The recorder captures requested checkpoints, not every intermediate edit or private model reasoning. Recordings include source text and command output; raw sessions stay local until you choose to share them.

## Package contents

- `SKILL.md`: instructions for the coding agent
- `scripts/record.mjs`: local recorder and export command
- `lib/recording.ts`: portable format validator
- `references/recording.md`: format details and limits
- `tests/recording.test.mjs`: recorder regression tests

Run tests from this directory with `node --experimental-strip-types --test tests/recording.test.mjs`.

The recorder and validator are published together. Update the complete skill directory to keep them in sync.
