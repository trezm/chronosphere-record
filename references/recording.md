# Recording format and limits

The recorder runs locally with Node 22.13+ and Git. No npm dependencies or hosted MCP service are needed. Use `node --experimental-strip-types <skill-dir>/scripts/record.mjs --help` for available commands.

## Capture and playback

Sessions default to `.chronosphere/recordings/<id>.jsonl` inside the target Git working copy. The first line contains a baseline of eligible text files, including existing uncommitted changes. Later lines contain ordered checkpoints with stable event IDs, timestamps, titles, brief change explanations, and reversible file replacements. Command events include arguments, exit status or signal, and bounded combined output.

Each file snapshot includes a SHA-256 hash. Import validates those hashes, event sequence, unique IDs, and the prior state of every replacement. These checks detect inconsistencies; they do not authenticate the author or establish that every intermediate edit was captured.

The `.local.json` companion contains the working-copy location and is needed to continue capturing or to locate the default export destination. It is not needed for playback and must stay local. A finished `.jsonl` can be exported without its companion by supplying `export --session <file> --out <destination>` explicitly.

For PR playback, finish the session and export to `.chronosphere/replay.jsonl` at the target repository root, then commit that artifact when sharing it is requested. Keep raw logs ignored using the rules in SKILL.md. Chronosphere reads that conventional path from the PR head commit. Alternatively, open the PR in Chronosphere and choose Attach recording. Manual attachments need reattaching after refresh; review drafts persist. The standalone `/recordings` view supports local inspection without GitHub authentication.

## Boundaries

- Capture uses Git-tracked and non-ignored untracked UTF-8 text files within the selected literal `--include` scopes. Renames appear as deletion plus addition.
- Common credential names, symlinks, binary files, and files larger than 256 KB are omitted. The log lists omissions within the selected scope. Filename exclusions cannot guarantee that source or command output contains no sensitive data; inspect before sharing.
- A previously captured file that becomes ignored or unreadable keeps its last recorded content and is listed as omitted, rather than being represented as deleted.
- Initial/current readable snapshots are limited to 8 MB and 5,000 files. Sessions are limited to 20 MB and 2,000 events. Command output is limited to 32,000 characters.
- The recorder excludes `.chronosphere`, so exported recordings do not recursively capture themselves.
- `run` refuses pending unrecorded changes, records command failures, and returns the command's exit status. Capture a checkpoint before running checks.
- One writer owns a session. Exclusive locks prevent interleaved appends. After a crash, inspect any lock and partial log before attempting recovery; never overwrite earlier history.
- Finished sessions cannot be appended to. Later work needs a new session or an explicit coverage note.
