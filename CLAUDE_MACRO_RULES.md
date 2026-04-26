# Claude Macro Rules

## Purpose

This document defines macro usage rules Claude must follow for this repository.

Macros are allowed only for short repeated code patterns.
They must not hide business logic or replace normal readable FORM logic.

## Macro Rule

- Define macros in `TOP`.
- Use macros only for short, highly repetitive patterns.

Common allowed macro purposes from the repository:

- initialization
- popup confirmation
- range build
- toolbar button append
- message text build
- date calculation
- conversion function wrap

Do not hide complex business logic inside macros.
