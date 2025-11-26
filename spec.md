RL4-CODEX v1.1 — Cognitive State Exchange Protocol (Draft Standard)

By R-Labs

“Let’s build the next cognitive protocol.”

⸻

0. Abstract

RL4-CODEX is a lightweight, deterministic, privacy-preserving protocol for serializing and exchanging the cognitive state of an AI workspace or conversation between different LLMs — without ever transferring source code or private text.

It enables:
	•	Multi-LLM interoperability
	•	Project-aware reasoning without exposing files
	•	Fast context teleportation
	•	Cognitive continuity across tools, devices, and models
	•	A universal cognitive snapshot of any session or workspace

RL4-CODEX does not transmit content.
It transmits meaning.

⸻

1. Purpose

Modern LLMs lack a standard way to:
	•	Share reasoning state
	•	Transfer session memory
	•	Maintain project context across models
	•	Persist long-term cognitive signals
	•	Exchange semantic information without raw data

RL4-CODEX solves this.

It provides:
	•	A compact snapshot (<4 KB target)
	•	Model-agnostic formatting
	•	Deterministic encoding
	•	Secure semantic abstraction
	•	Fully portable cognitive metadata

⸻

2. Core Principles
	1.	Zero Content Transfer
No code, no text, no diffs, no filenames. Only hashed, normalized semantic descriptors.
	2.	Deterministic Serialization
Identical input state → identical CODEX output.
	3.	Model Agnostic
Works with GPT, Claude, Mistral, Gemini, Llama, or any agent.
	4.	Stable, Minimal Size
Target payload < 4 KB, hard cap 10 KB.
	5.	Composable & Interoperable
Compatible with:
	•	Any LLM
	•	Any IDE extension
	•	RL4 Snapshot
	•	RL4 Cognitive Kernel
	•	Future CODEX versions
	6.	Decode-Friendly
A minimal decoder can reconstruct a CognitiveState object from the payload.

⸻

3. High-Level Structure

A CODEX payload contains five required blocks and one optional block, inside a top-level tag.

<RL4-CODEX v="1.1">
  <DATA>...</DATA>
  <TIMELINE>...</TIMELINE>
  <CONTEXT>...</CONTEXT>
  <INSIGHTS>...</INSIGHTS>
  <DECISIONS>...</DECISIONS>
  <OPTIONAL>...</OPTIONAL> <!-- optional -->
</RL4-CODEX>

All block contents are compact token streams, not JSON.

⸻

4. Required Blocks

4.1 DATA Block — Current Cognitive State

Represents the semantic state of the workspace/session.

Must include (in abstract form):
	•	Project identity (hashed)
	•	Active mode (strict / normal / flexible / exploratory)
	•	Phase (e.g., planning, building, refactoring)
	•	Task load (counts only)
	•	KPIs or health indicators
	•	Cognitive health score
	•	Workspace maturity level

No raw content allowed.

⸻

4.2 TIMELINE Block — Recent Activity

Summarizes the last 24–48h of cognitive activity.

Contains:
	•	Number of cycles
	•	Types of actions (edit / commit / refactor / analysis / idle)
	•	Activity bursts or droughts
	•	Hotspots (hashed module tags)
	•	Temporal anomalies

Never include timestamps or filenames.
Use compressed, symbolic descriptions only.

⸻

4.3 CONTEXT Block — Stable Metadata

Includes:
	•	Project type (frontend, backend, ML pipeline, mobile, etc.)
	•	Technology stack (hashed/normalized)
	•	Developer profile (“Developer DNA”)
	•	Constraints
	•	Success criteria
	•	Reasoning style preference
	•	Risk profile

This allows a remote LLM to calibrate to the user’s cognitive style.

⸻

4.4 INSIGHTS Block — Cognitive Signals

Synthesizes:
	•	Patterns
	•	Correlations
	•	Forecasts
	•	Trend signals

Rules:
	•	Only semantic summaries
	•	Only categories, weights, confidences
	•	No code-derived text
	•	No literal patterns

