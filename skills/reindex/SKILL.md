---
name: reindex
description: Make a skill that was just written or changed available now, instead of at the next session or the next scheduled sync. Forces whatever re-index, sync, marketplace update or reload the user's setup needs, then checks the skill is really discoverable and, where possible, loads it into the current session. Reads the user's private procedure from the skill-genie overlay first. Use after writing a skill, in the "skill, then task" order, or when the user says "force a reindex", "reindex the skills", "sync the new skill" or "make that skill available now".
---

# Reindex

A new skill is usually not available in the session that wrote it. This skill closes
that gap. Run it straight after a skill is written or updated and its source is
saved (committed and pushed, if the skill lives in a repository).

## 1. Use the user's procedure if there is one

Skill distribution differs per setup, so the real steps live outside this plugin.
Resolve the user's data folder in this order and stop at the first match:

1. `$SKILL_GENIE_HOME`
2. `$CLAUDE_USER_DATA/skill-genie/`
3. `~/.claude-user-data/skill-genie/`, if `~/.claude-user-data` exists

If that folder holds `reindex.md`, follow it. It wins over everything below. If it is
missing, use the generic steps in section 2, and once something works, offer to
save the steps as `reindex.md` so the next run skips the guessing.

## 2. Generic steps

Work out how the skill reaches agents, then do the matching step.

| Where the skill lives | How to make it available |
| --- | --- |
| A plain skills folder the agent scans (user or project level) | Nothing to index. Start a new session, or reload if the agent can. |
| A plugin installed from a marketplace | Push the plugin, update the marketplace (`claude plugin marketplace update <name>`), then update the plugin (`claude plugin update <plugin>@<marketplace>`). Reload plugins in the session if the agent supports it. |
| A plugin loaded from a local directory | Reload plugins, or restart the session. |
| A searchable skill catalogue or index | Trigger the catalogue's sync or re-index for that plugin now, forced if the commit hasn't moved. |

## 3. Verify the skill itself

Check the skill, not the command's exit code:

- the index or plugin cache shows the commit or version you just pushed;
- a search for an exact phrase from the new description returns the skill;
- the skill's text, as served, contains the change you made.

If it doesn't show up, allow a minute and retry once before you call it a failure.
Then say which step failed.

## 4. Use it in this session

If the setup lets you read a skill by name (a catalogue read tool, or the file in
the plugin cache), load the new text now and run the task through it. That gives
"skill, then task" its point. If nothing can load it before a restart, follow the
written steps by hand and note where the text and the run disagree.

## Report

One line:

```
reindex: <plugin>:<skill> live at <commit> (verified by <check>); loaded in session
```
