# Lesson 03.01 — Targets, source sets, and dependency reach

Status: Ready after Module 02

Module: 03 — KMP boundaries and resources

Estimated focused time: 45–70 minutes

## Product outcome

Temporary alphabet content leaves `App()` and becomes shared Alphabet-owned content that all three development hosts can render. The UI root stops constructing domain objects on every recomposition.

## Learning outcomes

By the end, the learner can:

- place shared product code by both source set and feature ownership;
- predict whether a dependency is visible to common, Android, iOS, and desktop code;
- distinguish a Gradle module from a Kotlin source set;
- independently move one declaration without changing behavior.

## Why the app needs this now

Module 02 proved the UI with hard-coded values inside `App()`. Module 03 will replace those values with packaged resources. First, the temporary content needs a clear shared owner so the resource work does not remain tangled with composition.

## Mental model

```text
shared module
├── commonMain       shared API and implementation
├── androidMain      Android-only additions/actuals
├── iosMain          iOS-only additions/actuals
└── desktopMain      development-host additions/actuals
```

A package answers “who owns this?” A source set answers “which targets compile this?” They are independent coordinates.

Dependencies flow from target source sets toward common source sets. Common code cannot import APIs available only in a child platform source set.

## Minimal demonstration

The coach traces one existing `Letter` declaration from `App()` and moves Aleph into a temporary shared Alphabet content declaration. This single move demonstrates package/source-set placement.

The demonstration stops before moving Bet and Gimel.

## Guided lab

1. Create a temporary `alphabet/data/StarterAlphabetContent.kt` in `commonMain`.
2. Move Aleph there with the learner choosing the declaration shape.
3. Import and render it from `App()`.
4. Confirm Desktop Hot Reload is unchanged.
5. Explain why Android and iOS can also see it.

## Independent task

Move the remaining temporary letters and ordered list out of `App()`.

### Acceptance criteria

- `App()` no longer constructs `Letter` values.
- Alphabet owns the temporary collection.
- The collection remains read-only and ordered.
- No Compose imports enter the content file.
- No Android, iOS, or desktop APIs enter `commonMain`.
- Existing explorer behavior is unchanged.
- Desktop compilation passes.

## Hint ladder

1. Restate behavior: the same list should arrive at the UI from a different owner.
2. Ownership: content belongs to Alphabet data, not the app root.
3. Shape: expose one read-only `List<Letter>`.
4. Skeleton: request a partial file only after an attempt.

## Teach-back

Explain the difference among `shared`, `commonMain`, and `alphabet.data`, and why moving content out of `App()` is more than file organization.

## Completion evidence

- [ ] One guided letter move works.
- [ ] Independent remaining content move works.
- [ ] UI behavior is unchanged.
- [ ] Desktop compilation passes.
- [ ] Placement reasoning is correct.

## References

- [Kotlin Multiplatform project structure](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html)

