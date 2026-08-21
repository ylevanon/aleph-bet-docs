# Lesson 02.05 — Lazy grids, stable keys, RTL, and semantics

Status: Ready after Lesson 02.04

Module: 02 — Compose foundations

Estimated focused time: 75–110 minutes

## Product outcome

Aleph, Bet, and Gimel render in a responsive three-column alphabet grid with stable identity, Hebrew-aware ordering, and one meaningful accessible action per card.

## Learning outcomes

By the end, the learner can:

- explain when lazy layout is useful even with a small initial data set;
- render domain values through the `LazyVerticalGrid` DSL;
- provide stable saveable keys derived from `LetterId`;
- distinguish content order from visual layout direction;
- inspect and improve card semantics;
- independently preserve selection behavior after changing layout containers.

## Prerequisites

- Stateless `LetterCard` supports selected styling and callbacks.
- The parent owns current selection.
- Modifier order and state ownership are understood.

## Retrieval warm-up

1. Why does the parent, not each card, own the selected ID?
2. What lifetime does `remember` provide?
3. Why does the card apply its caller modifier to the outermost node?

## Why the app needs this now

A vertical stack is not the intended alphabet explorer. The full alphabet needs a scrollable grid that composes visible items efficiently and preserves each card's identity when data order changes.

Hebrew also introduces two different questions:

- In what authored sequence are letters stored?
- From which side should the grid visually begin?

Those decisions must not be conflated.

## Mental model

A lazy layout receives a content DSL rather than eagerly executing a normal collection loop for every child:

```text
List<Letter>
     ↓
LazyGridScope declares keyed items
     ↓
grid composes items needed for current layout/scroll state
```

An item key tells Compose which domain item a composition belongs to as positions change. A key should be stable, unique, and supported by state restoration on relevant targets. `letter.id.value` is a safe string key for this lesson.

Content order and layout direction are independent:

- sorting by `Letter.order` determines the sequence;
- RTL layout determines which side is considered the start edge.

## React Native bridge

| React Native | Compose | Where the analogy breaks |
|---|---|---|
| `FlatList`/multi-column list | `LazyVerticalGrid` | Compose uses a receiver DSL and composition identity rather than rendering React elements from `renderItem`. |
| `keyExtractor` | `key` in `items` | Keys also participate in remembered/saveable item state identity. |
| `I18nManager`/direction styles | `LayoutDirection` and locale behavior | Direction can be scoped compositionally and should not reorder source data blindly. |
| Accessibility props | Semantics and higher-level interactive APIs | Material/Foundation components often provide and merge semantics automatically. |

## Vocabulary

| Term | Meaning |
|---|---|
| Lazy layout | Layout that composes/places item content as needed for the viewport. |
| DSL receiver | Scope in which functions such as `items` declare layout content. |
| Stable key | Unique identity associated with an item independent of its position. |
| Layout direction | Definition of start/end edges for layout. |
| Semantics | Meaning and actions exposed to accessibility services and UI tests. |
| Merged semantics | Descendant meaning represented as one logical interactive node. |

## Predict before running

Suppose the grid remembers state for an item at index 1 but no key is provided. Then Aleph is inserted at index 0.

Predict which item may inherit the remembered state. Repeat the prediction when each item is keyed by stable letter ID.

## Minimal demonstration — keyed lazy column

The coach first replaces the small non-lazy loop with a `LazyColumn` using keyed `items`. This isolates the DSL and key syntax without simultaneously solving the production grid.

The demonstration must preserve:

- existing card callbacks;
- selected styling;
- authored order.

Once the learner can explain the receiver DSL and key, move to the independent grid rather than polishing the temporary column.

## Guided lab — inspect semantics before changing them

### Step 1 — Inspect the current card

Use the semantics tree or UI tooling to identify text and click actions already supplied by Material/Foundation APIs.

### Step 2 — Choose one logical action

Make the whole letter card one actionable node. Avoid giving glyph, name, and sounds three separate identical click actions.

### Step 3 — Name the action

