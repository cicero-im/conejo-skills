---
name: conejo-code
description: Active coding loop — feature stages (brainstorm→plan→interface→tests→implement→improve→review→loop), red-green TDD execution, build-fix, deps-check, coding standards, bug-fix learning. Owns the canonical testing doctrine. Use when writing or changing code (backend, logic, APIs, libraries, CLIs).
---

# Conejo-code — active coding

The hands-on coding loop. Philosophy lives in [[conejo]]; this skill is how you execute it. Relentless, disciplined, experiment-driven and detail-oriented coding practice. ALWAYS set a timer to (1) wait for the user, (2) wait for Claude agents, (3) Claude or any other tool or (4) wait for opencode or forge agents. NEVER expect that you will be awaken by the response of any of these. YOU MUST BE FULLY AUTONOMOUS AND IMPLEMENT THE ORIGINAL PLAN INFERRING THE BEST ALTERNATIVES TO ENSURE IT WORKS.

## The loop (adapted feature stages)

- Brainstorm (Specs) → run "opencode run $PROMPT --model zai-coding-plan/glm-5.1 --dangerously-skip-permissions --dir  path/to/this/dir" to review your specs → Plan "opencode run $PROMPT --model deepseek/deepseek-v4-pro --dangerously-skip-permissions --dir  path/to/this/dir" to review your plan (glm-5.1 is smarter but slow, deepseek is a bit faster) → Interface → "opencode run $PROMPT --model deepseek/deepseek-v4-pro --dangerously-skip-permissions --dir  path/to/a/clone/of/this/dir" to create tests **failing tests** → "opencode run $PROMPT --model deepseek/deepseek-v4-pro --dangerously-skip-permissions --dir  path/to/a/clone/of/this/dir" to Implement → Improve tests → Code review  → (loop).
- Full stage notes: `references/feature-stages.md`. UI stages (design/UIUX-review) and any
visual work are owned by [[conejo-frontend]].
- Attention! Don't be afraid of sending several agents: split the tasks as much as possible. 
- PR over PR ALWAYS. Push. After it is at Github, clean their mess.
- PRESERVE your context no matter what! Send these other agents first, so we can have different perspectives, but if you need to do the work, send your agents. Don't pollute your context.

## Testing — READ FIRST

The universal red-green doctrine is canonical in `references/testing-doctrine.md`.
Backend/logic/CLI/library use `vitest`; UI behavior uses agent-browser click-flows
(see [[conejo-frontend]]). Never implement before a failing test.

## Standards & helpers

- Coding standards: `references/coding-standards.md` (+ `-api-design.md`).
- Dependency hygiene (bun): `references/deps-check.md`.
- Capture bug-fix learnings: `references/bug-fix-learning.md`.
- Strengthen tests after green: `references/auto-improve-tests.md`.
- Optional auto-trigger hooks: `hooks/` — install with `scripts/install-conejo-hooks.sh`.

## Plan stress-testing (Zanahoria family)

Three sibling skills that stress-test plans via @coderabbitai before you commit to implementation. Pick one based on how confident you are in the approach:

- **`references/zanahoria-plans`** — single plan, inverted and re-asserted until CR produces a battle-hardened version.
- **`references/zanahoria-multi-assumptions`** — 2–3 parallel framings of the same goal with deliberately shuffled assumptions, so CR has comparative material instead of a yes/no on a single approach.
- **`references/zanahoria-decisions`** — closes a multi-assumptions family by extracting the load-bearing assumption, naming a winner, capturing the decision as an ADR, and cleanly closing the rejected variants.

## Model preferences & multi-agent dispatch (READ BEFORE STAGE PROMPTS)

The line above ("Brainstorm... run opencode ... --model zai-coding-plan/glm-5.1 ...") is the **default** model. Override it whenever one of the conditions below applies.

**When to swap the default `glm-5.1`:**
- The user asks for a different point of view, contrarian take, devil's advocate, "what would X say", or expresses distrust of a single model.
- The user is "poor" (budget-constrained, cost-sensitive, or signals `cheap` / `free` / `avoid paid`).

**Preferred agent families (in order of preference):**

1. `zai-coding-plan/glm-5.1` — the default. Smartest but slow.
2. `minimax-coding-plan/minimax-m3` — the fast M3 coding plan; use for any task where speed > nuance.
3. `minimax` (vanilla) — only as a last-resort fallback.
4. `alibaba-coding-plan/qwen-3.7-max` — Alibaba's coding plan. Each family has multiple versions; **always substitute to the biggest available model of the same family** (e.g. `qwen-3.7-max` → next `qwen-3.x-max` or `qwen-4-max` if released).

**Invocation pattern — always parallel, always discrete:**

- Dispatch via `opencode run` (or `pi` when the user asks for it) in the **background** with `&` and `>/tmp/oc.log 2>&1`, then poll.
- **Always send multiple agents at the same time.** Split the task into discrete, independent units (one agent per unit). You may send **up to 20 agents in parallel**.
- Each agent gets a self-contained prompt: file paths, expected output, acceptance criteria, the model flag, and `--dangerously-skip-permissions --dir <workdir>`.
- Never reuse a working directory across agents that touch the same files — clone or copy first.
- Collect outputs, dedupe, pick the best, integrate.

**Canonical command template:**

```bash
nohup opencode run "$PROMPT" \
  --model <family>/<biggest-available-version> \
  --dangerously-skip-permissions \
  --dir "$WORKDIR" \
  > /tmp/oc-$AGENT_NAME.log 2>&1 &
```

**Replacing the `glm-5.1` in the loop above:**

| Stage | Default | If user wants a different POV | If user is "poor" |
|---|---|---|---|
| Brainstorm (Specs) | `zai-coding-plan/glm-5.1` | `minimax-coding-plan/minimax-m3` | `alibaba-coding-plan/qwen-3.7-max` (or biggest qwen) |
| Plan | `deepseek/deepseek-v4-pro` | `minimax-coding-plan/minimax-m3` | `alibaba-coding-plan/qwen-3.7-max` (or biggest qwen) |
| Interface (failing tests) | `deepseek/deepseek-v4-pro` | `minimax-coding-plan/minimax-m3` | `alibaba-coding-plan/qwen-3.7-max` (or biggest qwen) |
| Implement | `deepseek/deepseek-v4-pro` | `minimax-coding-plan/minimax-m3` | `alibaba-coding-plan/qwen-3.7-max` (or biggest qwen) |

When swapping, dispatch **2–4 agents in parallel** for that stage (one per family) and pick the best result; never sequentialize.

## Tooling

bun (not npm), vitest (not jest, not `bun test`), vite-plus (`vp`) for build/check. ESM only.
