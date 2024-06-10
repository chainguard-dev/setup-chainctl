# Setup `chainctl`

[![Test](https://github.com/chainguard-dev/setup-chainctl/actions/workflows/test.yaml/badge.svg)](https://github.com/chainguard-dev/setup-chainctl/actions/workflows/test.yaml)

This action installs the latest `chainctl` binary for a particular environment
and authenticates with it using identity tokens.

## Usage

```yaml
- uses: chainguard-dev/setup-chainctl@[VERSION]
  with:
    # the ID of the identity this workload should assume when speaking to Chainguard APIs.
    identity: "..."
```

## Scenarios

```yaml
permissions:
  id-token: write

steps:
- uses: chainguard-dev/setup-chainctl@[VERSION]
  with:
    identity: "deadbeef/badf00d"
```

See [Authenticating to Chainguard Registry](https://edu.chainguard.dev/chainguard/chainguard-images/registry/authenticating/#authenticating-with-github-actions) for more information about creating an identity to pull images from cgr.dev from GitHub Actions, using `setup-chainctl`.

# Runner Support

The action is tested to work on Linux, MacOS and Windows runners.

Note: If you use it on Windows, you must use `shell: bash`.