Provide a useful click label such as opening or selecting the named letter. Do not duplicate all visible text into a noisy content description without checking the merged result.

### Step 4 — Decide visual direction

Keep the source list sorted by `order`. Scope RTL layout to the grid only if the agreed product behavior is that Aleph begins at the right-hand start edge in an otherwise English UI.

### Step 5 — Check

Use Desktop Hot Reload for layout and an Android/iOS accessibility smoke check at the module checkpoint.

## Independent task — build the production grid

Stop before hints.

Replace the guided lazy column with the actual alphabet grid.

### Acceptance criteria

- Uses `LazyVerticalGrid`.
- Uses three fixed columns for the current V1 design checkpoint.
- Receives or reads the same ordered `List<Letter>` used by the explorer.
- Uses `letter.id.value` or an equivalently stable unique key.
- Applies consistent horizontal and vertical spacing.
- Preserves parent-owned selection and both callbacks.
- Aleph appears at the intended visual start edge without reversing the authored source list in place.
- Each card exposes one meaningful click action.
- The grid handles a narrow phone-sized desktop window without fixed screen dimensions.
- Desktop rendering and compilation succeed.

The complete grid implementation is intentionally not shown before the task.

## Test and debugging plan

1. Predict index identity with and without keys.
2. Verify the temporary keyed lazy column.
3. Implement the grid and count exactly three visible cards.
4. Select Bet, reorder only the input declaration temporarily, and confirm selection remains attached to Bet.
5. Inspect merged semantics for one card.
6. Resize to the agreed phone-sized window.
7. Run desktop Kotlin compilation.
8. Smoke-test visual order on Android and iOS before passing the module.

If cards disappear, inspect whether the lazy grid received bounded height from its parent before changing item code.

## Hint ladder

<details>
<summary>Hint 1 — Product behavior</summary>

The explorer needs one scrollable container with three columns and stable identity per letter.

</details>

<details>
<summary>Hint 2 — Ownership</summary>

The grid owns layout. `LetterCard` still owns one card's presentation, and the parent still owns selection.

</details>

<details>
<summary>Hint 3 — Shape</summary>

Use a lazy vertical grid with fixed columns. Inside its content receiver, declare keyed items from `letters`.

</details>

<details>
<summary>Hint 4 — Partial skeleton</summary>

```kotlin
LazyVerticalGrid(
    columns = GridCells.Fixed(/* count */),
    /* spacing and modifier */
) {
    items(
        items = letters,
        key = { letter -> /* stable string ID */ },
    ) { letter ->
        LetterCard(
            letter = letter,
            /* preserve state and callbacks */
        )
    }
}
```

</details>

## Teach-back

Explain:

1. why the source remains `List<Letter>`;
2. what the lazy-grid DSL does;
3. why keys use domain identity rather than position;
4. how source order differs from RTL visual direction;
5. why semantics matter for both accessibility and tests.

## Exit ticket

1. What bug can an index key cause after reordering?
2. Why use `letter.id.value` instead of list position?
3. Does RTL require mutating or reversing the source list?
4. What creates a merged interactive semantics node?
5. Which component owns item spacing: card or grid?

## Review rubric

Passing work requires stable identity, correct ownership through the layout change, deliberate RTL behavior, and meaningful semantics—not merely three columns on screen.

## Completion evidence

- [ ] Key identity prediction is correct.
- [ ] Guided keyed lazy column works.
- [ ] Independent three-column grid works.
- [ ] Selection remains attached to letter identity.
- [ ] Semantics are inspected and improved.
- [ ] Desktop compilation passes.
- [ ] Android and iOS layout smoke checks pass.
- [ ] Review findings are resolved.

## Next retrieval

Lesson 02.06 turns the semantics tree into test assertions and requires a fourth-letter variation without duplicating card code.

## References

- [Lazy lists and lazy grids](https://developer.android.com/develop/ui/compose/lists)
- [Compose semantics](https://developer.android.com/develop/ui/compose/accessibility/semantics)
- [Accessibility API defaults](https://developer.android.com/develop/ui/compose/accessibility/api-defaults)

