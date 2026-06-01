# Tyrant Unleashed Card Editor — v1.0

A standalone, offline-capable HTML card editor for creating and editing Tyrant Unleashed card XMLs. No server, no install — open the file in any browser.

---

## Features

### Card Structure
- **Card Types** — Assault, Structure, Commander
  - Structure & Commander lock Attack to 0 (greyed out)
  - Commander gets a 4th Fusion (F3) with a single level
- **Rarity-aware level structure** — automatically sets the correct number of fusions and levels per fusion:
  | Rarity | Fusions | Levels per Fusion |
  |--------|---------|-------------------|
  | Common (1) | 1 | 3 |
  | Rare (2) | 3 | 4 |
  | Epic–Mythic (3–6) | 3 | 6 |
  | Commander | 4 | 6 / 6 / 6 / 1 |
- **Card-wide settings** (apply to all fusions & levels): Rarity, Cost, Faction, Card Set, Card Type, Dominion type
- **Dominion types** — Alpha Dominion / Nexus Dominion buttons (auto-sets Card Set to 8500)

### ID Management
- **Live next-ID suggestion** — fetches `cards_section_21.xml` from `mobile.tyrantonline.com` at startup to suggest the next free ID; falls back to the embedded ID database if offline
- **Auto-fill IDs** — enter the base ID at Fusion 0 / Level 1 → all 18 (or fewer) IDs filled sequentially (+1 per step)
- **Collision detection** — all generated IDs checked against 55,548 known card IDs; conflicts shown as warnings with card name and location (e.g. "F1 Lvl 3")

### Skills
- **50 skills** — full dropdown, alphabetically sorted
- **Trigger buttons** — On Play / On Attacked / On Death, only shown where confirmed in card XMLs
  - Cooldown auto-dimmed when trigger is active
- **N / All mutual exclusion** — for the 6 skills that have both `n` and `all` in `skills_set.xml` (enhance, jam, overload, evolve, enrage, entrap): activating All clears and disables Count
- **Skill descriptions** — live description from `skills_set.xml` shown under each skill, adapts to All / Faction / both variants
- **Copy skills to ALL levels & fusions** — one button copies current level's skills everywhere
- **Summon auto-fill** — enter base summon card_id for F0L1; Fill all button auto-calculates:
  - Single-fusion: `base + (level - 1)`
  - Multi-fusion: `base + (fusion × 6) + (level - 1)`

### Warnings
- **Stat regression** — orange highlight + warning if Attack/Health/Skill value drops vs previous level
- **>3 skills** — warning when more than 3 skills are present (XML export still allowed; some cards have more)
- **Lore violations**:
  - 🚫 Never: `disease` on Imperial/Righteous, `leech` on Imperial, `venom` on Imperial/Righteous
  - ⚠ Rare: full list including faction exceptions (e.g. mend is fine on Progenitor)
- **Very rare skills** — Flying and Rush always trigger a warning
- **Rupture** — always flagged as hidden skill (only obtainable via Evolve Pierce→Rupture)

### Random Stats Generator
- Enter min values (F0 Lvl1) and max values (F2 Lvl6 / last level)
- Generates monotonically increasing Attack, Health, and Skill Strength/Count values
- Cooldown generates monotonically decreasing values
- Random distribution uses stars-and-bars across all intervals
- Skill values propagated to all levels/fusions before applying random sequence
- All fields use `−` / input / `+` controls (no browser spinners)

### Card Preview
- Live card preview with rarity-based gradient frame (6 rarity tiers)
- Faction icon centered between Attack and HP in stats bar
- Cost icon (0–4+) in cost badge
- Fusion tabs show fusion-level icon
- Card art upload with instant preview
- Up to 3 skills shown with descriptions

### Asset Bundle Auto-Resolve
- **5,066 picture → asset_bundle mappings** embedded (from all 21 card XMLs + `tu_data.json`)
- Auto-filled when an image is uploaded (matched by filename without extension)

### XML Export
- Exports all fusions and levels in correct TU XML format
- `trigger` attribute placed before `id` per game format
- `n` omitted when `all` is active
- `c` (cooldown) omitted when trigger is active
- `<picture>` and `<asset_bundle>` only included when set
- Predecessor name comment on F1+ units
- Copy to clipboard button

---

## Usage

1. Open `tyrant_card_editor.html` in any modern browser (Firefox, Chrome, Edge)
2. The editor fetches the latest ID suggestion from the live server on load (requires internet; falls back gracefully offline)
3. Fill in card details in the **Card-wide** block at the top
4. Use **Fusion** and **Level** tabs to navigate
5. Add skills, adjust stats, upload card art
6. Copy the XML output and paste into your card database file

---

## Data Sources

All reference data is embedded directly in the HTML file — no external files needed at runtime:

| Data | Source | Entries |
|------|--------|---------|
| Picture → Asset Bundle map | `cards_section_1–21.xml` + `tu_data.json` | 5,066 |
| ID → Card name map | `cards_section_1–21.xml` + `tu_data.json` | 55,548 |
| Skill definitions & descriptions | `skills_set.xml` | 50 skills |
| Trigger map | `cards_section_1–21.xml` + `tu_data.json` | 18 trigger-capable skills |
| Lore rules | Manual + XML analysis | — |
| Faction / Cost / Fusion icons | Game assets (base64 embedded) | 6 + 5 + 3 |
| Rarity icons | Game assets (base64 embedded) | 6 |

---

## Technical Notes

- **Single file, ~987 KB** — fully self-contained HTML/CSS/JS, no dependencies, no build step
- Live ID check: `GET https://mobile.tyrantonline.com/assets/cards_section_21.xml`
- ID lookup uses delta-encoded sorted array + deduplicated name index for compact storage
- Fusion structure rebuilds automatically when Card Type or Rarity changes

---

## Version History

### v1.0
Initial release. Full card editor with all features listed above.
