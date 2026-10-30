# Session records of a recorded AI-native research session (2026-10-31, 03:17 to 04:19 CST)

These are the agent logs behind the screen recording of one research session: checking the photon-channel baseline of a primordial black hole (PBH) multimessenger project and extending it to generalized beta prime mass functions. The researcher (Tae-Geun Kim) directs the agents; every instruction he typed is in `main/session.jsonl`.

| Folder | Session | Model / harness | Time (CST) |
|---|---|---|---|
| `main/session.jsonl` | Main research session: every human instruction and agent turn | Claude Opus 5.5 / omp | 03:17:58 to 04:19:03 |
| `main/subagents_and_logs/` | Report reviewers (UnderstandingReview, EvidenceReview) and tool logs | Claude Opus 5.5 / omp | 03:57 to 04:10 |
| `error_provenance/session.jsonl` | Agent launched on request to trace the error columns of the data file to their sources | Claude Opus 5.5 (low effort) / omp | 03:29 to 03:42 |
| `astra/session.jsonl`, `astra/subagents/` | Adversarial review of the baseline and the mass-function extension | GPT-6 Astra / omp | 03:29 to 03:47 |
| `argon/transcript_full.jsonl` | Proposal of figures for the report | Gemini 4 Argon / Antigravity | 04:01 to 04:03 |

## What was changed before release
Only removals and placeholders; nothing was rewritten.
- The harness system prompt stored with each subagent (it holds a personal skill catalog and account details) is replaced by a note.
- Credential entries are reduced to their timestamps.
- Personal e-mail addresses, a Slack user id, home-directory paths, private IP addresses, the device name and local hook tokens are replaced by placeholders.
- Three tool outputs that read files outside this project are replaced by a note saying what was removed: a search over application config files, the text of a private agent skill, and the preparation notes (`HANDOFF.md`) that the Argon session opened although its brief said not to.

`MANIFEST.sha256` lists the checksum of every file as released.

Not included: the aborted first attempt (03:13 to 03:16), the preparation session, and an accidental test session.

The forward pipeline the agents reuse was written by the researcher for an ongoing collaborative project (OSPREY). The COMPTEL rows of the background data were replaced during the session after the provenance agent found they did not match the cited thesis table; see `error_provenance/session.jsonl`.

License: CC BY 4.0.
