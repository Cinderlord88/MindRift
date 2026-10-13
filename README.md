# MindRift

![MindRift Dragon](MindRiftDragon.png)

### A Dynamic Stress & Sanity Engine for AI Dungeon

**Every mind has its breaking point.**

MindRift is a portable JavaScript psychological simulation engine for [AI Dungeon](https://aidungeon.com/). It gives player characters and NPCs persistent **Stress** and **Sanity** scores, then uses the storytelling AI to assess what they experience: danger, trauma, personal fears, moments of calm, recovery, and psychological breaking points.

Rather than forcing a particular plot, MindRift lets psychological consequences develop alongside the story. Characters can struggle, adapt, recover, and change. The engine runs behind the scenes, so players can focus on playing.

> **Because surviving the adventure doesn't mean escaping it unscathed.**

## Features

- **Persistent character psychology** — independent Stress and Sanity scores for characters discovered from Story Cards.
- **Context-sensitive assessment** — the AI evaluates events in the actual narrative, rather than relying on simple keyword matching.
- **Individual vulnerabilities** — each character receives a personal fear or flaw that can trigger direct Sanity loss.
- **Stress reactions** — reaching zero Stress triggers one of 20 temporary reactions and a small Sanity penalty.
- **Evolving derangements** — 25 possible psychological tendencies, acquired as Sanity falls through key thresholds.
- **Dynamic Psychology Story Cards** — persistent tendencies automatically appear in separate, AI-readable profiles; original Character cards stay untouched.
- **Meaningful recovery** — quiet time, deliberate relaxation, personal healing activities, and psychological treatment can restore lost points.
- **Narrative safeguards** — qualification thresholds, cooldowns, evidence checks, and metadata cleanup help protect the story from inaccurate assessments and leaked instructions.
- **Scenario-independent design** — no fixed cast or setting required; suitable for horror, survival, science fiction, fantasy, and more.

## Installation — Copy, Paste, Play

**You do not need to know JavaScript.** MindRift comes as four ready-to-use scripts. You only have to copy each one into the correct box in AI Dungeon.

### Step 1 — Open your scenario

1. Go to [AI Dungeon](https://aidungeon.com/) and open the **scenario you want to edit** (not an ongoing adventure).
2. Open the scenario's **Scripts** editor and **enable scripting** for that scenario.
3. You should see four places to put code: **Library**, **Input Modifier**, **Context Modifier**, and **Output Modifier**.

### Step 2 — Copy each file into the matching box

Do these **one at a time**. Open the file, select **everything** (`Ctrl+A` or `Cmd+A`), **copy** (`Ctrl+C` or `Cmd+C`), click the matching AI Dungeon script box, and **paste** (`Ctrl+V` or `Cmd+V`).

| Open this MindRift file | Copy **all** its text into this AI Dungeon box |
| --- | --- |
| **`01_Library.txt`** | **Library** |
| **`02_Input_Modifier.txt`** | **Input Modifier** |
| **`03_Context_Modifier.txt`** | **Context Modifier** |
| **`04_Output_Modifier.txt`** | **Output Modifier** |

**In plain English:**

- Open **`01_Library.txt`** → copy everything → paste into **Library**.
- Open **`02_Input_Modifier.txt`** → copy everything → paste into **Input Modifier**.
- Open **`03_Context_Modifier.txt`** → copy everything → paste into **Context Modifier**.
- Open **`04_Output_Modifier.txt`** → copy everything → paste into **Output Modifier**.

All four scripts in this repository are **`.txt` files**. They contain JavaScript code, but you do **not** need to rename them or convert them to `.js`. Just copy the text into the matching AI Dungeon box. Don't paste all four files into one box, and don't leave any part of a file out.

### Step 3 — Make sure your characters have Story Cards

MindRift discovers characters from your scenario's **Character** Story Cards. Create or keep one for each character you want tracked, including your player character if you want them tracked. The card's type/category should be **Character** (internally `character` or `characters`). You can use your existing Character cards; no special template is required.

### Step 4 — Save and play

**Save the scenario**, start an adventure from it, and play normally. That's it. You don't have to type special commands, assign points, or manage scores. MindRift works quietly in the background.

**Quick checklist:** Scripting enabled? Four files in four matching boxes? Character Story Cards present? Scenario saved? You're ready.

> **Already have other scripts?** MindRift uses all four script sections. Back up any existing scripts before replacing their contents; simply pasting MindRift over another script may remove that script's functionality.

### Optional: set a character's fear or flaw

By default, MindRift assigns each discovered character a personal vulnerability. To specify one yourself, add a line to that character's Story Card **entry**:

```text
Fear: Fear of being buried alive or trapped underground
```

Or:

```text
Flaw: cannot bear becoming dependent on others to survive
```

`Personal Fear:` and `Weakness:` are also accepted. The assigned fear or flaw, along with the character's scores and recovery activities, persists in script state.

## How the mechanics work

Everyone begins with **100 Stress** and **100 Sanity**. Higher is better. The storytelling AI proposes events, and the JavaScript checks whether they meet the rules before applying any change.

| Mechanic | Effect | Qualification / timing |
| --- | --- | --- |
| **Stress loss** | −1 / −2 / −5 / −10 Stress, based on severity | Score ≥80; 15-turn loss cooldown |
| **Direct Sanity loss** | −10 Sanity for personally confronting a specific fear or flaw | Score ≥90; separate 30-turn cooldown |
| **Calm** | +1 Stress during genuinely safe, low-threat narration | Score ≥80 |
| **Relaxation** | +10 Stress for meaningful deliberate relaxation | Score ≥85; 15-turn bonus cooldown |
| **Personal healing** | Up to +10 Sanity for meaningful engagement in a character's assigned healing activities | Score ≥90; shared 10-turn Sanity-recovery cooldown |
| **Psychological treatment** | Up to +10 Sanity for completing meaningful counseling or care | Score ≥90; same Sanity-recovery cooldown |

Scores are capped at 100. After the initial relaxation bonus, continued qualifying relaxation can still restore +1 Stress on subsequent turns. A crisis can block Stress recovery for the scene even when a Stress or Sanity loss is itself on cooldown.

### Breaking points

When **Stress reaches 0**, a temporary stress reaction is selected. Stress resets to **100**, and the character also loses **5 Sanity**. This automatic Sanity loss is separate from the direct-fear cooldown. The reaction is passed to the storyteller as a possible narrative consequence without dictating the player's choices.

As Sanity falls through **80, 60, 40, 20, and 0**, the character gains a persistent tendency drawn from a table of 25. MindRift creates or updates a separate **Psychology** Story Card so the storyteller can take those changes into account. If Sanity recovers across thresholds, excess tendencies and their profile entries are removed automatically.

### Recovery is part of the story

MindRift isn't intended to be a one-way spiral into madness. A frightening situation may become manageable; former enemies may become allies; meaningful rest and personal breakthroughs can matter. The engine tracks consequences without insisting that characters remain traumatized forever.

## Philosophy and limitations

MindRift is a **best-effort narrative system**, not a deterministic rules engine. Different AI models may sometimes miss events, overestimate danger, misjudge safety, cite imperfect evidence, or produce malformed assessment text. MindRift uses validation, cooldowns, and output cleanup to reduce the impact of those mistakes, but no script can guarantee perfect judgments or perfectly clean narration every turn.

A few intentional design choices:

- **The story comes first.** Uncertain events are often ignored rather than risking a false penalty or damaging narration.
- **Characters retain agency.** Psychological profiles describe tendencies, not mandatory actions or player decisions.
- **Minor offscreen Stress recovery can happen.** Calm and relaxation do not require the same explicit character-name evidence as major Sanity recovery.
- **No manual scorekeeping is required.** Scores live in AI Dungeon's persistent script state, not in rewritten Character Story Cards.

The fictional derangements are storytelling devices, **not representations of medical diagnoses**.

## Troubleshooting

**Nothing seems to happen.** Confirm that scripting is enabled, the four sections are installed in the right places, and the characters have recognized Character Story Cards. MindRift ordinarily runs silently; lack of visible text isn't necessarily a failure.

**I saw internal assessment text in the story.** The Output Modifier includes cleanup for common metadata leaks, but some AI generations may still expose unusual formatting. If the text is in the visible adventure history, edit it out where possible so the model doesn't repeat it.

**An event was missed or scored unexpectedly.** AI evaluation is subjective. The engine intentionally prioritizes avoiding false deductions over detecting every possible event.

**I need debug logs.** Set `VERBOSE_DIAGNOSTICS = true` in the Context or Output Modifier, or `MINDRIFT_LIBRARY_VERBOSE = true` in Library. Switch those values back to `false` for ordinary play.

**I'm upgrading from an older test build.** Back up the scenario first. v1.0 uses live persistent scores and doesn't migrate historical shadow-preview values. Use the full matching v1.0 script set; don't mix release files with older test versions.

## Release status

**MindRift v1.0** is based on the playtested v8.6 engine, with quieter diagnostics and release documentation. The package passed local JavaScript smoke tests covering core score changes, recovery safeguards, Stress outbursts, derangements, Psychology Story Cards, and metadata filtering. The exact release package should still be checked in AI Dungeon before publishing or updating a live adventure.

---

**MindRift — Every mind has its breaking point.**
