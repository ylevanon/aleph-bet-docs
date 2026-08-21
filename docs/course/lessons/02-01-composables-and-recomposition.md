# Lesson 02.01 — Composables and recomposition

Status: In progress — guided LetterCard complete; independent AlphabetHeader pending

Module: 02 — Compose foundations

Estimated focused time: 45–70 minutes

## Product outcome

The starter UI is split into feature-owned composables. One worked letter card establishes the mechanism; the learner independently extracts the alphabet header without receiving its completed implementation.

## Learning outcomes

By the end, the learner can:

- explain what a composable emits and what recomposition updates;
- pass explicit immutable inputs into a feature composable;
- distinguish observable remembered state from an ordinary local value;
- extract a second production composable independently.

## Prerequisites

- Lessons 01.01–01.04 completed at the current progression level.
- `Letter` and `LetterId` exist in `alphabet/domain`.
- Aleph, Bet, and Gimel render in the desktop development host.

## Retrieval warm-up

Answer without notes:

1. Why does `LetterCard` receive one `Letter` instead of separate glyph and name strings?
2. What changes when `bet.copy(sounds = listOf("v"))` runs?
3. Which source set should contain shared Compose UI?

## Why the app needs this now

`App()` currently owns the whole screen. Before building a grid, the app needs a boundary for one letter and a boundary for the screen header. These boundaries should make required inputs explicit without inventing a ViewModel or repository yet.

## Mental model

A composable function participates in composition:

```text
current parameters + observed Compose state
                     ↓
              composable functions
                     ↓
        description of UI for this pass
```

Initial composition creates the description. When observable state read by that composition changes, Compose schedules relevant work again and updates the composition.

A composable does not return a persistent Android `View` or iOS `UIView`. Compose may also skip calls whose inputs have not changed, so “the whole function always reruns” is only a beginner approximation.

## React Native bridge

| React Native | Compose | Where the analogy breaks |
|---|---|---|
| Function component | `@Composable` function | Compose is compiler/runtime driven; it is not React's virtual DOM. |
| Props | Function parameters | Kotlin types and default parameters are enforced at compile time. |
| Local state hook | `remember { mutableStateOf(...) }` | Remembered values live in the Composition, not a JavaScript component closure. |
| Conditional JSX | Kotlin `if` around composable calls | Kotlin control flow directly changes emitted composition structure. |

## Vocabulary

| Term | Meaning |
|---|---|
| Composition | The current tree-like description produced by composable execution. |
| Initial composition | The first time composables enter that composition. |
| Recomposition | Updating the composition after relevant observed state changes. |
| State read | Reading observable Compose state while composing, which records a dependency. |
| Emit | Call composables that contribute UI; not “return a native view.” |

## Predict before running

Given:

```kotlin
@Composable
fun Example(name: String) {
    Text("Hello, " + name)
}
```

Predict:

1. its Kotlin return type;
2. whether it can be called from an ordinary non-composable function;
3. what changes if its `name` parameter changes;
4. whether it owns `name`.

Do not run anything until the prediction is stated.

## Minimal demonstration — one letter card

The coach models one small production case: extracting the existing rendering of a single letter into `alphabet/presentation/LetterCard.kt`.

The complete demonstration is intentionally limited to structure:

```kotlin
@Composable
fun LetterCard(letter: Letter) {
    Column {
        Text(text = letter.glyph)
        Text(text = letter.latinName)

        if (letter.sounds.isNotEmpty()) {
            Text(text = letter.sounds.joinToString(" or "))
        }
    }
}
```

Expected checkpoint:

- Aleph, Bet, and Gimel still display;
- only Bet shows two sounds;
- `LetterCard` contains no state, repository, or platform imports.

Stop after verifying that output. Styling, modifiers, callbacks, and previews belong to later lessons.

## Guided lab — make recomposition visible

### Step 1 — Replace template behavior

Use the existing `showContent` state to reveal or hide real letter content instead of the generated greeting.

