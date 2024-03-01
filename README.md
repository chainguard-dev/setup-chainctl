# Setup `chainctl`

This action installs the latest `chainctl` binary for a particular environment
and authenticates with it using identity tokens.

## Usage

```yaml
- uses: chainguard-dev/setup-chainctl@main
  with:
    # the ID of the identity this workload should assume when speaking to Chainguard APIs.
    identity: "..."
```

## Scenarios

```yaml
permissions:
  id-token: write

steps:
- uses: chainguard-dev/setup-chainctl@main
  with:
    identity: "deadbeef/badf00d"
```

See [Authenticating to Chainguard Registry](https://edu.chainguard.dev/chainguard/chainguard-images/registry/authenticating/#authenticating-with-github-actions) for more information about creating an identity to pull images from cgr.dev from GitHub Actions, using `setup-chainctl`.
