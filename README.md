[![irsync: interval rsync](irsync-mast.jpg)](https://github.com/cjimti/irsync)
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fcjimti%2Firsync.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Fcjimti%2Firsync?ref=badge_shield)
# Interval [rsync]

Source: https://github.com/cjimti/irsync

[![Maintainability](https://api.codeclimate.com/v1/badges/17e7ef41f16a004d020f/maintainability)](https://codeclimate.com/github/cjimti/irsync/maintainability)
[![Go Report Card](https://goreportcard.com/badge/github.com/cjimti/irsync)](https://goreportcard.com/report/github.com/cjimti/irsync)
[![GoDoc](https://godoc.org/github.com/cjimti/irsync/irsync?status.svg)](https://godoc.org/github.com/cjimti/irsync/irsync)
[![Build Status](https://travis-ci.org/cjimti/irsync.svg?branch=master)](https://travis-ci.org/cjimti/irsync)

[![Docker Container Image Size](https://shields.beevelop.com/docker/image/image-size/cjimti/irsync/1.0.0.svg)](https://hub.docker.com/r/cjimti/irsync/)
[![Docker Container Layers](https://shields.beevelop.com/docker/image/layers/cjimti/irsync/1.0.0.svg)](https://hub.docker.com/r/cjimti/irsync/)
[![Docker Container Pulls](https://img.shields.io/docker/pulls/cjimti/irsync.svg)](https://hub.docker.com/r/cjimti/irsync/)

Command line utility and [Docker] container for running [rsync] on an interval. rsync, synchronizes (one way) files between two sources, **irsync** wrapps [rsync] and runs it on a specified interval.

As a basic command-line utility, the only requirement is [rsync]. As a [Docker] container the conly requirement is [Docker]. The docker container size with all the requirements is only around 5 megs.

## Background

I wrote up a little background on the motivation behind **irsync** on my [mk.imti.co] blog, [rSync Files on Interval](https://mk.imti.co/raspberry-pi-rsync-interval/).


## Demo

Setup a quick demo using [Docker]s `docker-compose` command. Included with this project is a `docker-compose.yaml` with a simple client/server setup. In this composer configuration `irsync` is set to check the server every 30 seconds (after sync is complete. The server mounts the `./data/source` directory and the client mounts the `./data/dest` directory. Drop files in `./data/source` and see them appear in `./data/dest`.

[Watch a 2 minute youtube video demoing the commands below](https://www.youtube.com/watch?v=gT_P2a-xpPw)

**Setup and run demo (requires [Docker]):**

```bash
# create a source and dest directories (mounted from the docker-compose)
mkdir -p ./data/source
mkdir -p ./data/dest

# make a couple of sample files
touch ./data/source/test1.txt
touch ./data/source/test2.txt

# get the docker-compose.yml
curl https://raw.githubusercontent.com/cjimti/irsync/master/docker-compose.yml >docker-compose.yml

# run docker-compose in the background (-d flag)
docker-compose up -d

# view logs
docker-compose logs -f

# drop some more files in the ./data/source directory
# irsync is configured to check every 30 seconds in this demo.

#### Cleanup

# stop containers
docker-compose stop

# remove containers
docker-compose rm

```

## Run From Source

#### Example #1 Local with interval defaults

```bash
go run ./irsync.go -pvrt --delete ./data/source/ ./data/dest/
```

## Run From Container

#### Example #1 Local

```bash
docker run --rm -v "$(pwd)"/data:/data cjimti/irsync \
    -pvrt --delete /data/source/ /data/dest/
```

#### Example #2 Local Every 10 Seconds

```bash
docker run --rm -v "$(pwd)"/data:/data cjimti/irsync \
    --irsync-interval-seconds=10 \
    -pvrt --delete /data/source/ /data/dest/
```

#### Example #3 Server to Local - Restart on Failure

```bash
docker run -d --name irsync-demo --restart on-failure \
    -v "$(pwd)"/data:/data cjimti/irsync \
    --irsync-interval-seconds=10 \
    -pvrt --delete /data/source/ /data/dest/
```

## Environment Configuration

- `RSYNC_PASSWORD=password` Use environment variable `RSYNC_PASSWORD` to keep from being prompted for a password for servers requiring authentication.

## Development

### Building and Releasing

Interval [rsync] uses [GORELEASER] to build binaries and [Docker] containers.

#### Test Release Steps

Install [GORELEASER] with [brew] (MacOS):
```bash
brew install goreleaser/tap/goreleaser
```

Build without releasing:
```bash
goreleaser --skip-publish --rm-dist --skip-validate
```

#### Release Steps

- Commit latest changes
- [Tag] a version `git tag -a v2.0 -m "Version 2.0"`
- Push tag `git push origin v2.0`
- Run: `GITHUB_TOKEN=$GITHUB_TOKEN goreleaser --rm-dist`

## Resources

- [GORELEASER]
- [Docker]
- [rsync]
- [homebrew]


[homebrew]: https://brew.sh/
[brew]: https://brew.sh/
[GORELEASER]: https://goreleaser.com/
[Docker]: https://www.docker.com/
[rsync]: https://en.wikipedia.org/wiki/Rsync
[Tag]: https://git-scm.com/book/en/v2/Git-Basics-Tagging
[mk.imti.co]: https://mk.imti.co


## License
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fcjimti%2Firsync.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2Fcjimti%2Firsync?ref=badge_large)