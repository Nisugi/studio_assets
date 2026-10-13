# studio_assets

Art packs for Hydra, the GemStone IV client, made in
[gs_studio](https://github.com/Nisugi/gs_studio). Nothing here is compiled
into Hydra: a player picks a pack from Hydra's list and it is fetched on that
click, never on its own.

## What is here

- `packs.toml`: every pack on offer, read raw from this repo's top by Hydra.
  Each `[[pack]]` has its `id`, `title`, `kind`, `version`, `size` (bytes),
  `sha256`, `url` and, when it has one, `preview`.
- `previews/<id>.png`: each pack's thumbnail (240 px tall), what a player
  sees in Hydra's list, and on the pack's release page, before fetching it.
- The packs themselves are **release assets**, one release per pack version,
  tagged `<id>-<version>` and holding `<id>.zip`.

## A pack

A zip of `pack.toml` (its `id`, `title`, `kind`, `version`, `format`) and a
`global/` tree laid out as the files sit in a pool. A client checks the zip
against `sha256` and installs `global/` into its data folder
(`gs_base::packs::install_zip`), keeping a ledger of what it installed, so an
update replaces only files the player has not changed. `pack.toml` itself is
not installed; the installed version lives in the ledger.

| Kind | Id | Where it installs |
|---|---|---|
| `dolls` | `doll_<picture>` | `global/images/dolls/<picture>.png`: an injury-doll picture, its calibration inside the PNG, and any overlays it has |
| `frames` | `frame_<recipe>` | `global/images/frames/packs/<id>/`: a frame recipe and the parts it names |
| `compass` | `compass_<set>` | `global/images/compass/<set>/`: `compass.toml` (its `layout`, `hub`, and the `ways` it lights), `rose.png` at rest, and `<way>.png` for each way -- `n`, `ne`, `e`, `se`, `s`, `sw`, `w`, `nw`, `up`, `down`, `out` -- each the rose's size, drawn over it while the room has that way out |

## Making a pack

In gs-studio, *Export pack* on the Dolls or Frames tab (or
`gs-studio --pack doll:<picture>` / `--pack frame:<recipe>`) writes the zip
and its entry in `packs.toml`; the version rises only when the zip changed.
The zip becomes a release here, and `packs.toml` is copied to this repo's top.
