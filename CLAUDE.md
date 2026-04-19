# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Mod Overview

**Banking Nation Improvements** (`rob.bankingexpansion`) — a Europa Universalis V mod (game version 1.1.5, mod version 1.3) that expands the `banking_country` government reform with new buildings, diplomacy interactions, advances, and a subject type.

## File Layout

```
in_game/common/         # Game-phase content (loaded during play)
  advances/             # Tech/advance unlocks for banking nations
  building_types/       # Custom buildings (foreign, military, trade, cultural)
  country_interactions/ # Diplomatic actions available to banking nations
  diplomatic_costs/     # Cost definitions for banking diplomacy
  government_reforms/   # Sub-reforms available to banking_country nations
  prices/               # Price/cost overrides for banking diplomacy
  production_methods/   # Production methods for custom buildings
  subject_types/        # realm_in_arrears subject type definition

main_menu/common/       # Main-menu-phase content (loaded at startup)
  static_modifiers/     # Persistent country modifiers (e.g. absorbed_foreign_branch)

main_menu/localization/english/  # All English localisation strings
  banking_changes_l_english.yml

.metadata/              # Mod metadata (name, version, game version, description)
```

## Key Design Patterns

**All content is gated on `has_reform = government_reform:banking_country`** — every building `country_potential`, every interaction `potential`/`allow`, and every advance `potential` checks this reform. Never add banking content accessible to non-banking nations without a deliberate reason.

**Building prefix convention:** banking buildings use the prefix `bank_` or `banker_` / `banking_` (e.g. `bank_recruitment`, `banker_shipwright_branch`, `banking_mining_interest`). Foreign buildings use `is_foreign = yes` and `need_good_relation = yes`.

**File prefixes:** Files in `building_types/` and `production_methods/` use the `z_` prefix (e.g. `z_banking_buildings.txt`) so they load after vanilla files. Use this for any file that overrides or follows vanilla content.

**`REPLACE:` and `REPLACE_OR_CREATE:` directives:** Used at the top of a definition to patch vanilla entries (e.g. `REPLACE:banking_office = {`, `REPLACE_OR_CREATE:take_over_loan = {`). Use `REPLACE_OR_CREATE` when the definition may not exist in all game versions; use `REPLACE` when you know it exists.

**`take_over_loan` change:** The vanilla interaction was modified so the actor pays `loan_amount` (principal only) instead of `remaining_debt` (principal + interest), enabling profit on purchased loans.

**`realm_in_arrears` subject type:** A level-2 subject that grants the overlord `can_overlord_recruit_regiments = yes` and `can_overlord_build_ships = yes` — this is the mechanism that lets banking nations access manpower and sailors through vassals.

## Localisation

All strings live in `main_menu/localization/english/banking_changes_l_english.yml` (UTF-8 BOM required by the game). Key categories already present: buildings, advances, modifiers, reforms, country interactions, subject type strings. Add new keys to this file; do not create additional localisation files unless adding a new language.

## Base Game Reference

The vanilla game files live at:

```
C:\Program Files (x86)\Steam\steamapps\common\Europa Universalis V\game\
```

This mirrors the mod's own directory structure (`in_game/common/`, `main_menu/common/`, etc.). When you need to look up an existing modifier, building field, subject type field, scripted trigger, or any vanilla definition to reference or override, find it here first. Key locations:

| What you need | Where to look |
|---|---|
| Country/location modifiers | `main_menu/common/static_modifiers/country.txt`, `location.txt` |
| Vanilla buildings to `REPLACE:` | `in_game/common/building_types/foreign_buildings.txt` |
| Vanilla `take_over_loan` | `in_game/common/country_interactions/` |
| Subject type fields | `in_game/common/subject_types/` |
| Scripted triggers/effects | `in_game/common/scripted_triggers/`, `scripted_effects/` |
| Script values (e.g. cost constants) | `in_game/common/script_values/`, `main_menu/common/script_values/` |
| Production method templates | `in_game/common/production_methods/` |

When proposing a new modifier name, building field, or scope function, verify it exists in the vanilla files before using it — EU5 script will silently ignore or error on unknown keys.

## No Build Step

This is a data-only mod (Jomini/PDS script). There is no compiler, linter, or test runner. Validation is done by loading the mod in-game. The game's error log (`Documents/Paradox Interactive/Europa Universalis V/logs/error.log`) reports script errors on startup.
