# The Well of Echoes — an example Book of Tales

This repository is a complete, working example book for the [Book of Infinite Tales](https://github.com/) reader. Fork it, rename it, rewrite the entries, and you have your own book.

## Try this book

In the Book of Infinite Tales app, load:

```
your-github-username/book-of-tales-example
```

(If you forked or renamed the repo, use that path instead.)

## How to make your own book

You need two files: a `book.json` manifest and an entries file. Both are plain JSON, so any text editor will do.

### 1. Fork or copy this repo

Click **Fork** on GitHub, or copy `book.json` and `entries.json` into a new public repo of your own. The repo must be public — the reader fetches files from `raw.githubusercontent.com` with no authentication.

### 2. Edit `book.json`

This is the manifest. It tells the reader what your book is called and where to find the entries.

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

- `schema` — leave this as `"book-of-infinite-tales/v1"`. It tells the reader what format to expect.
- `title` — shown at the top of the reader.
- `author`, `version`, `description` — optional, but nice to include.
- `entries` — the filename of your entries file (usually `entries.json`). You can also inline the entries directly here as an object, but a separate file is easier to edit.

### 3. Edit `entries.json`

This is the meat of your book. It's a JSON array of **entries**. Each entry is one numbered passage a player can be sent to.

```json
[
  {
    "id": "1",
    "title": "A Short Name for the Entry",
    "body": "The prose a player reads. Use blank lines to start a new paragraph.\n\nLike this.",
    "choices": [
      { "label": "What the player can decide to do.", "goto": "2" },
      { "label": "Another option.", "goto": "4" }
    ]
  }
]
```

Per-field notes:

- **`id`** — any unique string. Most books use numbers (`"1"`, `"2"`, `"42"`) because it matches the feel of the original genre, but you can use anything (`"well-1a"`, `"the-hermit"`). Numeric ids are sorted naturally in the reader's entry picker.
- **`title`** — optional. Shown as a heading.
- **`body`** — the text the player reads. Use `\n\n` (double newline) between paragraphs. Single newlines are ignored.
- **`choices`** — optional. An array of `{ label, goto }` objects. Each `goto` must point to another entry's `id`. Entries without choices are terminal — the encounter ends there.

The reader **validates** your book when it loads. If a choice points to a missing id, or the schema is wrong, you'll see a clear error.

### 4. Commit, push, share

```
git add book.json entries.json
git commit -m "My book: first draft"
git push
```

Share the repo path (`your-username/your-repo`) with anyone you want to play it with. They paste it into the Book of Infinite Tales reader and they're off.

## Tips for writing encounters

- **Keep choices meaningful.** If every option leads to the same next entry, the choice is decorative. Branch where the fiction actually diverges.
- **End decisively.** A terminal entry (no choices) should feel like an ending, not a dead link. The reader shows the entry and that's it.
- **Mind the second person.** Most tables read best in second-person present tense ("You come to a well..."), but your book is your book.
- **Test it yourself.** Load your repo in the reader and walk every path. Broken `goto` references will be caught automatically, but *boring* paths will not.
- **Versioning.** Bump `version` in `book.json` when you ship a meaningful revision. Players who re-load your book will get the latest.

## Advanced: loading from a subdirectory or branch

The reader accepts several forms:

- `owner/repo` — fetches from the default branch, root directory.
- `owner/repo@my-branch` — fetches from a specific branch or tag.
- `owner/repo@main/books/volume-one` — fetches from a subdirectory.
- `https://github.com/owner/repo/tree/main/books/volume-one` — same thing, as a URL.

This lets one repo hold multiple books, each in its own folder with its own `book.json`.

## License

The content of this example book (the prose in `entries.json`) is released under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) — public domain. You can copy any or all of it into your own book without attribution, though you're also welcome to write something entirely your own.
