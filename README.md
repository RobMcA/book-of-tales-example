# The Well of Echoes — an example Book of Tales

This repository is a complete, working example book for the [Book of Infinite Tales](https://github.com/RobMcA/Book-of-Infinite-Tales) reader. Fork it, rename it, rewrite the entries, and you have your own book.

> **Disclaimer:** This project contains no official content from *Tales of the Arthurian Knights* (© WizKids). It is a fan tool for creating and sharing original custom books. Book authors are responsible for the content they publish.

## Try this book

In the Book of Infinite Tales app, paste:

```
RobMcA/book-of-tales-example
```

(Replace with your own fork's path once you've forked it.)

---

## How an encounter is structured

In the game, a standard encounter flows through **two passages** (both with 4-digit numbers):

1. **Response passage** — the scene is described. The player is offered several **narrative responses**. Each response points to a *different* passage number.
2. **Resolution passage** (one per response) — more narrative. The player is offered one or more **skill options** to resolve the encounter. They commit to a skill, learn the target number, roll, and read the matching success or failure result (with rewards) inline.

Some simple responses skip the skill check and go straight to a terminal **result passage**. That's fine — not every choice is a roll.

```
         ┌──────────────────────┐
         │  Response passage    │
         │  "You come upon..."  │
         └──────┬───────┬───────┘
                │       │
    ┌───────────┘       └───────────────┐
    ▼                                   ▼
┌────────────────────┐            ┌────────────────┐
│ Resolution passage │            │ Result passage │
│  Use Piety (3):    │            │  "You ride on" │
│   ✓ …  ✗ …         │            │  (terminal)    │
│  Use Wisdom (4):   │            └────────────────┘
│   ✓ …  ✗ …         │
└────────────────────┘
```

---

## Passage numbering conventions

The official Book of Tales composes passage numbers from game state. You don't *have* to follow this scheme, but doing so makes your book feel right and lets it slot into existing components (encounter cards, feature cards, terrain, Ages).

| Encounter type | Number formula | Example |
|---|---|---|
| **Age start** | `1000`, `2000`, `3000` | `1000` is read when Age 1 begins |
| **Milieu** (terrain-based) | *Age-specific milieu base* + 2-digit terrain offset | Age 2 Mountain milieu: `2200 + 34` = `2234` |
| **Character** | 100-multiple (the character's base) + 2-digit Feature card offset | Giant (`1200`) + Amorous (`05`) = `1205` |
| **Location** | Pre-printed 4-digit number | `1500` (any age uses this same number) |

Conventions authors typically follow:
- `1xxx` → Age 1 (Golden Age)
- `2xxx` → Age 2 (Quest of the Holy Grail)
- `3xxx` → Age 3 (Final Wars)
- `xx00` round-hundreds → Character cards (each character "owns" a 100-block)
- `x2xx`–`x3xx` → Milieu passages (per-age base differs)
- Custom locations → anywhere in the range that doesn't collide

For custom books, use any string ids you want — but numeric ids sort naturally in the reader's entry picker. A common convention is to give each response option's resolution passage a consecutive number (`1234` → `1235`, `1236`, …).

---

## The two files

### `book.json` (manifest)

```json
{
  "schema": "book-of-infinite-tales/v1",
  "title": "Your Book's Title",
  "author": "Your Name",
  "version": "1.0.0",
  "description": "One sentence about your book.",
  "entries": "entries.json"
}
```

### `entries.json` (the passages)

A JSON array of entries. Each entry is one passage.

---

## Entry reference

Every entry has an `id` and `body`. Beyond that, what fields you use determines the passage's **type**:

| Passage type | Required fields |
|---|---|
| **Response passage** | `body`, `responses[]` |
| **Resolution passage** | `body`, `resolutions[]` |
| **Result passage** (terminal) | `body`, optional `rewards` |
| **Unusual encounter** | Any mix — `responses` + `resolutions` + `rewards` as needed |

### Shared fields

| Field | Description |
|---|---|
| `id` | Unique string. Numeric ids (`"1234"`) sort naturally in the picker. |
| `title` | Optional heading shown above the body. |
| `body` | Prose read aloud. `\n\n` separates paragraphs. |
| `romantic` | `true` marks the entry itself as romantic (✦ badge in reader). |
| `retinue` | `"beside"`, `"nearby"`, or `"absent"` — scene-setting note. |
| `goto` | Optional continuation to another entry after this passage. |

---

## Response passages

A **response passage** offers narrative choices. Each response points to another passage (usually a resolution passage).

```json
{
  "id": "1500",
  "title": "The Well of Echoes",
  "body": "The road bends through gorse and thorn and ends at an old well...",
  "responses": [
    {
      "label": "You may call down into the well.",
      "goto": "1501"
    },
    {
      "label": "You may draw water with the cup.",
      "goto": "1502"
    },
    {
      "label": "* You may lean close and greet the voice tenderly.",
      "romantic": true,
      "goto": "1503"
    },
    {
      "label": "You may leave and ride on.",
      "goto": "1504"
    }
  ]
}
```

Responses never have skill checks — that happens on the resolution passage they point to. Use a response passage for pure narrative branching.

---

## Resolution passages

A **resolution passage** is where the skill check happens. It has one or more `resolutions` — each is one way to resolve the encounter.

```json
{
  "id": "1501",
  "title": "A Voice from Below",
  "body": "You lean over the stone and call. Your voice returns — then again, changed: 'Who asks?'",
  "resolutions": [
    {
      "label": "answer with your true name and let faith guide you",
      "using": ["Piety", "Honor"],
      "target": 3,
      "success": {
        "body": "A face looks up at you that is almost your own, but kinder...",
        "rewards": {
          "destiny": 2,
          "renown": [{ "type": "Divinity", "delta": 1 }]
        }
      },
      "failure": {
        "body": "You cannot find the words. The water stills and loses interest.",
        "rewards": {
          "destiny": 1,
          "renown": [{ "type": "Divinity", "delta": -1 }]
        }
      }
    }
  ]
}
```

### Resolution option fields

| Field | Description |
|---|---|
| `label` | Optional — narrative action the skill represents ("answer with your true name"). |
| `using` | Array of skill names, categories, or renown types. The player picks one of these to use. |
| `target` | The difficulty (see below). |
| `romantic` | `true` marks this resolution option as romantic. |
| `success` | Inline outcome (body + rewards) for a successful roll. |
| `failure` | Inline outcome for a failed roll. |

Both `success` and `failure` are objects with:

| Field | Description |
|---|---|
| `body` | Prose read when this outcome occurs. |
| `rewards` | Structured reward block (see below). |
| `goto` | Optional continuation to another passage. |

### Skills, categories, and renown

The 12 canonical skill names:

| Martial | Spiritual | Courtly | Wilderness |
|---|---|---|---|
| Warfare | Piety | Diplomacy | Nature Lore |
| Sword & Shield | Wisdom | Cunning | Endure Hardship |
| Mounted | Honor | *(+ 2 more)* | *(+ 2 more)* |
| Hunting | Magic | | |

Use exact strings (capitalisation matters). You can also use:
- A category — `"Martial"`, `"Spiritual"`, `"Courtly"`, or `"Wilderness"` — to allow any skill within it
- A renown type — `"Divinity"`, `"Romance"`, or `"Villainy"` — for a renown check (no die roll, compares Rank count to target)

### Resolution targets

| Format | Meaning | Example |
|---|---|---|
| A plain number | Fixed difficulty | `3` |
| `{ "base": N, "addLocationNumber": true }` | Variable: `N + Location #` of current map space (1–6) | `{ "base": 2, "addLocationNumber": true }` |

Variable targets make dangerous terrain harder but also reward success more — the game pattern for milieu passages is often `base + Location #`.

---

## Rewards

Rewards render as an italic bracket: **[Gain 2 Destiny | Gain 1 Rank of Divinity]**

```json
"rewards": {
  "destiny": 2,
  "renown": [
    { "type": "Divinity", "delta": 1 },
    { "type": "Villainy", "delta": -1 }
  ],
  "skills": [
    { "name": "Piety" },
    { "category": "Spiritual", "count": 1 }
  ],
  "treasures": 1,
  "statuses": [
    { "action": "gain", "name": "Accompanied" },
    { "action": "lose", "name": "Obsessed" }
  ],
  "storyToken": "The Silver Hair",
  "movement": 2
}
```

| Field | Type | Meaning |
|---|---|---|
| `destiny` | number or `"location_number"` | Destiny Points. `"location_number"` = equals the current Location #. |
| `renown` | array of `{type, delta}` | Positive = gain, negative = lose. |
| `skills` | array | Specific: `{"name": "Piety"}`. Category choice: `{"category": "Spiritual", "count": 1}`. |
| `treasures` | number or string | Number = draw that many random cards. String = named card. |
| `statuses` | array of `{action, name}` | `"gain"` → Become *Name*. `"lose"` → remove early. |
| `storyToken` | string | Name of the Story Token gained. |
| `movement` | number or `"free"` | Bonus map movement. |

### Even failures earn something

In the Arthurian tradition, knights grow as much from failure as from success. Every outcome — success *and* failure — should give at least a small positive reward alongside any negative consequence.

---

## Retinue presence

Some passages specify whether the knight's retainers are present:

```json
"retinue": "beside"
```

| Value | Meaning |
|---|---|
| `"beside"` | Retainers are right there with the knight |
| `"nearby"` | Retainers are close but not at the knight's side |
| `"absent"` | Knight is scouting ahead; retainers are elsewhere |

---

## Romantic content

Prefix any response label with `*` **and** set `"romantic": true` on that response (or resolution, or whole entry) to mark it as optional romantic content. The reader renders it in a distinctive colour, and players may choose to skip such content.

```json
{
  "label": "* You may greet the voice as one greets a beloved.",
  "romantic": true,
  "goto": "1503"
}
```

---

## Unusual encounters

Some encounters don't follow the standard response → resolution → result flow. For those, just mix fields as needed on a single entry:

- A response passage that goes straight to a result with no skill needed → just use `responses` with `goto` pointing to simple result entries.
- A passage that offers skill options directly without a prior narrative branch → skip the response passage and put `resolutions` on the first passage.
- A passage with a reward block and a `goto` continuation → use `rewards` plus `goto`.

---

## Committing and sharing

```
git add book.json entries.json
git commit -m "My book: first draft"
git push
```

Share the path `your-username/your-repo` with players. They paste it into the Book of Infinite Tales reader.

### Multiple books in one repo

Put each in its own subdirectory:

```
my-repo/
  golden-age/book.json
  golden-age/entries.json
  grail-quest/book.json
  grail-quest/entries.json
```

Load the second with: `your-username/my-repo@main/grail-quest`

---

## License

The content of this example book is released under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) — public domain. Copy any of it freely into your own book. The *Tales of the Arthurian Knights* game system and its components are © WizKids and are not included here.
