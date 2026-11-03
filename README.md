# Setup `chainctl`

[![Test](https://github.com/chainguard-dev/setup-chainctl/actions/workflows/test.yaml/badge.svg)](https://github.com/chainguard-dev/setup-chainctl/actions/workflows/test.yaml)

This action installs the `chainctl` binary for a particular environment
and authenticates with it using GitHub OIDC identity tokens. By default it
installs the latest release; pass `version` to pin a specific one.

When `identity` is set, the action also:

- authenticates against the Chainguard Libraries host (`libraries-host`) and
  the APK host (`apk-host`), and
- registers `chainctl` as a Docker credential helper, so `docker pull
  cgr.dev/...` works in later steps.

## Usage

```yaml
permissions:
  id-token: write # Required to mint the OIDC token used to authenticate

steps:
- uses: chainguard-dev/setup-chainctl@[VERSION]
  with:
    # The ID of the identity this workflow should assume when talking to
    # Chainguard APIs.
    identity: "deadbeef/badf00d"
```

Pin the action to a full commit SHA rather than a tag, for example
`chainguard-dev/setup-chainctl@<sha> # vX.Y.Z`.

See [Authenticating to Chainguard Registry](https://www.chainguard.dev/docs/chainguard/containers/registry/authenticating#authenticating-with-github-actions)
for how to create an identity that GitHub Actions can assume.

## Inputs

| Name | Default | Description |
| --- | --- | --- |
| `identity` | | ID of the Chainguard identity to assume. When empty, `chainctl` is installed but not authenticated. |
| `version` | `latest` | `chainctl` version to install, such as `0.2.313` or `v0.2.313`. |
| `environment` | `enforce.dev` | Environment to download `chainctl` from (`https://dl.<environment>/`). Must be a bare domain. |
| `env-auth` | `false` | When `true`, exports `HTTP_AUTH` with an APK token for `apk-host` to later steps. The token is masked in logs. |
| `apk-host` | `apk.cgr.dev` | Host to authenticate against for APK access. Must be a bare `host[:port]`. |
| `libraries-host` | `libraries.cgr.dev` | Host to authenticate against for Chainguard Libraries. Must be a bare `host[:port]`. |
| `setup-python-keyring` | `false` | When `true`, installs the `keyrings-chainguard-libraries` pip package. Requires `python` on the runner. |
| `verbosity` | `0` | `chainctl` log verbosity, `0` (off) to `5`. |
| `config-path` | | Path to a `chainctl` config file whose values override the built-in defaults. |
| `retry-all-errors` | `true` | Retry the download on any error, not just transient ones. |
| `invite-code` | | **Deprecated**, use `identity`. Invite code used to register the workflow on first run. |
| `audience` | `issuer.<environment>` | **Deprecated**, use `identity`. OIDC token audience, used only with `invite-code`. |

Invalid values for `environment`, `version`, `verbosity`, `identity`,
`apk-host` and `libraries-host` fail the action before anything is downloaded.

## Scenarios

### Pull images from cgr.dev

```yaml
permissions:
  contents: read
  id-token: write

steps:
- uses: chainguard-dev/setup-chainctl@[VERSION]
  with:
    identity: "deadbeef/badf00d"
- run: docker pull cgr.dev/<org>/<image>:latest
```

### Pin `chainctl` and export APK credentials

```yaml
- uses: chainguard-dev/setup-chainctl@[VERSION]
  with:
    identity: "deadbeef/badf00d"
    version: "0.2.313"
    env-auth: "true"
# Later steps see HTTP_AUTH=basic:apk.cgr.dev:user:<token>
```

### Use Chainguard Libraries for Python

```yaml
- uses: actions/setup-python@<sha> # vX.Y.Z
- uses: chainguard-dev/setup-chainctl@[VERSION]
  with:
    identity: "deadbeef/badf00d"
    setup-python-keyring: "true"
```

## Security

- The binary is downloaded over HTTPS only (TLS 1.2+, no plain HTTP
  redirects) and verified against the published `chainctl_checksums.txt`
  before use. The action fails if the checksum does not match.
- All inputs are passed to scripts through environment variables, never
  interpolated into shell code.
- Tokens written to the environment (`env-auth`) are masked in logs.

## Runner Support

The action is tested on Linux, macOS and Windows runners.

On Windows, steps that call `chainctl` must use `shell: bash`.
