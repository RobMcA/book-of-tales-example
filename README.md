# Book of Tales Examples

These are example Books of Tales compatible with the [Book of Infinite Tales](https://book-of-infinite-tales.github.io) reader for use with the board game *Tales of the Arthurian Knights*.

To load these examples, open the reader and enter:

```
RobMcA/book-of-tales-example
```

---

## What's in this collection

### La Morte d'Arthur

An AI-generated Book of Tales inspired by Sir Thomas Malory's *Le Morte d'Arthur*. It provides new encounters, passages, and resolutions for all three game ages:

- **Golden Age of Camelot** — Camelot is young and the fellowship still discovering what it means to be worthy of itself.
- **Quest of the Holy Grail** — the court fractures under the Grail's terrible light; every crossroads holds a test the sword cannot pass.
- **Final Wars of Britain** — Mordred's shadow lies across the land; ride with what honour you have left.

The book covers each age's opening passage, character encounters with branching choices and skill resolutions, location visits, status encounters (imprisonment, pursuit), and a closing epilogue.

### The `aiGenerated` flag

`La Morte d'Arthur` sets `"aiGenerated": true` in its `book.json`. The reader app surfaces this flag to players so they know the prose was written by an AI model rather than hand-authored.

### `tales-of-the-arthurian-knights-components.json`

This file at the repo root defines all the game components shared across books in this collection: the three ages, six terrains, encounter features, twelve character types, nine milieus, twelve named locations, twenty-four quests, eighteen treasures, renown types, twelve skills, thirty story tokens, and all statuses. Each `book.json` references it via its `components` field so the definitions live in one place rather than being duplicated per book.

---

## Creating your own books

To create your own collection, fork the [Book of Tales Template](https://github.com/RobMcA/Book-of-Tales-Template). It contains:

- Setup and book-creation prompts for Claude
- A complete [format reference](https://github.com/RobMcA/Book-of-Tales-Template/blob/main/docs/book_format.md) covering every field in `book.json` and `components.json`
- Helper scripts and an example book to get you started
