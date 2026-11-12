# normalize — rambling prompt → engineering prompt

A skill for Claude Code and Codex that rewrites a casual, rambling prompt into a compact
engineering prompt: goal, checkable "must be true" list, "how to work" list. Same
requirements, less prose.

- **Works on any model, weak ones included.** The instructions are explicit, step by step,
  with a mandatory self-check, so small and cheap models follow them reliably — you don't
  need a top model to clean up a prompt. In Claude Code it runs in a forked subagent on a
  cheap model with low effort — a fraction of a cent and ~10 s per prompt, your main model is
  not spent on it.
- **Faithful.** Every output line traces to a phrase in your input. A mandatory second pass
  restores dropped requirements and deletes invented ones. Paths, identifiers and your
  CAPS emphasis stay verbatim; a hunch stays a hunch, not a task.
- **English, Ukrainian, Russian.** Answers in the language of the input, tells Ukrainian and
  Russian apart, fixes surzhyk, localizes section headers. Force a language with `--en`,
  `--uk` (`--ua`) or `--ru`.
- **Text only.** It never executes the prompt — you read the result and paste it where you
  want.

## Install in Claude Code

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

## Install in Codex

**As a personal skill** (invoke with `$normalize`):

```
git clone https://github.com/MarkForest/normalize-prompt /tmp/normalize-prompt
mkdir -p ~/.agents/skills && cp -r /tmp/normalize-prompt/skills/normalize ~/.agents/skills/
```

For one repository only, copy it to `.agents/skills/` in that repository instead. Or ask Codex
to install it for you:

```
$skill-installer install https://github.com/MarkForest/normalize-prompt/tree/main/skills/normalize
```

**As a plugin:** add the marketplace, then install `normalize` from the plugin browser
(`codex`, then `/plugins`) and start a new session:

```
codex plugin marketplace add MarkForest/normalize-prompt
```

**With the [skills CLI](https://skills.sh)** (Claude Code, Codex and other agents):
`npx skills add MarkForest/normalize-prompt --skill normalize`

## Use

In Claude Code:

```
/normalize so basically on the search page the meal filter should allow picking several options at once, like AI and UAI together. and keep it in the url. tests pls, and DON'T touch the backend api if you can avoid it
```

In Codex — the same text after `$normalize` (or pick the skill in `/skills`):

```
$normalize so basically on the search page the meal filter should allow picking several options at once ...
```

In either agent you can also just say "normalize this prompt: …" / "нормалізуй промпт: …" /
"нормализуй промпт: …".

Without a session — copy your draft, run one of these, paste the result into your working
session (macOS):

```
claude -p "/normalize $(pbpaste)" | pbcopy
```

```
codex exec "\$normalize $(pbpaste)" | pbcopy
```

Flags go before the text:

| Flag | Effect |
| ---- | ------ |
| `--max N` | Character budget (default 4000) |
| `--for /cmd` | Put `/cmd` on the first line, shape for that command |
| `--en` / `--uk` (`--ua`) / `--ru` | Force output language |

## Choosing the model

The skill works on any model; a bigger one gives a bit more polish on long, tangled prompts.

- **Claude Code** — set by `model:` in `skills/normalize/SKILL.md` (a cheap model by
  default). Change it to `sonnet` or `opus`, or delete the line to use the session's model.
- **Codex** — skills run on the session's model. For a cheap run pick a small model with
  `/model`, or pass `-m <model>` to `codex exec`.

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

Скіл для Claude Code і Codex: перетворює розмовний, «розтягнутий» промпт на стислий
інженерний — мета, перелік «має бути правдою», перелік «як працювати». Підходить для будь-якої
моделі, зокрема для слабких і дешевих: інструкції покрокові, з обов'язковою самоперевіркою.
У Claude Code працює в окремому субагенті на дешевій моделі, основна модель на це не
витрачається. Відповідає мовою промпту, розрізняє українську й російську, прибирає суржик.
Нічого не виконує — лише повертає текст, який ви вставляєте в робочу сесію.

Встановлення й використання — вище: `/normalize <ваш текст>` у Claude Code, `$normalize <ваш
текст>` у Codex або «нормалізуй промпт: …».

## Credits

Based on the `normalize` skill from [xtieume/testcase](https://github.com/xtieume/testcase)
(MIT). Changes in this repo: forked subagent on a cheap model in Claude Code, Codex support,
Ukrainian and Russian support, localized headers, explicit input block, plugin packaging.
License: MIT, see [LICENSE](LICENSE).
