Fixes #18721.

The CLI renderer currently raises `TypeError` for dates, filesystem paths, UUIDs and sets in JSON output and nested mapping cells. This change adds those conversions, including pure paths, and keeps set members as JSON values rather than turning numbers, booleans or tuples into display strings. Sets use a stable serialized-value sort key; pretty cells keep literal text and deterministic display order.

The report and original scope come from @I-Am-Finite. PR #22242 also addresses common result types and remains open. This alternative specifically preserves native JSON set member types; running the new tests against that PR's actual output-module patch yields 14 failures / 11 passes, while the unchanged main renderer yields 23 failures / 2 passes. No claim of assignment or acceptance is implied.

Validation: 25 new behavioral regression cases pass, covering public `Renderer.emit`, nested Pydantic results, mapping/list cells, unsupported types, and three Python hash seeds. The full CLI suite passes locally with Python3.14 / Click8.5 / Typer0.25.1: 992 passed,1 Windows-only skip. Black passes; wheel and sdist build, twine checks and both clean installs pass. Strict mypy checks all30 source files successfully with allowed Click8.1.8 / Typer0.23.1 dependencies. With Click8.5, the current base and this change have the same6 pre-existing ClassVar errors in errors.py; no dependency declarations were changed. Local preflight runs13 checks, with its90-day history ratchet explicitly skipped on the shallow clone; full-history forkCI will be verified before opening this PR. No live Omi services, credentials or production deployment were used.

Product-Invariants: none
This only changes CLI display serialization; it does not change memory authority, cross-platform wire schemas, authentication or persistence.

Failure-Class: none
The existing serializer-dialect and order-bearing-payload registry classes concern different producer/consumer contracts; this fixes unsupported Python result values in the CLI's shared renderer.

AI assistance: implemented and validated with OpenAI Codex. Tests exercise production rendering, not source-text assertions. Human maintainer review is still required for upstream acceptance.
