# A3dp-Slicer-profiles

Vendor print-profile bundle repo for [A3dp-Slicer](https://github.com/Advanced3dPrinting/A3dp-Slicer).

The slicer's in-app vendor profile updater (Configuration > Check for updates,
"Check for updates" / "Add a new vendor" dialog) reads this repo:

1. For each vendor ini shipped in the app (`resources/profiles/A3dp.ini`, `Modix.ini`),
   the `[vendor]` key `config_update_github = Advanced3dPrinting/A3dp-Slicer-profiles`
   points here.
2. The updater calls `GET api.github.com/repos/Advanced3dPrinting/A3dp-Slicer-profiles/tags`,
   parses every tag named `<config_version>=<slicer_version>` (e.g. `2.3.1=2.7.62.0`),
   and offers an upgrade when the tag's `config_version` is higher than the vendor ini
   currently installed in the user's data dir.
3. On install it downloads that tag's zipball and copies `profiles/<Vendor>.ini`
   plus the `profiles/<Vendor>/` asset folder into the user's vendor directory.

## Layout

- `profiles/A3dp.ini`, `profiles/Modix.ini` — the vendor bundles (mirrors of the
  app's `resources/profiles/` copies — keep the two in sync).
- `profiles/A3dp/`, `profiles/Modix/` — bed models, textures, thumbnails referenced
  by the inis.

## Versioning rules (important)

- Tags MUST be named `<config_version>=<slicer_version>`. Both parts must be valid
  semver. The `slicer_version` part must be <= the running slicer's version or the
  tag is never offered as "best".
- All vendors in this repo share the tag's `config_version` (the updater applies every
  tag to every vendor). So all vendor inis here carry the SAME `config_version`
  (currently `2.3.1`). Do not version vendors independently.
- Slicer release tags on the main repo must be 4-part (e.g. `2.7.63.0`,
  `2.7.63.0-beta1`): the semver library compares counter count first, so a 3-part
  tag like `2.7.63` is never seen as newer than a `2.7.63.0` build.

## Release procedure (profile change)

1. Bump `config_version` in BOTH `profiles/A3dp.ini` and `profiles/Modix.ini`
   (and in the app's `resources/profiles/` copies, committed to the main slicer repo).
2. Commit, then tag: `git tag 2.3.2=2.7.62.0 && git push origin 2.3.2=2.7.62.0`
   (replace the slicer part with the slicer version the profiles are for).
