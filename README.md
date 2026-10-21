# ESPHome GitHub Actions

A collection of reusable GitHub Actions and workflows for ESPHome projects.

## Reusable workflows

| Workflow | Description | Usage |
| --- | --- | --- |
| [`build-device`](./.github/workflows/build-device.yml) | Builds the input device and prepares release files as an artifact. | `uses: robin-thoni/esphome-actions/.github/workflows/build-device.yml@v1` |
| [`release-device`](./.github/workflows/release-device.yml) | Builds the input device, then uploads its artifact and manifest to a configured base URL. | `uses: robin-thoni/esphome-actions/.github/workflows/release-device.yml@v1` |

Call a reusable workflow at the job level and provide the device name. Its
configuration must be named `<device>.yaml` in the caller's repository. For a
release, pass the required repository secrets through:

```yaml
on:
  workflow_dispatch:

jobs:
  release:
    uses: robin-thoni/esphome-actions/.github/workflows/release-device.yml@v1
    with:
      device: living-room
    secrets: inherit
```

Replace `v1` with a published tag or other ref. Git references and releases
apply to the whole repository, so coordinate compatibility when using shared
version refs. The device version comes from the generated build manifest. The
release workflow forwards the `device` input to the build, passes
`ESPHOME_SECRETS` to it, and uses
`UPLOAD_BASE_URL`, `UPLOAD_USERNAME`, and `UPLOAD_PASSWORD` secrets. It uploads
release files and `.md5`, `.sha1`, and `.sha256` checksum sidecars to both
`<base-url>/<device>/<version>/` and `<base-url>/<device>/latest/`. The manifest
is also uploaded to `<base-url>/<device>/manifest.json`. Aggregate
`checksums.md5`, `checksums.sha1`, and `checksums.sha256` files list checksums
for the original release files, excluding checksum files themselves.
Both workflows accept the optional `esphome_version` input; if omitted, the
build workflow uses ESPHome `2026.9.0`. Set `esphome_version` under `with` to
override it.

## Actions

Actions live in their own directories under [`actions/`](./actions/), each
with an `action.yml` manifest at its root. Keep the action's implementation,
tests, and README alongside that manifest.

Use the action type that fits the implementation:

- **Composite** for steps implemented with shell commands or other actions.
- **JavaScript** for Node.js-based actions.
- **Docker** when the action needs a custom container environment.

For example, a composite action at `actions/example/action.yml` can be called
from a workflow step:

```yaml
steps:
  - uses: robin-thoni/esphome-actions/actions/example@v1
    with:
      example-input: value
```

Pin third-party actions used by an implementation to full commit SHAs where
practical.

### Available actions

| Action | Description | Usage |
| --- | --- | --- |
| _No actions have been added yet._ | | |

When adding an action, add it to this table with a short description and a
copyable `uses:` example.

## Adding an action

1. Create `actions/<action-name>/action.yml` with a descriptive name,
   description, inputs/outputs as needed, and the appropriate `runs` metadata.
2. Add an `actions/<action-name>/README.md` documenting purpose, inputs,
   outputs, permissions, and a complete workflow usage example.
3. Add or update tests and list the action in the catalog above.
4. Publish a repository tag after the action is ready to consume.
