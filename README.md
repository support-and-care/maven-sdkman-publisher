# Maven SDKman Publisher

Automated publishing of Apache Maven releases to [SDKman](https://sdkman.io/).

## Background

Publishing new Apache Maven releases to SDKman currently depends on a single person manually triggering an API call.
This repository provides a GitHub Actions workflow to make the process more resilient, accessible, and auditable.

See [Discussion #156](https://github.com/support-and-care/maven-support-and-care/discussions/156) for the full proposal.

## How It Works

The publish workflow derives a download URL from the version number, verifies the archive exists on `dlcdn.apache.org`, then calls the SDKman vendor API to register the release. It can optionally set the version as the SDKman default.

There are two ways to trigger a publish:

- **Workflow dispatch** — maintainers trigger it manually via the GitHub Actions UI with a version input
- **PR-based** — contributors update `versions/maven.json` in a PR; a verification workflow validates the version on the PR, and merging to `main` triggers the publish

Both paths use the official [sdkman-release-action](https://github.com/sdkman/sdkman-release-action) and [sdkman-default-action](https://github.com/sdkman/sdkman-default-action).

## Prerequisites

The following repository secrets must be configured:

- `SDKMAN_CONSUMER_KEY` — your SDKman vendor API consumer key
- `SDKMAN_CONSUMER_TOKEN` — your SDKman vendor API consumer token

## Usage

### Option 1: Manual Workflow Dispatch

1. Go to **Actions** → **Publish Maven to SDKman**
2. Click **Run workflow**
3. Enter the Maven version (e.g., `4.0.0`)
4. Choose whether to set it as the SDKman default
5. Click **Run workflow**

### Option 2: PR-Based Publishing

1. Fork this repository (or create a branch)
2. Edit `versions/maven.json` with the new version:
   ```json
   {
     "version": "4.0.0",
     "make_default": true
   }
   ```
3. Open a pull request — CI automatically verifies the version format and download URL
4. A maintainer reviews and merges — the publish workflow triggers automatically

### Download URL Construction

The download URL is derived automatically from the version:

```
https://dlcdn.apache.org/maven/maven-{major}/{version}/binaries/apache-maven-{version}-bin.tar.gz
```

The workflow verifies the URL exists before publishing to SDKman.

## Open Questions

- **Multiple versions in parallel**: Currently, the version file holds a single version. If Maven 3 and Maven 4 need to be published independently, separate branches (e.g., `maven-3`, `maven-4`) could each maintain their own `versions/maven.json`. The manual workflow dispatch already supports publishing any version independently.

## License

This project is licensed under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).
