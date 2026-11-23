# easyvm-reactos

Walkthroughs and CI for **ReactOS** guests, driven by
[`easydev-org/easyvm`](https://github.com/easydev-org/easyvm).

## What lives here

`reactos/steps/` - the `.walk` files for `install` and `smoke`, plus the reference images they
compare against. The other stages (`ssh`, `vm-configure`) are added once an install produces a disk
that boots.

## What makes this guest different

ReactOS IS A REIMPLEMENTATION OF WINDOWS NT, and that is why its installer looks like the
Windows 2000 one: a blue text-mode first stage, then a graphical second stage after a reboot. It is
32-BIT ONLY - the ISO is i386 - so it needs a machine that can start a 32-bit kernel, which
`-machine q35` does and a 64-bit-only firmware does not.

## The media is mirrored, not fetched from upstream

A stage pulls its installer image from this repository's ghcr package, never from the publisher:
`artifacts-publish.yml` downloads `iso-reactos-0.4.16`, checks its digest, packs it into a tar and pushes one
tag. Every stage afterwards asks the package.

```sh
gh workflow run artifacts-publish.yml -f which=iso-reactos-0.4.16
gh workflow run reactos-0.4.16-install.yml
gh workflow run reactos-0.4.16-smoke.yml
```

## What lives elsewhere, on purpose

The **engine** and the **CI machinery** are both in `easydev-org/easyvm`; these workflows call
`easydev-org/easyvm/.github/workflows/stage.yml@v0.0.3-dev` for the second.

Disks pull from and publish to this repository's own ghcr package, `easydev-org/easyvm-reactos`.
