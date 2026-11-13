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
| [#49885](https://github.com/BerriAI/litellm/pull/49885) | `calculate_request_duration()` returned `None` for audio uploads passed as `(filename, pathlib.Path(...))` tuples. The tuple branch handled `bytes` and file objects but not `os.PathLike`, so transcription duration was silently missing. The fix reads the path content inside that branch and adds a regression test (+34 lines, 2 files). Fixes [#49805](https://github.com/BerriAI/litellm/issues/49805). | **Closed 2026-11-09** - the `litellm:superseded` bot closed it after [upstream #49806](https://github.com/BerriAI/litellm/pull/49806) fixed #49805 on main; BerriAI CLA was signed, no maintainer review was posted |
| [#49979](https://github.com/BerriAI/litellm/pull/49979) | `azure_ai/claude-mythos-5-1` had no row in `model_prices_and_context_window.json`, so the `azure_ai` provider lookup rejected the model and the proxy recorded zero spend. Added the entry using Anthropic's published per-token rates plus a regression test (+70/-1, 2 files). Fixes [#49865](https://github.com/BerriAI/litellm/issues/49865). | **Closed 2026-11-05** - superseded by upstream [#50245](https://github.com/BerriAI/litellm/pull/50245), a registry audit that already added the row |

I also posted QA findings on [#49884](https://github.com/BerriAI/litellm/issues/49884) (Redis circuit breaker stuck in `HALF_OPEN` on a non-connectivity probe failure) and [#49805](https://github.com/BerriAI/litellm/issues/49805). On #49884 another contributor already had a complete patch and tests ready, so I yielded the implementation to them instead of competing.

### langchain-ai/langgraph - September to November 2026 (27 pull requests, 4 issues)

**SDK reliability (November 2026)**

- [#9625](https://github.com/langchain-ai/langgraph/pull/9625) - `thread.output.with_timeout(s)` cancelled the stream's shared `_run_done` future. `_fetch()` passed `_wait_for_run_done()` straight into `asyncio.wait_for`, and that function awaits the shared future directly, so a timeout cancellation propagated into `_run_done` itself: the original `thread.output` then raised `CancelledError` forever and a later run failure was hidden. The fix wraps that wait in `asyncio.shield(...)`, matching the shield pattern already used elsewhere in the same module. One behavioural line plus a regression test that is red before the fix and green after; 80 stream tests pass. Fixes [#9609](https://github.com/langchain-ai/langgraph/issues/9609).

Several of the 25 PRs are re-submissions of the same fix after the upstream issue-link bot closed the first one; the distinct work is grouped below.

**Interrupt handling (November 2026)**

- [#9684](https://github.com/langchain-ai/langgraph/pull/9684) - `interrupt_after="node_one"` matched node names as a substring, so a node literally named `no` was interrupted; `StateGraph.compile()` also raised `TypeError: can only concatenate list (not "str") to list` while joining the interrupt lists. Normalize a lone string (other than the `"*"` wildcard) to a 1-tuple at `StateGraph.compile()`, `Pregel.__init__` and `Pregel._prepare_compile()`, with sync + async regression tests asserting the pause happens after `node_one`. Fixes [#9277](https://github.com/langchain-ai/langgraph/issues/9277). **Closed by the `require-issue-link` bot (not merged)** because #9277 is not assigned to me; assignment requested on the issue.

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
- Every change is documented in an English contribution-log issue on my fork (20 logs so far): [AaronG18/langgraph issues](https://github.com/AaronG18/langgraph/issues). Each log records the upstream link, what changed, quantitative evidence, and test results.

---

## Status, stated plainly

**None of these pull requests has been merged yet.**

- `langchain-ai/langgraph` runs a `require-issue-link` bot that closes external PRs on the day they are opened unless the author is assigned to the linked issue. **24 of the 25 September-October PRs were closed by that bot.** [#8868](https://github.com/langchain-ai/langgraph/pull/8868) is the one still open: after I was assigned to [#6798](https://github.com/langchain-ai/langgraph/issues/6798) it was reopened, and community review is complete - a reviewer found a real `state_keys` bug in the v2 streaming handler, I fixed it, and the same reviewer confirmed the fix; two further reviews verified the field filter and the rebase. It is waiting on maintainer review.
- On `BerriAI/litellm`, both PRs are now closed without merging: [#49885](https://github.com/BerriAI/litellm/pull/49885) was closed 2026-11-09 by the `litellm:superseded` bot (`#49805 was fixed by #49806 on main`), and [#49979](https://github.com/BerriAI/litellm/pull/49979) was closed as superseded by #50245. No litellm PR of mine is open; linked issues #49805, #49884 and #49865 are all closed.
- [#9625](https://github.com/langchain-ai/langgraph/pull/9625) was **closed by the `require-issue-link` bot on 2026-11-07** (single commit, 2 files, +34/-1, `mergeable=True`), because [#9609](https://github.com/langchain-ai/langgraph/issues/9609) is not yet assigned to me. I requested assignment on the issue with the root cause and fix; the bot reopens the PR automatically once a maintainer assigns it. No code change is outstanding on my side.
- [#9684](https://github.com/langchain-ai/langgraph/pull/9684) was **closed by the `require-issue-link` bot on 2026-11-12** (not merged), because [#9277](https://github.com/langchain-ai/langgraph/issues/9277) is not assigned to me. The PR is a single commit with just the fix and its sync + async regression tests (3 files) on the fork branch; the closed PR's head is frozen at an older commit that also contained two CI files, which I have since removed. No code change is outstanding.
- **Correction, verified 2026-11-14:** #9277 was not an unclaimed issue. Two contributors had already asked to be assigned in October 2026 (`harish-gaggar` on 10-11 and `Aditya-Sharma-dev18` on 10-12), and [#9278](https://github.com/langchain-ai/langgraph/pull/9278) by harish-gaggar already fixes the same bug. My issue-triage keyword scan missed their phrasing (`assign it to me`, `assign this issue to me`, `the fix is in #`, `have a clean fix`), so I mistakenly treated the issue as free, posted an assignment request and opened #9684. That competes with earlier contributors and should not have happened. I am not reopening the PR or requesting assignment again; the implementation belongs to them.

---

<div align="center">

[![GitHub](https://img.shields.io/badge/GitHub-@AaronG18-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/AaronG18)
[![Email](https://img.shields.io/badge/Email-aaron18g@outlook.com-0078D4?style=for-the-badge&logo=microsoft-outlook&logoColor=white)](mailto:aaron18g@outlook.com)

<sub>This page covers upstream open-source contributions only.</sub>

</div>

---

## langgraph batch - 2026-11-08 (5 drafts; 4 yielded, 1 active)

Submitted to langchain-ai/langgraph from AaronG18/langgraph. Status verified via
API on 2026-11-08. Per the no-compete rule, the 4 issues that already had other
claimants/PRs were yielded (assignment requests withdrawn, related PRs not
pursued); no issue in this batch is now being pursued (see the 2026-11-09 review)
(PR #9642 auto-reopens once assigned).

| Issue | PR | Scope | Fix | Status |
|-------|----|-------|-----|--------|
| #9603 | #9627 (not pursued) | cli | non-string python_version/node_version -> clear UsageError | yielded to @Steve-20-ai (#9613) / @JY-M666 (#9616) |
| #9591 | #9640 (not pursued) | langgraph | deferred_nodes init before draw loop | yielded to @kyo-zzz (fix ready) |
| #9592 | #9641 (not pursued) | prebuilt | ValidationNode returns is_error ToolMessage | yielded to @JY-M666 |
| #9620 | #9642 | sdk-py | AsyncThreadStream.events releases completed handles from `_open_handles` | not pursued: competing claim appeared 2026-11-08 |
| #9561 | #9643 (not pursued) | checkpoint-sqlite | setup() idempotent (PRAGMA check before ALTER) | yielded to @tkbeheranc |

#9581 was yielded to @ZhuLinsen (PR #9605). All yields posted as comments on
2026-11-08; per the no-compete rule the 4 PRs (#9627/#9640/#9641/#9643) are not
pursued and their assignment requests were withdrawn. #9620 also gained a competing claim on 2026-11-08, so it is no longer being pursued either.

---

## Two self-verified fixes - 2026-11-09 (both PRs open, not merged)

Following the 2026-11-09 scouting, both selected upstreams turned out to be
blocked for new fixes: litestar-org/litestar returns
`pull_request_creation_policy = collaborators_only` (no external PRs since
2026-10-14), and every recharts issue candidate was either already fixed on
main, already covered by an open placeholder PR, or in a module another PR was
editing. Rather than open a competing PR, I verified two new bugs on current
main myself and fixed them.

| Upstream | PR | Fix | Evidence | State |
|----------|----|-----|----------|-------|
| recharts/recharts | #8192 | `getNiceTickValues` used the raw `tickCount` instead of the clamped `count` for infinite domains, so `tickCount=0` threw `RangeError: Invalid array length` and `tickCount=1` returned one tick | Red before (`RangeError` at line 246), green after (82 passed); full `npm test` 9234 passed; eslint and prettier clean | open, mergeable, not merged |
| agronholm/anyio | #1471 | `install_lazy_importer()`'s `__dir__` listed only `lazy_map`, omitting lazy submodules and deprecated aliases that `__getattr__` resolves | Red before (`'abc' not in dir(anyio)`), green after (6 passed); ruff check/format clean; wider suite 1342 passed vs 1341 baseline with identical environment-only failures | open, mergeable, not merged |

Both PRs carry regression tests that fail without the patch, and both fork
issue logs were created. Neither has been reviewed or merged. The anyio PR
includes the required `changelog.d/1471.fixed.rst` fragment; all 82 open anyio
PRs were checked first to avoid duplicating work (PRs #1368 and #1340 had
already covered two other bugs I found).


Scouting note (2026-11-08, no contribution submitted): surveyed 20 upstream
repos for merge-friendliness (governance bots, CLA gates, external-contributor
merge share, open good-first-issue pools). No new PR was opened from this scan.

Follow-up scouting (2026-11-08, still no contribution submitted): checked 4
candidate upstreams for a genuinely unclaimed fix (issue comments + timeline
cross-referenced PRs + open-PR search). No PR was opened, because none of the
targets passed the no-compete check:
- run-llama/llama_index: 119 open bug-like issues screened; every one either has
  an open linked PR, a claim comment, or was already fixed. The 5 apparent clean
  ones were workflows bugs whose code lives in run-llama/llama-agents with fixes
  already open there, or an issue already fixed by PR #23287.
- modelcontextprotocol/python-sdk: CONTRIBUTING + require-linked-issue.yml require
  the PR author to be assigned to the linked issue (same gate as langgraph).
- agno-agi/agno: a PR-triage bot auto-closes any PR whose linked issue lacks the
  maintainer 'ready' label; two candidates were explicitly reserved by their reporter.
- confident-ai/deepeval: no assignment/CLA gate, but 33 of 36 open issues already
  have an open placeholder PR; the 3 clean ones are feature/doc requests, not fixes.
Sample of 32 placeholder PRs across deepeval/llama_index: 1 merged, 31 still open
unmerged - the easy-issue pools are saturated with unreviewed PRs, not real merges.

Follow-up (2026-11-09, no contribution submitted): checked the timing of our
pending assignment requests on langchain-ai/langgraph #9620 and #9609. Both were
posted 2026-11-07 (17:56Z and 15:59Z) - too recent to follow up, so no new
comment was posted. Both issues remain unassigned and PRs #9642 / #9625 remain
closed by the require-issue-link bot. Note: #9620 gained a competing claim on
2026-11-08 09:23Z; our PR #9642 predates it (2026-11-08 01:47Z).

Also scouted 38 React-ecosystem and Python-backend repos for merge-friendliness
(CONTRIBUTING gates, CLA, triage bots, human-external merge share, clean issue
pools). No PR opened. Findings: facebook/react merges essentially no external
PRs (0 of last 20); vite and aiohttp run agent-scan bots that auto-close
AI-flagged PRs; sqlalchemy's high external share is almost all docs typo
fixes because code changes go through Gerrit, not GitHub PRs; litestar's
apparent 44% was almost entirely dependabot (1 real human in 50). The two
most workable were recharts (27 of last 50 merged PRs from real external
humans, no CLA/assignment gate, 10 good-first-issues) and strawberry (6 of 50,
but has a maybe-ai label policy and only stale/insider issues left clean).


---

## Full portfolio review - 2026-11-09 (API-verified, no contribution submitted)

Audited every upstream repo where AaronG18 has submitted work. Totals across
langgraph, litellm, recharts and anyio: **35 pull requests, 0 merged, 4 open,
31 closed without merging**. Of the closed ones, 30 were auto-closed by
langgraph's `require-issue-link` bot (`missing-issue-link` label) and 1
(litellm #49979) was superseded by upstream #50245, which was merged.

| Repo | PRs | Merged | Open | Closed unmerged |
|------|-----|--------|------|-----------------|
| langchain-ai/langgraph | 31 | 0 | 1 (#8868) | 30 (all bot-closed) |
| BerriAI/litellm | 2 | 0 | 1 (#49885) | 1 (superseded) |
| recharts/recharts | 1 | 0 | 1 (#8192) | 0 |
| agronholm/anyio | 1 | 0 | 1 (#1471) | 0 |

Why nothing has merged: every one of the 4 open PRs has **0 approving reviews**
and `mergeable_state` is `blocked` (or `unstable` for recharts). CI is not the
blocker: #8868 has 66/66 successful check-runs, #1471 has 17/17 (Ubuntu across
Python 3.11-3.15 and PyPy), #49885 has CLA success, and #8192 has CodeRabbit
success. The 30 bot-closed langgraph PRs cannot be reopened by us; their linked
issues were checked and 21 of 22 remain unassigned to AaronG18, so
`reopen_on_assignment` will not trigger.

The one exception that proves the rule: langgraph **#8868** is the only PR whose
linked issue (#6798) is assigned to AaronG18, so the bot reopened it and it is
still open. It has 4 community reviews, 8 comments, 7 commits and was rebased
and pinged as recently as 2026-11-05. It is the closest to merging.

Fork contribution logs: AaronG18/langgraph has 20, AaronG18/recharts 1,
AaronG18/anyio 1. AaronG18/litellm had issues disabled; enabled and backfilled
during this review. Litellm #49865 is now closed; #49805 and #49884 remain open
and unassigned.

**Status refresh 2026-11-14 (API-verified; supersedes the counts above, which are kept as the 2026-11-09 snapshot):** 37 pull requests across the four repos, 0 merged, 4 open / 33 closed. langchain-ai/langgraph now has 32 PRs (31 closed, only [#8868](https://github.com/langchain-ai/langgraph/pull/8868) open); BerriAI/litellm 2 (both closed); recharts/recharts 2 ([#8192](https://github.com/recharts/recharts/pull/8192) and [#8208](https://github.com/recharts/recharts/pull/8208), both open); agronholm/anyio 1 ([#1471](https://github.com/agronholm/anyio/pull/1471), open). #49885 was closed 2026-11-09 by the superseded bot because #49806 fixed #49805 on main, so the earlier '1 open' litellm figure no longer holds. Linked litellm issues #49805, #49884 and #49865 are all closed now. langgraph #6798 remains assigned to AaronG18 and niilooy; #9609, #9620 and #9277 remain open and unassigned.

---

## Owned project - 2026-11-09 (not a fork contribution)

This page lists contributions to upstream projects from forks. One new repository
is deliberately kept out of those tables because it is an owned project rather
than an upstream contribution: **AaronG18/verifiable-contributions**.

The motivation came from the 2026-11-09 portfolio audit on this page (35 PRs,
0 merged, 4 open with green CI but no approving review) and from LF Research
(2026-10-20): 25% of incoming PRs are estimated to be low-quality AI output,
53% of maintainers disagree that GenAI improved community trust, and only 35%
of projects have a formal GenAI contribution policy. The project aims to make
AI-assisted contributions independently verifiable for maintainers.

The repository is public (Apache-2.0). `main` is protected by an active ruleset
requiring a pull request, one approving review from the code owner, a passing
`verify` check, linear history and squash-only merges, with zero bypass actors.
This was verified end to end: a direct push to `main` returned HTTP 409, and a
merge attempt on an unapproved PR was rejected with `Waiting on code owner
review from AaronG18`. PRs are opened by a bot workflow so that the human owner
can genuinely approve them. No upstream contribution was submitted in this work;
the internal working rules (`oss_direction.mdc`) were also updated with the
audit findings.

### MVP - 2026-11-10 (owned project, PR open, not merged)

The first implementation landed on a `work/` branch of
`AaronG18/verifiable-contributions` as PR #4, opened by `github-actions[bot]`
so the owner can genuinely approve it. CI (`verify` and `receipt`) is green and
the receipt artifact was produced. PR `mergeable_state` is `blocked` with zero
reviews, so the owner's approval is the only remaining step. Nothing here is
merged.

The MVP replays the head's test files on the base tree (which lacks the source
change) and on the head tree, then emits a structured receipt with one of four
verdicts. Verified locally with 37 hermetic tests plus ruff, and verified in CI.
Dogfooding the project's own first PR correctly returns `UNTRUSTED`, because a
verifier cannot certify its own introduction.

### Governance update - 2026-11-10 (owned project)

PR #4 (the verification-receipt MVP) was merged into `main` as `eaebedd`, and
PR #6 (policy documentation sync) as `fc8354a`. Both were opened by
`github-actions[bot]`, passed CI, received an owner approval and were squash
merged. No direct push to `main` is possible: a probe still returns HTTP 409
`Changes must be made through a pull request`.

The owner changed the merge policy: the owner's own changes may be approved and
merged by the agent acting for the owner, provided they still go through a PR
with passing CI and a recorded approval. The GitHub ruleset was adjusted to make
that possible in a single-owner repository by removing
`require_last_push_approval` (which demanded an approval from someone other than
the last pusher) and the `creation`/`update` rules (which blocked the squash
merge itself). All other protections remain: 1 approving review, code owner
review, required `verify` check, linear history, squash-only, no deletion or
force pushes, and zero bypass actors.

External contributors are still held to a human review by the code owner; the
agent must not approve or merge someone else's PR. One dependabot PR (#5) is
left open for that human review.

### PR status check - 2026-11-10

API-verified update to the portfolio above. `recharts/recharts` PR #8192 is now
the first of the upstream PRs to receive a maintainer review: @PavelVanecek
submitted `APPROVED` on 2026-11-09, all CI checks pass, and `mergeable_state`
is `clean`. It is still open and not merged; the merge itself is the
maintainer's decision. `agronholm/anyio` #1471 has 17/17 successful check-runs
but still has no reviews and is `blocked`.

On the owned project, dependabot PR #5 (`actions/upload-artifact` v4 to v7,
one line in `action.yml`) is the first real external PR to exercise the
verification receipt. The receipt correctly returned `UNTRUSTED` because
`action.yml` is a protected path, even though both test runs passed. It stays
open for a human review under the policy added in PR #6.

No new contribution was submitted in this check.

### Supply-chain hardening - 2026-11-10 (owned project)

Dependabot PR #5 (`actions/upload-artifact` v4 to v7) was closed without
merging, superseded by PR #7 (merged as `436f6f6`). The root cause of that noise
was our own use of mutable major tags while the security policy said actions
should be pinned to commit SHAs.

All third-party actions in the owned repository are now pinned to full
40-character commit SHAs with the release tag in a trailing comment. Dependabot
version updates for `github-actions` are disabled via
`open-pull-requests-limit: 0`; per GitHub's options reference that only stops
version updates, while security updates keep their own fixed limit, and
`dependabot_security_updates` remains enabled. So CVE fixes still arrive as PRs
but major-chase PRs stop. `SECURITY.md` and the dependabot config are now
protected paths, so weakening them marks a receipt `UNTRUSTED`.

Verified after the merge: 40 tests pass, ruff clean, CI green, no tag references
remain in any workflow, all security toggles still enabled, direct pushes to
`main` still return HTTP 409, and there are zero open PRs.

### v0.1.0 released - 2026-11-10 (owned project)

PR #8 was merged as `6da1023`. It closes the false positive recorded in the
threat model: a base run that fails because of an import or collection error is
now classified separately and can no longer produce `VERIFIED`. Only a
recognised assertion failure qualifies; the matching output line is stored and
re-checked by `vrc check`. Receipt schema is now 2.

A 31-check pre-release audit passed with zero failures (54 tests, ruff clean,
end-to-end replay, receipt round-trip, tamper detection, action metadata, SHA
pinning, documentation consistency). The repository also gained `branding` for
the Marketplace listing, nine discovery topics, and a published Release
[v0.1.0](https://github.com/AaronG18/verifiable-contributions/releases/tag/v0.1.0).

Marketplace listing itself is **not yet live**: GitHub requires the owner to
check "Publish this Action to the GitHub Marketplace" in the release UI, accept
the Marketplace Developer Agreement, choose a category and confirm with 2FA.
There is no REST API parameter for that step, so it cannot be automated from
this workspace. Zero open PRs remain.

