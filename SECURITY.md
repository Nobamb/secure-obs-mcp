# Security policy

## Supported releases

| Version | Status | Security fixes |
|---|---|---|
| Released versions | None published | No released builds to patch |
| Development branch | Design stage | Fixes are reviewed on the development branch |
| 0.1.0-alpha.1 | Proposed first prerelease | Support scope will be stated in its release notes |

Once releases exist, supported lines will be listed here with their end dates. A security fix will be prepared for each affected supported line where applicable. An out-of-support release may require upgrading to a supported version. [Release policy](docs/RELEASE.md)

## Report a vulnerability privately

GitHub Private Vulnerability Reporting was disabled for this repository when checked on 2026-11-27. No alternative private reporting address has been verified. Keep vulnerability details private until the maintainers publish or confirm a private channel. If you already have a verified private route to a maintainer, use it. The repository administrator tracks enablement and an outside-account check in the [repository security checklist](init/REPOSITORY_SECURITY.md#before-opening-external-contributions).

Once enabled and verified, the private form will be available through the repository security page. [GitHub reporting instructions](https://docs.github.com/en/code-security/how-tos/report-and-fix-vulnerabilities/report-privately)

In the private report, include the affected version or commit, OBS/client versions, the affected security boundary, expected and observed behavior, and a minimal example using synthetic data. Remove credentials, original captures, complete logs, personal window names, and exact local paths before sharing. Keep suspected vulnerabilities out of public issues and PRs.

## Triage and coordinated disclosure

1. The maintainer aims to acknowledge a report within seven calendar days and share an initial triage update within 14 days of receipt. These are best-effort targets.
2. The maintainer and reporter confirm scope privately, identify affected releases, and agree on how to share any further evidence.
3. The maintainer prepares regression coverage, a fix or mitigation, and a private advisory. If a dependency is affected, coordinate with its maintainer.
4. Agree on a disclosure date with the reporter based on patch availability and user exposure. Publish affected versions, safe workarounds, and upgrade instructions. Credit reporters with their consent.

[Threat model](docs/security/THREAT_MODEL.md) · [Privacy and safe diagnostics](docs/PRIVACY.md)
