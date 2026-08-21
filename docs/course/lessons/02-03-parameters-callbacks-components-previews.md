# Lesson 02.03 — Parameters, callbacks, components, and previews

Status: Ready after Lesson 02.02

Module: 02 — Compose foundations

Estimated focused time: 55–85 minutes

## Product outcome

`LetterCard` becomes a stateless interactive component with a parent-owned click callback and focused previews for different letter content.

## Learning outcomes

By the end, the learner can:

- design a stateless composable API from required inputs and emitted events;
- pass a function as a callback without invoking it during composition;
- keep navigation and selection decisions outside a reusable card;
- write preview-only sample data;
- independently add a second event without introducing local business state.

## Prerequisites

- `LetterCard` is styled and exposes a public modifier.
- Modifier ownership and order are understood.
- The project already includes common preview tooling.

## Retrieval warm-up

1. Where should a caller-provided modifier be applied?
2. Why should a card not force the screen's width?
3. What is the function type of a click callback with no arguments and no return value?

## Why the app needs this now

The future grid needs to report that a learner selected a letter. The card should not decide whether that opens detail, selects an answer, or starts audio. It should emit an event and let its parent assign meaning.

Previews also let one component be inspected with Aleph and Bet content without launching the entire application.

## Mental model

A stateless UI contract has two directions:

```text
parent ── values ──> LetterCard
parent <── events ── LetterCard
```

Values describe what to render. Callbacks report what happened. The component does not secretly update product state that the parent also owns.

A callback type such as `() -> Unit` means “a function taking no arguments and returning no meaningful value.”

## React Native bridge

| React Native | Compose | Where the analogy breaks |
|---|---|---|
| Props | Typed Kotlin parameters | Default and nullable types are compiler checked. |
| `onPress={() => ...}` | `onClick = { ... }` | Kotlin distinguishes passing a function from invoking it through syntax and types. |
| Storybook/component preview | Compose `@Preview` | Preview execution is tooling-driven and is not the whole running app. |
| Presentational component | Stateless composable | Compose state reads and stability influence recomposition behavior. |

## Vocabulary

| Term | Meaning |
|---|---|
| Callback | Function supplied by a parent and invoked when an event occurs. |
| Event | Something that happened, such as a card tap. |
| Stateless component | Component whose rendered state is fully described by parameters. |
| Preview | Tooling rendering of a composable with supplied sample inputs. |
| Sample data | Non-production values used only to render or test a component. |

## Predict before running

Compare:

```kotlin
onClick = onLetterClick
```

and:

```kotlin
onClick = onLetterClick()
```

Given `onLetterClick: () -> Unit`, predict which passes the function and which calls it immediately. Predict the compiler complaint for the wrong form.

## Minimal demonstration — callback on the existing button

The coach uses the existing app button to isolate function values:

```kotlin
val toggleLetters: () -> Unit = {
    showContent = !showContent
}
```

Pass that function to the button without calling it during composition. Verify that the behavior remains unchanged, then decide whether the named local function improves the production code or should be inlined again.

This demonstration teaches callback syntax; it is not the card implementation.

## Guided lab — make LetterCard interactive

### Step 1 — Define the event contract

Add an `onClick: () -> Unit` parameter to `LetterCard`. The card still receives `Letter` and `Modifier`.

### Step 2 — Choose the interaction surface

Make the entire outer card interactive using an appropriate Material or Foundation API. Preserve one coherent accessibility node rather than attaching separate click handlers to each text child.

### Step 3 — Wire the parent

At each call site, provide a callback. For this lesson the callback may update a temporary parent-owned selected ID or report the event through existing visible state. The card itself must not choose navigation.

### Step 4 — Add focused previews

Create private preview functions in or beside `LetterCard.kt`. Render at least:

- Aleph with no sound row;
- Bet with two sounds.

Wrap previews in the current Material theme so typography, surface, and colors have valid theme values.

### Step 5 — Check

Use preview tooling for component states and Desktop Hot Reload for real interaction. Explain why the preview does not replace running the application.

## Independent task — add an audio-request event

Stop reading before hints.

Add a second callback representing a request to play the current letter's audio. The UI may use a temporary text button or icon placeholder; actual playback is not implemented.

### Acceptance criteria

- The callback API makes the required letter identity unambiguous.
- `LetterCard` does not import or construct an audio player.
- The parent receives the event.
- Clicking the card and clicking audio can be distinguished.
- Preview sample callbacks are inert and side-effect free.
- The component still owns no mutable product state.
- Desktop interaction and compilation succeed.

The learner chooses whether the callback is `() -> Unit` because the parent already knows the letter, or `(LetterId) -> Unit` because the component reports identity. The choice must be explained.

## Test and debugging plan

1. Predict pass-versus-invoke callback syntax.
2. Verify the card click once in the running desktop host.
3. Verify the audio placeholder invokes only the audio callback.
4. Render Aleph and Bet previews.
5. Inspect imports for platform audio dependencies.
6. Run desktop Kotlin compilation.

If the callback fires during composition, inspect whether parentheses were added at the call site.

## Hint ladder

<details>
<summary>Hint 1 — Product behavior</summary>

The card reports an audio request; it does not know how audio is played.

</details>

<details>
<summary>Hint 2 — Ownership</summary>

The parent already has the `Letter` used to render this card. Decide whether that makes an identity parameter redundant.

</details>

<details>
<summary>Hint 3 — Shape</summary>

Add one function-typed parameter and invoke it only inside the audio control's event handler.

</details>

<details>
<summary>Hint 4 — Partial skeleton</summary>

```kotlin
@Composable
fun LetterCard(
    letter: Letter,
    onClick: () -> Unit,
    onPlayAudio: /* choose function type */,
    modifier: Modifier = Modifier,
) {
    /* existing card */
}
```

</details>

## Teach-back

Explain:

1. which values flow into `LetterCard`;
2. which events flow out;
3. why the card does not navigate;
4. why the card does not own an audio player;
5. what previews prove and what they cannot prove.

## Exit ticket

1. What is the difference between passing and invoking a callback?
2. Who owns the reaction to a card click?
3. Should preview data enter the production repository?
4. Can a preview prove navigation works?
5. Why is the card still stateless after adding callbacks?

## Review rubric

Passing work requires explicit event contracts, no hidden navigation/audio behavior, at least two meaningful previews, and an independently designed audio callback.

## Completion evidence

- [ ] Card click callback is parent-owned.
- [ ] Preview renders Aleph and Bet variants.
- [ ] Independent audio-request callback works.
- [ ] No platform service leaks into the composable.
- [ ] Desktop compilation passes.
- [ ] Learner explains values-in/events-out.
- [ ] Review findings are resolved.

## Next retrieval

Lesson 02.04 gives the parent temporary selection state and decides which values deserve `remember`, saveable state, or no local state at all.

## References

- [Compose UI previews for Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform/compose-previews.html)
- [State and Jetpack Compose](https://developer.android.com/develop/ui/compose/state)

