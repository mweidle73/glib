# Abuild GLib archive

This repository preserves the historical GLib source revisions formerly
selected by Abuild. The authoritative project is maintained in the
[GNOME GLib repository](https://gitlab.gnome.org/GNOME/glib); the original
documentation and license remain part of every preserved source revision.

The long-lived branches have deliberately separate roles:

- `master` mirrors the official upstream `main` branch;
- `abuild` is the final source revision selected by Abuild;
- `archive/abuild-pin-c95e2ae9` preserves the other revision selected by
  historical Abuild commits; and
- `abuild-gh` adds only this maintenance README and files below `.github/` to
  `abuild`.

The final `abuild` branch is exactly the official `2.39.92` tag at
`599c669b`. Abuild initially selected the official `2.61.0` tag at `c95e2ae9`
and replaced it with `2.39.92` one day before removing the GLib and QEMU
submodules together.

No Abuild build rule or source dependency consumed this GLib submodule. Its
short-lived inclusion accompanied the former vendored QEMU experiment, so the
branches are retained for provenance and reproducibility rather than as a
supported Abuild runtime dependency or a recommended GLib version.

## Continuous integration

Run the same Trixie check locally with Docker:

```sh
.github/ci/run .github/ci/check
```

The check regenerates the historical Autotools files, disables the release's
obsolete warning-as-error policy for the modern compiler, builds the GLib
2.39.92 core library, runs all of its Trixie-compatible upstream tests, stages
the core installation, and verifies its header, static library, and source
version. The `gdatetime` test binary is explicitly excluded because this old
GLib release cannot parse current Trixie TZif data. The check also deliberately
does not build the unused historical GIO tools: their code generator requires
Python 2.5, which is not available in Trixie. Everything executes as the
image's `nobody` identity in a read-only container without network access or
Linux capabilities.

The old test suite contains randomized property tests. The archive harness
passes a fixed GLib test seed so identical source and toolchains produce a
repeatable result. This retains the `gvariant` coverage: an observed random
seed reproducibly crashed its historical serialiser test, while the committed
seed was verified repeatedly before it was selected.

The weekly upstream monitor compares `master` with the authoritative upstream
`main` branch and checks all mirrored tags whose names are plain three-part
version numbers. It reports drift but never updates branches automatically.