The coach provides the state mechanism because it already exists:

```kotlin
var showContent by remember { mutableStateOf(false) }
```

The learner decides which UI reads the value and removes the template greeting.

### Step 2 — Derive the label

Keep the button label as a read-only value derived from `showContent`. Do not create a second mutable state variable for it.

### Step 3 — Predict the event sequence

Before clicking, explain:

1. which callback changes state;
2. which composable code reads that state;
3. which value `remember` preserves;
4. which ordinary local values may be created again.

### Step 4 — Check

Use Desktop Hot Reload and click the button twice. Verify both labels and both visibility states. Then run the desktop Kotlin compilation task.

## Independent task — extract AlphabetHeader

Stop reading after the acceptance criteria and implement this before opening hints.

Extract the existing screen title and progress text into a new feature composable named `AlphabetHeader` in the Alphabet presentation package.

### Acceptance criteria

- `AlphabetHeader` accepts the title and progress text as parameters.
- It emits both pieces of text.
- It does not read global variables or construct progress text internally.
- It owns no mutable state.
- `App()` calls it instead of emitting those two `Text` nodes directly.
- Existing behavior remains unchanged.
- The learner chooses parameter names and internal layout.
- Desktop Hot Reload and compilation succeed.

The production implementation is not shown above this assignment.

## Test and debugging plan

1. First verify the original three letter cards still appear.
2. Extract `AlphabetHeader` and read any compiler error before requesting a fix.
3. Confirm that changing the title argument at the call site changes the rendered title.
4. Confirm the header itself contains no `remember`.
5. Run desktop Kotlin compilation.

If the compiler reports that a composable can only be invoked from another composable context, first identify which calling function lacks `@Composable`.

## Hint ladder

<details>
<summary>Hint 1 — Product behavior</summary>

The header needs exactly the two text values currently rendered above the button.

</details>

<details>
<summary>Hint 2 — Ownership</summary>

`App()` owns the current values. `AlphabetHeader` owns only their presentation.

</details>

<details>
<summary>Hint 3 — Shape</summary>

Start with a composable function that takes two `String` parameters and contains a small vertical layout.

</details>

<details>
<summary>Hint 4 — Partial skeleton</summary>

```kotlin
@Composable
fun AlphabetHeader(
    /* title parameter */,
    /* progress parameter */,
) {
    Column {
        /* title */
        /* progress */
    }
}
```

</details>

A complete solution is shown only after a real attempt and is followed by a small independent variation.

## Teach-back

Explain:

1. what `@Composable` changes about a function;
2. why `LetterCard` and `AlphabetHeader` accept explicit values;
3. what state caused recomposition in the guided lab;
4. why the derived label is not separate state;
5. where the React function-component analogy stops being exact.

## Exit ticket

1. Does a composable return a native view?
2. What causes the letter visibility composition to update?
3. What does `remember` preserve here?
4. Who owns the title value: `App()` or `AlphabetHeader`?
5. Why is the sound label derived rather than stored?

## Review rubric

Passing evidence requires the independent header extraction, correct input ownership, a working state-driven interaction, and an accurate recomposition explanation. Copying only the demonstrated `LetterCard` is guided progress, not yet a passed lesson.

## Completion evidence

- [x] Guided `LetterCard` renders three letters.
- [x] Template greeting is replaced by state-driven letter visibility.
- [x] Desktop compilation passes for the guided checkpoint.
- [ ] Independent `AlphabetHeader` works.
- [ ] Learner explains recomposition and ownership.
- [ ] Review findings are resolved.
- [ ] Public progress record is updated.

## Next retrieval

Lesson 02.02 revisits both components while introducing layout constraints, Material styling, and modifier order.

## References

- [Thinking in Compose](https://developer.android.com/develop/ui/compose/mental-model)
- [Lifecycle of composables](https://developer.android.com/develop/ui/compose/lifecycle)
- [Compose phases](https://developer.android.com/develop/ui/compose/phases)

