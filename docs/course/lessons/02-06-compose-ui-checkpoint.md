# Lesson 02.06 — Compose UI checkpoint

Status: Ready after Lesson 02.05

Module: 02 — Compose foundations

Estimated focused time: 80–120 minutes

## Product outcome

The hard-coded three-letter explorer has shared UI tests for content and interaction, passes Android/iOS smoke checks, and survives an independent fourth-letter extension without duplicating the card.

## Learning outcomes

By the end, the learner can:

- test Compose Multiplatform UI through semantics rather than implementation details;
- distinguish finders, assertions, and actions;
- write a focused component test and a screen interaction test;
- interpret a missing or ambiguous semantics-node failure;
- independently add Dalet and preserve grid/selection behavior;
- defend the Module 02 UI ownership decisions.

## Prerequisites

- The keyed three-column grid works.
- `LetterCard` exposes meaningful interaction semantics.
- Selection is parent-owned.
- Common Compose UI test support can be added to the shared module.

## Retrieval warm-up

1. Why are semantics relevant to both accessibility and tests?
2. Which value is the stable grid key?
3. Why does `LetterCard` not own selected state?
4. What does a preview verify that a running interaction does not?

## Why the app needs this now

The explorer has enough behavior to regress:

- one letter may omit sounds;
- one may have multiple sounds;
- selection must remain attached to identity;
- callbacks must reach the parent;
- the grid must render every authored item.

A UI checkpoint protects those behaviors before content, repositories, navigation, and ViewModels increase the number of moving pieces.

## Mental model

Compose UI tests operate through the semantics tree:

```text
setContent
    ↓
find semantic node
    ↓
assert state/content
    ↓
perform user action
    ↓
assert resulting state/content
```

The three main operations are:

- finder: locate one or more nodes;
- assertion: verify current semantics;
- action: simulate supported interaction.

A test should prefer visible text, roles, click actions, and stable test-only tags only where semantic lookup would otherwise be ambiguous.

## React Native bridge

| React Native | Compose Multiplatform | Where the analogy breaks |
|---|---|---|
| React Native Testing Library query | Compose semantics finder | Queries inspect the semantics tree rather than React component instances. |
| `fireEvent.press` | `performClick()` | The target must expose a click action in semantics. |
| Jest assertion | Compose assertion plus `kotlin.test` | Common Compose test execution is not JUnit `TestRule` based. |
| Component test render | `runComposeUiTest { setContent { ... } }` | Target tasks and experimental APIs depend on Compose Multiplatform setup. |

## Vocabulary

| Term | Meaning |
|---|---|
| Finder | Query locating semantics nodes. |
| Matcher | Condition a node must satisfy. |
| Assertion | Verification performed on located nodes. |
| Action | Simulated user interaction supported by a semantics node. |
| Test tag | Explicit identifier for testing when user-facing semantics are insufficient. |
| Common UI test | Test written in shared Kotlin and run through supported KMP targets. |

## Predict before running

Suppose a card is visually clickable but its semantics node has no click action.

Predict what happens when a test locates its text and calls `performClick()`. Then predict how using an interactive Material/Foundation API changes the semantics.

## Configuration checkpoint

Compose Multiplatform 1.11.1 UI testing is experimental. The coach explains and supplies the version-matched Gradle/catalog boilerplate when this lab begins; configuration is not an independent Kotlin exercise.

The shared test source set will receive the Compose UI test dependency. The desktop/JVM test target also needs the current desktop runtime, following the official Compose Multiplatform test setup.

After sync, verify the smallest empty `runComposeUiTest` before writing product assertions. If current APIs differ, follow the installed 1.11.1 signatures and record the adjustment in this page.

## Minimal demonstration — one text assertion

The coach demonstrates a deliberately small test rendering one `Text` and finding it by visible text. The example proves the harness only; it does not test `LetterCard` or the explorer.

The learner identifies:

- where `setContent` belongs;
- which line is the finder;
- which line is the assertion.

Delete or keep the harness test according to whether it adds continuing value.

## Guided lab — test LetterCard

