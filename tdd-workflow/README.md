# TDD Workflow

Atomic red-green-refactor loop for a single unit of work — requirement first, code as the output of tests.

## What It Does

- **Behavior list first** — derives testable behaviors from the requirement (or from the project plan's acceptance criteria and behavior test IDs when used inside the project pipeline)
- **Red** — comprehensive failing tests (positive, negative, boundary, error) + stub; verifies they fail for the right reason
- **Green** — minimal implementation, iterating error → boundary → positive
- **Refactor** — cleanup under green tests, explicitly part of the loop
- **Per-unit guidance** — pure functions, class public methods (public interface only), API endpoints (contract + authz tests), and small requirements (decompose, bottom-up)

## When to Use

New pure logic, new API endpoints, public class methods, bug fixes (regression test first), refactoring untested code, or any scoped requirement. Skip it for UI layout, throwaway spikes, and glue code.

## Usage

> "Write a calculateDiscount function using TDD"

> "TDD the POST /api/v1/sessions endpoint"

> "Fix this bug — regression test first"

Inside the project delivery pipeline, project-implementation's worker agents apply this loop per coding task.
