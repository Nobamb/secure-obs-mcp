# GitHub repository security checklist

Initial setup plan for [issue #14](https://github.com/Nobamb/secure-obs-mcp/issues/14). Apply repository settings separately with administrator authorization. On 2026-11-27, the repository metadata identified a public repository with `main` as the default branch. The Private Vulnerability Reporting API returned `enabled: false`. Other live controls are pending administrator verification.

## Before opening external contributions

| Control | Administrator actions | How to verify |
|---|---|---|
| Private vulnerability reporting | Enable the feature in repository code security settings. Assign notifications to maintainers. | Check the enabled state and confirm an outside account can open the private form. Update [SECURITY.md](../SECURITY.md) after verification. [GitHub guide](https://docs.github.com/en/code-security/how-tos/report-and-fix-vulnerabilities/configure-vulnerability-reporting/configure-for-a-repository) |
| `main` branch rules | Require PR review and applicable status checks, block deletion and force push, and minimize bypass permissions. Add required checks after they exist. | Inspect the active rule and confirm a safe test PR cannot merge without its required review and checks. [Rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets) |
| Secret scanning and push protection | Confirm availability and enable the repository controls. Assign alert owners. | Review the enabled state and alert routing. Use an approved synthetic test token if a live test is needed. [Push protection](https://docs.github.com/en/code-security/concepts/secret-security/push-protection) |
| Local ignore rules | Keep production settings and capture outputs outside the working tree. Use [local ignore rules](../.gitignore) for common generated files. | Run `git check-ignore .env config.local.toml screenshots/demo.png recordings/demo.mkv logs/demo.log` and review tracked files. Ignore rules do not remove data already committed. |

## Before merging and releasing code

| Control | Administrator actions | How to verify |
|---|---|---|
| Dependabot | Enable dependency graph, alerts, and security updates where supported. Add an ecosystem configuration after the lock and workflows exist. | Inspect the dependency inventory and alert state, and verify the update workflow detects the supported lock or manifest. [Dependabot security updates](https://docs.github.com/en/code-security/how-tos/secure-your-supply-chain/secure-your-dependencies/configure-security-updates) |
| Code scanning | Check CodeQL availability for the public repository and Python. If unsuitable, document the chosen scanner and why. | Run a clean scan and review the results. Require the appropriate status after the job is stable. [CodeQL](https://docs.github.com/en/code-security/concepts/code-scanning/codeql/codeql-code-scanning) |
| Actions permissions and forks | Default workflow permissions to read-only. Assign explicit job permissions. Pin external actions to full commit SHAs. Keep untrusted PR code out of privileged workflows and jobs with secrets. | Inspect workflow YAML and run a fork PR test with no secrets or write access. Review effective token permissions in the job summary. [Actions security](https://docs.github.com/en/actions/reference/security/secure-use) |
| Release identities and provenance | Restrict release permissions, protect release tags or environments, and prefer scoped short-lived credentials. Grant publication and attestation permissions only to the reviewed release workflow. | Verify the required actor, commit and tag using a staged release and the [release verification procedure](../docs/RELEASE.md#verify-a-download). |

For every control record date, responsible administrator, setting and tested result. Use **enabled**, **disabled**, **unavailable**, or **unverified** and record a fallback for unavailable controls. If a real secret is exposed, revoke or rotate it immediately, assess the scope and coordinate any history cleanup separately.
