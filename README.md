# MindRift

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

## Installation

MindRift uses all four sections of AI Dungeon's scenario scripting system. **Scripting must be enabled for the scenario.**

1. Open your scenario's **Scripts** editor.
2. Copy each file into its corresponding section:

   | Repository file | AI Dungeon section |
   | --- | --- |
   | `01_Library.js` | **Library** |
   | `02_Input_Modifier.js` | **Input Modifier** |
   | `03_Context_Modifier.js` | **Context Modifier** |
   | `04_Output_Modifier.js` | **Output Modifier** |

   If your download contains the `.txt` versions instead, they contain the same paste-ready script sections.

3. Make **Character** Story Cards for the player character and any NPCs you want tracked. MindRift recognizes internal Story Card types `character` and `characters`.
4. Save your scenario and start playing normally. You don't have to enter commands, track points, or write assessment tags yourself.

**Important:** Paste each **entire** file into its matching script section. Don't combine the scripts in one field or mix files from different MindRift versions.

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
