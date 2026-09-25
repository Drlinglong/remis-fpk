# remis-fpk

`remis-fpk` is a small, read-only reader and safe extractor for the observed
Surviving Mars FLPK version 1 archive profile. It validates archive structure
and file payloads before extraction. It never executes Lua, changes the input
archive, or repacks files into an archive.

This standalone package was extracted from [Project Remis](https://github.com/Drlinglong/Remis).
It is licensed under AGPL-3.0-only. This is an independent community tool and
is not official software or endorsed by Paradox Interactive or Haemimont
Games. It does not include game files or proprietary Mod archives. Users are
responsible for ensuring they have permission to inspect and extract their
input files; extracted game resources remain subject to the applicable game
terms and rights holders.

## Install

```console
git clone https://github.com/Drlinglong/remis-fpk.git
cd remis-fpk
python -m pip install .
```

You can also install directly from Git:

```console
python -m pip install "remis-fpk @ git+https://github.com/Drlinglong/remis-fpk.git"
```

The project is not published on PyPI.

## Use

Validate an archive and print a JSON inventory, including the archive SHA-256:

```console
remis-fpk list ModContent.fpk
```

Extraction requires the SHA-256 reported by `list`, and the destination must
not already exist. The extractor writes to a new staging directory and moves
it into place only after every file passes validation:

```console
remis-fpk extract ModContent.fpk unpacked-mod --expected-sha256 <sha256-from-list>
```

The Python API is available as `remis_fpk.inspect_archive(path)` and
`remis_fpk.extract_archive(path, destination, expected_sha256=...)`.

## Safety and supported files

The reader supports raw file entries and the observed chunked Zstandard
container, including archives that mix compressed chunks and exact-length
literal chunks. It applies bounded archive, index, file, total output, entry,
nesting, and Zstandard-window limits. It rejects unsafe Windows path names,
duplicate paths, overlapping payloads, symbolic links, malformed records, and
unsupported FLPK variants. Extraction never overwrites an existing
destination. The tool does not create or repack FLPK archives.

See [FORMAT.md](FORMAT.md) for the format profile and current default limits.
These limits describe this implementation and do not establish support for
other game versions or archive variants.

## Development

```console
python -m pip install -e ".[test]"
python -m pytest -q
python -m build
```

The CI workflow builds and installs both wheel and source distributions on
Windows and Ubuntu with Python 3.10 and 3.13, then runs the synthetic-fixture
tests against the installed package and command-line entry point.
