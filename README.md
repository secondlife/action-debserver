# action-debserver

Incredibly basic debserver action which:

1. Scans a target directory for `*.deb`s using `dpkg-scanpackages` to produce
   package indexes
2. Starts a simple HTTP server using `python -m http.server`

There are several limitations to this action. Notably, it requires
**dpkg-scanpackages** and **python3** to be installed. These should be available
with GitHub's Ubuntu runners, but may not be on self-hosted and non-linux hosts.

## Example

```yaml
name: Build

on:
  pull_request:
  push:
    branches: [main]

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - name: Build package 
        uses: secondlife/action-nfpm@v2
      # Scan packages and start a debserver listening on 0.0.0.0:12321
      - uses: secondlife/action-debserver@v1
        id: debserver
        with:
          path: dist
      - uses: docker/build-push-action@v6
        with:
          tags: user/app:latest
          push: true
          # Pass deb server URL as build arg to image using docker's default network gateway (host)
          build-args: LOCAL_DEB_SERVER_URL=http://172.17.0.1:${{ steps.debserver.outputs.port }}
```
