# normalize — rambling prompt → engineering prompt, on Haiku

A Claude Code skill that rewrites a casual, rambling prompt into a compact engineering prompt:
goal, checkable "must be true" list, "how to work" list. Same requirements, less prose.

- **Cheap.** Runs in a forked subagent on **Haiku** with low effort — about $0.004 and ~10 s
  per prompt. Your main model is not spent on it.
- **Faithful.** Every output line traces to a phrase in your input. A mandatory second pass
  restores dropped requirements and deletes invented ones. Paths, identifiers and your
  CAPS emphasis stay verbatim; a hunch stays a hunch, not a task.
- **English, Ukrainian, Russian.** Answers in the language of the input, tells Ukrainian and
  Russian apart, fixes surzhyk, localizes section headers. Force a language with `--en`,
  `--uk` (`--ua`) or `--ru`.
- **Text only.** It never executes the prompt — you read the result and paste it where you
  want.

## Install

**As a plugin** (command becomes `/normalize:normalize`):

```
claude plugin marketplace add MarkForest/normalize-prompt
claude plugin install normalize@markforest-normalize-prompt
```

**As a personal skill** (command is just `/normalize`):

```
git clone https://github.com/MarkForest/normalize-prompt /tmp/normalize-prompt
cp -r /tmp/normalize-prompt/skills/normalize ~/.claude/skills/
```

Or with the [skills CLI](https://skills.sh): `npx skills add MarkForest/normalize-prompt --skill normalize`

## Use

In any Claude Code session:

```
/normalize so basically on the search page the meal filter should allow picking several options at once, like AI and UAI together. and keep it in the url. tests pls, and DON'T touch the backend api if you can avoid it
```

Or say "normalize this prompt: …" / "нормалізуй промпт: …" / "нормализуй промпт: …".

Without a session — copy your draft, run this, paste the result into your working session
(macOS):

```
claude -p "/normalize $(pbpaste)" | pbcopy
```

Flags go before the text:

| Flag | Effect |
| ---- | ------ |
| `--max N` | Character budget (default 4000) |
| `--for /cmd` | Put `/cmd` on the first line, shape for that command |
| `--en` / `--uk` (`--ua`) / `--ru` | Force output language |

Want more quality than Haiku? Change `model: haiku` to `model: sonnet` in `SKILL.md`.

## Example

Input (Russian, `--uk`):

> надо чтобы письмо агентству после регистрации уходило сразу, а не через час, и в нём была
> ссылка на вход. проверь на стенде, прод не трогай

Output:

```
## Мета
Після реєстрації агентству одразу надсилається лист із посиланням на вхід.

## Має бути правдою
- Лист агентству після реєстрації надсилається одразу, а не через годину.
- У листі є посилання на вхід.
- Прод не змінено.

## Як працювати
- Перевірити на стенді.
```

## Why a skill and not a hook

A Claude Code `UserPromptSubmit` hook cannot replace your prompt — it can only add context
next to it or block it. So the main model would see both versions. A skill gives you the
clean text to review and paste.

## Українською

Скіл для Claude Code: перетворює розмовний, «розтягнутий» промпт на стислий інженерний —
мета, перелік «має бути правдою», перелік «як працювати». Працює в окремому субагенті на
Haiku (≈ $0.004 і ~10 с за промпт), основна модель на це не витрачається. Відповідає мовою
промпту, розрізняє українську й російську, прибирає суржик. Нічого не виконує — лише
повертає текст, який ви вставляєте в робочу сесію.

Встановлення й використання — вище: `/normalize <ваш текст>` або «нормалізуй промпт: …».

## Credits

Based on the `normalize` skill from [xtieume/testcase](https://github.com/xtieume/testcase)
(MIT). Changes in this repo: forked Haiku subagent, Ukrainian and Russian support, localized
headers, explicit input block, plugin packaging. License: MIT, see [LICENSE](LICENSE).
