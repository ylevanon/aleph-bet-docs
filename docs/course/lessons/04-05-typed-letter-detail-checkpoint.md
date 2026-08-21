# Lesson 04.05 — Typed letter detail and feature checkpoint

Status: Ready after Lesson 04.04

Module: 04 — Alphabet feature vertical slice

Estimated focused time: 90–130 minutes

## Product outcome

Selecting a letter opens a stable detail destination by ID, the destination resolves current domain content through `AlphabetRepository`, and adding a fourth letter requires content changes rather than UI duplication.

## Learning outcomes

By the end, the learner can:

- distinguish a typed route from the domain object displayed at the destination;
- pass stable identity rather than a whole mutable/display object through navigation;
- resolve destination state through the repository;
- handle unknown IDs without crashing;
- independently add a fourth letter only through bundled content;
- verify explorer/detail flow on Android and iOS.

## Why the app needs this now

The explorer can render real content, but a browsable reference requires detail. Passing an entire `Letter` through navigation would couple route serialization to the full domain shape and make stale copies possible. The route should carry only stable identity.

## Mental model

```text
tap LetterCard
    ↓
navigate with stable letter ID value
    ↓
LetterDetailRoute
    ↓ repository lookup
LetterDetailUiState
    ↓
LetterDetailScreen
```

A route describes destination identity and arguments. It does not contain screen UI or repository behavior.

## Configuration checkpoint

The coach provides version-matched Compose Multiplatform Navigation and serialization boilerplate after explaining why the dependency is now needed. Configuration is safe to copy; route modeling and destination behavior remain learner work.

Module 06 later expands this minimal Alphabet graph into the full app graph and introduces ViewModel/Koin ownership.

## Minimal demonstration

The coach creates two argument-free temporary destinations and demonstrates push/back behavior. This isolates `NavHost`, controller, and back stack without revealing the letter-detail route.

Remove the temporary destination after the mechanism is understood.

## Guided lab

1. Define an explorer route.
2. Design a serializable detail route carrying only the stable authored ID value.
3. Make `LetterCard` emit selection to the navigation owner.
4. Navigate without passing `Letter`.
5. Decode the route argument at detail.
6. Convert it to `LetterId` at the boundary.
7. Resolve the current `Letter` through `AlphabetRepository`.
8. Render a stateless detail screen with glyph, name, sounds, and reviewed explanation/audio affordance.
9. Support back navigation.
10. Verify the flow in the desktop host.

## Independent task

Add Dalet only through bundled JSON/resources, then verify explorer-to-detail behavior. Also handle an unknown detail ID.

### Acceptance criteria

- No Dalet-specific composable or branch is added.
- JSON/DTO/mapping/repository pipeline supplies Dalet.
- Explorer automatically renders the fourth keyed card.
- Selecting Dalet opens the shared detail screen.
- The route carries stable ID data, not a full `Letter` or DTO.
- Unknown ID produces explicit not-found UI with a back action.
- Back returns to the same explorer flow.
- Mapping/repository/navigation checks pass.
- Android and iOS both complete explorer → detail → back.
- Corrupting one reference fails at the mapping boundary; restoring it returns the suite to green.

## Hint ladder

1. Product behavior: detail needs to know which letter, not receive every current display field.
2. Ownership: navigation owns route/back stack; repository owns factual lookup; screen owns rendering.
3. Shape: serializable route data class with one string ID value, converted to `LetterId` at destination.
4. Request a partial route/destination skeleton only after defining the route in English.

## Teach-back

Explain why routes carry stable IDs, why detail resolves through the repository, why an unknown ID is possible even with bundled content, and how adding Dalet proves the vertical slice can scale.

## Feature checkpoint

Before Module 05:

- real bundled JSON drives explorer;
- DTOs remain in data;
- domain invariants and document validation pass;
- repository has a fake and bundled implementation;
- loading/failure/retry behavior works;
- explorer and detail share one domain model;
- Android and iOS complete the same flow;
- a fourth letter is a content change.

## Completion evidence

- [ ] Typed explorer/detail routes work.
- [ ] Detail resolves by `LetterId`.
- [ ] Unknown ID UI works.
- [ ] Independent Dalet content extension works.
- [ ] Corrupt-reference failure is interpreted.
- [ ] Common tests pass.
- [ ] Android/iOS flow passes.
- [ ] Module 04 architecture defense passes.

## Next module

Module 05 uses the real Alphabet repository to build a deterministic plain-Kotlin learning session engine before any ViewModel or Room code is introduced.

## References

- [Navigation in Compose Multiplatform](https://kotlinlang.org/docs/multiplatform/compose-navigation.html)
- [Navigation and routing](https://kotlinlang.org/docs/multiplatform/compose-navigation-routing.html)

