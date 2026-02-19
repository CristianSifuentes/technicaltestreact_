# technicaltestreact_

This repository is now a **structured React 19 learning roadmap** to build interview-level and professional-level knowledge.

## 1) What the images are telling us

From your screenshots, there are two strong signals:

1. **Prompt quality matters** (clear role, scope, constraints, and specific questions).
2. Your React learning targets are broad and interview-oriented:
   - Context and composition
   - State updates and batching
   - React 19 compiler and React 19 changes
   - `useState`, `useEffect` (dependencies + async behavior)
   - React Hook Form + schema validation (Zod/Yup)
   - TypeScript modeling
   - Architecture styles (Screaming, Clean, container/presentational)
   - Axios + interrupts/cancelation + API patterns
   - Portals, HOCs, custom hooks
   - Suspense + lazy
   - Styling strategies (CSS modules / SCSS / style objects)

That is exactly the scope of a senior React interview preparation path.

---

## 2) Professional React 19 mental model

If you want to think like a senior React engineer, keep this model:

### A. React is a rendering engine, not your business logic layer
- Components describe UI from state.
- Business rules should live in domain services, hooks, or use-cases.
- Keep UI components focused on rendering and interaction.

### B. State is a snapshot
- Every render sees a fixed snapshot of state.
- State updates schedule a future render.
- Multiple updates in the same event are batched.

### C. Effects are for synchronization, not computation
- Use effects to sync React with **external systems** (APIs, subscriptions, DOM APIs).
- Do not use effects to derive data that can be computed during render.

### D. React 19 direction
React 19 continues the shift toward:
- better performance ergonomics,
- compiler-assisted optimizations,
- improved async + form workflows,
- cleaner defaults around modern React patterns.

---

## 3) Deep-dive by concept (from your image list)

## 3.1 Context vs Composition ("why composition can replace some context")

Use **composition first** when data is only needed by a subtree and can be passed as children/props in a controlled way.
Use **Context** when many distant components need shared state (theme, auth session, feature flags, locale).

**Anti-pattern:** put every shared value in global context. This creates unnecessary re-renders and hidden coupling.

**Pro pattern:**
- keep context values stable,
- split contexts by concern,
- colocate state close to where it is used.

---

## 3.2 Batching and state updates

React batches state updates in event handlers and many async boundaries.

```tsx
setCount(c => c + 1);
setCount(c => c + 1);
// results in +2 because functional updates chain correctly
```

Always prefer functional updates when the next state depends on previous state.

---

## 3.3 React 19 compiler (what to know)

The React compiler is meant to reduce manual memoization burden in many cases.

What matters professionally:
- Understand when recomputation is expensive.
- Keep components pure.
- Avoid side effects in render.
- Still know `useMemo` / `useCallback` for explicit control and migration scenarios.

---

## 3.4 `useState` (interview depth)

Key points:
- Initializer can be lazy: `useState(() => expensiveInit())`
- State setter is async from the perspective of current render.
- Never mutate state objects directly; always create new references.

---

## 3.5 `useEffect` (correct usage)

### When to use it
- HTTP requests
- subscriptions
- timers
- imperative APIs

### Dependency array rules
- Include every reactive value used inside the effect.
- If dependencies explode, refactor logic into stable callbacks or custom hooks.

### Async inside effects
Do not mark effect callback directly as `async`; instead:

```tsx
useEffect(() => {
  let active = true;

  async function load() {
    const data = await fetchData();
    if (active) setData(data);
  }

  load();
  return () => {
    active = false;
  };
}, [fetchData]);
```

This prevents stale updates after unmount.

---

## 3.6 React Hook Form + Zod/Yup

Professional recommendation:
- Prefer schema-first validation with **Zod** or **Yup**.
- Use `resolver` integration.
- Keep server validation as source of truth (client validation improves UX, not security).

Why RHF is strong:
- performant uncontrolled inputs,
- easy field arrays,
- clean integration with TS types and schemas.

---

## 3.7 TypeScript modeling in React

