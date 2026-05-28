# Example: Frontend / UI Change

---

## Rough User Prompt

```
dashboard pe ek loading spinner lagao jab data fetch ho raha ho,
abhi blank screen aata hai, kuch dikhna chahiye na
```

---

## Detected Task Type

**UI/Frontend Change — Loading State**

Detection signals:
- "spinner lagao" → UI component addition
- "data fetch ho raha ho" → tied to async data loading
- "blank screen aata hai" → current behavior identified
- "kuch dikhna chahiye" → expected behavior is visual feedback
- No backend change needed — this is a frontend-only change

---

## Optimized Claude Code Prompt

```markdown
# Optimized Claude Code Prompt

## Task
Add a loading spinner to the Dashboard page that displays while data is being fetched.

## Objective
The Dashboard page must show a loading indicator immediately when the page mounts and data is being fetched. The blank screen must be replaced with a spinner. Once data loads, the spinner is replaced with the dashboard content.

## Task Type
UI/Frontend Change — Loading State

## Inspect First
- Dashboard page component (glob for `Dashboard.tsx`, `Dashboard.vue`, `dashboard.component.ts`, or `pages/dashboard.*`)
- Data-fetching logic inside the component (look for `useEffect`, `onMounted`, `ngOnInit`, or `fetch`/`axios` calls)
- Existing loading state variable in the component (look for `isLoading`, `loading`, `isFetching`)
- Any shared Spinner or Loader component already in the codebase (glob for `Spinner.*`, `Loader.*`, `Loading.*`)

## Investigation Order
1. Locate the Dashboard page component file.
2. Identify how data is currently fetched — custom hook, direct `useEffect`, SWR, React Query, or a store action.
3. Check if a `loading` or `isLoading` state variable already exists in the component or the data-fetching hook.
4. Search the codebase for an existing Spinner or Loader component before creating a new one.
5. Implement the loading state: set `isLoading = true` before the fetch, `isLoading = false` after.
6. Conditionally render the Spinner when `isLoading` is true, and the dashboard content when false.

## Constraints
- Do not scan the entire repository first.
- Start with the most relevant files/modules listed in "Inspect First".
- Use targeted grep/search before opening large files.
- Expand search only if the root cause is not found in those files.
- Do not rewrite unrelated logic.
- Do not refactor unless explicitly requested.
- Preserve existing folder structure.
- Make the smallest safe change.
- Do not add dependencies unless necessary.
- Reuse an existing Spinner/Loader component if one exists — do not create a duplicate.
- Do not change the data-fetching logic, API calls, or response handling.
- Do not add error state handling unless the user explicitly asked for it.
- Keep the spinner implementation inside the Dashboard component — do not create a global loading overlay unless asked.

## Expected Output
1. Describe whether an existing Spinner component was reused or a new one was created, and why.
2. List all files changed.
3. Provide full updated code only for changed files.
4. Show the relevant conditional render block.

## Verification
- Open the Dashboard page on a slow network (Chrome DevTools → Network → Slow 3G).
- The spinner must be visible immediately on page load before data arrives.
- Once data loads, the spinner must disappear and the dashboard content must render.
- Verify that the spinner does not appear on subsequent renders when data is already cached.
- Verify no console errors are introduced.

## What Not To Do
- Do not replace the data-fetching mechanism — only add the loading state wrapper around the existing fetch.
- Do not install a spinner library (e.g., `react-spinners`) unless no spinner component exists and a CSS-only solution is not acceptable.

## When To Expand Search
- If the data fetching is inside a custom hook (e.g., `useDashboardData`), read that hook file to find where to add the `isLoading` state.
- If the Dashboard uses a state management library (Redux, Zustand, Pinia), check the store slice for an existing `loading` state before adding one to the component.
```

---

## Why This Optimized Prompt Is Better

| Rough Prompt | Optimized Prompt |
|---|---|
| "spinner lagao" — no detail | Specifies exact behavior: show on mount, hide on data load |
| No reuse check | Instructs Claude to search for existing Spinner component first |
| No data-fetch context | Directs investigation to the data-fetching mechanism |
| No scope control | Explicitly excludes error state, global overlay, library install |
| No network test | Verification uses Slow 3G for realistic loading test |
| No framework awareness | Notes useEffect, SWR, React Query, Pinia as possibilities |

---

## Approval Preview (shown to user before implementation)

```markdown
# Optimized Prompt Preview

## Detected Task Type
UI/Frontend Change — Loading State

## Optimized Claude Code Prompt

[full optimized prompt above]

---

## Estimated Prompt Tokens

| Prompt | Estimated Tokens |
|---|---:|
| Original rough prompt | ~35 |
| Optimized prompt | ~200 |

> Estimates use ~1 token per 3.5 characters for mixed developer text. The optimized prompt checks for an existing Spinner component first, preventing both a duplicate component and an unnecessary library install.

## Next Action

Reply with one of:

- `y` or `yes` — proceed with the optimized prompt
- `e` or `edit` — revise the optimized prompt before proceeding
- `c` or `cancel` — stop, do not execute anything
```

**User replies:** `c`

**Skill output:** "Cancelled. Nothing was executed."

**Skill stops.** No files are read, no implementation is attempted.

---

## Post-Task Token Optimization Report

*(Not shown — task was cancelled.)*

> The post-task report is only generated after an approved implementation completes. Cancelled tasks produce no report.
