---
name: skill-or-not
description: Decide whether a task should become a reusable skill before doing it as a one-off. Runs the five-step checklist (repeatable? automatable? already in our library? already written by someone else? otherwise create it) and acts on the verdict. Use when the user says "should this be a skill", "is this a skill candidate", "run the skill checklist", "to skill or not to skill", "I think this is replicable", or "we need a skill for this, and also do X" (which also sets the order of skill and task).
---

# To skill or not to skill

Run the checklist below against the task in front of you, which is either the one the
user just described or the one they point at. Work through the steps in order and stop
at the first exit. Record the evidence for each answer, not just yes or no.

## 1. Is it repeatable?

Look at two things:

- **Shape.** Would anyone plausibly do this only once? Tasks like "reconcile this
  month's invoices", "triage this inbox" or "check this site on mobile" recur by
  their nature. Tasks like "rename this one file" do not.
- **History.** Check the agent's memory, past sessions, shell history, and the
  current repo for earlier runs of the same pattern. Two earlier occurrences settle
  it.

Lean towards yes. If the answer is clearly no, **exit: do the task.**

## 2. Can it be automated or assisted?

Can available tools do the task, in whole or in part? That includes MCP servers,
APIs, browser automation, the shell, or written guidance an agent could follow step
by step. Partial counts: a skill that does the lookup and leaves the decision to the
user still pays off. Set the bar low.

If nothing about it can be automated or guided, **exit: do the task.**

## 3. Is it already in the library?

Skill discovery should already have surfaced a matching skill. If it didn't, search
explicitly before you conclude nothing exists:

- the skills and plugins installed in this session;
- the user's plugin catalogue or marketplaces (public, private, third-party), using
  whatever search tool the environment provides;
- the local clones of the user's plugin repos.

A search that returns nothing may be blind rather than conclusive: the wrong index,
an empty index, or a tool that can't see that namespace. Make sure the search
actually reaches the library before you trust a miss.

The opposite failure is just as likely. Hybrid and semantic search always return
*something*, and an unrelated result can score as high as a true match. Describe the
task in plain words, then decide by reading each result's description. The score
alone tells you nothing.

If you find a match, **exit: use that skill for the task.** If it needs a small fix
to fit, fix it in its plugin repo and note that.

## 4. Has someone else already written it?

Search public sources for third-party skills, plugins, or boilerplates covering the
workflow: official vendor plugins, public marketplaces, GitHub. Then judge how
personal the workflow is:

- **Fits as-is.** Add it to the user's third-party collection as a pointer to the
  upstream repo, pinned to a ref. Don't copy it. Then use it for the task. **Exit.**
- **Close, but the workflow is individual.** Use it as the base for step 5 and credit
  the upstream in the new skill.
- **Nothing suitable.** Go to step 5.

## 5. Create it

1. **Pick the plugin.** It goes in the public plugin if nothing in it is personal:
   no names, accounts, hosts, or internal infrastructure. Otherwise it goes in the
   private one. A skill that would only be public after depersonalising can ship
   publicly in a generic form, with the specifics kept in a private sibling.
2. **Write the skill.** Follow the conventions of the target plugin. Don't invent
   new ones. If a skill-extraction skill is available (for example, one that
   turns the procedure just followed into a `SKILL.md`), use it for the draft.
3. **Deploy it** the way the user's plugins normally go live, for example push and
   index or sync. Then confirm the skill is actually discoverable.
4. **Validate it on the real task.** Validate the skill against a real run of the
   task you were about to do. Fix whatever that run exposes before you call the skill
   done. Whether the skill or the task comes first is set by the order of operations
   below.

## Order of operations

When the user asks for the skill and the task together ("we need a skill for this,
but also do X"), choose one of three orders before you start.

| Order | Sequence | When it fits |
| --- | --- | --- |
| **A. Task, then skill** (default) | Do the task, then write the skill from the run | Most cases. The skill records what actually happened, pitfalls included, not what you expected to happen. |
| **B. Skill, then task** | Write the skill, re-index or sync it, then do the task | The procedure is already clear, or the context to capture is fresh and would be lost in a long task. |
| **C. Skill, task, update** | Draft the skill, run the task through it, then fold the delta back in | Complex or high-value workflows. The run tests the draft, and the update records pitfalls, wrong turns and steps that turned out different. |

**Asking.** If you have a way to ask (a multiple-choice question tool), offer the
three orders as a menu before you start: A first and labelled recommended, then C,
then B. Ask once, with no extra explanation. If you can't ask, or the run is
unattended, use A and say so in one line ("order: task, then skill").

**A: don't forget the skill.** At the start, record the pending skill where it will
be seen again, such as a task-list item or a one-line note in your reply. Write the
skill before you report the task as done, and include it in the final report.

**B: re-index straight away.** A new skill is usually not available in the session
that wrote it. Right after deploying it, run skill `skill-genie:reindex`. It follows
the user's own procedure if they have saved one, verifies the skill is live, and
loads it into the session where it can. Then run the task through the skill. If it
can't be loaded yet, follow the written steps by hand and note any gap between the
text and the run.

**C: update with the delta.** After the task, compare the run with the draft. Add
pitfalls, fix steps that were wrong or missing, and remove steps that turned out to
be unnecessary. Redeploy and re-index. Report what changed ("skill updated: 2
pitfalls, 1 step reordered").

## Delegate the writing

The live workflow is the user's priority, so keep the main thread on the task. Hand
skill writing that comes after the task (the skill in A, the update in C) to a
background subagent, when the environment has them:

1. **Brief it fully.** The subagent sees only the brief. Include the task, the exact
   steps that worked in order, commands and tool calls with their real arguments,
   the pitfalls and wrong turns, where the skill goes (plugin, visibility), and the
   draft's path for C. Strip anything personal if the destination is public.
2. **Point it at the writing skill.** If a skill-capture skill is available (for
   example `skill-this:skill-this` to write, `skill-this:improve-skill` to update),
   tell the subagent to follow it. Otherwise give it the target plugin's
   conventions.
3. **Have it finish the job.** It saves, commits and pushes as the user's shipping
   rules allow, then runs `skill-genie:reindex`.
4. **Keep working, then check.** Carry on with the user's work. When the subagent
   reports, check the result yourself: read the skill as served and confirm the
   pitfalls made it in. Then name the skill in your report.

Write it yourself instead when no subagents are available, when the user wants to
review the skill before it ships, or for B, where the task waits on the skill
anyway.

## Approval

Steps 1–4 are read-only, so go ahead with them. Creating a skill, publishing it, or
adding it to a collection follows the user's standing approval rules. If none are
known, state the verdict and the plan in two lines and ask once.

## Report

Finish with a short verdict, for example:

```
skill-or-not: step 5 → new skill `invoice-reconcile` in <private plugin>
evidence: monthly pattern (3 prior sessions); the vendor API covers it
order: A (task, then skill)
first run: <task> completed; skill written from the run by a subagent, reindexed
```
