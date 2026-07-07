# Coding Conventions

This project follows two PLCopen guideline documents as its baseline:

- **[PLCopen Coding Guidelines V1.0 (2016)](https://www.plcopen.org/guidelines/software-construction-guidelines/)** — naming, comments, and general coding practice for IEC 61131-3.
- **[PLCopen Guidelines for Object-Oriented Programming (2021)](https://www.plcopen.org/guidelines/software-construction-guidelines/)** — object-oriented patterns enabled by the 3rd edition of IEC 61131-3.

The 2016 Coding Guidelines intentionally leave several conventions for each project to define (type prefixes, casing, name length, etc.) — those choices are documented here.

## Naming

| Element                | Prefix / Style      | Example                |
|------------------------|---------------------|------------------------|
| Interface              | `I` + PascalCase    | `ITrafficLight`        |
| Function block         | `FB_` + PascalCase  | `FB_TrafficLight`      |
| Test suite (FB)        | `FB_` + `_Test` suffix | `FB_TrafficLight_Test` |
| Program                | `PRG_` + PascalCase | `PRG_TestRunner`       |
| Enumeration            | `E_` + PascalCase   | `E_LightState`         |
| Enum members           | PascalCase          | `Red`, `RedAmber`, `Green`, `Amber` |
| Struct                 | `ST_` + PascalCase  | `ST_PhaseConfig`       |
| Global variable list   | `GVL_` + PascalCase | `GVL_Config`           |
| Method                 | PascalCase          | `RequestGreen()`       |
| Property               | PascalCase          | `CurrentState`         |
| Local variable         | camelCase           | `actualState`          |
| FB instance variable   | `fb` + PascalCase   | `fbIntersection`       |
| Private backing field  | `_` + camelCase     | `_state`               |
| Constant               | UPPER_SNAKE_CASE    | `MIN_GREEN_TIME_MS`    |

Rationale: prefixes make the *kind* of thing visible at the call site — you can tell an interface from an FB from an enum without jumping to the declaration. This is a widespread TwinCAT convention and satisfies Coding Guidelines V1.0 §3.2.8 ("Define name prefixes for user defined types") and §3.1.2 ("Define type prefixes for Variables").

Names are English, descriptive, and avoid abbreviation unless the term is a well-known domain acronym.

## Comments

- **Intention over restatement** — comments explain *why*, not *what the code obviously does* (Coding Guidelines V1.0 §4.1).
- **All non-trivial elements are commented** — interfaces, function blocks, methods, and non-obvious state (§4.2).
- **Single-line style** (`//`) preferred over block comments; no nested comments (§4.3, §4.5).
- **Language: English only**, throughout the codebase and commit messages (§4.6).

## Coding Practice

Rules from Coding Guidelines V1.0 §5 that are actively followed and visible in this codebase:

- All variables are initialized at declaration where a safe default exists (§5.3). Enums default to the safe state — e.g. `E_LightState := Red`.
- Type conversions are explicit — e.g. `TO_INT(E_LightState.Red)` rather than implicit coercion (§5.26).
- No unused variables in declarations (§5.25).
- POUs have a single logical exit; early `RETURN` is avoided (§5.15).
- Global variables are limited; state lives inside the FBs that own it (§5.19).
- Physical outputs would be written once per PLC cycle in the application layer (§5.13) — enforced by the adapter design (see architecture doc).

## Object-Oriented Design

The Coding Guidelines V1.0 (2016) predate widespread OOP adoption in IEC 61131-3, so its rules focus on procedural function blocks. PLCopen addressed this gap separately in the 2021 **Guidelines for Object-Oriented Programming**, which cover the OOP features introduced by the 3rd edition of IEC 61131-3. This project applies OOP under that newer guideline:

- **Interfaces as ports** — `ITrafficLight`, `ISensor`, and `IActuator` define contracts that decouple the domain from I/O and enable unit testing without hardware.
- **Interface-typed composition** — the `Intersection` coordinator holds lights as `ITrafficLight[]` rather than the concrete FB type, depending on the contract rather than the implementation.
- **Encapsulated state via properties** — internal state (e.g. `_state`) is exposed through properties (`CurrentState`) rather than public variables.

Together, the two documents cover the full design: V1.0 for how the code *reads* (naming, comments, practice), and the 2021 OOP guideline for how it's *structured* (interfaces, composition, testability).

## Git Commit Messages

Commit messages follow the [Conventional Commits](https://www.conventionalcommits.org/) convention:

```
type: short summary in imperative mood

optional body explaining why, if not obvious
```

Types used in this repo: `feat`, `fix`, `test`, `refactor`, `docs`, `chore`.
