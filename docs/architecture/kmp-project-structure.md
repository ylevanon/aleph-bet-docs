# Teaching-first Kotlin Multiplatform architecture

Status: Working architecture decision

Last updated: 2026-08-18

## Short answer

Aleph Bet should not copy [`philipplackner/CMPMemeCreator`](https://github.com/philipplackner/CMPMemeCreator), or any other sample, as its project template. We will use a small, feature-oriented architecture that grows through the alphabet vertical slice and makes each Kotlin/KMP concept visible while it is being learned.

`CMPMemeCreator` remains a useful example of the outer Kotlin and Compose Multiplatform shell and a few presentation conventions. It is evidence to inspect, not a standard to inherit.

The reviewed source was commit [`9c020a2`](https://github.com/philipplackner/CMPMemeCreator/commit/9c020a2687b508204c8a7084826dc7d3f3a07a18). At that revision the repository had two commits, one main shared application module, no database, no repository-backed curriculum, and no substantive common tests. Its simplicity is appropriate for a meme editor but leaves several boundaries Aleph Bet needs undefined.

The goal is not to produce the most elaborate architecture. The goal is to keep product behavior, UI state, persistent learner data, and platform integrations understandable and independently testable.

## Architecture principles

### Grow boundaries from working behavior

Start with one three-letter vertical slice. Add a package or abstraction when it gives a current piece of behavior a clear home, not because a diagram says every app needs it.

The first slice should make this path real:

```text
bundled letter content
    -> repository
    -> lesson behavior
    -> ViewModel state
    -> Compose UI
    -> learner action
    -> progress persistence
```

Do not create empty `data`, `domain`, `usecase`, and `repository` packages in every feature before they contain a meaningful responsibility.

### Share product behavior; isolate operating-system behavior

Lesson rules, progress rules, content mapping, ViewModels, and most Compose UI belong in `commonMain`. Database construction, audio session details, and other operating-system integrations stay behind small boundaries in platform source sets when required.

### Prefer plain Kotlin at the center

The lesson engine and progress rules should be ordinary Kotlin that can run in `commonTest`. Compose, Room, Koin, Android, and iOS are adapters around that behavior, not dependencies of the behavior itself.

### Use one module until a split solves a measured problem

Packages give us navigation and conceptual boundaries while learning. Separate Gradle modules become worthwhile when we need compiler-enforced dependencies, independent reuse, faster isolated builds, or clearer ownership. We will not pay that complexity in advance.

## Useful ideas in `CMPMemeCreator`

### `composeApp` and `iosApp`

The sample uses:

```text
composeApp/
    src/commonMain/
    src/androidMain/
    src/iosMain/
iosApp/
```

That is the correct outer shape for this project:

- `commonMain` contains shared Compose UI, navigation, domain logic, content loading, Room declarations, and most ViewModels;
- `androidMain` contains Android entry points and implementations that require Android APIs;
- `iosMain` contains iOS implementations and exposes the shared Compose root;
- `iosApp` remains the native Xcode application wrapper.

Aleph Bet does not need multiple Gradle feature modules on day one. A single shared application module with disciplined packages is easier to learn and build. Gradle modules can be extracted later when compilation time, ownership, reuse, or dependency enforcement creates a real need.

### Feature-first packages

The sample groups most code under `meme_editor` and `meme_gallery`, rather than placing every screen in a global `screens` package. Aleph Bet should also organize code primarily around product areas.

### Unidirectional presentation flow

The meme editor has a separate immutable state, sealed action type, and ViewModel. That maps well to Compose:

```text
UiState -> Composable
UiAction -> ViewModel
ViewModel -> new UiState
```

Use this convention for behavior-rich destinations such as Lesson and Practice. Do not create ceremonial action files for tiny stateless views where ordinary callbacks are clearer.

### Typed navigation routes

The sample's serializable sealed routes are a good starting convention. Routes should carry stable identifiers such as `lessonId` or `letterId`, not entire domain objects.

### `expect`/`actual` only at platform boundaries

The sample uses common contracts with Android and iOS implementations for storage, sharing, and platform utilities. Aleph Bet will need similar boundaries for concerns such as Room database construction and possibly audio playback.

Prefer a platform-independent interface when ordinary dependency injection is sufficient. Use `expect`/`actual` when the declaration itself genuinely differs by target; do not use it merely because KMP supports it.

### Shared Compose resources

The sample places fonts and images in `commonMain/composeResources`. Aleph Bet should stage its bundled Hebrew JSON, font, and reviewed audio into this resource system.

## What not to copy blindly

### Do not keep template leftovers

Files such as `Greeting.kt` and generic platform-name demonstrations are starter-template examples, not architecture. Remove them when the real application shell exists.

### Do not put data models under presentation

The sample's `MemeTemplate` lives under `core/presentation`, which is acceptable for a tiny UI-only asset model. Aleph Bet's `Letter`, `Lesson`, progress, and exercise models have meaning outside the UI and belong in domain or data boundaries.

### Do not let ViewModels absorb the learning engine

The sample's editor behavior fits in one ViewModel. Aleph Bet's queue generation, answer evaluation, progress transitions, and review scheduling should be plain Kotlin domain logic or use cases that can be tested without Compose or a platform runtime.

### Do not infer a database architecture from this sample

It has no Room database, migrations, DAOs, repository abstraction, or persisted session model. Those must be designed for Aleph Bet rather than invented by analogy.

### Do not infer a testing strategy from this sample

An empty or nearly empty `commonTest` is not the target. The lesson engine and progress rules are ideal deterministic Kotlin tests and should be treated as first-class work.

### Do not add dependencies only because the sample uses them

The sample includes Koin, Coil, adaptive Material libraries, and sharing/export dependencies for its own needs. Their presence is not a reason for Aleph Bet to adopt them. Each dependency must correspond to a feature we actually need.

## Dependency injection decision: Koin Annotations

Aleph Bet will use Koin's annotation style instead of hand-written Koin module DSL.

The intended vocabulary is:

- `@Single` for app-lifetime repositories, the database, and stable services;
- `@Factory` for inexpensive objects that should be recreated;
- `@KoinViewModel` for screen-level ViewModels;
- `@Module` for explicit DI boundaries;
- `@ComponentScan` with a deliberately narrow package rather than an unrestricted application-wide scan;
- annotated provider functions for external types such as a Room database that we cannot annotate directly.

Use constructor injection by default. A class should declare what it needs in its constructor, and the generated Koin definitions should assemble that graph. Avoid service-locator calls from domain code and Composables.

### Compiler plugin, not legacy KSP processing

The current Koin path for KMP annotations is the Koin Compiler Plugin plus `koin-annotations`. The older `koin-ksp-compiler` processor and its per-platform KSP configuration are deprecated. We should therefore verify mutually compatible Kotlin, Compose, Koin, Room, and compiler-plugin versions when the project is created rather than copying an older tutorial's Gradle block.

This choice still deserves teaching attention. Annotations reduce binding boilerplate, but they do not remove DI concepts. Before relying on a generated binding, we should be able to answer:

- What is the object's lifetime?
- Which interface does it satisfy?
- Where is its concrete implementation selected?
- Is it common or platform-specific?
- Who owns and closes it?
- Can the consumer be tested by passing a fake directly?

Koin should assemble the application at its edges. Plain constructor injection should keep individual classes testable without starting Koin.

## Directional project tree

The exact package prefix is omitted here. This is a destination and responsibility map, not a request to scaffold every directory before the vertical slice needs it.

```text
composeApp/
  src/
    commonMain/
      composeResources/
        files/hebrew/
        files/audio/he/
        font/
        values/
      kotlin/.../
        App.kt

        core/
          audio/
          database/
          designsystem/
          navigation/
          time/

        alphabet/
          data/
            content/
            repository/
          domain/
            model/
            repository/
          presentation/
            explorer/
            detail/

        lessons/
          data/
            content/
          domain/
            model/
            engine/
            usecase/
          presentation/
            home/
            session/
            completion/

        practice/
          domain/
          presentation/

        progress/
          data/
            local/
            repository/
          domain/
            model/
            repository/

        settings/
          presentation/

    commonTest/
      kotlin/.../
        lessons/domain/engine/
        progress/domain/
        alphabet/data/content/

    androidMain/
      kotlin/.../
        core/audio/
        core/database/

    iosMain/
      kotlin/.../
        core/audio/
        core/database/

iosApp/
```

This is intentionally a hybrid:

- top-level product areas are feature-first;
- packages inside a feature separate `data`, `domain`, and `presentation` only when all three exist;
- shared technical capabilities live in a small `core`;
- `core` must not become a dumping ground for anything used twice.

## Dependency direction

The important rule is more valuable than the exact folder names:

```text
presentation -> domain <- data
                     ^
                     |
              platform implementations
```

- Presentation knows domain models and use cases.
- Domain defines behavior and repository contracts without Compose, Room, Android, or iOS types.
- Data implements domain repository contracts using bundled content and local persistence.
- Platform source sets provide implementations where a shared library cannot hide target differences.

In a single Gradle module these are conventions rather than compiler-enforced walls. Tests and code review enforce them initially. Separate modules can enforce them later if needed.

## Where the core models belong

### Alphabet content

- `Letter`, `LetterForm`, and `LetterSound`: alphabet domain models.
- JSON serialization DTOs: alphabet data/content.
- Mapping from DTO to domain model: alphabet data/content.
- `AlphabetRepository` contract: alphabet domain/repository.
- Bundled implementation: alphabet data/repository.

### Lesson content and behavior

- `Lesson`, `LessonStep`, and exercise definitions: lessons domain/model.
- Authored lesson DTOs and resource loader: lessons data/content.
- Queue generation and answer evaluation: lessons domain/engine.
- Screen-specific `LessonUiState`: lessons presentation/session.

### Progress

- Learning stage and progress rules: progress domain.
- Room entities and DAOs: progress data/local or core/database when database-wide.
- Entity/domain mappings: progress data.
- Repository contract: progress domain/repository.
- Room-backed implementation: progress data/repository.

Do not expose Room entities directly to Composables. Database shape, domain meaning, and screen rendering evolve for different reasons.

## Room and bundled content

The app has two data sources with different purposes:

```text
Compose resources / JSON
    immutable authored curriculum
            |
            v
      content repository
            |
            +------> domain/use cases ------> ViewModel ------> UI
            |
Room <------+
    mutable learner progress
```

Room should contain learner state, not copies of every bundled letter and explanation. A repository or use case joins content and progress when a screen needs both.

For KMP, common code can declare the database, entities, DAOs, and repositories. Database construction may require small Android and iOS source-set implementations because the platform supplies the database path and driver configuration.

## React Native to KMP mental map

| React Native concept | KMP/Compose counterpart | Important difference |
|---|---|---|
| React component | `@Composable` function | Recomposition is driven by observed state; avoid performing work during rendering. |
| Props | Function parameters | Keep screens stateless where practical and pass events upward. |
| `useState` | `remember` / `rememberSaveable` | Appropriate for UI-local state, not durable learning progress. |
| Redux/Zustand screen store | ViewModel plus `StateFlow<UiState>` | ViewModel lifetime is usually tied to a navigation destination. |
| Action/reducer flow | Sealed `UiAction` and ViewModel/domain transitions | Reducers are optional; immutable state remains useful. |
| React Navigation | Navigation Compose | Pass stable IDs through routes and load content at the destination. |
| AsyncStorage | DataStore | Best for small preferences, not relational progress queries. |
| SQLite library/ORM | Room KMP | Entities and DAOs are compile-time Kotlin APIs with explicit migrations. |
| Bundled JS/JSON assets | Compose Multiplatform resources | Resource access is shared, but platform audio behavior may still need an abstraction. |
| Native module | Platform interface or `expect`/`actual` | Keep the platform boundary narrow and inject it into shared code. |
| Jest logic tests | `commonTest` with `kotlin.test` | Pure domain tests run without Compose UI or a device. |

## Teaching and implementation order

The structure should grow with the vertical slice instead of being scaffolded empty in advance.

1. Create the standard `composeApp` and `iosApp` shell, then identify what each source set can legally import.
2. Render one hard-coded letter to learn the Composable and state basics; discard the hard-coded path once understood.
3. Load three letters from a bundled resource and map serialized DTOs into domain models.
4. Introduce the repository contract when the UI needs content without knowing how it was loaded.
5. Build one pure Kotlin lesson session and test its state transitions before connecting Compose.
6. Expose the session as immutable `LessonUiState` and actions through a ViewModel.
7. Add Koin Annotations to assemble the real repository and ViewModel, while tests continue to construct classes directly.
8. Add Room when the slice first needs durable progress, and distinguish Room entities from domain models.
9. Add Android and iOS audio behavior behind one common contract.
10. Expand to all letters only after the slice survives process recreation and offline use.

## Tests that define the architecture

The first high-value tests are not screenshot tests. They are behavior tests:

- a lesson queue introduces each intended letter;
- answer choices never omit the correct answer;
- choices contain no duplicate concept IDs;
- an incorrect answer updates progress once;
- resuming a lesson returns to a valid step;
- a final form is associated with its base letter;
- bundled JSON maps all 27 forms successfully;
- a curriculum cannot reference a nonexistent letter or audio asset;
- changing a letter to `SOLID` follows the agreed product rule;
- database migrations preserve lesson and concept progress.

These tests keep the learning behavior independent from Compose and Room details.

## Current decision

Borrow these ideas where they remain useful:

- the `composeApp`/`iosApp` shell;
- shared and platform source sets;
- typed navigation;
- immutable presentation state and sealed actions;
- shared Compose resources;
- narrow platform-specific implementations.

Design Aleph Bet's own approach for:

- domain and data boundaries;
- Room and DataStore;
- bundled curriculum repositories;
- the lesson/session engine;
- progress rules;
- common tests;
- feature ownership beyond a two-screen app.

The starting point is one Gradle application module organized by feature, plain Kotlin learning logic, explicit repository contracts, shared Compose UI, and Koin Annotations for application assembly. This is a hypothesis we will test through the vertical slice, not a permanent commitment to a sample repository's layout.

## Primary references

- [Kotlin Multiplatform project structure](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html)
- [Compose Multiplatform platform-specific behavior](https://kotlinlang.org/docs/multiplatform/compose-platform-specifics.html)
- [Android architecture and unidirectional data flow](https://developer.android.com/topic/architecture/ui-layer)
- [Koin Annotations for Kotlin Multiplatform](https://insert-koin.io/docs/reference/koin-annotations/kmp/)
- [Koin annotation definitions](https://insert-koin.io/docs/reference/koin-annotations/definitions/)
- [Koin module and component scanning](https://insert-koin.io/docs/reference/koin-annotations/modules/)
