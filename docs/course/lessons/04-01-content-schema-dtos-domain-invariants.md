# Lesson 04.01 — Content schema, DTOs, and domain invariants

Status: Ready after Module 03

Module: 04 — Alphabet feature vertical slice

Estimated focused time: 70–100 minutes

## Product outcome

The reviewed three-letter JSON has an explicit schema and source-shaped DTOs. One Aleph DTO maps manually into a valid domain `Letter`, proving the boundary before serialization is introduced.

## Learning outcomes

By the end, the learner can:

- distinguish authored JSON shape, DTOs, and domain models;
- choose nullable/default DTO fields from content compatibility rather than UI convenience;
- state domain invariants at the construction/mapping boundary;
- map one source-shaped object without exposing it to UI;
- independently extend the schema for Bet's multiple sounds.

## Why the app needs this now

The app has packaged JSON but still renders temporary Kotlin declarations. Decoding directly into `Letter` would couple editorial file changes to every domain consumer. We need a source representation that can change with the content format and a domain representation that stays valid for application behavior.

## Mental model

```text
alphabet.json
    ↓ decode later
AlphabetContentDto / LetterDto
    ↓ validate + map
Letter domain values
    ↓
repository and presentation
```

DTOs are allowed to resemble the file. Domain models are allowed to differ when valid behavior needs a stronger shape.

## Minimal demonstration

The coach models only Aleph:

1. write the smallest reviewed Aleph JSON object;
2. define the matching plain `LetterDto` fields;
3. manually construct that DTO;
4. map it into the current `Letter`;
5. render the resulting domain value.

No serializer annotation or JSON library is used in the demonstration.

## Guided lab

1. List the reviewed facts actually available for Aleph, Bet, and Gimel.
2. Decide the top-level document shape.
3. Separate a letter-name audio reference from per-sound references if the assets require it.
4. Write the DTO types that match that authored shape.
5. Write a pure mapping function for Aleph.
6. Add `require` checks or mapping failures for nonblank ID/glyph/name and positive order.
7. Keep Compose and resource loading out of the mapper.

## Independent task

Extend the schema and mapper so Bet represents its reviewed `b` and `v` sound variants without flattening them into one display string.

### Acceptance criteria

- JSON remains readable to a content editor.
- Bet sound variants are distinct source values.
- DTO nullability/defaults match real authored compatibility needs.
- Domain values contain no DTO types.
- UI receives no DTO.
- Invalid blank identity or non-positive order fails at mapping/construction.
- The learner explains every optional field.
- Focused common tests cover Aleph and Bet mapping.

## Hint ladder

1. Product behavior: Bet needs two independently authored sound facts.
2. Ownership: the DTO mirrors content; the domain model represents valid app facts.
3. Shape: consider a nested sound DTO/list rather than one formatted string.
4. Request a partial DTO signature only after drafting the JSON.

## Teach-back

Explain why the app should not decode JSON directly into the exact model used by Compose, and name one change that should affect only DTO/mapping code.

## Completion evidence

- [ ] Three-letter schema is explicit.
- [ ] Aleph guided mapping works.
- [ ] Bet independent sound mapping works.
- [ ] Domain invariants reject invalid core fields.
- [ ] Common mapping tests pass.
- [ ] No DTO reaches presentation.

## References

- [Kotlin serialization overview](https://kotlinlang.org/docs/serialization.html)

