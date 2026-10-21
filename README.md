# CLI config transaction validation artifact

This branch contains only a workflow, a five-file patch and this explanation.
It is an isolated fork validation artifact for BasedHardware/omi issue #22972;
it is not the final contribution branch and must not be included in that PR.

- Public upstream base: `d6b734be95c367a1c5750d342ab2bee094d47265`.
- Patch SHA-256: `76ce279408900ba2e716500cf8749b5f0bb006bdeed2892c61a9fdc6961d55f1`.
- Source patch files: four CLI runtime modules and `test_config_transactions.py`.
- Matrix: Linux Python 3.10, Linux Python 3.12, Windows Python 3.12.
- The SDK checkout includes the existing read-only OpenAPI contract fixture; its hash is verified.
- Every matrix job checks out the fixed upstream source, verifies and applies
  the same patch, and runs the complete SDK pytest suite with synthetic config.
- Linux 3.12 additionally builds a wheel and runs the repository's original
  clean-install smoke script. No publish/release/deployment step is present.
- The workflow has contents-read permission and receives no application secrets.

This code was prepared with OpenAI Codex assistance. No independent human
technical review is claimed. Successful jobs demonstrate this applied patch
on the stated source snapshot, not upstream PR exact-head CI or payment approval.
The final PR must retain truthful test results and any remaining limitations.

The separate Ubuntu preflight job checks out the full public source and complete
commit history, applies the same patch, and creates an ephemeral local commit
using the contributor's public Git identity. It generates invariant citations
with the repository's own --suggest command and runs the formal make preflight.
It supplies the PR body outside the checkout and fixes origin/main to the base
for comparison. Any SKIP notices remain limitations, not executed-check passes.
No temporary source commit is pushed, and no maintainer CI requirement is bypassed.
