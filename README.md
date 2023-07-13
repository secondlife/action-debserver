# action-debserver

Incredibly basic debserver action which:

1. Scans a target directory for *.debs using `dpkg-scanpackages` to produce
   package indexes
2. Starts a simple HTTP server using `python -m http.server`

There are several limitations to this action, notable: it requires
dpkg-scanpackages and python3 to be installed. This is true of Github's
ubuntu runners, but may not be of self-hosted and non-linux hosts.

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
      - uses: secondlife/action-debserver@v1 
        with:
          path: dist
      - uses: secondlife/action-build-docker@v1
        with:
            image: secondlife/my-image
            build-args: local_debian_repository_url=http://172.17.0.1:12321
```
