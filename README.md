# Windows supplement for the CLI config transaction validation

This CI-only branch contains a workflow, the unchanged reviewed five-file patch,
and this explanation. It is not the final contribution branch.

- Fixed public source: BasedHardware/omi commit d6b734be95c367a1c5750d342ab2bee094d47265.
- Patch SHA-256: 76ce279408900ba2e716500cf8749b5f0bb006bdeed2892c61a9fdc6961d55f1.
- This version runs only Windows Python 3.12. It restores the patch's original
  bytes from its Git blob before checking its hash, avoiding checkout CRLF conversion.
- The SDK checkout includes the pinned read-only OpenAPI contract fixture.
- The complete CLI suite uses synthetic configurations and prints JUnit totals.
- Linux Python 3.10/3.12 and full repository preflight are not repeated here;
  their separate results are retained in run 39573563256:
  https://github.com/ZhaZhaBei/omi/actions/runs/39573563256
- That run's Windows job stopped before applying the patch or running tests;
  its failure was a validation-artifact hash mismatch, not a runtime test verdict.
- The workflow has contents-read permission, receives no application secrets,
  and has no deployment, package publishing or release step.

The code was prepared with OpenAI Codex assistance. No independent human
technical review is claimed. A successful job demonstrates the same applied
patch on this fixed source snapshot, not upstream PR exact-head CI or payment.
The earlier invalid-workflow and Windows-artifact failures remain recorded.
