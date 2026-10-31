# kups-tizen

Apps and tools for my Samsung Tizen TVs. Each folder is a separate git
repository, included here as a [git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules):

| Folder | Repository | What it is |
| --- | --- | --- |
| `monitor-home/` | [kups-tizen-monitor](https://github.com/kupara/kups-tizen-monitor) | Glass Home (Kups Home) for the monitor TV: home screen, photo website, TV tools |
| `runinga-home/` | [kups-tizen-runinga](https://github.com/kupara/kups-tizen-runinga) | Glass Home for the Runinga TV, with that TV's logo and assets |
| `utube/` | [kups-tizen-utube](https://github.com/kupara/kups-tizen-utube) | uTube (TznTube): a patched TizenBrew that opens TizenTube straight away, plus a dev bridge, launcher and splash |

`monitor-home/` and `runinga-home/` start from the same Glass Home code and are kept as
separate copies on purpose, so each TV can change without affecting the other.

## Get it

```sh
git clone --recurse-submodules git@github.com:kupara/kups-tizen.git
```

If you cloned without `--recurse-submodules`:

```sh
git submodule update --init
```

## Set up a checkout

Secrets, certificates and build output are not in git (see
[What isn't in git](#what-isnt-in-git)), so a fresh clone needs them recreated:

- **monitor-home / runinga-home**: follow `docs/INSTALL.md` in that folder.
  In short: `cp scripts/tizen-env.example.sh scripts/tizen-env.sh`, fill in the
  TV's IP and device ID, make the Samsung certificate (`scripts/make-cert.sh`),
  then `scripts/build.sh` and `scripts/install.sh`. `scripts/build.sh` makes the
  app/service shared key (`app/js/local-key.js`) on its first run.
- **utube**: `tools/build-install.sh` builds and signs `dist/TznTube.wgt`
  (add `install` to put it on the TV). It reads the Tizen settings from
  `../runinga-home/scripts/tizen-env.sh`, so set up runinga-home first. The
  dev bridge's allowed Mac IP is in `devbridge/service/index.js`.

## Working with the submodules

Each folder is its own repository: commit and push inside it as usual.

```sh
cd monitor-home
git add -A && git commit -m "..." && git push
```

Then record the new commit in kups-tizen:

```sh
cd ..
git add monitor-home && git commit -m "Update monitor-home" && git push
```

Pull everything, including the submodules:

```sh
git pull --recurse-submodules
git submodule update --init
```

`git status` here shows `modified: monitor-home (new commits)` when a submodule has
commits that kups-tizen doesn't point at yet.

## What isn't in git

Each repository's `.gitignore` keeps these out:

- Signing material: `*.p12`, `*.pwd`, `*.pem`, `*.crt`, `*.csr`, keys, and
  uTube's `signature1.xml` / `author-signature.xml`
- Per-checkout settings and secrets: `scripts/tizen-env.sh`,
  `app/js/local-key.js`, `.env*`
- TV data: `backup/` (device info, DUID, app lists, distributor certificate),
  `state/` and `tools/state/` (TLS keys, remote-control tokens), `photos/`
- Build output: `dist/`, `*.wgt` (uTube's built `tizenbrew-ui/dist` and
  `service-nextgen/service/dist` bundles *are* committed, because they hold
  the patches)
- Personal media: copied macOS wallpapers, third-party app logos, your logo
