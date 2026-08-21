# Lesson 03.04 — Cross-platform resource checkpoint

Status: Ready after Lesson 03.03

Module: 03 — KMP boundaries and resources

Estimated focused time: 70–100 minutes

## Product outcome

The same reviewed Hebrew font and three-letter resource subset are verified in the desktop harness, Android app, and iOS app. A deliberate source-set error is diagnosed from compiler evidence.

## Learning outcomes

By the end, the learner can:

- trace a shared resource from disk to generated/common access and each host;
- diagnose an illegal platform import by source-set reach;
- distinguish desktop convenience from Android/iOS parity;
- verify packaged-resource behavior on both shipping targets;
- independently add one resource without adding platform loaders.

## Why the app needs this now

Desktop Hot Reload proves the shared UI path quickly, but V1 ships through Android and iOS hosts. Before parsing content, we need evidence that the real assets and typography are present in both application bundles.

## Minimal demonstration

The coach introduces one reversible compile failure in a scratch branch or temporary edit: a common file imports one Android-only API. The learner predicts and reads the common/iOS failure before the edit is removed.

No broken code is committed.

## Guided checkpoint

1. Draw the path from `composeResources` to `Res` and the shared theme.
2. Run the focused desktop resource probe.
3. Run the Android app and verify the reviewed glyph/font.
4. Run the iOS app and verify the same content.
5. Confirm the raw JSON byte count/hash is non-empty on both targets.
6. Confirm the reviewed audio asset URI/bytes resolve on both targets.
7. Record any platform rendering difference before changing code.

## Independent task

Add one additional reviewed resource—string, glyph sample, or audio clip—through the existing common resource structure and verify it on both shipping targets.

### Acceptance criteria

- The asset is reviewed and product-relevant.
- It is placed in the correct resource directory.
- Common code accesses it without target conditionals.
- Android and iOS both package and resolve it.
- Desktop remains a development check, not parity proof.
- The learner explains why no new platform source file was required.
- All temporary deliberate failures are removed.

## Hint ladder

1. Product behavior: the new asset must be reachable through the same shared resource path on both shipping targets.
2. Ownership: place it in common Compose resources unless the asset itself is intentionally target-specific.
3. Shape: mirror the directory and access pattern of the already verified resource of the same kind.
4. Request exact accessor/path syntax only after adding the reviewed file.

## Failure triage

Classify failures before fixing:

| Failure | First likely boundary |
|---|---|
| `Res` accessor unresolved | Resource path/name or generated-accessor build |
| Android API unresolved in common | Illegal source-set dependency |
| Desktop works, iOS asset missing | Packaging/path parity, not Compose state |
| Glyph differs across hosts | Font application/fallback, not JSON parsing |
| Raw bytes load but content invalid | Mapping/serialization in Module 04 |

## Teach-back

Explain why common resource success on desktop is necessary but insufficient, and identify the exact evidence that proves Android/iOS parity for this checkpoint.

## Completion evidence

- [ ] Deliberate source-set failure is diagnosed.
- [ ] Desktop resource probe succeeds.
- [ ] Android font/resources succeed.
- [ ] iOS font/resources succeed.
- [ ] Independent reviewed asset succeeds on both.
- [ ] No target-specific resource wrapper was added.
- [ ] Module 03 placement defense passes.

## Next module

Module 04 parses the bundled JSON into DTOs, maps it into valid domain values, exposes it through an Alphabet repository, and replaces temporary content in the explorer.

## References

- [Compose Multiplatform resources overview](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-resources.html)
- [Kotlin Multiplatform project structure](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html)
