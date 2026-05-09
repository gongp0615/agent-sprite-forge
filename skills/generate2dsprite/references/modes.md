# Modes

Use this file when the user's wording leaves room for multiple valid asset plans.

## Asset Types

- `player`: controllable overworld hero
- `npc`: role-readable town or field character
- `creature`: monster, beast, spirit, boss, summon
- `character`: side-view or non-overworld humanoid unit that is not specifically a player or NPC
- `spell`: castable magic or skill sequence
- `projectile`: loopable traveling object such as orb, arrow, fireball, bullet, beam segment
- `impact`: hit burst, explosion, contact FX
- `prop`: item, weapon, shrine object, pickup, deployable
- `summon`: conjured unit or creature entrance asset
- `fx`: generic visual effect sheet

## Actions

- `single`: one static sprite
- `idle`: looped breathing / stance / aura cycle
- `cast`: spell or skill wind-up / release
- `attack`: attack-only body animation; for controllable heroes/main characters, wide slash arcs and hit FX should be separate `fx`/`impact` sheets
- `shoot`: ranged attack body action; projectile and muzzle flash should usually be separate assets
- `jump`: airborne takeoff / rise / fall / landing action
- `hurt`: damage reaction
- `combat`: combined attack + hurt sheet
- `walk`: travel loop
- `run`: faster travel loop
- `hover`: airborne idle / travel loop
- `charge`: power-up or dash prep
- `projectile`: loopable travel motion
- `impact`: contact burst
- `explode`: stronger impact or destruction burst
- `death`: defeat / vanish / collapse sequence

## Bundle Presets

- `single_asset`: one sprite or one sheet
- `unit_bundle`
  - default: `idle` + `combat`
  - optional: `walk`
- `spell_bundle`
  - default: `cast` + `projectile` + `impact`
- `combat_bundle`
  - default: `idle` + `attack` + `hurt`
- `line_bundle`
  - default: 1-3 forms
  - per form, choose only the needed sheets
- `hero_action_bundle`
  - default: one sheet per action, often `idle` + `run` + `attack` or `shoot` + `jump`
  - every body action defaults to 8 directions; rows are directions and columns are action frames for that direction
  - keep projectiles, muzzle flashes, slash arcs, weapon trails, impacts, and dust as separate assets unless the runtime supports wider per-action cells plus explicit origins
  - body action sheets should preserve idle/run body scale; split wide FX rather than letting it shrink the body in a fixed cell
  - assemble an engine atlas only after each action passes QC
- `engine_atlas`
  - delivery format only when it combines unrelated actions
  - build from processed action sheets, not from one mixed-action raw image

## Sheet Presets

- `single`
  - 1 frame
- `1x4`
  - 4 frames in one row
  - projectiles
  - simple looping FX
- `2x2`
  - 4 frames
  - projectile, impact, explode, or explicitly non-directional rough body actions
- `2x3`
  - 6 frames
  - explicitly non-directional cast, death, or slightly richer combat actions
- `2x4`
  - 8 frames
  - explicitly non-directional creature / monster locomotion
- `3x3`
  - 9 frames
  - explicitly non-directional large creature idle
  - explicitly non-directional boss aura loops
  - high-value showcase idles
- `3x4`
  - 12 frames
  - longer body actions where 16 frames would be excessive
- `4x4`
  - 16 frames
  - legacy topdown 4-direction player walk sheet only when requested
  - non-directional 16-frame action sequence when the user asks for richer casting, summoning, charging, transformation, or death animation
- `5x5` or `custom_grid`
  - use as raw generation only for one coherent long action sequence, prop packs, tileset-like atlases, or another single action family
  - for default body actions, use an 8-direction `8x8` custom grid
  - use as a delivery atlas for mixed actions only after separate action sheets have been generated and QC'd

## Frame Count Defaults

When `frames=auto`, choose the smallest useful count:

- static `single` asset: 1 frame
- body actions default to 8 directions; multiply the per-direction frame count by 8 to get total cells
- every body action: 8 frames per direction (`8x8`, 64 cells), including `idle`, `walk`, `run`, `attack`, `shoot`, `cast`, `jump`, `hurt`, `hover`, `charge`, `summon`, `death`, transformation, large creatures, and bosses
- coherent long non-directional action: 16 frames (`4x4`) only when richer timing is useful
- projectile loop: 4 frames (`2x2` by default; use a row strip only when the engine requires it)

## 8-Direction Body Action Layout

Use this row order for every 8-direction body-action sheet unless the target engine requires a different order:

1. down
2. down-left
3. left
4. up-left
5. up
6. up-right
7. right
8. down-right

Columns are the action frames for that direction. Generate one action per raw sheet; do not mix idle, walk, run, attack, and hurt in different rows of the same raw image.

## Agent-First Mapping Hints

- `"make a main hero"` -> `player` + 8-direction `player_sheet`
- `"make a 4-direction main hero"` -> only use legacy 4-direction output if the user explicitly wants 4 directions; otherwise upgrade to 8 directions
- `"make a side-view hero with idle, run, shoot, and jump"` -> `player` + `hero_action_bundle`; generate one action sheet per action and keep projectile/impact assets separate
- `"make a side-view hero melee attack"` -> `player` + `hero_action_bundle`; generate a body-only attack grid and separate slash/impact FX when the attack needs a wide arc
- `"make a healer npc"` -> `npc` + `single_asset`, `role=healer`
- `"make a healer npc walk sheet"` -> `npc` + `walk`
- `"make a boss idle"` -> `creature` + `idle`; prefer 8-direction `8x8`
- `"make a wizard throwing a magic orb"` -> `spell_bundle`
- `"make a fireball projectile"` -> `projectile` + `projectile`; prefer `1x4`
- `"make a hit explosion"` -> `impact` + `impact`; prefer `2x2`
- `"make a summon entrance"` -> `summon` + `cast` or `impact`
- `"make a full fire samurai creature line"` -> `line_bundle`; plan 1-3 forms, then choose sheets per form

## Legacy Compatibility

Keep these mappings working:

- `player_sheet`: 8-direction overworld body action sheet by default; legacy 4-direction only when requested
- `player_walk`: 8-direction walk by default; legacy 2x2 down-facing walk only when requested
- `npc_walk`: 8-direction walk by default; legacy 2x2 down-facing walk only when requested
- `combat`: 2x2 attack + hurt
- `evolution`: legacy concept sheet

## Processor Defaults

- use `shared_scale=true` for any multi-frame sheet unless inconsistent scale is intentional
- use `align=bottom` or `feet` for grounded actors
- use `align=center` for floating effects, projectiles, and detached FX
- use `component_mode=largest` when raw sheets contain detached sparkles or edge debris
- use `component_mode=all` when detached effects are an intentional part of the asset silhouette
- for body-only controllable hero actions, use `component_mode=largest`; for projectile, impact, aura, slash FX, or intentionally detached FX sheets, use `component_mode=all`
- reject fixed-cell hero/player body actions when the body visibly shrinks compared with the accepted idle/run scale; split the wide FX or use a runtime with wider per-action cells and explicit origins
- use a layout guide for prop packs, tileset-like atlases, fixed atlas rows, and non-directional 16-frame VFX-heavy action sequences; avoid making it the default for 8-direction body sheets

## Output Shape

- any sheet mode: transparent sheet + per-frame PNGs + GIF
- `player_sheet`: plus direction strips and eight GIFs
- `single_asset`: cleaned transparent PNG
- `hero_action_bundle`: per-action folders, per-action GIFs, separate projectile/impact assets when needed, and optional assembled engine atlas
- bundles: one output folder per asset inside the bundle root
