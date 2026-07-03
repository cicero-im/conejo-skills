---
name: conejo-frontend
description: VERY STRICT frontend gate. Enforces the universal red-green doctrine PLUS the UI superset — agent-browser E2E click-flows (NEVER tRPC to assert UI), 24/7 dev server, test isolation, React + Tailwind v4 + shadcn (NO Ant Design), mandatory screenshot + browser-console + design-critique loop before "done". Indexes the design/UI reference skills. Use for any web UI work.
---

# Conejo-frontend — the strict UI gate

Frontend is [[conejo-code]] + the additions below. The red-green discipline is the
same; the vehicle for UI behavior is agent-browser click-flows. Doctrine is canonical
in `../conejo-code/references/testing-doctrine.md` — read it first.

## Hard rules (refuse work that violates these)

1. **Red-green, always.** No UI implementation before a failing agent-browser click-flow.
2. **NEVER use tRPC to assert UI behavior.** tRPC only for tokens/auth/seeding state.
3. **Dev server runs 24/7;** UI checks click against it via agent-browser (see
   [[browser-test-agent]]).
4. **Test isolation:** each click-flow seeds/resets its own state and cleans up.
5. **Stack:** React + Tailwind v4 + shadcn. **No Ant Design. No Vue.**
6. **Screenshot proof or it didn't happen.** IF you claim visible work is done THEN
   fresh screenshots from THIS session exist (desktop + mobile). A green test with an
   ugly page is a failing test with extra steps.
7. **Clean console or not done.** IF the browser console shows any error or warning
   during the click-flow THEN the work is not done. No exceptions for "just a
   hydration warning" or "just a 404 on a font".
8. **Critique loop is mandatory** for any visible change (below). Consult the relevant
   reference before accepting frontend work as done.

## The critique loop (run after every green click-flow)

Green tests prove behavior. They prove nothing about looks. IF you changed anything
the user can see THEN run this loop before calling it done:

1. **Screenshot.** Via agent-browser, capture the changed screens at desktop (~1440px)
   AND mobile (~390px) widths; light + dark if the app is themed. IF you cannot
   produce a fresh screenshot from this session THEN the work is NOT done — no
   memory-quoting, no "it looked fine earlier".
2. **Console.** Read the browser console across the whole flow (load → interact →
   navigate). IF any error or warning appears THEN stop and fix it first — console
   noise is a bug with a confession attached. Check the network tab for failed
   requests while you're in there.
3. **Critique — adversarial, ideally other-corpus.** Judge the screenshots against the
   reference bar: `refs/frontend-design` (taste), `refs/ui-ux-pro-max` (UX rules),
   `refs/typeset` (typography), `refs/colorize` (color), `refs/layout` (layout).
   Better: dispatch a DIFFERENT-corpus agent
   (`../conejo-code/references/agent-dispatch.md`) with the screenshot and the prompt:
   "Critique this UI. List concrete violations — spacing, hierarchy, contrast,
   alignment, empty/loading/error states. Zero credit for praise." A different corpus
   catches different taste failures — hidden gems apply to design too.
4. **Iterate.** IF the critique found real issues THEN fix → re-screenshot →
   re-console → re-critique. ELSE attach the final screenshots to the PR (evidence
   before claims) and stop. Two clean passes in a row = actually done.

## Reference index (folded skills under `refs/`)

- **Design taste & critique:** `refs/frontend-design`, `refs/refine-distill-frontend`,
  `refs/ui-ux-pro-max`, `refs/increase-impact-personality-frontend`, `refs/stitch-design-taste`
- **Typography:** `refs/typeset`, `refs/type-mania`
- **Color:** `refs/colorize`
- **UX planning:** `refs/ux-design-brief`
- **shadcn parity:** `refs/shadcn-parity`
- **Routing:** `refs/tanstack-router`, `refs/tanstack-router-best-practices`
- **Generative UI:** `refs/json-render`
- **SEO:** `refs/seo-audit`
- **Layout:** `refs/layout`

Foundational React/styling skills stay top-level: [[react]], [[react-best-practices]],
[[react-composables]], [[tailwind-v4]].
