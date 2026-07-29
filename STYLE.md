# Writing style for skill content

These files are read by an agent mid-task, not by a person browsing docs. Every
line should change what the agent does next. The rules below are the ones that
were repeatedly broken while writing the current content — they exist because
the mistakes recur, not because the prose needs policing.

## The shape

Most entries work as **imperative, then fact, then consequence**:

> **Always set `AppName` explicitly, even though the SDK does not require it.**
> It defaults to `Agent`, so omitting it fails nothing: the app initializes,
> exports, and lands on the platform under that name. Two services that both
> omit it are indistinguishable there, and anything keyed on service name
> points at the wrong thing.

The instruction first, so it survives skimming. The fact second, so the
instruction is checkable. The consequence last, so it can be weighed against
a real situation.

## Cut these

**Provenance.** How we learned something is invisible to the reader and
unusable by them. Not "(verified in CI)", "(string literals in the assembly)",
"(reproduced against the reference solution)". State the behaviour. If
confidence needs marking, one word inline does it — "measured" — and only
where the reader would otherwise reasonably doubt it.

**Editorial framing.** "and that is a trap", "the one to watch", "the trap is
that". These say how to feel, not what to do. Rewrite as the action.

**Cross-language asides.** An agent instrumenting a Python app is not holding
the .NET reference. Each language file states its own behaviour; comparisons
between them cost every reader a line to serve almost none.

**Filler connectives.** "Concretely", "Worth knowing in both directions",
"since the trigger is not obvious". If a sentence needs announcing, it is
usually the announcement that should go.

**Illustrations that outnumber the rule.** Three example paths where the rule
stated plainly is shorter and clearer.

## Keep these

**Named failure modes.** `ModuleNotFoundError: No module named 'httpx'` beats
"the import fails" — the agent can match the symptom.

**Explanations that prevent wrong generalisation.** A "why" earns its place
when knowing the mechanism changes the diagnosis. The LangChain hoisting
explanation stays because it tells you the problem applies to projects with no
LangChain in them.

**Honest limits.** Where something is untested or version-specific, say so
plainly in the body. That is not provenance; it is a fact about how far the
guidance reaches.

## Mechanics

- Wrap prose at 79 characters. Code blocks and tables may exceed it.
- American spelling, matching the existing files.
- Bold lead-ins for list entries and rule paragraphs.
- Name real identifiers — attributes, env vars, exception types, packages —
  rather than describing them.

## Before committing

Read each new paragraph and ask what the agent does differently for having
read it. If the answer is nothing, it is background, and background belongs
in a bug report or a commit message instead.
