<div align="center">

<img src="https://readme-typing-svg.demolab.com/?font=Fira+Code&size=26&duration=3200&pause=900&color=2F81F7&center=true&vCenter=true&width=760&lines=Hi%2C+I%27m+AaronG18.;I+build+AI+AGENTS+and+RAG+systems.;I+make+their+behavior+verifiable." alt="Typing SVG" />

**`build agent -> enforce rules -> verify evidence -> publish`**

<sub>AI Agent + RAG engineer. Two owned Python packages. Upstream contributions to LLM infrastructure.</sub>

</div>

<div align="center">

[![GitHub](https://img.shields.io/badge/GitHub-@AaronG18-181717?style=for-the-badge&logo=github)](https://github.com/AaronG18)

[![verifiable-contributions](https://img.shields.io/github/v/release/AaronG18/verifiable-contributions?style=for-the-badge&color=a371f7&label=VERIFIABLE-CONTRIBUTIONS)](https://github.com/AaronG18/verifiable-contributions)
[![langgraph-harness](https://img.shields.io/github/v/release/AaronG18/langgraph-harness?style=for-the-badge&color=a371f7&label=LANGGRAPH-HARNESS)](https://github.com/AaronG18/langgraph-harness)

</div>

## Contribution footprint

<table>
<tr>
<td align="center" width="20%"><a href="https://github.com/search?q=is%3Apr+author%3AAaronG18&type=pullrequests"><img src="https://img.shields.io/badge/108-PULL%20REQUESTS-2f81f7?style=for-the-badge&logo=git" alt="107 pull requests"/></a><br/><sub>authored</sub></td>
<td align="center" width="20%"><a href="https://github.com/search?q=is%3Apr+author%3AAaronG18+is%3Amerged&type=pullrequests"><img src="https://img.shields.io/badge/61-MERGED-3fb950?style=for-the-badge&logo=git-merge" alt="61 merged"/></a><br/><sub>merged</sub></td>
<td align="center" width="20%"><a href="https://github.com/search?q=is%3Aissue+author%3AAaronG18&type=issues"><img src="https://img.shields.io/badge/135-ISSUES%20OPENED-d29922?style=for-the-badge&logo=github" alt="134 issues opened"/></a><br/><sub>issues opened</sub></td>
<td align="center" width="20%"><a href="https://github.com/search?q=author%3AAaronG18&type=commits"><img src="https://img.shields.io/badge/196-COMMITS-8b949e?style=for-the-badge&logo=git" alt="193 commits"/></a><br/><sub>commits</sub></td>
<td align="center" width="20%"><a href="https://github.com/search?q=is%3Apr+author%3AAaronG18&type=pullrequests"><img src="https://img.shields.io/badge/CODE%20CHANGES-%2B45.9k%20%2F%20--32.7k-a371f7?style=for-the-badge" alt="+45.9k / -32.7k lines changed"/></a><br/><sub>code changes</sub></td>
</tr>
</table>

<sub>Snapshot **API-verified 2026-11-18** across all repositories, including PRs to my own projects; only **2** of the merged PRs are upstream merges (see the table below). Click any number to open the live, per-item search.</sub>

---

## What I build

I work on the infrastructure around **LLM agents and RAG pipelines**: how an agent
gets its instructions, how its tool calls are governed, and how anyone can verify
that a change is actually covered by tests. The two packages below are the concrete
result; the upstream PRs after them are the field tests.

---

## Owned projects

### [langgraph-harness](https://github.com/AaronG18/langgraph-harness) — per-node harness for LangGraph agents
[![PyPI](https://img.shields.io/pypi/v/langgraph-harness?style=for-the-badge&color=3fb950)](https://pypi.org/project/langgraph-harness/)
[![License](https://img.shields.io/github/license/AaronG18/langgraph-harness?style=for-the-badge&color=8b949e)](https://github.com/AaronG18/langgraph-harness/blob/main/LICENSE)

A LangGraph node is a plain callable: `add_node` has no notion of a per-node instruction
document, and `create_agent` has one global prompt. `langgraph-harness` fills that gap:
`@harnessed(harness)` binds a Markdown HARNESS to one node, exposes it via
`current_harness()`, validates the node's output against `MUST` / `MUST NOT` rules, and
`harness_gate()` routes violations to a repair node. LLM-agnostic; verified end-to-end
with a local Ollama model. Published as `langgraph-harness 0.7.0` on PyPI, 97 tests pass.

### [verifiable-contributions](https://github.com/AaronG18/verifiable-contributions) — prove tests exercise a diff
[![License](https://img.shields.io/github/license/AaronG18/verifiable-contributions?style=for-the-badge&color=8b949e)](https://github.com/AaronG18/verifiable-contributions/blob/main/LICENSE)

A GitHub Action born from this portfolio's own problem: CI says tests pass on the head
commit, but not whether the PR's tests actually exercise the change. It replays the
suite against base and head from a policy read only from the base ref, and emits a
tamper-evident receipt (`VERIFIED` / `NEEDS_EVIDENCE` / `NEEDS_RERUN` / `UNTRUSTED`).
`vrc audit` mutation-tests each change point. Seven releases, `v0.1.0` to `v0.5.0`;
adopted in my own
[enterprise-rag-service](https://github.com/AaronG18/enterprise-rag-service).

<sub>Honest boundary: a receipt is a reproducible measurement, not proof the test asserts
the right thing; a local receipt is a self-check, not a CI attestation.</sub>

---

## Upstream field tests

Most PRs were closed by upstream issue-link bots, not rejected on technical grounds.
Statuses are literal and API-checked **2026-11-18**.

| Upstream | PR | Area | State |
| --- | --- | --- | --- |
| [anyio](https://github.com/agronholm/anyio) | [#1471](https://github.com/agronholm/anyio/pull/1471) | lazy importer `dir()` missed submodules | **merged 2026-11-13** (`4d53ea15`) |
| [recharts](https://github.com/recharts/recharts) | [#8192](https://github.com/recharts/recharts/pull/8192) | `getNiceTickValues()` infinite domains | **merged 2026-11-17** (`548327520b0d`) |
| [recharts](https://github.com/recharts/recharts) | [#8208](https://github.com/recharts/recharts/pull/8208) | `rangeStep()` infinite-loop cap | **approved**, queued on maintainer |
| [recharts](https://github.com/recharts/recharts) | [#8254](https://github.com/recharts/recharts/pull/8254) | `allowDecimals=false` ticks outside the domain | open, CI running |
| [crewAI](https://github.com/crewAIInc/crewAI) | [#9539](https://github.com/crewAIInc/crewAI/pull/9539) | `kickoff()` ignored `max_execution_time` | open, CI running |
| [pydantic-ai](https://github.com/pydantic/pydantic-ai) | [#13889](https://github.com/pydantic/pydantic-ai/pull/13889) | unbracketed IPv6 `Host` header on default port | open, CI running |
| [langgraph](https://github.com/langchain-ai/langgraph) | [#8868](https://github.com/langchain-ai/langgraph/pull/8868) | `stream_mode="messages"` state-key filter | open, community-reviewed |
| [llama-agents](https://github.com/run-llama/llama-agents) | [#902](https://github.com/run-llama/llama-agents/pull/902) | `wait_for_event` overwrite of resolved waiter | open, regression test added |

Two performance ideas of mine landed upstream under maintainer PRs with an `@AaronG18`
credit: [langgraph #9629](https://github.com/langchain-ai/langgraph/pull/9629) and
[#9630](https://github.com/langchain-ai/langgraph/pull/9630).

<sub>Self-verified fix in progress: [langgraph #9681](https://github.com/langchain-ai/langgraph/issues/9681)
(ToolNode ignores `override(state=...)`), full diff and test evidence in the issue, PR
ready on assignment.</sub>

---

## Toolbox

<div align="center">

[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![pytest](https://img.shields.io/badge/pytest-0A9EDC?style=for-the-badge&logo=pytest&logoColor=white)](https://pytest.org/)

<br/>

<img src="https://skillicons.dev/icons?i=py,ts,git,githubactions,linux,bash,md&theme=dark" alt="Tech stack" />

</div>

---

<div align="center">

[![Email](https://img.shields.io/badge/Email-aaron18g@outlook.com-0078D4?style=for-the-badge&logo=microsoft-outlook&logoColor=white)](mailto:aaron18g@outlook.com)

<sub>Local, detailed contribution archive: `oss-workspace/index.html` (not public).</sub>

</div>
