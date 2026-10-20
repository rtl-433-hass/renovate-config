# Shared Renovate configuration

Dependency update policy for rtl-433-hass repositories.

## Usage

For a project using the shared policy, add `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>rtl-433-hass/renovate-config"]
}
```

For Python projects that also need pre-commit hook updates, use
`github>rtl-433-hass/renovate-config:python` instead.

References follow this repository's default branch. A preset change therefore
applies to consumers on their next Renovate run. A Git tag can be appended to a
reference when a consumer needs a fixed version.

## Policy

The default preset extends `config:recommended` and pins GitHub Actions to digests.
It enables automerge for dependency updates, including major versions, after a
three-day cooldown. Release timestamps are required; strict internal checks hold
updates until the cooldown is satisfied. Automerge still depends on the consuming
repository's checks and branch protection.

GitHub Actions digest and pin updates disable automerge and require dependency
dashboard approval. Approval allows Renovate to create the update; merging remains
manual. These rules preserve the existing code repositories' behavior.

The Python preset adds Renovate's built-in pre-commit manager. The default preset
does not enable that manager, allowing repositories with custom hook handling to
retain their existing setup.

## Migration

- `pyrtl_433` and `mutmut_ratchet`: replace their configuration with the Python
  preset. Their existing configurations are identical.
- `rtl_433`: extend the Python preset, retaining local custom managers, ignore paths,
  dependency groups, Home Assistant/Python constraints, screenshot harness rules,
  and the first-party cooldown exemption.
- `rtl_433-hass-addons`: extend the default preset, retaining
  `:preserveSemverRanges`, custom managers, image grouping, and semantic commit rule.
- `rtl-433-hass.github.io`: currently uses only `config:recommended`. Adopting the
  default preset would introduce automerge, cooldowns, and action digest pinning;
  that is a separate policy decision.

Remove duplicated global settings and GitHub Actions rules when adopting a preset.
The old action-release rules repeated global defaults (including the default
`automergeType: pr`); the add-ons versions contained no settings at all. The old
major-update descriptions said manual review while the effective setting enabled
automerge. This extraction preserves that setting and corrects the documentation.

Repository settings and package rules apply after inherited preset settings.
Keep the `rtl_433` first-party cooldown exemption local and last so its
`minimumReleaseAge: null` continues to override cooldown rules.

Consumer migrations are separate from publishing these presets.

## Validation

Pull requests and pushes to `main` run the official Renovate config validator in
strict mode against every root JSON file, including all presets and this
repository's own Renovate configuration. Run the same check locally:

```sh
npx --yes --package=renovate@44.171.8 renovate-config-validator --strict --no-global *.json
```

The version is pinned in the workflow and managed by Renovate. Validation checks
the files in the checkout; it does not resolve remote presets, which would read
published files rather than the proposed changes in a pull request.

See [Renovate's preset documentation](https://docs.renovatebot.com/config-presets/).
