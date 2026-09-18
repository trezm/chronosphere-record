# Chronosphere Record

A self-contained agent skill that captures ordered code-change checkpoints, concise explanations, and command results for replay and PR review in Chronosphere.

## Install

Give your coding agent this instruction:

> Install the chronosphere-record skill from https://github.com/trezm/chronosphere-record. Install the complete skill directory, including its scripts, lib, and references folders, not just SKILL.md.

You can also [download the complete bundle](https://github.com/trezm/chronosphere-record/archive/refs/heads/main.zip), extract it, and place its contents in a `chronosphere-record` directory under your agent's skill installation directory. Keep the directory structure intact. This repository is public; no access to the private Chronosphere application repository is required.

Requires **Node 22.13+ and Git**. No npm install, API key, or remote MCP service is needed for recording. GitHub access is needed separately when reviewing a private PR in Chronosphere.

## Use

Ask your agent to use `chronosphere-record` while making code changes. It starts a local session before editing, records checkpoints after coherent changes, captures check results, and finishes the recording.

When a recording is ready to share with a PR, the skill asks whether you want to upload it as a **secret GitHub gist**. If you approve, it gzip-compresses and Base64-encodes the log in `replay.json`, uploads that file, pins its revision, and appends this line to the PR description:

```text
chronosphere-replay: https://gist.github.com/<owner>/<gist-id>/<revision>
```

Secret means unlisted, not private: anyone with the URL can read the replay, even if the PR is private. The agent waits for explicit consent before uploading. Posting requires GitHub CLI authentication with gist permission; local recording still needs no API access. Gist uploads support up to 10 MB of compressed envelope data, with a separate 20 MB limit on expanded JSONL. Compression does not encrypt the source. Existing plain `replay.jsonl` gists remain supported.

If you decline, the replay stays local. You can choose to commit **`.chronosphere/replay.jsonl`** instead or attach the `.jsonl` manually when opening the PR. Those options support logs up to 20 MB. Gist tags require a Chronosphere deployment that supports gist-linked recordings.

The recorder captures requested checkpoints, not every intermediate edit or private model reasoning. Recordings include source text and command output; raw sessions stay local until you choose to share them.

To prepare a compressed gist upload:

```sh
node --experimental-strip-types scripts/record.mjs export --compress --session /path/to/session.jsonl --out /temporary/replay.json
```

The compressed envelope includes the format version, encoding, expanded byte count and SHA-256 hash. Decompression counts streamed bytes rather than trusting the declared length, then checks the length, hash and recording format. Committed `.chronosphere/replay.jsonl` exports remain plain JSONL.

## Package contents

- `SKILL.md`: instructions for the coding agent
- `scripts/record.mjs`: local recorder and export command
- `lib/recording.ts`: portable format validator
- `lib/recording-transport.ts`: gzip + Base64 encoding and bounded decoding
- `references/recording.md`: format details and limits
- `tests/recording.test.mjs`: recorder regression tests

Run tests from this directory with `node --experimental-strip-types --test tests/*.test.mjs`.

The recorder and validator are published together. Update the complete skill directory to keep them in sync.