Core practices:
- Model API contracts with explicit DTO types.
- Distinguish domain models from transport models.
- Use discriminated unions for UI states:
  - `loading | success | error | empty`

This reduces impossible UI states and runtime bugs.

---

## 3.8 Architecture styles

### Screaming Architecture
Structure folders by business capability (e.g., `orders/`, `billing/`, `auth/`) so the project “screams” domain intent.

### Clean Architecture (frontend adaptation)
- presentation (components/pages)
- application/use-cases
- domain (entities/rules)
- infrastructure (API, storage)

### Container / Presentational
Still useful mentally:
- Container: data fetching + orchestration
- Presentational: pure UI

In modern React, many teams implement this separation using hooks rather than classically named containers.

---

## 3.9 Axios + cancelation/interruption

When navigating quickly, outdated requests should be canceled.

- Use `AbortController` (native fetch) or Axios cancel mechanisms.
- Prevent race conditions where slow old responses overwrite new state.

---

## 3.10 Portals, HOCs, Custom Hooks

### Portals
Render modals/tooltips outside normal DOM hierarchy while preserving React tree semantics.

### HOCs
Legacy but still seen in mature codebases. Know how to read and migrate them.

### Custom hooks
Main reuse primitive in modern React.
Rules:
- prefix with `use`
- compose hooks safely
- keep responsibilities narrow

---

## 3.11 Suspense + lazy

- `React.lazy` splits bundle by component.
- `Suspense` shows fallback while component/data is pending.

Professional note: use with route-level boundaries and intentional loading UX, not random spinners everywhere.

---

## 3.12 Styling strategies

- **CSS Modules/SCSS**: local scoping, maintainable naming.
- **Inline style objects**: useful for dynamic style fragments, but limited for pseudo-classes/media queries.
- Choose one primary strategy and enforce consistency.

---

## 4) 8-week professional learning plan

### Weeks 1–2: Core React correctness
- render cycle, state snapshots, batching
- hooks correctness (`useState`, `useEffect`, `useMemo`, `useCallback`)
- effect cleanup and race-condition prevention

### Weeks 3–4: Forms + APIs + typing
- React Hook Form + Zod
- request lifecycle patterns and cancelation
- robust TS modeling for async UI states

### Weeks 5–6: Architecture + scaling
- feature-based folder structure
- custom hooks for business workflows
- context boundaries and performance

### Weeks 7–8: Advanced React 19 and interview prep
- compiler concepts and memoization strategy
- Suspense/lazy boundaries
- build and explain tradeoffs in a mini app (auth + dashboard + CRUD)

---

## 5) Interview-grade questions you should master

1. When should data be in local state, context, URL, or server cache?
2. How do you prevent stale async updates in `useEffect`?
3. Why is functional `setState` safer in batched updates?
4. How do you design custom hooks to avoid hidden coupling?
5. How would you migrate a legacy HOC-heavy app to modern hooks?
6. What are the tradeoffs between CSS Modules, utility CSS, and CSS-in-JS?
7. How do Suspense boundaries affect UX and error handling?
8. What changes with React 19 compiler assumptions for memoization?

---

## 6) How to use AI prompts to learn React faster (from your first image)

Use this template:

```txt
Role: You are a senior React 19 interviewer.
Goal: Teach me [topic] until I can explain tradeoffs.
Context: I know [your level].
Constraints: Give practical examples in TypeScript, avoid toy examples.
Output format:
1) Mental model
2) Correct code
3) Common mistakes
4) Interview follow-up questions
```

Then iterate with highly specific prompts:
- “Give me 3 broken `useEffect` examples and ask me to debug them.”
- “Refactor this component to remove unnecessary effects.”
- “Compare context vs composition for this exact tree.”

---

## 7) Execution standard (what “professional knowledge” means)

You are professional when you can:
- explain why a pattern is chosen,
- defend tradeoffs (performance, maintainability, complexity),
- implement with clean TypeScript boundaries,
- test behavior and failure states,
- communicate decisions clearly in PRs and code reviews.

If you want, next step we can convert this roadmap into a **daily checklist with exercises and expected answers**.