### Step 1 — Render reviewed sample data

Render Bet inside the current Material theme with inert callbacks.

### Step 2 — Assert content branches

Verify that Bet's glyph, Latin name, and both-sound presentation exist. Add a separate Aleph case proving that an empty sounds list does not render a fake or blank sound label.

### Step 3 — Assert interaction

Supply a test-owned variable or callback recorder, perform the card click through semantics, and assert that the callback occurred.

### Step 4 — Read failures first

Before changing production code, read whether a failure means:

- no node matched;
- multiple nodes matched;
- the node lacked a click action;
- displayed text differed.

Explain the category before receiving the fix.

## Independent task — add Dalet and test the explorer

Stop reading before hints.

Add Dalet as the fourth hard-coded letter and update the explorer checkpoint tests.

### Acceptance criteria

- Dalet has a typed ID, order 4, glyph `ד`, Latin name, and reviewed sound text.
- It enters the existing ordered collection without duplicating `LetterCard`.
- The grid renders four keyed items.
- Selecting Dalet produces the same selected treatment as other letters.
- Existing Aleph/Bet/Gimel behavior remains.
- At least one test would fail if Dalet were omitted from the grid.
- Component and screen tests pass on the configured desktop target.
- Android and iOS smoke checks show the same four letters and interaction.
- No repository, Room, ViewModel, or navigation layer is added merely for the checkpoint.

## Test and debugging plan

1. Run the minimal harness assertion.
2. Run the focused `LetterCard` content test.
3. Run the card callback test.
4. Add Dalet and run the explorer tests.
5. Trigger one deliberate failure, classify it, then restore the passing behavior.
6. Run desktop compilation and configured UI tests.
7. Run Android and iOS smoke checks.

## Hint ladder

<details>
<summary>Hint 1 — Product behavior</summary>

The same grid and card must render one more domain value; no new UI component is needed.

</details>

<details>
<summary>Hint 2 — Ownership</summary>

Dalet belongs beside the other temporary authored letters. The parent collection supplies it to the existing grid.

</details>

<details>
<summary>Hint 3 — Test shape</summary>

Write an assertion that observes Dalet through user-visible semantics, not by reading the source list directly.

</details>

<details>
<summary>Hint 4 — Partial skeleton</summary>

```kotlin
@Test
fun explorerDisplaysDalet() = runComposeUiTest {
    setContent {
        /* theme and explorer with four letters */
    }

    /* locate Dalet through visible semantics and assert it exists */
}
```

</details>

## Teach-back

Explain:

1. why UI tests inspect semantics;
2. how finder, assertion, and action differ;
3. which tests belong at card versus explorer scope;
4. how stable keys and parent-owned selection survived Dalet;
5. what still requires real Android/iOS smoke testing.

## Exit ticket

1. Why should tests avoid internal composable implementation details?
2. What does `performClick()` require from semantics?
3. When is a test tag justified?
4. Which test would catch a missing Dalet grid item?
5. What Module 02 state must not move to Room?

## Review rubric

Module 02 passes only when the independent Dalet variation works, meaningful UI tests pass, semantics remain accessible, and the learner can defend component/state/layout ownership.

## Completion evidence

- [ ] Version-matched common UI test harness runs.
- [ ] `LetterCard` content branches are tested.
- [ ] Card click is tested through semantics.
- [ ] Independent Dalet variation works.
- [ ] Explorer test observes Dalet.
- [ ] One failure is classified before fixing.
- [ ] Desktop compilation and UI tests pass.
- [ ] Android and iOS smoke checks pass.
- [ ] Module 02 teach-back passes.
- [ ] Review findings are resolved.

## Next retrieval

Module 03 moves fonts, strings, and reviewed letter assets into KMP resources and verifies which source sets and hosts can access them.

## References

- [Testing Compose Multiplatform UI](https://kotlinlang.org/docs/multiplatform/compose-test.html)
- [Compose semantics](https://developer.android.com/develop/ui/compose/accessibility/semantics)
- [Compose testing cheat sheet](https://developer.android.com/develop/ui/compose/testing-cheatsheet)

