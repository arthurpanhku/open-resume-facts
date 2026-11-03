# resume-facts

*[中文版](README.md) · English*

> Manage your job-search résumé with Claude or Codex.
> In one line: **the fact base is the only long-lived asset; a résumé is a disposable artifact.**

This is a **skill (a method), not a tool library.** It will not help you sound impressive.
Its entire design goal is: **every sentence in the résumé should survive being probed in an interview.**

## Why bother

Ask an AI to write your résumé directly and you will almost certainly get these four
failure modes — and the more fluent the prose, the more dangerous they are:

| What the AI writes | What was actually true | What happens in the interview |
|---|---|---|
| "Led the design of platform X" | You were one of several implementers | Falls apart on the first architecture question |
| "Rolled out group-wide" | Still a pilot | Falls apart on the first coverage question |
| "Delivered X for clients" | Internal function, no external clients | Falls apart when asked to name a client |
| "9 years in security" | 9 years total career, 6 in security | The timeline doesn't add up |

These are not wording problems. They are **factual** problems. A plain but accurate résumé is
far safer than a fluent one that collapses under follow-up questions.

## Core design

1. **Interview first, write later.** Setting up starts by getting your facts straight —
   one group of questions at a time. Anything the assistant isn't sure about is marked
   `v: todo` for you to confirm item by item. The AI does not infer on your behalf.
2. **One fact base, `facts.yaml`.** Every fact carries a verification state
   (`user` / `cv` / `todo`) and a note (which day, in which conversation it was confirmed,
   and which details you did not supply). When you correct yourself, the note is **appended** —
   the old statement is never overwritten.
3. **Résumés are disposable.** One `CV_<company>_<role>.md` per job; delete it after applying.
   Keep the ones you actually submitted (you need to see the exact version you sent when the
   interview comes); delete speculative drafts you never sent.
4. **Constraints are machine-readable.** `rules.yaml` + `lint.py` check dates, regulators,
   certifications, years-of-experience claims, internal-only figures, and unconfirmed facts,
   reporting file names and line numbers. Nothing relies on "I'll be careful."

## Install

### Claude Code

```bash
git clone https://github.com/arthurpanhku/open-resume-facts ~/.claude/skills/resume-facts
```

Or scoped to a single project:

```bash
git clone https://github.com/arthurpanhku/open-resume-facts .claude/skills/resume-facts
```

Then say "help me set up a résumé repo" in a session, or invoke `/resume-facts`.

> Note: the repository is named `open-resume-facts`, but the **install directory must be named
> `resume-facts`** so it matches the `name` in the `SKILL.md` frontmatter. The clone commands
> above already take care of this.

### claude.ai

Upload this repository (including `SKILL.md`) under Settings → Capabilities → Skills.

### Codex / other agent CLIs

Use the contents of `SKILL.md` as a project rules file:

```bash
cp SKILL.md AGENTS.md
```

Or paste the whole thing as your first message. The method itself is agent-agnostic.

> **Language note:** `SKILL.md` — the method body the agent reads — is currently written in
> Chinese. It works fine with any model that handles Chinese (Claude and GPT-class models do),
> and the résumés it produces are in whatever language you ask for. An English `SKILL.md`
> has not been written yet.

## How to use it

**First run** (roughly 30–60 minutes, most of it you answering questions):

```
You: help me set up a résumé repo
AI:  creates the directory structure, runs git init, makes the first commit, then starts
     interviewing — first asking whether you already have a résumé or LinkedIn profile to
     extract from, then working through your roles, education, certifications, languages,
     projects, awards, citizenship and work authorization, and which numbers you are
     allowed to state publicly.
```

Until the interview is done, **the AI should not write any résumé.**

**Day-to-day applications:**

```
You: <paste the full job description>
AI:  1. reads facts.yaml and rules.yaml, then reads the JD
     2. lists out every responsibility and requirement in the JD
     3. maps each one against your facts in three buckets: supported by evidence /
        no evidence / "looks like a match but isn't" (this last one is called out explicitly)
     4. writes the résumé (projects before employment for platform/technical roles,
        employment first for traditional roles; two pages is the target)
     5. runs lint to zero errors, builds the PDF, checks the page count
     6. creates an application record under Applications/, including open questions and risks
     7. lists a separate section, "new facts introduced here", which only goes into
        facts.yaml after you confirm it
```

Step 7 is the most important human step in the whole workflow — see below.

## What this does not guarantee

**`lint.py` is pattern matching. It does not understand meaning.**

- It catches: dates that don't line up, the wrong regulator, certifications absent from
  your fact base, banned phrasings, miscalculated years of experience, leaked internal-only
  figures, and facts still marked `v: todo` making it into a résumé.
- **It does not catch: overstated wording, details that go beyond what the facts support, or
  companies / titles / achievements invented out of thin air.**

Therefore:

- Step 7, "new facts introduced here", **cannot be skipped.** It is the only step that stops
  semantic-level fabrication.
- **Passing lint ≠ ready to send.** The responsibility is yours; read it yourself before you
  put your name on it.

One more thing: this method will not make your résumé more *striking*. It will make it
**more conservative and more specific.** If what you want is packaging, this skill won't help.

## What's in this repository

```
README.md       # Chinese version (default)
README.en.md    # this file
SKILL.md        # the method body, read by Claude / Codex (written in Chinese)
LICENSE         # MIT
```

**`lint.py` and `build_cv.py` are deliberately not included.** The checking rules are personal —
which phrasings to ban, which regulator each role may mention, what belongs in the
certification allowlist all depend on your facts. `SKILL.md` contains a full specification for
both scripts, so the AI writes them against your own fact base when you set up. That fits
better than shipping a generic script.

Your own `facts.yaml` is personal data. **Do not commit it to a public repository.**

## License

MIT, see [LICENSE](LICENSE).
