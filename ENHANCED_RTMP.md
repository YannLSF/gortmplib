# Enhanced RTMP multitrack support

This branch contains experimental Enhanced RTMP extensions used by the
companion MediaMTX fork:

https://github.com/YannLSF/mediamtx

The primary goal is to support OBS Enhanced Broadcasting / Enhanced RTMP
streams containing multiple video renditions while preserving the information
required to forward those streams through MediaMTX.

## Status

Current upstream base:

- gortmplib: `v1.0.3`
- custom branch: `enhanced-rtmp-multitrack`

This fork is consumed by the MediaMTX
`enhanced-rtmp-multitrack` branch through a Git submodule.

## Main changes

The Enhanced RTMP branch adds or extends support for:

- Enhanced RTMP multitrack video messages;
- explicit video track IDs;
- key-frame information on multitrack video messages;
- H.264 and H.265 random-access detection when writing multitrack video;
- Enhanced RTMP metadata required to describe multiple tracks;
- publisher capability advertisement for:
  - AVC (`avc1`);
  - HEVC (`hvc1`);
  - MPEG-4 Audio / AAC (`mp4a`);
- graceful publisher shutdown using `FCUnpublish` and `deleteStream`.

These changes are intended to preserve the track information needed by
Enhanced RTMP publishers and downstream MediaMTX forwarding.

## Relationship with MediaMTX

The companion MediaMTX fork contains this repository as:

```text
gortmplib-local
```

Its `go.mod` redirects the upstream gortmplib dependency to the submodule:

```go
replace github.com/bluenviron/gortmplib => ./gortmplib-local
```

The MediaMTX Git commit pins an exact gortmplib commit. This is intentional:
cloning MediaMTX with `--recurse-submodules` therefore reproduces the tested
gortmplib revision instead of following the tip of this branch automatically.

## Testing

The complete test suite can be run with Go installed locally:

```sh
go test ./...
```

It can also be run entirely through Docker:

```sh
docker run --rm \
  --user "$(id -u):$(id -g)" \
  -e HOME=/tmp \
  -e GOCACHE=/tmp/go-cache \
  -e GOMODCACHE=/tmp/go-mod-cache \
  -v "$PWD:/src" \
  -w /src \
  golang:1.26-alpine \
  go test ./...
```

Before committing changes, also run:

```sh
git diff --check
git status --short
```

## Updating from upstream

This branch is currently based on gortmplib `v1.0.3`.

When porting the work to a newer gortmplib revision:

1. fetch the latest upstream history and tags;
2. create a new branch from the gortmplib revision used by the target
   MediaMTX release;
3. port the Enhanced RTMP changes rather than blindly assuming the old patch
   still applies;
4. update protocol tests and byte-level fixtures where required;
5. run the complete test suite;
6. update the `gortmplib-local` gitlink in the companion MediaMTX repository;
7. rebuild MediaMTX from a fresh recursive clone.

The current Enhanced RTMP changes can be inspected with:

```sh
git diff v1.0.3..enhanced-rtmp-multitrack
```

## Upstream

This repository is a fork of:

https://github.com/bluenviron/gortmplib

The Enhanced RTMP work is maintained separately and should not be assumed to
be supported by the upstream gortmplib project.
