[README.md](https://github.com/user-attachments/files/27223689/README.md)
# Pivot RPG — Foundry VTT System

A Foundry VTT v12 system for the Pivot RPG. Built step by step.

## What's in v0.10.0

**20 skills + level-driven skill points + d100 skill checks.**

### Resolution
Skill checks roll a single **d100 ≤ target**. Same low-roll engine as attacks. Lower = better.

```
target = anchorStat + ranks + difficultyMod + talentBonus
• Natural 1   = critical success
• Natural 100 = critical failure
```

### Skill points
Each character earns **+5 skill points per level**. L1 = 5 points, L20 = 100. Per-skill rank cap = `floor(level × 2.5)`, so an L1 character maxes any single skill at 2 ranks; an L20 character can stack 50 ranks into one skill.

Use the **+ / −** buttons next to each skill to spend ranks. The Skills header shows current spend / budget and the rank cap.

### The 20-skill list
Grouped into three sections on the sheet:

**Physical** — Athletics (Vigor/Grace), Endurance (Vigor), Acrobatics (Grace), Stealth (Grace/Instinct), Sleight of Hand (Grace), Ride / Pilot (Grace/Instinct)

**Knowledge** — Perception (Instinct), Survival (Instinct/Vigor), Tracking (Instinct), Arcana (Theory), Investigation (Theory/Instinct), Medicine (Theory/Grace), Engineering (Theory), History / Lore (Theory/Presence)

**Social** — Intimidation (Vigor/Presence), Animal Handling (Instinct/Presence), Persuasion (Presence), Deception (Presence/Grace), Performance (Presence), Insight (Presence/Instinct)

Skills with two stats listed roll off either — the dialog defaults to whichever is higher.

### Difficulty modifiers
Clicking the 🎲 button opens a dialog where the player picks the stat (if applicable) and the GM picks difficulty:

| Difficulty | Mod |
|---|---|
| Trivial   | +30 |
| Easy      | +20 |
| **Normal**| 0 |
| Hard      | −15 |
| Very Hard | −30 |
| Heroic    | −50 |

The chat card shows the full breakdown (`Theory 75 + 12 ranks −0 Normal` → `≤ 87`) and the outcome.

### Talents that touch skills
The talent effect schema gained 3 new optional keys: `skillBonus.<key>`, `skillBonusByTag.<physical|knowledge|social>`, `skillBonusAll`. Existing talents now grant relevant skill bonuses (e.g. **Acrobat** → +10 Acrobatics / +5 Athletics; **Ambusher** → +10 Stealth / +5 Perception; **Formal Theorem** → +10 Arcana; **Apex** → +5 to every skill).

Three new skill-focused talents (the pack is now **36 talents**):
- **Jack of All Trades** (hybrid, L5): +3 to every skill
- **Polymath** (hybrid, L9, Theory 60): +10 to all Knowledge skills
- **Silver Tongue** (hybrid, L7, Presence 60): +10 to all Social skills

Bonuses appear with a ★ next to the rolled target on the sheet; full breakdown is in the chat card.

## What's in v0.9.0

**20-level progression + lateral talent tree.**

### Levels
- Level 1 = 250 PIVOT-point cap (creation budget). Level 20 = 500 (5×100, max build).
- Per-stat ceiling stays fixed at **100** at every level — only the budget grows.
- Each level above 1 grants **+13 PIVOT points**, with a **+14 bump** at levels 5, 10, and 15. Sum across all 19 ups = +250.
- Each level also grants **+2 HP max** and **+1 Stamina max**, on top of the formula values, so glass-cannon characters don't get one-shot late game.
- Use the **+ / −** buttons next to **Level** on the character sheet. Level changes are blocked by the same point-buy enforcement: lowering level can shrink your cap below current spend and the system will block the change with a notification.

### Talent slots
Talents are picked at levels **3, 5, 7, 9, 11, 13, 15, 17, 19, 20** — 10 picks total over 20 levels.

The character sheet shows `Talents: used / available` and a hint pointing to your next pick level.

### Talent categories (7)
| Category | Theme |
|---|---|
| **melee** | Vigor / Grace strikes |
| **ranged** | Bows, firearms, throwing |
| **magic** | Theory / Presence schools |
| **armor** | Damage soaking, durability |
| **acrobatics** | Speed, mobility, evasion |
| **defense** | Parry / Counter-spell / Intercept tweaks |
| **hybrid** | Cross-cutting universal perks |

### Starter Talents compendium (33)
Drag any talent from the compendium onto a character sheet to acquire it. The system enforces:
- talent-slot cap (matches level)
- minimum level
- (warning-only) PIVOT-stat thresholds and required-talent prereqs

GM users can drop talents that bypass the cap and prereq warnings (useful for narrative / NPC builds).

Highlights from the starter pack:
- **Heavy Strike** (melee, L3, Vigor 50): +3 melee damage
- **Marksman** (ranged, L7, Instinct 70): +2 ranged damage and +5 to crit DC
- **Counterspell Savant** (magic, L9, Theory 60 + Presence 40): all magic weapons gain counter-spell, regardless of stat
- **Acrobat** (acrobatics, L3, Grace 50): −10 vs Dodge defenses, +5 ft move
- **Riposte Master** (melee/defense, L11, Grace 60 + Vigor 40): your Dodge becomes a Riposte
- **Apex** (hybrid, L20, all stats 90): +2 all damage, +5 hit, +5 crit, +1 base die step

### Talent effect schema (data-driven)
Talents store a JSON `effects` block read directly by the engine — no per-talent code is required. Numeric fields stack additively across all equipped talents. Supported keys:

- **Damage / attack**: `bonusDamage` + `bonusDamageOn` (`all` | `hit` | `crit` | `melee` | `ranged` | `magic`), `bonusBaseDie` (steps die size), `bonusBonusDice` (extra dice), `hitBonus`, `critBonus`
- **Defense**: `defenseBonus.{vigor,grace,instinct,theory,presence}`, `interceptRangeBonus.{vigor,theory}`
- **Resources / movement**: `hpBonus`, `staminaBonus`, `tempHPCapMult`, `moveSpeedBonus`, `fallReductionFt`
- **Special flags**: `riposteOnDodge`, `stealthAfterKill`, `counterspellAnyMagic`

Flat damage bonuses double on a crit (mirroring the dice rule). Defense bonuses subtract from the defender's d100 (lower is better).

You can author custom talents from `Items → Create Item → talent`, or use the Starter Talents compendium as a base.

## What's in v0.8.0

### PIVOT STATS
- Five stats: Vigor, Grace, Instinct, Theory, Presence (max 100 each)
- Total point-buy cap **250** (hard cap, enforced at the data layer)

### Derived resources
- **HP max** = floor(Vigor × 0.6)
- **Stamina max** = floor(Vigor × 0.4 + Grace × 0.2)

### Initiative (no rolls)
- GM toolbar in the Combat Tracker: **Party first / Enemies first / Simultaneous**

### Attack mechanic
- **Hit**: 1d100 ≤ actor's primary stat
- **Crit**: only on a hit. 1d100 ≤ actor's secondary stat

### Damage *(new)*
Each weapon now has a fixed **base damage die** (d4–d20). On a successful hit:

- **Bonus dice** = `floor(primary stat ÷ 20)` — same size as the base die
- **Hit damage**  = `(1 + bonus) × baseDie`
- **Crit damage** = `(1 + bonus) × baseDie × 2` (the entire damage roll is doubled)

#### Bonus-dice scaling

| Primary stat | Bonus dice | Total dice on hit | On crit |
|---|---|---|---|
| 0–19   | 0 | 1 | 2 |
| 20–39  | 1 | 2 | 4 |
| 40–59  | 2 | 3 | 6 |
| 60–79  | 3 | 4 | 8 |
| 80–99  | 4 | 5 | 10 |
| 100    | 5 | 6 | 12 |

#### Worked examples
- **Greatsword** (d12 base) wielded by Vigor 60: hit = 4d12, crit = 8d12
- **Rapier** (d8 base) wielded by Grace 80: hit = 5d8, crit = 10d8
- **Throwing Knives** (d6 base) wielded by Instinct 35: hit = 2d6, crit = 4d6

### Starter Weapons Compendium (25 weapons)
Built-in compendium with one weapon for every primary × secondary combo. Drag from the Compendium tab onto a character.

| Primary \ Secondary | Vigor | Grace | Instinct | Theory | Presence |
|---|---|---|---|---|---|
| **Vigor** | Greatsword (d12) | Warhammer (d10) | Spiked Maul (d12) | Siege Crossbow (d10) | Banner Lance (d10) |
| **Grace** | Heavy Saber (d10) | Rapier (d8) | Dueling Daggers (d6) | Spell-Etched Estoc (d8) | Officer's Saber (d8) |
| **Instinct** | Hunting Bow (d10) | Throwing Knives (d6) | Ambusher's Garrote (d10) | Diviner's Sling (d6) | Hawk-Whistle Bow (d8) |
| **Theory** | Repeating Pistol (d8) | Precision Rifle (d12) | Arcane Focus (d10) | Ritual Codex (d12) | Songblade (d8) |
| **Presence** | Champion's Maul (d12) | Dancer's Whip (d8) | Herald's Horn-Pistol (d10) | Speaker's Staff (d10) | Crown Blade (d10) |

Read the matrix as: **row** = stat that controls hit chance and damage scaling, **column** = stat that controls crit chance.

#### Base-die assignments at a glance
- **d12** (5 weapons): Greatsword, Spiked Maul, Precision Rifle, Ritual Codex, Champion's Maul
- **d10** (10 weapons): heavies and most ranged/signature weapons
- **d8** (7 weapons): mid-weight blades, focuses, finesse signatures
- **d6** (3 weapons): Throwing Knives, Dueling Daggers, Diviner's Sling — the lightest/fastest

### Damage application
- Players use Foundry's normal **target** mechanism (T key to target a token) before clicking Attack.
- The chat card shows the targeted token names and ×0, ½, ×1, ×2 buttons (GM only).
- Clicking applies the multiplied damage to all targets at once. HP clamps at 0.
- Temporary HP absorbs damage first, then real HP.
- A receipt message posts to chat showing each target's HP before → after.
- The actual write always runs on a GM client — if a player triggers the apply somehow, it routes to a GM via socket.

### Active Defenses & Interception *(new in v0.7.0)*
After a HIT lands, the attack chat card sprouts a **defense tray** for each target. Each defense is keyed off a PIVOT STAT and rolled as `1d100`.

**Contest rule**: lower raw d100 wins. **Ties go to the defender.** Defenses cost no Stamina.

| Defense | Stat | Self / Intercept | On success |
|---|---|---|---|
| Parry / Riposte | Vigor | Self or intercept within **5 ft** | Negate damage + immediate counterattack with defender's first weapon |
| Dodge | Grace | Self only | Negate damage |
| Stealth | Instinct | Self only | Negate damage + Hidden until defender next acts |
| Counter-spell | Theory | Self or intercept within **15 ft**, only vs. Theory/Presence weapons | Negate damage |
| Meditation | Presence | Self only | Negate damage + tempHP equal to prevented amount, until end of combat |

**Interception**
- Allies (player-side actors) within range of the original target see an **Intercept** button on the card.
- The interceptor rolls in place of the original target. On success, damage is negated. On failure, **damage is redirected to the interceptor**.
- First eligible ally to click wins — each target slot resolves exactly once.

**Visibility**: each user only sees defense buttons for tokens they own and that are eligible (in range / weapon-gate satisfied). Resolved slots show a status badge so everyone knows the outcome.

**Lifecycle**
- Hidden status drops automatically when the defender starts their next turn.
- All temporary HP and lingering Hidden effects are cleared when combat ends.

### NPC Actor Type & Enemy Scale *(new in v0.8.0)*
A second actor type, **NPC**, joins `character`. NPCs use the same PIVOT STAT contest engine but have authored HP (no point-buy) and a **tier preset system** for fast statting.

**Tier scale (calibrated against a level-1 PC):**

| Tier | HP | Hit DC | Crit DC | Base Die | Avg dmg / hit | "Good player hits to kill" |
|---|---|---|---|---|---|---|
| Mook    | 25  | 30 | 20 | d4  | 2.5 | 1–2 (1 rifle shot from a built PC) |
| Soldier | 50  | 45 | 30 | d6  | 3.5 | 2–3 |
| Veteran | 90  | 55 | 40 | d8  | 4.5 | 3–4 |
| Elite   | 150 | 65 | 50 | d10 | 5.5 | 5–6 |
| Boss    | 250 | 75 | 60 | d12 | 6.5 | 9–10 |

A fragile glass-cannon PC (HP ≈9, like Jaune) dies in roughly **3 landed Mook hits** or **2 Boss hits** — they live or die by their dodge. A more durable PC (HP ≈25–30) survives roughly **6 landed Soldier hits** or **4 Boss hits**, matching the design target.

**Starter Enemies compendium (18 NPCs)**
- 3 Mooks: Bandit Cutpurse, Sewer Rat-Mage, Conscript Pikeman
- 4 Soldiers: City Watch Guard, Bandit Crossbowman, Cult Initiate, Hired Knife
- 4 Veterans: Ironclad Sergeant, Hedge Sorcerer, Forest Stalker, Court Duelist
- 4 Elites: Inquisitor, Knight-Captain, Master Assassin, Storm Adept
- 3 Bosses: Warlord of Ash, Archmagister, Nightqueen

Each ships with one weapon item already equipped, and stat values set so the actor's primary/secondary PIVOT STATs match its tier's hit and crit DCs.

**NPC sheet**
- One-click tier presets that set HP and reset to full
- Free-form pivot stat editing (no point-buy ceiling)
- Same weapon list / attack flow as players
- Same defense flow — NPC defenders show up to the GM exactly like player defenders show up to players

## Install

1. Unzip into your Foundry `Data/systems/` folder so the path is `Data/systems/pivot-rpg/system.json`
2. Restart Foundry → New World → pick "Pivot RPG"
3. Foundry auto-migrates the NeDB compendium to LevelDB on first load

## Tuning

- Stat-to-bonus-die ratio: `STAT_PER_BONUS_DIE` in `module/data/item-weapon.mjs` (currently 20)
- HP / Stamina formulas: `FORMULAS` in `module/data/actor-character.mjs`
- Add a new die size: extend `VALID_DICE` in `module/data/item-weapon.mjs`

## Roadmap
- Stamina costs on attacks (action economy + Stamina-gated abilities)
- Armor / damage reduction
- Combat actions: Aim, Dual Action, Suppressing Fire (Stamina-gated)
- Contested skill rolls (sheet-to-sheet stealth-vs-perception duels)
