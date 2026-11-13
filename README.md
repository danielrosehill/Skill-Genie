# Skill Genie

Proactive skill development that fits a live workflow. A user-level heuristic for AI
agents: before starting a task, ask whether it should become a skill, and when the
skill and the task are asked for together, do them in a sensible order.

Formerly `To-Skill-Or-Not-To-Skill` (renamed 2026-11-13; the old GitHub URL redirects).

Skills and plugins are defined patterns of work, and their value is cumulative. The
current pace of AI development is chaotic partly because there is so much to define.
Workflows are being written for the first time on top of tooling that, until
recently, did not exist. Agents are now very good at writing skills and plugins
themselves, so it costs little to ship the workflow you need. The leverage is in
noticing that a task should be captured at all.

This repo gives you that habit in four forms:

- a **snippet** for the user-level instruction file of whatever agent you use
  (`CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, …), so the lens is always on;
- a **skill**, `reindex`, that makes a newly written skill available at once;
- a **skill**, `skill-or-not`, that runs the full checklist on demand ("I think this
  is replicable, run the checklist");
- an **installer** that propagates the snippet into every agent you have.

## The heuristic

For every task:

1. **Is it repeatable?** The shape of the task often answers this. When it doesn't,
   check history: memory, past sessions, shell history, the repo.
2. **Can it be automated or assisted with tools available now?** That means MCP,
   APIs, browser use, the shell, or written guidance. Partial automation counts, and
   the bar for yes is deliberately low.
3. **Is it already in our library?** In theory, skill discovery makes this step
   moot, because a relevant skill should surface on its own. In practice, search
   before you conclude it is missing.
4. **Has someone else written it?** If a third-party skill fits as-is, add it to
   your third-party collection as a pointer to the upstream. If it is close, use it
   as the base for your own.
5. **Otherwise, create it.** Add it to your public or private plugin, deploy it, and
   validate it by running the task you were about to do as a one-off.

A "no" at step 1 or 2 means you just do the task.

### Skill and task together: order of operations

Often the request is "we need a skill for this, and also do X". There are three
orders:

1. **Task, then skill (default).** Do the task, then write the skill from the
   experience, pitfalls included. The risk is forgetting the skill, so the agent
   notes it up front and writes it before reporting the task as done, usually by
   handing a full brief of the run to a background subagent, so the live work
   carries on.
2. **Skill, then task.** Write the skill while the request is fresh, force a
   re-index straight away (skill `reindex`), then do the task through the skill.
3. **Skill, task, update.** Draft the skill, run the task through it, then update
   the skill with the delta: pitfalls, wrong turns, steps that differed.

When it can, the agent offers these as a menu with task-then-skill recommended.
Otherwise it uses the default and says so.

### Forcing a re-index: your own procedure

How a new skill becomes available depends entirely on your setup: a skills folder, a
marketplace plugin, or a searchable catalogue. The `reindex` skill carries generic
steps for each, but reads your own procedure first from a private overlay outside the
plugin:

```
$SKILL_GENIE_HOME/reindex.md                 # or
$CLAUDE_USER_DATA/skill-genie/reindex.md     # or
~/.claude-user-data/skill-genie/reindex.md
```

Put the exact commands for your setup there (push, sync, verify, load in session).
Your deployment details stay out of this public repo. If the file is missing, the
skill offers to write it once the generic steps have worked.

The same text is a habit in [Agent-Habits](https://github.com/danielrosehill/Agent-Habits/blob/main/library/habits/skill-or-not.md), which is its source. Use either one, not both.

The exact text injected into instruction files is in
[`snippet/heuristic.md`](snippet/heuristic.md). The step-by-step version is in
[`skills/skill-or-not/SKILL.md`](skills/skill-or-not/SKILL.md).

## The setup it assumes

The heuristic pays off most with a library that is easy to add to and easy to search:

- **Several plugin collections** ("marketplaces", although nothing is for sale):
  - a **public** one for everything that can be open-sourced;
  - a **private** one for skills that carry personal or business detail;
  - a **third-party** one that holds pointers to other people's plugins, pinned to a
    ref and never copied.
- **Progressive discovery** of tools and skills, so the agent finds what is relevant
  without everything loading up front.
- **User-level configuration**, so habits follow you into every project.
- **Federated access to tools** through a single gateway, rather than per-project
  server lists.

## Why user level, not project level

The heuristic governs how an agent works in general. It is not about any one
codebase. In a project's `CLAUDE.md` it would apply only inside that repo, and every
repo would need its own copy. The installer refuses any file inside a git work tree
unless you pass `--force`.

## Install

As a Claude Code plugin, from the `danielrosehill` marketplace:

```
/plugin marketplace add danielrosehill/Claude-Code-Plugins
/plugin install skill-genie@danielrosehill
```

Then inject the snippet at user level. You can ask the agent to "install the skill
heuristic", which runs the `install-heuristic` skill, or run the installer yourself:

```bash
git clone https://github.com/danielrosehill/Skill-Genie
cd Skill-Genie
./install.sh --dry-run        # preview: every agent whose config dir exists
./install.sh                  # write it
./install.sh --list           # known agents and their user-level files
./install.sh --agent claude   # just one
./install.sh --remove         # take it out again
```

| Agent | User-level file |
| --- | --- |
| Claude Code | `~/.claude/CLAUDE.md` |
| Codex CLI | `~/.codex/AGENTS.md` |
| Gemini CLI | `~/.gemini/GEMINI.md` |
| Qwen Code | `~/.qwen/QWEN.md` |
| opencode | `~/.config/opencode/AGENTS.md` |

For any other agent, pass `--file PATH`. The block sits between
`<!-- BEGIN skill-genie -->` markers (blocks under the pre-rename
`to-skill-or-not-to-skill` markers are replaced in place). Re-running the installer replaces
the block in place, and the first write to an existing file leaves a `.bak-` copy.

## Hook or skill?

Both work. The instruction-file snippet is the always-on version: the agent applies
the lens to every task without being asked. The skill is the explicit version, run
when you suspect a task is a candidate. A `UserPromptSubmit` hook could force the
check on every prompt, but the snippet does the same job without the per-prompt
overhead, so no hook ships here.

## License

MIT
