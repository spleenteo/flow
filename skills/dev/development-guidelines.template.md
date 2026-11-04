# Development guidelines

Rules the plans and the code must follow. `/flow:dev` passes this file to every plan, review and execution. Keep it short and checkable: a rule nobody can verify isn't a rule.

<!-- Prose about naming, placement and how tests are written goes in files of the `guidelines/` folder next to this file, each opening with a frontmatter of `description` and, optionally, `applies` (globs, or `always`). This file keeps its four sections. -->

## References

<!-- External guides and skills this project follows (style guides, framework skills, internal handbooks). One line each, with where to find it. A document Flow's agents must read goes in `sources:` of `.flow.yml`, with when it applies. -->

## Tests

Level: <none | essential | full>
Sensitive areas: <path or concept>, …

<!-- Framework and command only. How tests are written goes in a `guidelines/` file. -->

## Gate

<!-- What must be green before a slice closes. Exact commands, in order. -->

```bash
npm test
npm run typecheck
npm run build
```

## Deviations

<!-- Where this project departs from its references, and why. -->
