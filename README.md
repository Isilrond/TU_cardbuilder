# Tyrant Unleashed Card Editor — v46

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
- **Base Fusion mode** — checkbox-based alternative fusion structure: F2 only, 6 levels, Card Set 2500
- **Card-wide settings** (apply to all fusions & levels): Rarity, Cost, Faction, Card Set, Card Type, Dominion type
- **Dominion types** — `''` / Alpha Dominion / Nexus Dominion buttons (auto-sets Card Set to 8500)

### ID Management
- **Live next-ID suggestion** — fetches `cards_section_21.xml` from `mobile.tyrantonline.com` at startup to suggest the next free ID; falls back to the embedded ID database if offline
- **Auto-fill IDs** — enter the base ID at Fusion 0 / Level 1 → all 18 (or fewer) IDs filled sequentially (+1 per step)
- **Collision detection** — all generated IDs checked against 55,548 known card IDs; conflicts shown as warnings with card name and location (e.g. "F1 Lvl 3")
- **Default resource name** — the name of the default recipe resource is displayed next to the ID field for reference

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

### Recipe Resources Panel
Generates the recipe resource list required for card upgrade XMLs.

- **Default Mode** — automatically derives all recipe resources from the card's fusion/level structure:
  - Level upgrades use 2× the previous level's card as resource
  - Fusion crossovers use 2× the last level of the preceding fusion
- **Custom Resources Mode** — allows specifying individual resources for fusion entry points (F1L1, F2L1) instead of using the automatic defaults
- **Resource dropdown** — pre-populated with common resources; Vindicator Reactor is the first selectable option
- **Manual ID entry** — text-type input (no browser spinner arrows) for entering resource IDs by hand
- Commander cards are excluded from resource group selections

### XML Import Panel
Parses existing TU card XML and populates the editor UI.

- Accepts `<unit>...</unit>` blocks (paste or upload)
- Auto-detects card type from the first `<unit>` element (`type` attribute: 1 = Assault, 2 = Structure, 3 = Commander)
- Reads Rarity, all stats, and Skills including Trigger attributes
- Processes `<upgrade>` tags as subsequent levels within a fusion
- Reconstructs the full `fusionData` structure based on detected rarity and card type
- Transfers all parsed fields into the editor UI

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

1. Open `tyrant_card_editor_v46.html` in any modern browser (Firefox, Chrome, Edge)
2. The editor fetches the latest ID suggestion from the live server on load (requires internet; falls back gracefully offline)
3. Fill in card details in the **Card-wide** block at the top
4. Use **Fusion** and **Level** tabs to navigate
5. Add skills, adjust stats, upload card art
6. Optionally use **XML Import** to load an existing card's XML into the editor
7. Configure recipe resources in the **Recipe Resources** panel
8. Copy the XML output and paste into your card database file

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

- **Single file, ~1.1 MB** — fully self-contained HTML/CSS/JS, no dependencies, no build step
- Live ID check: `GET https://mobile.tyrantonline.com/assets/cards_section_21.xml`
- ID lookup uses delta-encoded sorted array + deduplicated name index for compact storage
- Fusion structure rebuilds automatically when Card Type or Rarity changes
- Resource inputs use `type="text"` to suppress browser spinner arrows

---

## Version History

### v46
- **Base Fusion mode** — new checkbox-based F2-only fusion structure (Card Set 2500)
- **Recipe Resources panel** — automatic and custom recipe resource generation for card upgrade XMLs
- **XML Import panel** — paste existing `<unit>` XML to populate the editor automatically
- **Default resource name** shown next to the ID field
- **Commander cards** excluded from recipe resource group selections
- **CSS fix** — removed spinner arrows from resource ID inputs (switched to `type="text"`)

### v1.0
Initial release. Full card editor with all features listed above.
