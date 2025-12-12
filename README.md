# NoHedging

Reduce unnecessary defensive programming (hedging) that makes code unstable.

**Install**

```sh
pipx install cursorcult
cursorcult link NoHedging
```

Rule file format reference: https://cursor.com/docs/context/rules#rulemd-file-format

**When to use**

- You’re debugging a codebase with lots of silent fallbacks.
- You want data models to reflect reality instead of “best effort” guesses.
- You prefer explicit errors over “safe” but wrong values.

**What it enforces**

- Never return hacked defaults to mask missing data.
- Required values are required: fail or model absence explicitly.
- Prefer structured data models (dataclasses / Pydantic in Python).

**Credits**

- Developed by Will Wieselquist. Anyone can use it.
