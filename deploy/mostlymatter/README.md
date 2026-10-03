# Mostlymatter build

This fork builds Mattermost with Framasoft's limitless patch (Mostlymatter) and
publishes the image to GHCR. The workflow is `.github/workflows/build-mostlymatter.yml`.

## How it builds

1. Checks out the upstream tag `v<version>` from this fork.
2. Downloads `limitless.patch` from Framasoft's `v<version>-limitless` tag and
   checks it against the sha256 in `patch-checksums.txt`.
3. Applies the patch and compiles the server from `server/cmd/mostlymatter`.
4. Copies the binary into the official `mattermost/mattermost-team-edition:<version>`
   image, which provides the webapp, mmctl and bundled plugins.
5. Smoke tests the image, then pushes `ghcr.io/zollege/mostlymatter:<version>-limitless`.

The server is compiled without the `enterprise` and `sourceavailable` tags, so
the source-available code under `server/enterprise` is not in the binary.

## Building a version

Actions tab, Build Mostlymatter image, Run workflow. Leave `source_ref` empty to
build the upstream tag, or set it to a branch of this fork to build your own changes.

## Moving to a new version

Framasoft publishes one patch per release, and it often needs fixing between minor versions.

    curl -fsSL https://framagit.org/framasoft/framateam/mostlymatter/-/raw/v11.12.0-limitless/limitless.patch -o limitless-v11.12.0.patch
    git apply --check limitless-v11.12.0.patch   # run inside a checkout of the upstream tag
    sha256sum limitless-v11.12.0.patch

Read the patch, then add the sha256 line to `patch-checksums.txt`.

## Notes

- Backend only. The webapp is stock Mattermost.
- SSO is not supported by Mostlymatter upstream and is not tested here.
- Do not use Framasoft's logo (CC BY-NC).
- AGPL section 13: users of a modified server must be able to get the source. This
  repository is that source.
- Upstream workflows in this fork are not meant to run. In Settings, Actions, General,
  allow only the workflows you use.
