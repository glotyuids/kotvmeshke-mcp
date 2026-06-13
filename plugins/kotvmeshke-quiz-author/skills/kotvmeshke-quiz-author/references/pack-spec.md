# Pack spec — full field reference

The shapes below are the `arguments` for `create_package` (the whole object)
and `add_questions` (`{category_id, questions: [Question, …]}`). Omitted
optional fields get sensible defaults — prefer omitting to guessing.

## Package

| field | type | notes |
|---|---|---|
| `title` | string | required |
| `description` | string | shows in the catalog |
| `language` | string | `"ru"`, `"en"`, … — match the question language |
| `difficulty` | int 0–10 | package-level shelf label; default 5 |
| `tags` | string[] | lowercase topical tags |
| `authors` | string[] | display credits |
| `rounds` | Round[] | required, 1–10 |

## Round

| field | type | notes |
|---|---|---|
| `name` | string | default "Раунд N" / "Round N" (by pack language) |
| `type` | string | omit for standard; `"FINAL"` for a final round |
| `categories` | Category[] | the topics on the board; ≤100 per pack total |

## Category (topic)

| field | type | notes |
|---|---|---|
| `title` | string | default "Тема N" / "Category N" |
| `questions` | Question[] | ≤1000 per pack total |

## Question

| field | type | notes |
|---|---|---|
| `text` | string | required, 1–2000 chars |
| `answer` | string | required. free_text: canonical answer. select: correct option's letter (`"B"`) or its exact text. numeric: the number as a string (`"42"`, `"3.14"`) |
| `answer_type` | string | `"free_text"` (default) \| `"select"` \| `"numeric"` |
| `alt_answers` | string[] | free_text only; ≤50; spellings, transliterations, abbreviations |
| `options` | string[] | select only; 2–8 entries, mapped to letters A, B, C… |
| `deviation` | number ≥0 | numeric only; accepted tolerance around the answer |
| `score` | int >0 | default `100 × (position+1)` within the topic |
| `difficulty` | int 1–5 | default `min(position+1, 5)` |
| `kind` | string | default `"STANDARD"`. Special board kinds: `"STAKE"`, `"STAKE_ALL"`, `"SECRET"`, `"SECRET_PUBLIC_PRICE"`, `"SECRET_NO_QUESTION"`, `"NO_RISK"`, `"FOR_ALL"` — use only when the user asks for «Своя игра» mechanics |
| `host_notes` | string | visible to the host only |
| `answer_comment` | string | extra text shown on the answer reveal |
| `question_media_url` | string | absolute http(s) URL — re-hosted at import |
| `answer_media_url` | string | same, for the reveal screen |
| `media_type` | string | `IMAGE` \| `AUDIO` \| `VIDEO`; usually inferred from the file extension |

## Worked examples

Free text with forgiving matching:

```json
{
  "text": "Какая группа выпустила альбом «Nevermind»?",
  "answer": "Nirvana",
  "alt_answers": ["Нирвана", "нирвана"],
  "host_notes": "альбом 1991 года",
  "answer_comment": "Обложку с младенцем снимали в бассейне Пасадены."
}
```

Multiple choice (answer may be the letter or the exact option text):

```json
{
  "text": "Which planet has the most moons?",
  "answer_type": "select",
  "options": ["Jupiter", "Saturn", "Uranus", "Neptune"],
  "answer": "B"
}
```

Numeric with tolerance:

```json
{
  "text": "В каком году открыли первую линию московского метро?",
  "answer_type": "numeric",
  "answer": "1935",
  "deviation": 0
}
```

Minimal whole pack (defaults do the scoring):

```json
{
  "title": "Ретро-вечер",
  "language": "ru",
  "tags": ["музыка", "90-е"],
  "rounds": [
    {
      "name": "Разминка",
      "categories": [
        {
          "title": "Музыка 90-х",
          "questions": [
            { "text": "…лёгкий вопрос…", "answer": "…" },
            { "text": "…посложнее…", "answer": "…" },
            { "text": "…самый сложный…", "answer": "…" }
          ]
        }
      ]
    }
  ]
}
```

## Other tools' arguments

- `get_package`: `{package_id, detail?: "outline" (default) | "full"}`
- `add_questions`: `{category_id, questions: Question[1..50]}`
- `add_round`: `{package_id, name}` → returns `round_id`
- `add_category`: `{round_id, title}` → returns `category_id`
- `rename_item`: `{kind: "round"|"category", id, name}`
- `reorder_items`: `{package_id, parent_id, ordered_ids}` — `parent_id` is the
  pack id (reorders rounds), a round id (topics) or a topic id (questions);
  `ordered_ids` is the complete permutation of current children
- `update_package`: `{package_id, title?, description?, tags?}` — only passed
  fields change
- `attach_media`: `{package_id, question_id, url, media_type?, placement?: "question"|"answer", alt_text?}`
- `delete_item`: `{kind: "round"|"category"|"question"|"package", id}` — irreversible
- `publish_package`: `{package_id, action?: "validate"|"publish" (default)}`
