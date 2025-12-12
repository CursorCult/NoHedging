---
description: "Avoid hedged defaults; require explicit, validated data"
alwaysApply: true
---

# NoHedging Rule

NoHedging is a correctness‑first, language‑agnostic rule: do not hedge results with hacked defaults.

Every time you “return something safe” instead of the real, required value, you hide bugs and make failures harder to diagnose. Be precise and confident. Your data structures either contain the values you need, or those values were never needed.

## Guidelines

- Never invent placeholder or fallback defaults just to satisfy a return type.
- If a default is truly required, think hard about where it belongs and define it in exactly one place in the codebase (typically at a boundary or constructor), not scattered across call sites.
- If required data is missing, fail loudly (raise, return an explicit error, or make absence part of the type).
- Prefer explicit types over ambiguous containers.
- Avoid “just in case” optional fields; model reality.
- Subtle bugs happen when well‑meaning hedges prevent failures; treat missing‑data crashes as valuable signals, not inconveniences to paper over.

## Addendum: language‑specific notes

### Python

If you’re in Python, use structured, validated types:

- Use `@dataclass` for simple immutable/typed data containers.
- Use Pydantic models when you need validation, parsing, or boundary checks.
- Avoid default values for required fields; make callers provide them.
- Under these rules, never do `x.get("hello", "")` (or any `dict.get(key, default)` that invents a value). Access the key directly and handle absence explicitly.

Examples:

Not allowed: hedging with a hacked default.

```py
def user_age(user) -> int:
    return user.age or 0  # hides missing data
```

Preferred: make absence explicit or fail.

```py
def user_age(user) -> int:
    if user.age is None:
        raise ValueError("user.age is required")
    return user.age
```

Or encode absence in the type.

```py
from dataclasses import dataclass
from typing import Optional

@dataclass(frozen=True)
class User:
    age: Optional[int]
```

### JavaScript / TypeScript

- Don’t use `||` or `??` to invent “safe” defaults for required values. If absence is possible, make it explicit in the type (`T | undefined`) and handle it intentionally.
- Validate inputs at boundaries (API, file I/O, env). If a field is required, fail fast rather than substituting `""`, `0`, `{}`, or `[]`.
- Prefer explicit error returns (`throw`, `Result`-style objects) over silent fallbacks.

### C++

- Don’t return sentinel defaults (empty strings, zeroed structs) to hide missing data.
- Model optionality with `std::optional` and failure with `std::expected`/exceptions; let callers handle absence explicitly.
- Use strong structs/types to encode required invariants; assert or throw when invariants aren’t met.
