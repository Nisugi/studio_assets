# studio_assets

Art packs for Hydra, the GemStone IV client, made in
[gs_studio](https://github.com/Nisugi/gs_studio). Nothing here is compiled
into Hydra: a player picks a pack from Hydra's list and it is fetched on that
click, never on its own.

## What is here

- `packs.toml`: every pack on offer, read raw from this repo's top by Hydra.
  Each `[[pack]]` has its `id`, `title`, `kind`, `version`, `size` (bytes),
  `sha256`, `url` and, when it has one, `preview`.
- `previews/<id>.png`: each pack's thumbnail, what a player sees in Hydra's
  list, and on the pack's release page, before fetching it: 240 px tall, but
  an `orb_fills` or `strip_fills` pack's is a full contact sheet (its fills in seven colours)
  that Hydra shows small and enlarges on a click.
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
| `dolls` | `doll_<name>` | `global/images/dolls/`: a style's male and female injury-doll pictures (`stealth_male_<style>.png`, `stealth_female_<style>.png`; `doll_stealth` and `doll_stealthblue` the generic pairs), or one picture (`doll_nishi`) -- each its own doll, its calibration inside the PNG, with any overlays it has |
| `frames` | `frame_<recipe>` | `global/images/frames/packs/<id>/`: a frame recipe and the parts it names |
| `frame_kits` | `frame_kits_<group>` | `global/images/frames/kits/<id>/<style>/`: a window frame kit -- corners `tl`, `tr`, `bl`, `br` and looping runs `top`, `bottom` (across) and `left`, `right` (down), with `kit.toml` (`bar` = 96, the frame's thickness; `overhang`, how far decoration reaches inward over the window; `corner`; `run`). Draw the runs repeated along each side, then the corners over the ends, all scaled by thickness / 96; the frame is flush with the window's edge and nothing goes past it |
| `compass` | `compass_<set>` | `global/images/compass/<set>/`: `compass.toml` (its `layout` -- `hub`, `out` the rose's centre, or `column`, `out` between `up` and `down` -- and the `ways` it lights), `rose.png` at rest, and `<way>.png` for each way -- `n`, `ne`, `e`, `se`, `s`, `sw`, `w`, `nw`, `up`, `down`, `out` -- each the rose's size, drawn over it while the room has that way out |
| `orb_fills` | `orb_fills_<group>` | `global/images/fills/orb/<id>/`: orb fills, `<name>.png`, 384 x 384, grey (Hydra multiplies them by the bar's colour) -- the liquid's circle centred, 0.898 of the picture (the orb overlay's opening, 0.883, plus a seam under its rim), clear round it, the same size as the overlay and background so the three stack |
| `orb_backgrounds` | `orb_backgrounds_<set>` | `global/images/backgrounds/orb/<id>/`: empty glass drawn under an orb's fill, `<name>.png`, 384 x 384, laid out as the orb fills are (circle 0.898 of the picture, clear round it), its own opacity kept |
| `orb_overlays` | `orb_overlays_<group>` | `global/images/overlays/orb/<id>/`: orb frames, `<style>.png`, 384 x 384, painted with an empty opening (the standard 0.883 circle) and laid over the orb's background and fill, the three the same size |
| `strip_fills` | `strip_fills_<group>` | `global/images/fills/strip/<id>/`: looped strips, `<name>.png`, 96 px tall, grey, for rings (wrapped round, the length round the circle, the height across the band) and bars (laid along, the height across, repeated); the end meets the start |
| `strip_backgrounds` | `strip_backgrounds_<set>` | `global/images/backgrounds/strip/<id>/`: the empty track under a ring's or a bar's fill, `<name>.png`, 96 px tall, grey with its own alpha, and `backgrounds.toml`: per background `span = true` (drawn once round the ring, clockwise from the top, or once along the bar, stretched -- the graduated ones) or `false` (repeated like a fill strip) |
| `strip_overlays` | `strip_overlays_<group>` | `global/images/overlays/strip/<id>/<style>/`: a kit for bars and rings -- `rails.png` (96 px tall, looping: a bar repeats it along its length, a ring wraps it round, the top rail outside), `cap_left.png` and `cap_right.png` (drawn over each end of a bar), `kit.toml` (`rail` and `cap`: the channel the fill goes in is rail .. height-rail and cap .. length-cap, px at 96 tall; the cap pieces' widths) |
| `vessels` | `vessels_<group>` | `global/images/vessels/<id>/<style>/`: for each of `hourglass`, `flask`, `lantern`, `<vessel>.png` (384 x 384 RGBA, drawn on top) and `<vessel>_mask.png` (384 x 384 grey, the glass that shows: the fill's coverage). Draw any orb fill at the same size, coloured, through the mask below the level, then the vessel; the level is set by area over the mask, so only visible glass counts |
| `emblems` | `emblems_<group>` | `global/images/emblems/<id>/<style>/<stat>.png`: 256 x 256 RGBA, the emblem centred with its longer side 0.70 of the picture, in its own colours (never tinted). The 13 stats `health`, `mana`, `stamina`, `spirit`, `stance`, `encumbrance`, `mind`, `field_experience`, `next_level`, `roundtime`, `cast_time`, `pulse`, `blood_points`, and two second choices: `stamina_lungs` (for stamina) and `pulse_bell` (for pulse). Drawn square over the middle of the stat's panel, its side the panel's shorter side, over the panel's fill |

## Making a pack

In gs-studio, *Export pack* on the Dolls or Frames tab (or
`gs-studio --pack doll:<picture>` / `--pack frame:<recipe>`) writes the zip
and its entry in `packs.toml`; the version rises only when the zip changed.
The zip becomes a release here, and `packs.toml` is copied to this repo's top.
The compass sets and the doll pairs were made by image-gen's
`compass_packs.py` and `doll_packs.py`, which zip a pack the same way.
