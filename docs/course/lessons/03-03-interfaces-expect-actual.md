# Lesson 03.03 — Common interfaces versus expect/actual

Status: Ready after Lesson 03.02

Module: 03 — KMP boundaries and resources

Estimated focused time: 45–70 minutes

## Product outcome

The generated generic platform demo is removed from production code, and the resource path remains fully common because Compose Resources already solves it. The learner designs the future audio boundary without implementing playback early.

## Learning outcomes

By the end, the learner can:

- choose among common implementation, ordinary interface injection, and `expect`/`actual`;
- explain why platform-specific code requires an actual platform need;
- identify the cost of adding a new target to an expect/actual declaration;
- remove unused scaffold code safely;
- sketch a fakeable audio contract without importing platform APIs.

## Why the app needs this now

Resources are loading in common code. It would be easy to assume every cross-platform capability needs `expect`/`actual`. That would create platform files even where a common library already works.

The generated `Platform`/`getPlatform()` example gives us real code to evaluate and remove instead of inventing a toy hierarchy.

## Decision model

Use the smallest boundary that works:

```text
Can common code/library implement it?
├── yes → keep it common
└── no
    Can a common interface receive platform implementations?
    ├── yes → prefer interface + injection
    └── declaration itself differs by target → consider expect/actual
```

An ordinary interface is usually easier to fake directly in tests. `expect`/`actual` creates a compile-time obligation for every targeted platform.

## Minimal demonstration

Trace the existing generated declarations:

- common `Platform` interface;
- expected `getPlatform()` factory;
- Android, iOS, and desktop actual functions.

Temporarily remove one desktop actual declaration and read the compiler failure, then restore it.

This demonstrates the obligation without creating new product code.

## Guided lab

1. Find every production call to `Greeting`, `Platform`, and `getPlatform()`.
2. Confirm whether the current Aleph Bet UI needs any of them.
3. Remove one unused generated path with build checks.
4. Verify shared resources still work with no platform resource wrapper.
5. Explain why `Res.readBytes` belongs in common code.

## Independent task

Remove the remaining unused greeting/platform scaffold safely, then design—but do not implement—the future audio boundary.

### Acceptance criteria

- No generated greeting/platform text remains in the app.
- Unused common and actual declarations are removed together.
- Android, iOS, and desktop compilation remain valid.
- The proposed audio contract contains common operations such as play/stop and domain/resource identity.
- The proposal contains no `MediaPlayer`, `AVAudioPlayer`, `NSURL`, or Android `Context`.
- The learner explains why platform implementations can satisfy an ordinary common interface.
- No playback code is added before Module 08.

## Hint ladder

1. Product behavior: remove scaffold the product does not use.
2. Ownership: resource loading is common; audio playback will belong to Audio.
3. Shape: a common interface can expose operations while platform classes implement them later.
4. Ask for an audio signature skeleton only after describing required operations in English.

## Teach-back

Give one Aleph Bet example for each choice: common implementation, interface injection, and a case where `expect`/`actual` might genuinely be justified.

## Completion evidence

- [ ] Missing-actual failure is interpreted.
- [ ] Unused scaffold is removed.
- [ ] All current targets compile.
- [ ] Resource code remains common.
- [ ] Future audio boundary is designed without implementation leakage.

## References

- [Expected and actual declarations](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html)
- [Use platform-specific APIs](https://kotlinlang.org/docs/multiplatform/multiplatform-connect-to-apis.html)

