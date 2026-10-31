# Steno Tutor — Chorded Keyboard Academy

A single-file, fully offline steno (chorded keyboard) tutor. Open
[`steno_tutor.html`](steno_tutor.html) in any browser — no CDNs, fonts, scripts,
network calls or build steps. Dictionary, SVG artwork, emoji, Chinese characters
and grammar rules are all embedded in the file.

---

## The interface

The default view is deliberately bare: **the typing box on the left, the morpheme
word tree on the right**, nothing else. No headers, no toolbars, no status text —
only the words and artwork you produce.

| Key | Does |
| :--- | :--- |
| `?` or `Alt+K` | show the full shortcuts card |
| `Alt+F` | toggle the full interface (all panels and controls) |
| `Alt+Shift+F` | browser fullscreen |
| `Esc` | close the card / clear the box |

Feedback is silent: the box flashes green on a good chord and amber on a
mistyped one, instead of printing a status message.

---

## What it teaches

Press several keys **together** as a chord; on release the chord expands into a
word.

| Chord | Output |
| :--- | :--- |
| `c + a + n` | can |
| `d + g + o` | dog |
| `a + f + s` | fast |
| `b + k + o` | book |
| `e + f + i + l` | file |
| `1 + e + f + i + l` | life (anagram of *file* + number key) |
| `e + h + z + 4` | 这 (zhè — *this*) |

### Rules the engine follows

- **Order independence** — key order is irrelevant; chords are sorted in memory.
- **One key per finger** — each finger presses at most one key per chord.
- **Collision resolution** — anagrams get a numeric modifier (`file` / `life` → `efil` / `1efil`).
- **Morpheme engine** — striking a root chord arms its derivatives
  (`take` → `intake`, `overtake`, `mistake`…).
- **Grammar inflection** — tap `1`–`4` to inflect the last word by part of speech
  (verbs: past / -ing / 3rd person / past participle; nouns: plural / possessive…).
- **Grammar is stored per word** — every lemma carries its own forms, and so does
  every morpheme compound. `do` inflects to *did · doing · does · done*, while
  `undo` inflects to **undid · undoing · undoes · undone** — the compound never
  borrows its root's paradigm. Noun compounds such as `income` take noun forms
  (*incomes · income's*).
- **No invented grammar** — a word is inflectable only if its record carries forms.
  Chinese characters, function words (`the`, `this`, `of`…) and plain typed words
  have none, so tapping `1`–`4` on them leaves the word untouched and reports
  *No Grammar* instead of producing `这ing` or `jumpeded`.

### Where grammar lives

Every word owns its paradigm, in the same record that owns its chord:

```js
{ word: "go", chord: "go", keys: "go", note: "…",
  grammar: { pos: "verb", forms: { "1": "went", "2": "going", "3": "goes", "4": "gone" } } }
```

```js
"nu": { "morpheme": "un-", "chord": "nu", "word": "undo", "char": "撤销",
        "grammar": { "pos": "verb", "forms": { "1": "undid", "2": "undoing", "3": "undoes", "4": "undone" } },
        "emoji": "↩️", "note": "Reverse action" }
```

Adding a word is a single edit. `GRAMMAR_DATA` is just an index assembled from
those records at load, so the dictionary and the grammar tables cannot drift
apart (they previously disagreed about `upturn`).
- **Mistyped chords self-clean** — a multi-key strike that maps to nothing is
  treated as a typo: its letters are removed instead of being left in the box, so
  **Copy** and the **Unmapped** list stay clean. Letter-by-letter typing (one key
  at a time) is untouched.

---

## Output modes

The default is **Combo** — every chord shows its artwork *and* its word.

| Mode | Shortcut | Shows |
| :--- | :--- | :--- |
| Words | `Alt+1` | the word |
| 汉字 | `Alt+2` | the Hanzi character |
| SVG | `Alt+3` | the pictogram only |
| Combo | `Alt+4` | pictogram + word |

`Alt+M` cycles through them.

Chinese characters carry a **pictogram of their meaning** (这 → 👉, 狗 → 🐶,
爱 → ❤️, 书 → 📖, 大 → 🐘), so the graphic mode reads as a cue rather than a
repeat of the character.

---

## Other shortcuts

| Keys | Action |
| :--- | :--- |
| `Alt+S`, `Ctrl+/` | focus the typing box |
| `Ctrl+C` | copy English words |
| `Ctrl+Shift+C` | copy English only |
| `Ctrl+V` | paste |
| `Ctrl+L`, `Esc` | clear the box |
| `Alt+U` | copy the unmapped list |
| `Alt+Shift+U` | open the unmapped list |
| `Space` | commit the word / disarm the root |
| `1`–`4` | grammar inflection |
| `Backspace` | undo a word / split a compound |

---

## The three views (full interface)

1. **Training** — guided lessons with live finger highlighting on dual hands,
   key-by-key breakdown, streak tracking and sound feedback.
2. **Sandbox** — the typing box and the morpheme word tree.
3. **Finger Guide** — the 10-finger colour map and layout reference.

The word tree shows each root and its derivatives with pictogram, English word,
Chinese translation and chord hint.

---

## 10-Finger key mapping

| Finger | Hand | Keys |
| :--- | :--- | :--- |
| **Pinky** | Left | `1`, `q`, `a`, `z` |
| **Ring** | Left | `2`, `w`, `s`, `x` |
| **Middle** | Left | `3`, `e`, `d`, `c` |
| **Index** | Left | `4`, `5`, `r`, `f`, `v`, `t`, `g` |
| **Thumb** | Left | `b` |
| **Thumb** | Right | `n` |
| **Index** | Right | `6`, `7`, `y`, `h`, `u`, `j`, `m` |
| **Middle** | Right | `8`, `i`, `k`, `,` |
| **Ring** | Right | `9`, `o`, `l`, `.` |
| **Pinky** | Right | `0`, `p`, `;`, `/`, `'` |

---

## What's inside the file

| Data | Content |
| :--- | :--- |
| `STENO_DICTIONARY` | 289 chorded entries (English + Chinese), each carrying its own `grammar` when it inflects |
| `CHORD_MAP` | extra ergonomic aliases (~100) |
| `MORPHEME_ROOTS_TREE` | 52 roots, 160 derivative words — roots and compounds carry their own `grammar` |
| `WORD_VISUALS` | ~320 pictograms with colour palettes |
| `CHINESE_TO_ENGLISH` | 75 Hanzi → English mappings |
| `CJK_PICTOGRAMS` | 95 meaning pictograms for Chinese tokens |
| `GRAMMAR_DATA` | *derived at load* from the records above — not hand-maintained |

---

## Roadmap — Chinese Pinyin chording

Typing Hanzi directly from Pinyin chords is designed but not fully wired in:

| Chord | Output |
| :--- | :--- |
| `4zhe` | 这 (zhè, tone 4) |
| `4shi` | 是 (shì, tone 4) |
| `1ta` | 他 (tā, tone 1) |
| `2na` | 拿 (ná, tone 2) |
| `3gou` | 狗 (gǒu, tone 3) |
