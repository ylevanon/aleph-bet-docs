# Lesson 04.04 — Connect bundled content to presentation

Status: Ready after Lesson 04.03

Module: 04 — Alphabet feature vertical slice

Estimated focused time: 80–115 minutes

## Product outcome

The explorer stops using temporary Kotlin content and renders asynchronously loaded domain letters from `AlphabetRepository`, with visible loading and failure behavior.

## Learning outcomes

By the end, the learner can:

- load suspend repository data without running work directly in a composable body;
- represent loading, content, and failure as one coherent UI state;
- keep DTO/resource errors outside the stateless screen;
- preserve previews/tests with fake state;
- independently implement retry behavior.

## Why the app needs this now

`Res.readBytes` is suspend and repository loading may fail. The current explorer assumes data is immediately available. The product now needs honest loading and failure states before real bundled content can replace temporary values.

A ViewModel is intentionally deferred to Module 06. This lesson uses the smallest route-level coordinator that works and documents its temporary lifetime.

## Mental model

```text
AlphabetRoute(repository)
    ├── owns loading effect/state temporarily
    └── AlphabetScreen(uiState, callbacks)
            └── renders only
```

The stateless screen must not decode JSON or catch serialization exceptions.

## Minimal demonstration

The coach demonstrates a suspend call inside `LaunchedEffect` using a tiny fake delay/value unrelated to the repository. The learner predicts how often it runs for a stable key.

The demonstration is removed after the mechanism is understood.

## Guided lab

1. Define a small sealed `AlphabetUiState`: loading, content, failure.
2. Keep `AlphabetScreen` parameter-driven and previewable.
3. Create an `AlphabetRoute` that receives `AlphabetRepository`.
4. Start loading through a keyed effect rather than the composable body.
5. Map repository/domain failures into user-safe failure state.
6. Pass loaded letters into the existing grid.
7. Construct the bundled repository manually at the app root for now.
8. Remove `StarterAlphabetContent` only after the real path renders.

## Independent task

Implement retry from the failure UI.

### Acceptance criteria

- Initial state is visible and non-crashing.
- Success renders domain `Letter` values, never DTOs.
- Failure shows a useful message and retry action.
- Retry causes exactly one new load attempt.
- Recomposition without retry does not repeatedly reload content.
- Stateless screen previews cover loading, content, and failure.
- Temporary route state is clearly marked for replacement by ViewModel in Module 06.
- Desktop behavior and common tests pass.

## Hint ladder

1. Product behavior: failure stays visible until the learner asks to try again.
2. Ownership: route coordinates loading; screen renders state and emits retry.
3. Shape: include a retry generation/key or a dedicated load function invoked by an event.
4. Request a partial route skeleton only after modeling the three UI states.

## Teach-back

Explain why suspend work cannot run unguarded in the composable body, why the screen should receive UI state, and what Module 06 will move into a ViewModel.

## Completion evidence

- [ ] Loading/content/failure states exist.
- [ ] Bundled repository drives explorer.
- [ ] Temporary content declarations are removed.
- [ ] Independent retry works without reload loops.
- [ ] State previews exist.
- [ ] Desktop build/tests pass.
- [ ] No DTO enters UI.

## References

- [Side-effects in Compose](https://developer.android.com/develop/ui/compose/side-effects)
- [State and Jetpack Compose](https://developer.android.com/develop/ui/compose/state)

