# Lesson 02.02 — Layout, Modifier, theme, and modifier order

Status: Ready after Lesson 02.01

Module: 02 — Compose foundations

Estimated focused time: 60–90 minutes

## Product outcome

`LetterCard` becomes a recognizable, responsive Material card. `AlphabetHeader` receives deliberate spacing and typography without hard-coded screen dimensions.

## Learning outcomes

By the end, the learner can:

- explain how parent constraints flow down and measured sizes flow up;
- apply a caller-provided `Modifier` to a component's outermost node;
- distinguish external placement from component-owned internal spacing;
- predict how modifier order changes drawing and touch/layout bounds;
- use Material theme roles instead of arbitrary styling values;
- independently style a second component from visual requirements.

## Prerequisites

- `LetterCard` and the independent `AlphabetHeader` exist.
- Both are stateless and accept explicit display inputs.
- Desktop Hot Reload works.

## Retrieval warm-up

1. Does a composable return a persistent native view?
2. Why is the button label derived rather than remembered separately?
3. Which component owns the title value, and which owns its visual arrangement?

## Why the app needs this now

The current card is only a vertical stack of text. A grid of unbounded text stacks would be difficult to scan and impossible for a parent to size consistently. The component needs a public layout hook while keeping its internal spacing private.

## Mental model

Compose layout has a two-way contract:

```text
parent sends constraints down
            ↓
child chooses a permitted size
            ↓
child reports size and parent places it
```

A `Modifier` is an ordered chain of behaviors wrapped around a composable. The order is observable because each modifier can affect the constraints, drawing, interaction, or semantics seen by the next element in the chain.

The conventional component contract is:

```kotlin
modifier: Modifier = Modifier
```

The component applies that exact modifier to its outermost node. It does not append caller-controlled behavior to an arbitrary inner child.

## React Native bridge

| React Native | Compose | Where the analogy breaks |
|---|---|---|
| `style` prop | `Modifier` parameter | A modifier is an ordered behavior chain, not a merged CSS-like style object. |
| Padding on component container | Inner `Modifier.padding` | Modifier order changes what receives background, clipping, and click bounds. |
| Theme tokens | `MaterialTheme` roles | Values are read compositionally rather than imported as a plain global object. |
| Flex constraints | Compose constraints/layout | Compose measurement follows its own down/up constraint protocol. |

## Vocabulary

| Term | Meaning |
|---|---|
| Constraint | Minimum/maximum width and height permitted by the parent. |
| Modifier chain | Ordered behaviors applied around a composable. |
| External spacing | Space controlled by the parent around the component. |
| Internal spacing | Space owned inside the component boundary. |
| Theme role | Semantic style such as `titleMedium` rather than a one-off font size. |
| Surface | Material container providing shape, color, elevation, and optional interaction. |

## Predict before running

Compare:

```kotlin
Modifier
    .background(Color.Yellow)
    .padding(16.dp)
```

with:

```kotlin
Modifier
    .padding(16.dp)
    .background(Color.Yellow)
```

Predict whether the yellow region includes the padding in each chain. Draw two boxes before running the example.

## Minimal demonstration — modifier order on progress text

The coach demonstrates modifier order on the existing progress text only, not on `LetterCard`.

Use a temporary background and padding in one order, observe the result, reverse the order, and observe again. Remove the temporary background after the prediction is confirmed.

Expected discovery: earlier modifiers wrap later modifiers and the content. Modifier order is behavior, not formatting preference.

## Guided lab — establish the card boundary

### Step 1 — Add the public modifier contract

Add a defaulted `Modifier` parameter to `LetterCard`. Apply it to the outermost container.

The coach may provide the function signature after the learner identifies why the parent needs this hook.

### Step 2 — Choose the outer container

Replace the structural outer container with a Material `Surface`. Use the current theme's shape and a small tonal elevation. Do not hard-code a color.

### Step 3 — Keep internal spacing private

Inside the surface, keep a vertical layout and give that inner layout 16 dp of padding. Center the text and use one consistent small vertical gap.

### Step 4 — Apply typography roles

Choose existing Material typography roles for:

- the Hebrew glyph as the strongest text;
- the Latin name as secondary hierarchy;
- sounds as supporting text.

The coach explains the available roles; the learner chooses and justifies the mapping.

### Step 5 — Check

Render all three cards. Resize the desktop window. Confirm that the component does not assume a phone width or fixed card height.

## Independent task — style AlphabetHeader

Stop before hints and style `AlphabetHeader` without copying the card's implementation.

### Acceptance criteria

- It accepts a caller-provided `Modifier = Modifier`.
- The modifier is applied to its outermost layout.
- The component owns its internal spacing.
- Title and progress use different Material typography roles.
- It uses theme colors rather than literal colors.
- It does not set a fixed screen width or height.
- Changing the caller's outer padding changes placement without changing internal spacing.
- Desktop rendering and compilation succeed.

## Test and debugging plan

1. Verify the modifier-order prediction with the temporary demonstration.
2. Confirm `LetterCard` still renders all content.
3. Pass a visible caller modifier from `App()` and confirm it affects the surface.
4. Check the card at narrow and wide desktop window sizes.
5. Inspect for hard-coded colors and font sizes.
6. Run desktop Kotlin compilation.

When a modifier seems ignored, identify which node received it before adding another modifier.

## Hint ladder

<details>
<summary>Hint 1 — Product behavior</summary>

The parent needs to place the whole header, while the header should decide spacing between its own title and progress.

</details>

<details>
<summary>Hint 2 — Ownership</summary>

Caller modifier goes on the outermost layout. A new internal modifier can own padding inside that boundary.

</details>

<details>
<summary>Hint 3 — Shape</summary>

The header can remain a `Column` with a public modifier, theme typography on each `Text`, and component-owned spacing.

</details>

<details>
<summary>Hint 4 — Partial skeleton</summary>

```kotlin
@Composable
fun AlphabetHeader(
    title: String,
    progressText: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = /* caller boundary plus internal choice */,
        verticalArrangement = /* spacing */,
    ) {
        /* title with theme role */
        /* progress with theme role */
    }
}
```

</details>

## Teach-back

Explain:

1. why a reusable composable accepts `Modifier = Modifier`;
2. why that modifier belongs on the outermost node;
3. why padding before and after background differs;
4. who owns external versus internal spacing;
5. why theme roles are preferable to arbitrary font sizes at this stage.

## Exit ticket

1. In which direction do constraints flow?
2. In which direction do measured sizes flow?
3. Is modifier order observable?
4. Should `LetterCard` force its own screen width?
5. What does a parent gain from the modifier parameter?

## Review rubric

Passing work must preserve a clean component boundary, use meaningful Material roles, respond to parent constraints, and demonstrate modifier-order reasoning. Visual preference alone is not sufficient if modifier ownership is wrong.

## Completion evidence

- [ ] Modifier-order prediction is verified.
- [ ] `LetterCard` exposes and applies its modifier correctly.
- [ ] Card internal layout and typography are styled.
- [ ] Independent `AlphabetHeader` styling works.
- [ ] Desktop compilation passes.
- [ ] Learner explains modifier ownership and order.
- [ ] Review findings are resolved.

## Next retrieval

Lesson 02.03 reuses the modifier contract while adding callbacks and previews for multiple card states.

## References

- [Constraints and modifier order](https://developer.android.com/develop/ui/compose/layouts/constraints-modifiers)
- [Compose modifiers](https://developer.android.com/develop/ui/compose/modifiers)
- [Material 3 in Compose](https://developer.android.com/develop/ui/compose/designsystems/material3)

