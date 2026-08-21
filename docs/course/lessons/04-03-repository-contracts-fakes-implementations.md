# Lesson 04.03 — Repository contracts, fakes, and implementations

Status: Ready after Lesson 04.02

Module: 04 — Alphabet feature vertical slice

Estimated focused time: 65–95 minutes

## Product outcome

Alphabet presentation can request ordered letters and look up one letter without knowing whether content comes from JSON, a test fake, or a future source.

## Learning outcomes

By the end, the learner can:

- define a repository around consumer needs rather than storage verbs;
- distinguish repository contract, bundled implementation, loader, and mapper;
- implement a fake directly for tests/previews;
- avoid leaking DTOs, resource paths, or serialization errors to UI;
- independently add one repository behavior from a real detail-screen need.

## Why the app needs this now

The JSON pipeline works, but presentation should not call `Res.readBytes` or `Json.decodeFromString`. The explorer needs ordered domain letters; detail needs one letter by ID. That is enough consumer evidence to introduce `AlphabetRepository` now.

## Mental model

```text
presentation
    ↓ domain-oriented calls
AlphabetRepository
    ↓
BundledAlphabetRepository
    ├── resource loader
    ├── DTO decoder
    └── mapper/validator
```

The repository hides source mechanics. It should not hide learning order, mastery, or unrelated progress behavior.

## Minimal demonstration

The coach writes one small consumer-first contract for ordered letters only. A tiny fake returns two supplied domain values. A common test proves the consumer sees the fake values.

The bundled implementation is not shown in the demonstration.

## Guided lab

1. Name the explorer's required operation in product terms.
2. Decide whether immutable bundled content needs a `Flow`; justify the answer.
3. Define `AlphabetRepository` in `alphabet/domain`.
4. Create `FakeAlphabetRepository` in test support.
5. Implement the ordered-list operation in `BundledAlphabetRepository` using the loader/mapper.
6. Ensure DTOs and resource paths remain in `alphabet/data`.
7. Test authored ordering through the repository.

## Independent task

Add the repository operation needed by letter detail: resolve one `LetterId`.

### Acceptance criteria

- Contract accepts `LetterId`, not arbitrary display text.
- Missing ID behavior is explicit: nullable result or domain error, with justification.
- Fake and bundled implementations obey the same behavior.
- No DTO or resource path appears in the contract.
- Repository does not expose a mutable collection.
- Common tests cover found and missing IDs.
- Presentation can be constructed with the fake without resource setup.

## Repository ownership

`AlphabetRepository` belongs to Alphabet because it exposes alphabet facts. A future screen that combines a `Letter` with learner mastery may call both Alphabet and Progress sources through a coordinator; that does not move mastery into this repository.

## Hint ladder

1. Product behavior: explorer needs all ordered letters; detail needs one typed identity.
2. Ownership: contract belongs to domain-facing Alphabet; implementation belongs to Alphabet data.
3. Shape: one suspend operation for each current consumer is sufficient.
4. Request a contract skeleton after writing the two operations in English.

## Teach-back

Explain what the repository hides, what it deliberately does not own, and why a fake is not the same thing as mocking `Res.readBytes`.

## Completion evidence

- [ ] Consumer-first contract exists.
- [ ] Fake supports ordered content.
- [ ] Bundled implementation uses loader/mapper.
- [ ] Independent typed lookup works.
- [ ] Found/missing tests pass.
- [ ] DTO/resource details do not leak.

## References

- [Dependency inversion principle](https://kotlinlang.org/docs/multiplatform/multiplatform-connect-to-apis.html)

