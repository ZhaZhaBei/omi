Fixes #22972.

Two CLI logins could both report success while the later whole-file save erased the other profile's credentials. Hold a shared cross-process lock around loading the latest configuration, applying the mutation, and saving it. All eight credential, profile, setting, and local-configuration write sites use this transaction.

The lock uses a retained sibling file so atomic replacement of the TOML file does not change the lock's identity. The CLI README documents that the credential-free lock file remains beside the configuration and should be left in place. Prompts and network requests stay outside the critical section. Configuration URL normalization reuses the upstream validated_url helper before entering the write transaction, while preserving request-time validation. Existing corruption refusal, private credential-file permissions, forward-compatible fields, and atomic replacement are retained.

Add 15 regression cases using synthetic credentials and isolated temporary configuration. The process-based regression tests cover two profile logins, each mutation family, process termination, and a same-process retry after a write error. Against the verified original module, nine cases fail on lost acknowledged updates; all 15 pass with the fix.

## Product invariants affected

none

Failure-Class: FC-cache-merge-write-race

## Validation

- macOS / Python 3.14 on the current merged branch: 947 tests passed, one Windows-only DACL test skipped, zero failures/errors (JUnit: 948 cases). This includes all 15 transaction regressions and all nine upstream malformed-API-base cases. Tests used isolated synthetic configurations, mocked HTTP, and localhost test sockets.
- Black check and strict mypy passed. Type checking used declared-compatible Click 8.1.8 / Typer 0.23.1 plus tomli for the configured Python 3.10 target; the latest dependency combination has the same seven mypy errors on the unchanged base. Dependency declarations are unchanged.
- Wheel build and the repository's clean-install script passed locally.
- Prior fork validation of the original transaction patch: complete CLI suite passed on [Linux Python 3.10](https://github.com/ZhaZhaBei/omi/actions/runs/39573563256/job/119133430673) and [Linux Python 3.12](https://github.com/ZhaZhaBei/omi/actions/runs/39573563256/job/119133430747); the latter also passed wheel build and clean-install checks.
- Prior fork validation of the original transaction patch on [Windows Python 3.12](https://github.com/ZhaZhaBei/omi/actions/runs/39574392595/job/119136182611): 935 passed, four skipped, zero failures (JUnit: 939 tests).
- Current merge commit `7a1bacd`: `make preflight` passed all 13 checks with full Git history and no skips, including the 90-day failure-class guard ratchet.

The cross-platform fork jobs above validate the original transaction patch; they were not rerun for the later upstream URL-validation merge. The current merged branch was revalidated locally as listed above. The separate fork CI artifact checks out base `d6b734be95c367a1c5750d342ab2bee094d47265`, verifies the five-file patch and resulting source hashes, and tests the applied snapshot. It is supporting fork validation, not an upstream PR head check. The CI-only workflow is not part of this PR.

AI assistance: implementation, tests, and review used OpenAI Codex. No independent human technical review is claimed.

Could you confirm whether this contribution qualifies for the project's paid bounty program and whether any claim steps remain?


<!-- This is an auto-generated description by cubic. -->
<a href="https://cubic.dev/pr/BasedHardware/omi/pull/23012?utm_source=github" target="_blank" rel="noopener noreferrer" data-no-image-dialog="true"><picture><source media="(prefers-color-scheme: dark)" srcset="https://www.cubic.dev/buttons/review-in-cubic-dark.svg"><source media="(prefers-color-scheme: light)" srcset="https://www.cubic.dev/buttons/review-in-cubic-light.svg"><img alt="View guided diff" src="https://www.cubic.dev/buttons/review-in-cubic-light.svg"></picture></a>
<!-- End of auto-generated description by cubic. -->

### Windows lock review follow-up

Replaced the Windows `time.sleep` lock polling loop with a blocking `LockFileEx` byte-range lock and paired `UnlockFileEx`. The range remains compatible with older clients using `msvcrt` byte locking. Native tests cover cross-process contention, waiting longer than the CRT retry limit, legacy interoperability, process exit release and error propagation. Because this is a synchronous kernel wait, Ctrl-C can be delayed until the lock becomes available.

Validation on this exact reviewed patch:
- Windows Python 3.12: 21 targeted tests passed; full CLI suite 950 passed, 4 skipped.
- Linux Python 3.12: 19 targeted tests passed, 2 Windows-only skipped; full CLI suite 951 passed, 3 skipped; Black and wheel build passed.
- The default Linux dependency set has 7 mypy errors on both the unmodified base and this patch; their normalized diagnostics match. With Click 8.1.8 and tomli 2.5.0, mypy passed 29 files.

Validation runs: https://github.com/ZhaZhaBei/omi/actions/runs/40925889620 and https://github.com/ZhaZhaBei/omi/actions/runs/40928044968.
