# Release and supply-chain verification

**Status:** Planned process. No project release exists. The first candidate is SemVer `v0.1.0-alpha.1` with Python version `0.1.0a1`. [Version policy](COMPATIBILITY.md#product-versioning)

## Prepare the release

- [ ] Select the reviewed commit on the protected release branch. Check that the tag, wheel metadata and changelog describe the same source and version.
- [ ] Run the applicable [test gates](testing/TESTING.md#release-gates). Publish exact validated versions, enabled features, failures, and unrun checks.
- [ ] Resolve and hash-pin direct and transitive dependencies for the supported environments. Scan their versions for disclosed vulnerabilities and assess unresolved findings. Block a release with unresolved material vulnerabilities or unverified dependency sources.
- [ ] Inventory dependencies and bundled files with their versions and licenses. Confirm project attribution, preserve required notices and check for unexpected OBS code or binaries. [Third-party notices](../THIRD_PARTY_NOTICES.md)
- [ ] Update API, setup, privacy, compatibility, support and recovery documents. Verify that private reporting works and document any settings migration and rollback constraints.
- [ ] Build from the reviewed commit in a clean pinned workflow. Limit write permissions to the build steps that need to attest or publish. Build the wheel and source archive, and prepare the offline install set and lock.
- [ ] Generate an SPDX or CycloneDX SBOM for the released contents. Publish a SHA-256 manifest for the wheel, source archive, offline install bundle, lock, and SBOM. Generate provenance attestations for the manifest and deliverables.
- [ ] Verify the staged assets using the same steps users will follow. Compare package metadata, SBOM, notices and pinned versions against the published instructions. Stop publication on any mismatch.

## Verify a download

1. Obtain assets from the selected tag on [GitHub Releases](https://github.com/Nobamb/secure-obs-mcp/releases). Confirm the tag and commit named in the release notes. The release must name its trusted GitHub workflow and exact asset names.
2. Verify the SHA-256 manifest against a provenance attestation from the designated release workflow. Substitute the full source commit from the expected release tag for `<commit-sha>`. Example for the proposed tag-triggered GitHub workflow:

```powershell
gh attestation verify .\SHA256SUMS.txt --repo Nobamb/secure-obs-mcp --signer-workflow Nobamb/secure-obs-mcp/.github/workflows/release.yml --source-ref refs/tags/v0.1.0-alpha.1 --source-digest '<commit-sha>'
Get-FileHash -Algorithm SHA256 -LiteralPath .\secure_obs_mcp-0.1.0a1-py3-none-any.whl
```

3. Compare the computed digest for every downloaded artifact with the matching manifest entry. Check that the locked package versions and SBOM describe the same build. A hash obtained from an unverified manifest alone does not establish the publisher.
4. Stop if an asset, expected attestation, checksum, identity or commit fails verification. If the repository later adopts signatures instead of attestations, the release policy must specify a verified public key and signer before that method is accepted. [GitHub provenance verification](https://docs.github.com/en/actions/how-tos/secure-your-work/use-artifact-attestations/use-artifact-attestations)

## Support, updates and rollback

No released line is supported today. At first publication, record each supported line and end date in the release notes and [security policy](../SECURITY.md). Give notice of planned retirement and explain upgrade paths when possible. Publish any incompatible tool or schema change with a migration and rollback note.

### Update and rollback

1. Finish any owned recording and shut down the active MCP server. Back up the version-matched local policy and store it securely. Leave existing recordings in the user-managed capture root.
2. Verify the new artifacts and install into a separate environment. Run the documented migration locally if the schema changed. Recheck settings, credentials, device scope and target identity before registration.
3. Switch the client to the verified interpreter, then test status, an approved PNG and a short silent recording. Check the result and rollback if the smoke test fails.
4. For rollback, stop the new server cleanly, restore the older verified environment and its matching settings, and rerun the smoke test. Do not restore a version known to have an applicable unmitigated security issue.

Updates and rollbacks are user initiated. The runtime performs no background installation or update.