⸻

4.5 DECISIONS Block — High-Level Decision Context

Includes:
	•	Active ADR identifiers (hashed)
	•	Drift signals (plan drift, task drift, phase drift)
	•	Anomalies detected
	•	Integrity / health warnings
	•	Recommended adjustments (semantic only)

This block lets the decoder reconstruct the “reasoning climate” of the session.

⸻

5. Optional Block

May contain:
	•	Vendor-specific metadata
	•	Additional cognitive metrics
	•	Session fingerprint
	•	LLM version metadata

Rules:
	•	< 512 characters
	•	No raw content
	•	Must not break compatibility

⸻

6. Encoding Rules
	1.	Tag Order is mandatory:
DATA → TIMELINE → CONTEXT → INSIGHTS → DECISIONS → OPTIONAL
	2.	Whitespaces are flexible
The CODEX should remain readable but compressible.
	3.	Compression strategy is implementation-defined
Human-readable token streams are preferred.
	4.	Ignore unknown tags
Mandatory for forward compatibility.
	5.	Size recommendations
	•	Entire CODEX: < 4 KB target
	•	Maximum allowed: 10 KB

⸻

7. Validation Requirements

A CODEX payload is valid if:
	•	The top-level <RL4-CODEX> tag exists
	•	All five required blocks exist (even if empty)
	•	No raw content is present
	•	Hashes/normalizations are used for identifiers
	•	The payload is parseable

A decoder must:
	•	Parse what exists
	•	Warn about missing or corrupted blocks
	•	Continue gracefully

⸻

8. Decoding Specification

A compliant decoder reconstructs an internal:

CognitiveState Object

Containing:
	•	ProjectContext
	•	TemporalContext
	•	CognitiveSignals
	•	DecisionContext
	•	DeveloperProfile

Missing fields must be interpreted as unknown, not zero.

A decoder must never expect:
	•	File paths
	•	Raw code
	•	Exact timestamps
	•	Literal patterns
	•	Absolute identifiers

Only semantic abstractions.

⸻

9. Security & Privacy

Strictly forbidden:
	•	Raw code
	•	Text excerpts
	•	Commit messages
	•	Diffs
	•	File paths
	•	Identifiable names
	•	Credentials

Mandatory hashing/normalization for:
	•	Filenames
	•	Module identifiers
	•	ADR references
	•	Usernames
	•	Branch names

⸻

10. Versioning
	•	v1.1 is backward-compatible with v1.0
	•	Unknown fields must be ignored
	•	Missing v1.1 additions treated as optional
	•	Implementations should always include all required blocks

⸻

11. Reference Encoder Prompt (LLM-Only Edition)

This is the minimal prompt a user can run on any LLM to encode a conversation into CODEX:

You are an RL4-CODEX encoder.
Summarize this conversation into a <4 KB cognitive snapshot using the RL4-CODEX v1.1 structure.
Include DATA, TIMELINE, CONTEXT, INSIGHTS, DECISIONS blocks.
Use semantic descriptions only. No raw quotes, no code, no file contents.
Respond only with the XML payload.

12. Reference Decoder Prompt

This is how anyone can load a CODEX into any model:

You are an RL4-CODEX decoder.
Given the CODEX payload, reconstruct the cognitive state:
project context, timeline, insights, decisions, developer profile.
Treat missing fields as unknown.
Do not expect raw code.
Resume the conversation as if the entire history were present.

13. Why RL4-CODEX Matters

RL4-CODEX is the first protocol designed to:
	•	Move cognitive state without content
	•	Allow multi-LLM interoperability
	•	Enable reasoning continuity across tools
	•	Provide a deterministic, universal “mental snapshot”
	•	Work in IDEs, chats, agents, and pipelines

This is the missing layer between LLMs and real project cognition.

It aims to be the HTTP of cognitive context.

⸻

14. License

MIT (recommended)
This encourages adoption and ecosystem growth.

⸻

END OF SPEC — RL4-CODEX v1.1

