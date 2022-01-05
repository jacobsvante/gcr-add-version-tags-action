GitHub action for adding Docker image tags to Google Container Registry, based on version identifiers

Example, used together with the excellent [release-please-action](https://github.com/google-github-actions/release-please-action):

```yml
on:
  push:
    branches:
      - main

name: Main
jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: google-github-actions/release-please-action@v2
        id: release
        with:
          release-type: simple
          package-name: my-package
          bump-minor-pre-major: true
      - uses: actions/checkout@v2
      - name: Tag major and minor versions
        if: ${{ steps.release.outputs.release_created }}
        uses: jacobsvante/gcr-add-version-tags-action@v0
        with:
          repository: eu.gcr.io/project-123456/my-package
          source-tag: ${{ github.sha }}  # Must already exist in GCR repository
          major: ${{ steps.release.outputs.major }}
          minor: ${{ steps.release.outputs.minor }}
          patch: ${{ steps.release.outputs.patch }}
          push-latest: true  # If you want to also push an updated `latest` tag
```
