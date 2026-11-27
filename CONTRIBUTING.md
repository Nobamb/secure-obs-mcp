# Contributing

Use the [bug report](.github/ISSUE_TEMPLATE/bug_report.md) or [feature request](.github/ISSUE_TEMPLATE/feature_request.md) template for ordinary issues. Use the [security policy](SECURITY.md) for possible vulnerabilities. Keep public submissions free of credentials, original recordings and screenshots, raw logs and personal paths.

## Work on a change

1. Read the [development specification](init/development-spec/README.md) and relevant [official documentation](docs/README.md). Discuss behavior changes in an issue before implementing them.
2. Create a topic branch and keep the patch focused. The repository currently contains design and documentation. When implementation and a dependency lock land, set up the tested Python version in a dedicated Windows virtual environment and follow the checked-in hash-verified test setup.
3. Match surrounding code style. Use explicit types and validation at MCP, RPC, OBS metadata and storage boundaries. Keep only the required RPCs and fields in the adapter. Preserve least privilege, local consent, and metadata-only responses.
4. Run unit and fake-OBS integration tests before testing an actual OBS profile. Use a synthetic target and follow the [test plan](docs/testing/TESTING.md). Document the exact command, result and any unrun cases in the PR.
5. Update the reference, install or compatibility document when changing its contract. Include a regression test for changes to policy or state transitions. Request separate security review for changes affecting credentials, RPC allowlists, storage, local approvals, client isolation, network or release provenance.

## Open a pull request

In the [pull request template](.github/PULL_REQUEST_TEMPLATE.md), link the related issue and explain the user-facing change. Describe the verification environment, checked scenarios, pending tests, and security implications. Reviewers check the relevant acceptance gates and any changes to assumptions or exposed data before merge.

For documentation-only changes, verify listed paths and links, compare any version claims with primary sources, and confirm the text distinguishes design from observed behavior. [Documentation structure](init/spec/Secure_OBS_MCP_Documentation_Structure.md)
