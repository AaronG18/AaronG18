<div align="center">

# AaronG

**Open-source contributions to LLM infrastructure, made through my forks:**

[AaronG18/langgraph](https://github.com/AaronG18/langgraph) → [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph)
· [AaronG18/litellm](https://github.com/AaronG18/litellm) → [BerriAI/litellm](https://github.com/BerriAI/litellm)

**Sep - Nov 2026**

</div>

---

## Recent upstream contributions

Everything below is work I submitted to an upstream project from my own fork: fixes, regression tests, performance analyses, and issue triage. My own repositories are intentionally not listed on this page.

### BerriAI/litellm - November 2026 (2 pull requests)

| Pull request | What I changed | Status |
| --- | --- | --- |
| [#49885](https://github.com/BerriAI/litellm/pull/49885) | `calculate_request_duration()` returned `None` for audio uploads passed as `(filename, pathlib.Path(...))` tuples. The tuple branch handled `bytes` and file objects but not `os.PathLike`, so transcription duration was silently missing. The fix reads the path content inside that branch and adds a regression test (+34 lines, 2 files). Fixes [#49805](https://github.com/BerriAI/litellm/issues/49805). | **Open** - BerriAI CLA not signed yet |
| [#49979](https://github.com/BerriAI/litellm/pull/49979) | `azure_ai/claude-mythos-5-1` had no row in `model_prices_and_context_window.json`, so the `azure_ai` provider lookup rejected the model and the proxy recorded zero spend. Added the entry using Anthropic's published per-token rates plus a regression test (+70/-1, 2 files). Fixes [#49865](https://github.com/BerriAI/litellm/issues/49865). | **Closed 2026-11-05** - superseded by upstream [#50245](https://github.com/BerriAI/litellm/pull/50245), a registry audit that already added the row |

I also posted QA findings on [#49884](https://github.com/BerriAI/litellm/issues/49884) (Redis circuit breaker stuck in `HALF_OPEN` on a non-connectivity probe failure) and [#49805](https://github.com/BerriAI/litellm/issues/49805). On #49884 another contributor already had a complete patch and tests ready, so I yielded the implementation to them instead of competing.

### langchain-ai/langgraph - September to November 2026 (26 pull requests, 4 issues)

**SDK reliability (November 2026)**

- [#9625](https://github.com/langchain-ai/langgraph/pull/9625) - `thread.output.with_timeout(s)` cancelled the stream's shared `_run_done` future. `_fetch()` passed `_wait_for_run_done()` straight into `asyncio.wait_for`, and that function awaits the shared future directly, so a timeout cancellation propagated into `_run_done` itself: the original `thread.output` then raised `CancelledError` forever and a later run failure was hidden. The fix wraps that wait in `asyncio.shield(...)`, matching the shield pattern already used elsewhere in the same module. One behavioural line plus a regression test that is red before the fix and green after; 80 stream tests pass. Fixes [#9609](https://github.com/langchain-ai/langgraph/issues/9609).

Several of the 25 PRs are re-submissions of the same fix after the upstream issue-link bot closed the first one; the distinct work is grouped below.

**State, checkpoint, and store correctness**

- [#8799](https://github.com/langchain-ai/langgraph/pull/8799) / [#8980](https://github.com/langchain-ai/langgraph/pull/8980) - `InMemoryStore` treated dotted filter keys such as `user.access-level` as literal top-level keys. Resolve them against nested values, matching `SqliteStore` behaviour ([#7795](https://github.com/langchain-ai/langgraph/issues/7795)).
- [#8812](https://github.com/langchain-ai/langgraph/pull/8812) - SQLite stores ignored per-operation TTL `refresh` flags in batched reads. Align batch and single-item flag resolution with `BaseStore` and `PostgresStore` ([#8806](https://github.com/langchain-ai/langgraph/issues/8806)).
- [#8844](https://github.com/langchain-ai/langgraph/pull/8844) / [#8878](https://github.com/langchain-ai/langgraph/pull/8878) - public `__all__` import test suite that fails with the exact list of non-importable symbols when the advertised API drifts ([#5810](https://github.com/langchain-ai/langgraph/issues/5810)).

**Graph and runtime correctness**

- [#8800](https://github.com/langchain-ai/langgraph/pull/8800) / [#8981](https://github.com/langchain-ai/langgraph/pull/8981) - `StateGraph.compile()` mutated `StateNodeSpec` objects in place and leaked its generated error-handler node into the builder, so compiling the same builder twice raised. Apply defaults to copies ([#8612](https://github.com/langchain-ai/langgraph/issues/8612)).
- [#8982](https://github.com/langchain-ai/langgraph/pull/8982) - resolve a runtime-injected `store` when `update_state` / `aupdate_state` executes a conditional-edge router or node ([#6340](https://github.com/langchain-ai/langgraph/issues/6340)).
- [#9295](https://github.com/langchain-ai/langgraph/pull/9295) - a `@task` awaited inside a `StateGraph` node resolved to `None` under `astream_events` while `ainvoke` returned the value. Fix task result resolution on the streaming path ([#9284](https://github.com/langchain-ai/langgraph/issues/9284)).

**Performance in the Pregel loop**

- [#8856](https://github.com/langchain-ai/langgraph/pull/8856) / [#8877](https://github.com/langchain-ai/langgraph/pull/8877) - `_scratchpad` scanned `pending_writes` twice per candidate task. Resolve the task-independent resume write once per superstep: O(tasks x writes) → O(tasks + writes) ([#8855](https://github.com/langchain-ai/langgraph/issues/8855)).
- [#8858](https://github.com/langchain-ai/langgraph/pull/8858) - `put_writes` rescanned every channel per task for `UntrackedValue`. Compute the check once, since channels are fixed for the run ([#8857](https://github.com/langchain-ai/langgraph/issues/8857)).
- [#8860](https://github.com/langchain-ai/langgraph/pull/8860) - `on_done` called `should_stop` on the accumulated `done` set, rescanning k futures on the k-th completion. Check only the newly finished future: O(tasks^2) → O(tasks) ([#8859](https://github.com/langchain-ai/langgraph/issues/8859)).

**API surface and guardrails**

- [#8868](https://github.com/langchain-ai/langgraph/pull/8868) - `stream_mode="messages"` streamed every `BaseMessage` in state, leaking internal reasoning channels to clients. Add a configurable state-key restriction, wired into both the sync and async paths, with tests ([#6798](https://github.com/langchain-ai/langgraph/issues/6798)).
- [#8849](https://github.com/langchain-ai/langgraph/pull/8849) / [#8874](https://github.com/langchain-ai/langgraph/pull/8874) - private channels stay checkpointed and restorable but are excluded from `get_state` / `get_state_history` ([#8644](https://github.com/langchain-ai/langgraph/issues/8644)).
- [#8848](https://github.com/langchain-ai/langgraph/pull/8848) / [#8873](https://github.com/langchain-ai/langgraph/pull/8873) - input validators at compile time, applied in both `update_state` and `aupdate_state` ([#7119](https://github.com/langchain-ai/langgraph/issues/7119)).
- [#8847](https://github.com/langchain-ai/langgraph/pull/8847) / [#8872](https://github.com/langchain-ai/langgraph/pull/8872) - bind run context at the graph level instead of requiring `context=` on every invoke ([#7990](https://github.com/langchain-ai/langgraph/issues/7990)).
- [#8846](https://github.com/langchain-ai/langgraph/pull/8846) / [#8875](https://github.com/langchain-ai/langgraph/pull/8875) - per-node call counting and configurable limits, raising `NodeCallLimitError` on runaway invocations ([#5883](https://github.com/langchain-ai/langgraph/issues/5883)).

**Reliability and error reporting**

- [#9456](https://github.com/langchain-ai/langgraph/pull/9456) - retry transient `httpx` read/connect/protocol errors and timeouts in the SDK HTTP clients instead of propagating the raw exception ([#5819](https://github.com/langchain-ai/langgraph/issues/5819)).
- [#8845](https://github.com/langchain-ai/langgraph/pull/8845) / [#8876](https://github.com/langchain-ai/langgraph/pull/8876) - preserve the structured server payload in `RemoteException` instead of flattening it to one line ([#2559](https://github.com/langchain-ai/langgraph/issues/2559)).

**Upstream issues I opened**

- [#8855](https://github.com/langchain-ai/langgraph/issues/8855), [#8857](https://github.com/langchain-ai/langgraph/issues/8857), [#8859](https://github.com/langchain-ai/langgraph/issues/8859) - three Pregel-loop performance analyses with complexity measurements.
- [#8861](https://github.com/langchain-ai/langgraph/issues/8861) - an order-dependent flaky test that fails only in the full suite.

---

## Triage, QA, and contribution records

- Commented on **24 upstream issues** and **4 pull-request threads** in `langchain-ai/langgraph`: reproduction details, sync/async parity, cross-store consistency, and regression-test notes.
- Where another contributor had a complete patch ready, I did QA and yielded the implementation rather than opening a competing PR.
- Every change is documented in an English contribution-log issue on my fork (19 logs so far): [AaronG18/langgraph issues](https://github.com/AaronG18/langgraph/issues). Each log records the upstream link, what changed, quantitative evidence, and test results.

---

## Status, stated plainly

**None of these pull requests has been merged yet.**

- `langchain-ai/langgraph` runs a `require-issue-link` bot that closes external PRs on the day they are opened unless the author is assigned to the linked issue. **24 of the 25 September-October PRs were closed by that bot.** [#8868](https://github.com/langchain-ai/langgraph/pull/8868) is the one still open: after I was assigned to [#6798](https://github.com/langchain-ai/langgraph/issues/6798) it was reopened, and community review is complete - a reviewer found a real `state_keys` bug in the v2 streaming handler, I fixed it, and the same reviewer confirmed the fix; two further reviews verified the field filter and the rebase. It is waiting on maintainer review.
- On `BerriAI/litellm`, [#49885](https://github.com/BerriAI/litellm/pull/49885) is open and [#49979](https://github.com/BerriAI/litellm/pull/49979) was closed as superseded. Both also require signing the BerriAI CLA.
- [#9625](https://github.com/langchain-ai/langgraph/pull/9625) is open and mergeable (single commit, 2 files, +34/-1), but [#9609](https://github.com/langchain-ai/langgraph/issues/9609) is not yet assigned to me. I requested assignment on the issue; the `require-issue-link` bot may close this PR until a maintainer assigns it.

---

<div align="center">

[![GitHub](https://img.shields.io/badge/GitHub-@AaronG18-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/AaronG18)
[![Email](https://img.shields.io/badge/Email-aaron18g@outlook.com-0078D4?style=for-the-badge&logo=microsoft-outlook&logoColor=white)](mailto:aaron18g@outlook.com)

<sub>This page covers upstream open-source contributions only.</sub>

</div>
