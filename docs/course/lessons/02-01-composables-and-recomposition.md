# Lesson 02.01 — Composables and recomposition

Status: Passed on 2026-08-20

Module: 02 — Compose foundations

## Product outcome

The shared application now has its first feature-owned UI component: a reusable `LetterCard` in `alphabet/presentation`. The starter greeting was removed, and observable Compose state now reveals or hides the real Aleph, Bet, and Gimel content.

## Why the app needed this

The starter `App()` function directly rendered unrelated text and template content. Before building a grid, the application needs a component boundary for one letter.

The boundary chosen in this lesson is:

```text
App
└── LetterCard(letter)
    ├── glyph
    ├── Latin name
    └── authored sounds when present
```

`LetterCard` owns how one letter is presented. It does not own where letters come from, which lesson is active, or whether a learner has mastered the letter.

## The first composable

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

Important observations:

- It is an ordinary Kotlin function marked with `@Composable`.
- Its input is the domain value it needs to render.
- It emits Compose UI and therefore does not return a native view object.
- The optional sound row is derived from the current `Letter`; it is not separately stored UI state.
- The component contains no repository, Room, audio player, navigation, or ViewModel dependency.

## What `@Composable` means

A composable function participates in the Compose runtime. During composition, calling it describes UI nodes that should exist for the current inputs and state.

This is not a function that constructs and returns a persistent `View`:

```kotlin
fun LetterCard(letter: Letter): View
```

It is also not accurately described as React's virtual DOM. React knowledge helps with declarative inputs and state-driven rendering, but Compose uses compiler/runtime machinery to track composable calls and observable state reads.

For now, the practical model is:

```text
current parameters + current observed state
                    ↓
             composable functions
                    ↓
          UI description for this pass
```

## Feature ownership

The component lives at:

```text
shared/src/commonMain/kotlin/
└── com/ylevanon/alephbet/
    └── alphabet/
        └── presentation/
            └── LetterCard.kt
```

Why:

- it renders an Alphabet-domain concept;
- both Android and iOS use it;
- it is feature UI rather than a generic design-system primitive;
- moving it into `common` or `components` would erase useful ownership.

Cross-platform placement and feature ownership answer different questions:

- `commonMain`: which platforms can compile this code?
- `alphabet.presentation`: which product area owns this code?

## Rendering the collection

`App()` calls the component for each current letter:

```kotlin
letters.forEach { letter ->
    LetterCard(letter)
}
```

This is acceptable for the current three-item non-scrolling demonstration. The real explorer will use a lazy grid with stable keys in Lesson 02.05.

## Observable state and recomposition

The starter state was reused for a product-relevant interaction:

```kotlin
var showContent by remember { mutableStateOf(false) }
```

The button changes it:

```kotlin
Button(onClick = { showContent = !showContent }) {
    Text(buttonLabel)
}
```

The application reads it when deriving the label and deciding whether to emit letter content:

```kotlin
val buttonLabel =
    if (showContent) "Hide letters" else "Start learning"

AnimatedVisibility(showContent) {
    // LetterCard calls
}
```

The runtime sequence is:

1. The composable first runs with `showContent == false`.
2. Reading the observable value registers a dependency for the current composition.
3. The click callback assigns a new value.
4. Compose schedules affected composable work.
5. `App()` describes the UI again using the new value.
6. `remember` retains the state holder across that recomposition.
7. `AnimatedVisibility` receives the updated Boolean and emits the letter content.

## `remember` does not preserve everything

The authored `Letter` values and list are currently ordinary local values inside `App()`. They are recreated when that code runs again.

Only the value stored through `remember` is preserved by that call:

```kotlin
remember { mutableStateOf(false) }
```

The static alphabet content will leave `App()` when the real bundled content source is introduced. This lesson does not add `remember` around static domain content as a substitute for correct ownership.

## State versus derived values

`showContent` is state because it changes over time and must trigger UI updates.

`buttonLabel` is derived:

```kotlin
val buttonLabel =
    if (showContent) "Hide letters" else "Start learning"
```

It should not become a second mutable state value. Storing both would permit contradictions such as `showContent == true` with a “Start learning” label.

The sound text is derived for the same reason: `Letter.sounds` is already the source of truth.

## Review evidence

The learner:

- created `LetterCard.kt` in the Alphabet presentation package;
- passed a complete `Letter` rather than loose glyph/name arguments;
- rendered optional sounds from a read-only list;
- replaced direct rendering in `App()` with component calls;
- replaced the template greeting with observable state controlling real letter content;
- used Desktop Hot Reload to verify the interaction;
- distinguished remembered state from ordinary local values recreated during recomposition.

The coach removed only mechanical leftovers: unused template imports, retired resource references, and formatting issues.

Desktop Kotlin compilation succeeded.

## Exit ticket

1. Why does `LetterCard` accept `Letter` rather than reading global content?
   - Its dependencies remain explicit, it is independently renderable, and it cannot silently choose a different letter.

2. Does `LetterCard` return a native view?
   - No. It emits UI while participating in composition.

3. Why does changing `showContent` update the screen?
   - It is observable Compose state read by the composition; assignment invalidates affected work.

4. Why can `buttonLabel` remain a `val`?
   - It is derived again from the current state during recomposition, not reassigned in place.

5. Why is `LetterCard` under Alphabet instead of a generic shared-components package?
   - Reuse across platforms does not make feature-specific UI generic.

## Completion record

- [x] Feature-owned `LetterCard` exists in `commonMain`.
- [x] The component takes explicit domain input.
- [x] Aleph, Bet, and Gimel render through the component.
- [x] Template greeting UI is removed.
- [x] Observable state drives real content visibility.
- [x] Remembered state and recreated local values are distinguished.
- [x] Desktop Kotlin compilation succeeds.

## Next lesson

Lesson 02.02 turns the structural component into an actual card using `Modifier`, spacing, Material theme values, and deliberate modifier order.

