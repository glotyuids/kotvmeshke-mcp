---
name: kotvmeshke-quiz-author
description: >-
  Author and manage quiz packs on kotvmeshke.com («Кот в мешке») through its
  MCP server. Use this whenever the user wants to create, edit, restructure,
  validate or publish a quiz / trivia pack on kotvmeshke (they may say «пак»,
  «квиз», "pack", "quiz night", "викторина"), wants to add rounds, topics or
  questions, attach images/audio/video to questions, fix or reorder an existing
  pack, or asks how to connect an AI assistant to kotvmeshke. Also use it when
  kotvmeshke MCP tools (list_my_packages, create_package, add_questions,
  publish_package, …) are available in the session and the task involves quiz
  content — even if the user never says the word "kotvmeshke".
---

# kotvmeshke quiz author

kotvmeshke.com is a quiz platform («Своя игра»-style boards played live with
friends). Its MCP server exposes 12 tools that act strictly on the signed-in
user's own packs — read (`list_my_packages`, `get_package`), build
(`create_package`, `add_round`, `add_category`, `add_questions`), edit
(`update_package`, `rename_item`, `reorder_items`, `delete_item`), media
(`attach_media`) and release (`publish_package`). Everything you create is a
private draft until the user explicitly publishes.

## Check the connection first

The tools normally appear as `mcp__kotvmeshke__<tool>` (the middle part is
whatever server name the user chose when adding it).

If the kotvmeshke tools are NOT available in this session, don't stall — do
both of these:

1. Give the user the setup path: create a token at
   https://kotvmeshke.com/settings ("AI access" / «Доступ для ИИ» section —
   any registered account works; the token starts with `qmcp_` and is shown
   exactly once), then for Claude Code:

   ```
   claude mcp add --transport http kotvmeshke \
     https://api.kotvmeshke.com/mcp \
     --header "Authorization: Bearer qmcp_THEIR_TOKEN"
   ```

   Other clients (Cursor, VS Code, Claude Desktop, Windsurf):
   https://kotvmeshke.com/docs/mcp/clients

2. Draft the pack anyway — structure, topics, finished questions — and present
   it as the exact `create_package` arguments, so once they connect, creating
   it is one call. Ask which details matter (audience, difficulty, language)
   before writing dozens of questions in the wrong direction.

The token is a password: let the user run the command themselves rather than
asking them to paste the token into the conversation.

## The pack model

```
pack  (title, description, language, tags)
├── round 1                  ("type": "FINAL" for a final round)
│   ├── topic «music»        (a category on the board)
│   │   ├── question for 100   ← easiest
│   │   ├── question for 200
│   │   └── question for 300   ← hardest
│   └── topic «movies»
└── round 2 …
```

- **Scores and difficulty default by position** in the topic: first question →
  100 points / difficulty 1, second → 200 / 2, … (difficulty caps at 5). Omit
  `score` and `difficulty` unless the user asks for specific values — "easy to
  hard" comes free, and hand-set scores must stay ascending to feel right.
- **Lifecycle**: packs are created as private drafts → `publish_package` makes
  them public and queues human moderation → after the pack is first played it
  freezes forever (edits return `PACKAGE_LOCKED`).
- **Limits**: ≤10 rounds, ≤100 topics, ≤1000 questions per pack;
  `add_questions` takes 1–50 per call; ≤50 alt answers per question; media
  files ≤25 MB.

## Writing questions that play well

These rules exist because answers are auto-checked by string match against
`answer` + `alt_answers`, and because a host reads the questions aloud to a
room:

- One clearly correct, verifiable answer per question. If reasonable people
  could defend two answers, rewrite the question.
- Keep canonical answers short (1–3 words). Add `alt_answers` for spelling
  variants, transliterations («Нирвана» / "Nirvana"), abbreviations, and
  with/without-article forms — that's the difference between a fun game and
  players arguing with the screen.
- Write the quiz content in the audience's language, whatever language the
  conversation happens in, and set the pack `language` field to match ("ru",
  "en", …). Confirm the language if it isn't obvious.
- `select` questions: 2–8 options, exactly one correct, wrong options plausible
  enough to tempt. `numeric`: set `deviation` when near-misses should count.
- Use `host_notes` for things the host needs (pronunciation, context,
  acceptable-answer judgment calls) and `answer_comment` for a fun fact shown
  on the answer reveal.
- When the user supplies source material, use only facts from it — never pad
  with invented ones.

For every field, value ranges and worked examples per answer type, read
[references/pack-spec.md](references/pack-spec.md) before composing
`create_package` / `add_questions` arguments.

## Tool workflows that work

- **New pack → one `create_package` call** with the whole tree. `add_round` /
  `add_category` / `add_questions` are for extending packs that already exist,
  not for building new ones piecemeal.
- **Read before editing**: `get_package` (outline) gives the real ids of
  rounds, topics and questions. Address things by those ids — never guess or
  reuse ids from earlier in the conversation if the pack may have changed.
- `add_questions` returns a per-item result. If some items failed, report
  exactly which and why — don't summarize a partial success as success.
- `reorder_items` takes the COMPLETE new order of a parent's children — a
  permutation of the current ids, no additions or omissions. On
  `ORDER_MISMATCH`, re-fetch the outline and retry once.
- `attach_media` needs a direct public http(s) link to the media file itself
  (not a page containing it). The server downloads and re-hosts the file;
  `placement` is `"question"` (default) or `"answer"` for the reveal screen.
- After any create or edit, show the user a compact outline of what changed —
  the tool results carry ids and stats; translate them into words.

## Publishing and destructive actions

- Always run `publish_package` with `action: "validate"` first and fix the
  violations it lists. Actually publish only when the user explicitly says so:
  publishing makes the pack public and sends it to moderation — that's the
  user's call, never your initiative.
- `delete_item` cannot be undone, and deleting a round/topic/pack takes all
  its contents with it. Confirm first unless the user just asked for exactly
  that deletion.
- `PACKAGE_LOCKED` means the pack has been played and is permanently frozen.
  The path forward is a new version: `get_package` with `detail: "full"`, then
  recreate the (amended) content via `create_package`.

## When tools return errors

| error | what it means | what to do |
|---|---|---|
| `UNAUTHENTICATED` | token missing / expired / revoked | point the user to https://kotvmeshke.com/settings to mint a new token |
| `NOT_FOUND` | no such pack/topic/question among the user's | `list_my_packages` / `get_package` and work from real ids |
| `PACKAGE_LOCKED` | pack was played; frozen forever | offer to build a new version (see above) |
| `INVALID_STRUCTURE` | something blocks publishing | run validate — it lists each violation with a fix |
| `ALREADY_PUBLISHED` | nothing to do | tell the user it's already live |
| `ORDER_MISMATCH` | reorder ids didn't match current children | re-fetch outline, rebuild the permutation |
| `INVALID_MEDIA_URL` / `MEDIA_FETCH_FAILED` | link not public or not a direct file | ask for a direct public file URL |
| `MEDIA_TOO_LARGE` / `UNSUPPORTED_MEDIA` | >25 MB or not image/audio/video | smaller file; SVG is not accepted |
