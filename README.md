# action-debserver

Incredibly basic debserver action which:

1. Scans a target directory for *.debs using `dpkg-scanpackages` to produce
   package indexes
2. Starts a simple HTTP server using `python -m http.server`

There are several limitations to this action. Notabley, it requires
**dpkg-scanpackages** and **python3** to be installed. These should be available
with Github's Ubuntu runners, but may not be on self-hosted and non-linux hosts.

## Example

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build package 
        uses: secondlife/action-nfpm@v1
      # Scan packages and start a debserver listening on 0.0.0.0:12321
      - uses: secondlife/action-debserver@v1
        id: debserver
        with:
          path: dist
      - uses: docker/build-push-action@v4
        with:
            tags: user/app:latest
            push: true
            # Pass deb server URL as build arg to image using docker's default network gateway (host)
            build-args: LOCAL_DEB_SERVER_URL=http://172.17.0.1:${{ steps.debserver.outputs.port }}
```
