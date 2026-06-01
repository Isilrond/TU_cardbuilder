# Tyrant Unleashed Card Editor — v1.0

A standalone, offline-capable HTML card editor for creating and editing Tyrant Unleashed card XMLs. No server, no install — open the file in any browser.

---

## Features

### Card Structure

- **Card Types** — Assault, Structure, Commander
  - Structure & Commander lock Attack to 0 (greyed out)
  - Commander gets a 4th Fusion (F3) with a single level

- **Rarity-aware level structure** — automatically sets the correct number of fusions and levels:

  | Rarity | Fusions | Levels per Fusion |
  |--------|---------|-------------------|
  | Common (1) | 1 | 3 |
  | Rare (2) | 3 | 4 |
  | Epic–Mythic (3–6) | 3 | 6 |
  | Commander | 4 | 6 / 6 / 6 / 1 |

- **Base Fusion checkbox** — marks card as Base Fusion (set 2500, F2 only, 6 levels)

- **Card-wide settings** (apply to all fusions & levels): Rarity, Cost, Faction, Card Set, Card Type, Base Fusion, Dominion type

- **Dominion types** — Alpha Dominion / Nexus Dominion buttons (auto-sets Card Set to 8500)

---

### ID Management

- **Live next-ID suggestion** — fetches `cards_section_21.xml` from `mobile.tyrantonline.com` at startup; falls back to embedded ID database if offline
- **Auto-fill IDs** — enter base ID at F0L1 → all IDs filled sequentially (+1 per step across all levels and fusions)
- **ID field** — always shows the current level's generated ID; editable only on F0L1; readonly on other levels
- **Collision detection** — all generated IDs checked against 55,548 known card IDs; conflicts shown as warnings with card name and position

---

### Skills

- **50 skills** — full dropdown, alphabetically sorted
- **Trigger buttons** — On Play / On Attacked / On Death, only where confirmed in card XMLs; Cooldown auto-dimmed when trigger is active
- **N / All mutual exclusion** — for the 6 skills confirmed in `skills_set.xml` (enhance, jam, overload, evolve, enrage, entrap): activating All clears and disables Count
- **Skill descriptions** — live from `skills_set.xml`, adapts to All / Faction / both variants
- **Copy skills to ALL levels & fusions** — one button
- **Summon auto-fill** — ⟳ Fill all button with two modes:
  - **Normal card** — verified offset table (confirmed from Wisp family, cards_section_21):

    | Fusion | L1 | L2 | L3 | L4 | L5 | L6 |
    |--------|----|----|----|----|----|----|
    | F0 | +0 | +0 | +0 | +0 | +1 | +1 |
    | F1 | +1 | +1 | +2 | +2 | +3 | +3 |
    | F2 | +3 | +3 | +3 | +4 | +4 | +5 |

  - **Base Fusion** — F2L1=base+0, F2L2=base+1, ..., F2L6=base+5
  - If base summon ID field is empty: auto-calculates as max(card IDs) + 1
  - Info label shows which summoned level and ID each position uses

---

### Warnings

- **Stat regression** — orange highlight if Attack/Health/Skill value drops vs previous level
- **>3 skills** — warning when more than 3 skills present (XML export still allowed)
- **Lore violations** — 🚫 Never / ⚠ Rare per faction, including Progenitor exceptions
- **Very rare skills** — Flying and Rush always warn
- **Rupture** — flagged as hidden skill (only via Evolve Pierce→Rupture)
- **ID collisions** — warns if any generated ID is already used

---

### Random Stats Generator

- Enter min (F0L1) and max (F2L6 / last level) values
- Generates monotonically increasing Attack, Health, Skill Strength / Count
- Cooldown generates monotonically decreasing values
- Skills are propagated to all levels/fusions before applying the random sequence
- N derived from actual fusion structure (works for Common / Rare / Commander)
- All input fields use − / input / + controls

---

### Card Preview

- Live preview with rarity-based gradient frame (6 tiers)
- Faction icon centered between Attack and HP
- Cost icon (0–4+) in cost badge
- Fusion tabs show fusion-level icon
- Card art upload with instant preview
- Up to 3 skills shown with descriptions

---

### XML Export

- Exports all fusions and levels in correct TU format
- `trigger` before `id`, `n` omitted when `all` active, `c` omitted when trigger active
- `<picture>` and `<asset_bundle>` only when set
- Predecessor name comment on F1+ units
- Copy to clipboard button

### Recipe Output

- Auto-generates standard upgrade recipes (Lx = L(x-1) × 2) when no resources specified
- Custom recipes: add any number of resource entries (Card ID + quantity) with name lookup
- Copy to clipboard button

---

### Asset Bundle Auto-Resolve

- **5,066 picture → asset_bundle mappings** embedded (from all 21 card XMLs + `tu_data.json`)
- Auto-filled on image upload

---

## Usage

1. Open `tyrant_card_editor.html` in any modern browser
2. The editor fetches the latest next-ID suggestion from the live server on load (falls back offline)
3. Fill in card details in the **Card-wide** block at the top
4. Use **Fusion** and **Level** tabs to navigate
5. Add skills, adjust stats, upload card art
6. Copy the **XML output** and/or **Recipe output** into your card database files

---

## Data Sources

All reference data embedded in the HTML — no external files needed at runtime:

| Data | Source | Entries |
|------|--------|---------|
| Picture → Asset Bundle | `cards_section_1–21.xml` + `tu_data.json` | 5,066 |
| ID → Card name | `cards_section_1–21.xml` + `tu_data.json` | 55,548 |
| Skill definitions & descriptions | `skills_set.xml` | 50 skills |
| Trigger map | XMLs + `tu_data.json` | 18 skills |
| Lore rules | Manual + XML analysis | — |
| Summon offset table | Verified from Wisp family (cards_section_21) | 18 positions → 6 IDs |
| Faction / Cost / Fusion icons | Game assets (base64) | 6 + 5 + 3 |
| Rarity icons | Game assets (base64) | 6 |

---

## Technical Notes

- **Single file, ~995 KB** — self-contained HTML/CSS/JS, no dependencies, no build step
- Live ID check: `GET https://mobile.tyrantonline.com/assets/cards_section_21.xml`
- ID lookup uses delta-encoded sorted array + deduplicated name index
- Fusion structure rebuilds automatically on Card Type or Rarity change

---

## Version History

### v1.0
Initial release.

**Card editor:** Card types (Assault/Structure/Commander), rarity-aware fusion structure, Base Fusion mode, Dominion types (Alpha/Nexus), card-wide settings block.

**ID system:** Live next-ID fetch from server, auto-fill all levels/fusions, per-level ID display, collision detection against 55,548 known IDs.

**Skills:** 50 skills, triggers verified from XMLs, N/All mutual exclusion, live descriptions from `skills_set.xml`, copy-all button, summon auto-fill with verified offset rule.

**Random stats:** Monotone generation for Attack/HP/Skill attrs, skill propagation across all levels/fusions, dynamic N from fusion structure.

**Assets:** 5,066 AB mappings, 6 faction icons, 5 cost icons, 3 fusion icons, 6 rarity icons — all base64 embedded.

**Recipe output:** Auto upgrade recipes + custom resource entries with ID name lookup.

**XML export:** Correct TU format, trigger/all/cooldown rules, predecessor comments.
