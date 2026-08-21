# Lesson 03.02 — Compose resources, fonts, JSON, and audio assets

Status: Ready after Lesson 03.01

Module: 03 — KMP boundaries and resources

Estimated focused time: 70–105 minutes

## Product outcome

The shared app renders Hebrew with the reviewed bundled font, and the three-letter JSON/audio subset is packaged as common resources on Android, iOS, and desktop.

## Learning outcomes

By the end, the learner can:

- place fonts, raw JSON, strings, and audio in the Compose resource hierarchy;
- use generated font accessors from common Compose code;
- explain why raw files are read asynchronously;
- distinguish packaged availability from parsing or playback;
- independently add and verify one reviewed resource.

## Why the app needs this now

Hebrew typography is a product requirement, not polish. Curriculum and audio must also ship offline. Before writing loaders, the assets must be packaged through the shared resource system and proven reachable on every target.

## Resource layout

```text
shared/src/commonMain/composeResources/
├── font/
├── files/alphabet/
├── files/audio/he/
└── values/
```

Use only reviewed assets. Do not create fake shipping audio to satisfy the lesson.

## Minimal demonstration

The coach adds one reviewed Hebrew font file to `font/`, builds to generate its accessor, and applies it to one typography role in a tiny theme demonstration.

The demonstration stops before converting the whole app typography.

## Guided lab

1. Inspect the exact font license and filename.
2. Add the font under `composeResources/font`.
3. Build once so `Res.font.*` accessors exist.
4. Create the app typography using `Font(Res.font...)`.
5. Render one Hebrew glyph and one Latin label.
6. Compare fallback and bundled rendering.

## Independent task

Package the reviewed three-letter content subset and at least one reviewed audio clip, then prove both resources are reachable without parsing JSON or playing audio yet.

### Acceptance criteria

- Raw JSON lives under `files/alphabet`.
- Audio uses a stable path convention under `files/audio/he`.
- The JSON contains only reviewed content and valid UTF-8 Hebrew.
- A small common suspend probe can read the JSON bytes through `Res.readBytes`.
- A common resource URI or byte probe confirms the reviewed audio asset is packaged.
- No `java.io`, Android asset manager, or iOS bundle API appears in common code.
- Font rendering works on desktop and is smoke-checked on Android/iOS.

## Important boundary

This lesson proves packaging:

```text
resource exists → common resource API can reach it
```

It does not yet prove:

```text
JSON is valid → DTO maps → domain invariants pass
audio URI exists → platform player can decode/play it
```

Those belong to Modules 04 and 08.

## Hint ladder

1. Product behavior: all shipped targets need the same offline files.
2. Ownership: shared authored assets live in common Compose resources.
3. Shape: raw files use `Res.readBytes("files/...")`; fonts use generated accessors.
4. Ask for exact configuration/import boilerplate only when the asset is present.

## Teach-back

Explain why the font has a generated accessor while arbitrary raw JSON is addressed by path, and why successful byte loading is not equivalent to successful domain mapping.

## Completion evidence

- [ ] Reviewed font renders Hebrew.
- [ ] Three-letter JSON is packaged.
- [ ] At least one reviewed audio asset is packaged.
- [ ] Common resource probes succeed.
- [ ] No platform file API leaks into common code.
- [ ] Android/iOS font and resource smoke checks pass.

## References

- [Compose Multiplatform resources setup](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-resources-setup.html)
- [Using multiplatform resources](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-resources-usage.html)

