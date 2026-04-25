# The Well of Echoes — an example Book of Tales

This repository is a complete, working example book for the [Book of Infinite Tales](https://github.com/RobMcA/Book-of-Infinite-Tales) reader. Fork it, rename it, rewrite the entries, and you have your own book.

> **Disclaimer:** This project contains no official content from *Tales of the Arthurian Knights* (© WizKids). It is a fan tool for creating and sharing original custom books. Book authors are responsible for the content they publish.

## Try this book

In the Book of Infinite Tales app, load:

```
RobMcA/book-of-tales-example
```

(Replace `RobMcA` with your username if you have forked the repo.)

---

## How to make your own book

A book is two JSON files: a **manifest** (`book.json`) and an **entries file** (`entries.json`). The repo must be **public** — files are fetched via `raw.githubusercontent.com`.

### 1. Fork or copy this repo

Click **Fork** on GitHub, or copy `book.json` and `entries.json` into a new public repo.

### 2. Edit `book.json`

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

- `schema` — must be exactly `"book-of-infinite-tales/v1"`.
- `entries` — filename of your entries file, or an inline object of entries keyed by id.

### 3. Edit `entries.json`

This is a JSON array of **entry** objects. Each entry is one numbered passage.

---

## Entry reference

### Minimal entry (no choices — terminal)

```json
{
  "id": "5",
  "title": "The Road Onward",
  "body": "You remount and ride on.\n\nDouble newlines create paragraph breaks."
}
```

| Field | Required | Description |
|---|---|---|
| `id` | ✅ | Unique string. Numeric ids (`"1"`, `"42"`) sort naturally. |
| `title` | — | Optional heading shown above the body. |
| `body` | ✅ | Prose the reader reads aloud. `\n\n` = new paragraph. |
| `romantic` | — | `true` marks entry as romantic content (displayed with ✦). |
| `retinue` | — | `"beside"`, `"nearby"`, or `"absent"` — displayed as a scene note. |
| `choices` | — | Omit (or leave empty) for a terminal entry. |
| `rewards` | — | Reward block shown at the end of the entry. |

---

## Choices

Two kinds of choices exist: **simple** (no roll) and **checked** (skill/renown roll).

### Simple choice — no roll

```json
{
  "label": "Leave the well and ride on.",
  "goto": "5"
}
```

The reader narrates the label and turns directly to the `goto` entry.

### Checked choice — skill or renown roll

```json
{
  "label": "Call down into the well, trusting your faith to guide you.",
  "check": {
    "using": ["Piety", "Wisdom", "Spiritual"],
    "target": 3,
    "success": "2s",
    "failure": "2f"
  }
}
```

The player picks one option from `using`, rolls (or compares), and the reader turns to `success` or `failure` based on the result.

| Field | Description |
|---|---|
| `using` | Array of skills, categories, or renown types the player may choose from (see below). |
| `target` | The difficulty — a fixed number, or `{ "base": 2, "addLocationNumber": true }` for variable. |
| `success` | Entry id to turn to on success. |
| `failure` | Entry id to turn to on failure. |

Both choices support `"romantic": true` to mark optional romantic content.

---

## Skills, categories, and renown

### The 12 skills

| Martial | Spiritual | Courtly | Wilderness |
|---|---|---|---|
| Warfare | Piety | Diplomacy | Nature Lore |
| Sword & Shield | Wisdom | Cunning | Endure Hardship |
| Mounted | Honor | *(+ 2 others)* | *(+ 2 others)* |
| Hunting | Magic | | |

Use the exact strings above in `using` arrays. Capitalisation matters.

### Skill categories

Use `"Martial"`, `"Spiritual"`, `"Courtly"`, or `"Wilderness"` to allow any skill within that category.

### Renown types

Use `"Divinity"`, `"Romance"`, or `"Villainy"` to allow a renown check instead of a skill roll.
For renown checks the player compares their Rank count (no die roll) against the target.

---

## Resolution targets

| Format | Meaning | Example |
|---|---|---|
| A plain number | Fixed difficulty | `3` |
| `{ "base": N, "addLocationNumber": true }` | Variable: base + Location # of current map space (1–6) | `{ "base": 2, "addLocationNumber": true }` |

Variable targets make an encounter harder in dangerous terrain but also reward success more — use them when the terrain should matter.

---

## Rewards

Add a `rewards` block to any resolution entry. All fields are optional; include only what applies. In the reader they display as a styled bracket: **[Gain 2 Destiny | Gain 1 Rank of Divinity]**

```json
"rewards": {
  "destiny": 2,
  "renown": [
    { "type": "Divinity", "delta": 1 },
    { "type": "Villainy", "delta": -1 }
  ],
  "skills": [
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

| Field | Type | Description |
|---|---|---|
| `destiny` | number or `"location_number"` | Destiny Points earned. `"location_number"` = equals the Location # of the current space. |
| `renown` | array of `{type, delta}` | Positive delta = gain ranks; negative = lose ranks. |
| `skills` | array | Specific skill: `{"name": "Piety"}`. Category choice: `{"category": "Spiritual", "count": 1}`. |
| `treasures` | number or string | Number = draw that many at random. String = search for the named card. |
| `statuses` | array of `{action, name}` | `"gain"` → Become *Name*. `"lose"` → remove the status early. |
| `storyToken` | string | Name of the Story Token gained. |
| `movement` | number or `"free"` | Bonus movement spaces after the encounter. |

### Even failures earn something

In the Arthurian tradition, knights grow as much from failure as from success. Every resolution entry — success *and* failure — should give at least a small positive reward alongside any negative consequence.

---

## Retinue presence

The Book of Tales specifies whether the knight's retainers are present. Use the optional `retinue` field:

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

Prefix any choice label with `*` **and** set `"romantic": true` on that choice to mark it as optional romantic content. Players may choose to ignore these choices and any entries marked `"romantic": true`.

```json
{
  "label": "* Lean over the rim and call out a tender greeting.",
  "romantic": true,
  "check": { ... }
}
```

---

## Passage numbering conventions

The official game uses passages **1000–3000** grouped by Age (1xxx = Age 1, 2xxx = Age 2, 3xxx = Age 3). For custom books you can use any string ids you like, but numeric ids sorted naturally are easiest to navigate at the table:

- `"1"`, `"1s"`, `"1f"` — use suffixes for success/failure branches off the same scene
- `"100"` through `"199"` — group related encounters in ranges

---

## Multiple books in one repo

You can host several books in one repository by putting each in its own subdirectory:

```
my-repo/
  golden-age/book.json
  golden-age/entries.json
  grail-quest/book.json
  grail-quest/entries.json
```

Load the second book with: `your-username/my-repo@main/grail-quest`

---

## Committing and sharing

```
git add book.json entries.json
git commit -m "My book: first draft"
git push
```

Share the path `your-username/your-repo` with players. They paste it into the Book of Infinite Tales reader.

---

## License

The content of this example book is released under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) — public domain. Copy any of it freely into your own book. The *Tales of the Arthurian Knights* game system and its components are © WizKids and are not included here.
