# graphify skill (vendored)

This repo carries the [**graphify**](https://github.com/Graphify-Labs/graphify)
skill as a project-scoped Claude Code skill. graphify turns a codebase (plus
docs, SQL, configs, PDFs) into a persistent, queryable **knowledge graph**, so
the assistant answers architecture and "how does X work" questions by traversing
a scoped subgraph instead of grepping and reading many files — which is where the
token savings come from.

## What's installed

```
.claude/skills/graphify/
  SKILL.md              # the /graphify skill (progressive-disclosure core)
  references/           # loaded on demand: query, update, exports, extraction-spec, …
  .graphify_version     # pins the skill version (0.9.89)
CLAUDE.md               # always-on nudge: prefer `graphify query` for codebase Qs
```

The skill self-bootstraps: on first `/graphify` it installs the `graphifyy`
package (via `uv tool` or `pip`) into `graphify-out/.graphify_python` — **no API
key required**. Code is parsed locally with tree-sitter (deterministic, free);
only docs/papers/images use an LLM, and only if `GEMINI_API_KEY` is set.

## Usage (in Claude Code)

```
/graphify .                         # build the graph for this repo
/graphify query "how does X work?"  # answer from the graph (cheap, scoped)
/graphify path "AuthModule" "Database"   # shortest path between two concepts
/graphify explain "SomeConcept"     # focused, plain-language explanation
/graphify . --update                # re-extract only changed files after edits
```

Outputs land in `graphify-out/` (`graph.html`, `graph.json`, `GRAPH_REPORT.md`)
and persist across sessions, so later work reuses the graph instead of rebuilding
it.

## Note on the PreToolUse hook

`graphify claude install` can also register a `.claude/settings.json` PreToolUse
hook that hard-nudges the assistant to the graph before raw file reads. It is
**intentionally not committed here**: that hook embeds an absolute path to the
`graphify` binary resolved at install time, which would not exist in a
freshly-cloned session and would fire against a missing binary. The `CLAUDE.md`
guidance above achieves the same behavior portably. To opt in locally:

```
graphify claude install --project --strict
```

---
Upstream: https://github.com/Graphify-Labs/graphify · License: see upstream.
