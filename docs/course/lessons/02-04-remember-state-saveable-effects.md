# Lesson 02.04 — Remembered state, saveable state, and effects

Status: Ready after Lesson 02.03

Module: 02 — Compose foundations

Estimated focused time: 65–95 minutes

## Product outcome

The hard-coded explorer can select one letter and render that selection through stateless cards. State ownership is explicit, and audio remains an event rather than a side effect of composition.

## Learning outcomes

By the end, the learner can:

- choose the lowest owner that needs temporary UI state;
- use remembered observable state to drive selected-card rendering;
- distinguish recomposition survival, host recreation survival, and durable persistence;
- avoid duplicating derivable state;
- explain why effects are reserved for work caused by entering/changing composition;
- independently add a clear-selection behavior.

## Prerequisites

- `LetterCard` accepts display values and callbacks.
- Card previews exist.
- The learner can explain values-in/events-out.

## Retrieval warm-up

1. Why is `LetterCard` still stateless after receiving callbacks?
2. What happens if a callback is invoked with parentheses during composition?
3. Which modifier controls parent placement of a card?

## Why the app needs this now

The learner must see which card was tapped. That choice should survive recomposition while the explorer remains on screen, but it is not yet durable learning progress and does not belong in Room.

The app also has an audio-request event. Calling playback directly from the composable body would replay unpredictably during recomposition. Event handling and effects need separate mental models.

## Mental model

State has several possible lifetimes:

| Lifetime | Example | Candidate |
|---|---|---|
| One composition instance | Current selected card | `remember` |
| Host recreation where restoration is supported | Draft text or tab selection | `rememberSaveable` with saveable type/saver |
| Screen/session coordinator | Practice question and choices | ViewModel state |
| App relaunch and durable history | Attempts and progress | Room |
| Durable preference | Auto-play setting | DataStore |

`remember` retains a value while that composable instance remains in the Composition. It does not turn temporary state into database persistence.

An effect is for work that must occur because a composable entered composition or because an effect key changed. A click is already an event callback; it generally does not need to be converted into a `LaunchedEffect`.

## React Native bridge

| React Native | Compose | Where the analogy breaks |
|---|---|---|
| `useState` | `remember { mutableStateOf(...) }` | Remembered identity is positional in the Composition. |
| Persisted store | Room/DataStore | `remember` is not durable storage. |
| `useEffect` | Compose effect APIs | Compose effects are keyed and follow composition lifecycle; there is no dependency-array copy-paste rule. |
| Derived render value | Ordinary Kotlin `val` | It is recalculated from current state during recomposition. |

## Vocabulary

| Term | Meaning |
|---|---|
| State owner | Scope responsible for storing and changing a value. |
| State hoisting | Moving state to a caller and passing value/callback down. |
| Remembered state | Value retained for one composable identity in the Composition. |
| Saveable state | State encoded for supported host restoration, not general persistence. |
| Effect | Controlled bridge from composition lifecycle to external or asynchronous work. |
| Derived state | Value computed from another source of truth. |

## Predict before running

Suppose `selectedLetterId` is remembered in the explorer parent.

Predict what survives:

1. ordinary recomposition;
2. hiding and showing only a child card while the parent stays composed;
3. removing the parent from composition;
4. killing and relaunching the app;
5. reinstalling the app.

Then classify whether selection belongs in Room.

## Minimal demonstration — one parent-owned Boolean

The existing `showContent` value is the demonstration:

```kotlin
var showContent by remember { mutableStateOf(false) }
```

Trace its state read, click write, and derived label. Do not add new state in the demonstration.

Then remove or rename this temporary mechanism when the real selected-letter state replaces its product role.

## Guided lab — select one letter

### Step 1 — Choose the owner

The explorer parent renders every card and needs to compare their IDs, so it owns a nullable selected `LetterId`.

State the initial value before writing syntax.

### Step 2 — Hoist state into card parameters

`LetterCard` receives whether it is selected and emits its click event. It does not store a second selected Boolean.

### Step 3 — Derive visual state

Use the selected input to choose a theme-based visual treatment. Do not persist the selection and do not copy the selected letter into another mutable variable.

### Step 4 — Keep audio event-driven

The audio callback may update a temporary visible message for the development harness, but it must not call a player during composition. No `LaunchedEffect` is needed merely because a button was clicked.

### Step 5 — Check lifetimes

Select Bet, toggle any unrelated UI state, and confirm selection survives recomposition. Then fully remove the explorer from composition or restart the process and observe the boundary.

## Independent task — clear selection

Stop before hints.

Add a product-appropriate way to clear the selected letter from the parent.

### Acceptance criteria

- The parent remains the only selection owner.
- Clearing produces the same state as the initial unselected screen.
- Cards do not mutate parent state directly; they invoke callbacks.
- The selected style is fully derived from the selected ID.
- No Room, DataStore, ViewModel, or platform API is introduced.
- No effect is used for a synchronous click.
- Desktop behavior and compilation succeed.

## Test and debugging plan

1. Predict state lifetime before running.
2. Select each card and verify only one selected treatment.
3. Trigger unrelated recomposition and confirm selection remains.
4. Clear selection.
5. Restart the desktop process and explain the reset.
6. Run desktop Kotlin compilation.

If more than one card appears selected, inspect whether selection is stored independently inside each card.

## Hint ladder

<details>
<summary>Hint 1 — Product behavior</summary>

Clearing means returning the single parent-owned selected ID to its initial value.

</details>

<details>
<summary>Hint 2 — Ownership</summary>

The control that requests clearing emits an event. The explorer parent performs the state change.

</details>

<details>
<summary>Hint 3 — Shape</summary>

Use the same nullable state variable already used for selection; do not add `isCleared`.

</details>

<details>
<summary>Hint 4 — Partial skeleton</summary>

```kotlin
var selectedLetterId by remember {
    mutableStateOf<LetterId?>(/* initial value */)
}

/* clear event */
{
    selectedLetterId = /* initial value */
}
```

</details>

## Teach-back

Explain:

1. who owns selected ID and why;
2. why each card receives a derived Boolean;
3. what `remember` survives;
4. why this selection is not Room data;
5. why a synchronous click does not need an effect.

## Exit ticket

1. Does `remember` survive app relaunch?
2. Is `rememberSaveable` a database?
3. Why not store `isSelected` inside every card?
4. When would an effect be justified?
5. Where will practice-session state eventually live?

## Review rubric

Passing work requires one source of truth, correct lifetime reasoning, event-driven callbacks, and no unnecessary effect or persistence dependency.

## Completion evidence

- [ ] Parent owns nullable selected ID.
- [ ] Cards derive selected styling.
- [ ] Audio remains an event.
- [ ] Independent clear-selection behavior works.
- [ ] Lifetime observations are explained.
- [ ] Desktop compilation passes.
- [ ] Review findings are resolved.

## Next retrieval

Lesson 02.05 moves the cards into a lazy grid, where stable keys preserve identity and semantics expose meaningful interaction.

## References

- [State and Jetpack Compose](https://developer.android.com/develop/ui/compose/state)
- [Lifecycle of composables](https://developer.android.com/develop/ui/compose/lifecycle)
- [Side-effects in Compose](https://developer.android.com/develop/ui/compose/side-effects)

