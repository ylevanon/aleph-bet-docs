# Lesson 04.02 — Serialization, validation, and mapping

Status: Ready after Lesson 04.01

Module: 04 — Alphabet feature vertical slice

Estimated focused time: 80–120 minutes

## Product outcome

The packaged `alphabet.json` is decoded through kotlinx.serialization, validated as one coherent curriculum document, and mapped into domain letters with focused common tests.

## Learning outcomes

By the end, the learner can:

- configure Kotlin serialization in a KMP shared module;
- decode a raw UTF-8 resource into DTOs;
- separate decoding errors from content-validation errors;
- enforce duplicate ID/order and missing-reference rules before `associateBy` can hide them;
- independently add one validation rule from a product invariant.

## Why the app needs this now

`associateBy` silently keeps the later value when keys repeat. A JSON decoder can also produce structurally valid DTOs whose references do not point to real content. The mapping boundary is the first place the whole authored document is available and should fail clearly before invalid content reaches the UI.

## Minimal demonstration

Configuration boilerplate is safe to copy after its role is explained:

- serialization compiler plugin matching the Kotlin version;
- version-catalog JSON dependency;
- commonMain dependency.

The coach then decodes one tiny inline Aleph JSON string in a common test. This proves configuration only; it is not the resource loader.

## Guided lab

1. Annotate DTOs, not the domain model, for JSON decoding.
2. Configure a deliberate `Json` instance and explain each non-default option used.
3. Decode the packaged raw bytes into the top-level DTO.
4. Validate unique letter IDs before building a map.
5. Validate unique authored order.
6. Validate every content reference against the document or reviewed asset manifest.
7. Map only after document-level validation passes.
8. Return or throw a domain-specific content error with useful context.

## Independent task

Add a new corruption test and the validation that makes it fail clearly.

Choose one:

- duplicate letter ID;
- duplicate order;
- blank glyph/name;
- referenced audio asset absent from the reviewed manifest;
- a future base-letter reference pointing to no letter.

### Acceptance criteria

- The test fails before the rule exists.
- The failure names the offending ID/reference.
- Validation occurs before `associateBy` can overwrite data.
- JSON exceptions and content-rule failures are distinguishable.
- Valid three-letter content still maps.
- No Compose, Android, iOS, desktop, or Room dependency enters the mapper.
- Focused and full common tests pass.

## Mapping-boundary meaning

A mapping boundary is the conversion point where source-shaped data becomes trusted application data:

```text
untrusted authored DTOs
        ↓ validate all relationships
trusted domain values
```

“Missing reference” means one record names another ID or asset that the complete source cannot resolve. This is not a nullable UI field; it is inconsistent bundled content and should fail during development/testing.

## Hint ladder

1. Product behavior: invalid shipped curriculum should fail before a learner sees it.
2. Ownership: whole-document rules belong around DTO-to-domain mapping.
3. Shape: collect IDs first, compare sizes/references, then map.
4. Ask for one test skeleton before requesting validation code.

## Teach-back

Explain why a JSON file can decode successfully and still be invalid product content, and why `associateBy` must not be the uniqueness validator.

## Completion evidence

- [ ] Serialization configuration works.
- [ ] Packaged JSON decodes.
- [ ] Duplicate IDs/orders are rejected.
- [ ] Required references are resolved.
- [ ] Independent corruption test passes after implementation.
- [ ] Valid content maps to domain values.
- [ ] Common tests pass.

## References

- [Get started with Kotlin serialization](https://kotlinlang.org/docs/serialization-get-started.html)
- [JSON serialization](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/)

