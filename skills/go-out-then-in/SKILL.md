---
name: go-out-then-in
description: Turn private skills, a private plugin or a private tool into a public, generic open-source release, then graft the user's private details back on as an overlay so their own install uses the public version like any other user's. Out (extract and depersonalise), publish, then in (profile/overlay plus a slimmed private plugin that only keeps what is truly theirs). Use on "go out then in", "bring it out then in", "can this be public?", "make this generic so others can use it", "share this with my partner/team but keep my details private", or when a private plugin turns out to hold mostly reusable mechanics.
---

# Go out, then in

Something built privately often turns out to be mostly reusable: the mechanics are generic,
only the names, numbers, paths and preferences are personal. Going out then in splits it in
two, so the generic part is shared and maintained once, and the private part shrinks to a
thin overlay.

**Out:** extract the generic part into a public plugin with no personal details.
**Publish:** release and list it.
**In:** put the user's details into an overlay the public plugin reads, and strip the
moved parts out of the private original.

## 0. Read the user's procedure

Resolve the skill-genie data folder as in `reindex` (`$SKILL_GENIE_HOME`,
`$CLAUDE_USER_DATA/skill-genie/`, `~/.claude-user-data/skill-genie/`). If it holds
`go-out-then-in.md`, follow it: it names the user's marketplaces, release rules, sync
route and private-overlay location, and wins over the defaults below.

## 1. Split: what goes out, what stays in

Read every skill, command and script in the private source. Sort each into:

| Goes out (generic) | Stays in (private) |
|---|---|
| Mechanics: API calls, endpoints, verification, ordering, failure modes | Names, phone numbers, emails, chat/account ids, hosts, LAN URLs, file paths |
| Safety rules that apply to anyone (rate limits, ban risk, approval defaults) | Relationship rules, voice/tone, per-person policies |
| Validated facts about the tool (versions, quirks), dated | Anything about specific people, even as an example |

A skill that is mostly personal stays private whole; a skill that is mostly generic moves.
Where one skill mixes both, the generic version moves and the private one keeps only the
delta (or is replaced by the overlay entirely).

## 2. Design the overlay before writing

The public plugin must work for a stranger and for the user without edits. If the user
follows the **Public Plugin, Private Overlay** standard (plugin `public-private-overlay`),
its folder rules, secrets rule and "no parallel private fork" rule govern this step; its
`audit-plugins` skill is the check afterwards. Give the plugin:

- A **profile/overlay format** documented in the plugin (`references/profile.md` or
  similar): every value the skills need that is personal (identity, tool/server name,
  local conventions, routes to other tools such as "how to save a contact", "how to stage
  a file", "where to log").
- A **lookup order**: an env var, then `$CLAUDE_USER_DATA/<plugin>/`, then
  `~/.claude-user-data/<plugin>/`, then a memory/context record for hosted clients with no
  local files.
- **Several profiles** when more than one person or account may use it, chosen by name
  or `default: true`.
- A **no-profile path**: ask for the two or three essential values, do the task, offer to
  save the profile.

Routes to the user's other private tools go in the overlay as plain text ("skill X",
"script Y"), not as hard dependencies in the plugin.

## 3. Write it out

- New repo, public, named at maximum specificity, MIT unless the user says otherwise.
- **History tripwire: never make an existing private repo public without checking its
  history.** Flipping visibility (or reusing a private repo, including an empty-looking
  placeholder) publishes every past commit, not just the current tree. Run the leak check
  below over the whole history (`git log -p --all`, plus commit messages and author
  emails), not just the working tree. Clean (or zero commits): flipping it is fine. Any
  private detail or PII in history: don't rewrite history. Create a fresh public repo with
  clean content, delete the private one (confirm with the user first; it's deletion), then
  bring the public one back in as usual. Same if the private repo has forks or old releases.
- Rewrite rather than copy-and-scrub: copied text drags personal examples along. Use
  neutral examples (`Alex`, `Acme Hardware`, `15551234567`).
- Keep dated validation notes ("verified 2026-11-17 on version X"), minus personal ids.
- README: what it does, requirements, setup (the profile), cautions, and a History line
  saying where it came from.
- A short CLAUDE.md that forbids personal details in the repo.
- **Leak check before the first push**: grep the repo for the user's name, partner's and
  contacts' names, phone numbers, email domains, hostnames, private IPs and home paths.
  Zero hits, or fix first. A pushed leak in a public repo is permanent.

## 4. Publish

Push, cut the first release per the user's versioning rule, add the marketplace entry
(and changelog/catalogue steps the marketplace requires), then make it discoverable now
(`reindex`). For a plugin new to a marketplace, a full re-sync may be needed.

## 5. Come back in

1. Write the user's profile(s) in their private data area, with every value from step 2:
   real ids, aliases, routes to their private skills, and a pointer to the private plugin
   for the rules that stayed in. Commit only that file if the area is a shared repo.
2. Strip the moved skills from the private plugin. In its README, say which public plugin
   now holds them and that the profile carries the details. Update cross-references in
   the remaining private skills to the new public names.
3. Bump the private plugin's version and its marketplace entry; re-index both plugins.
4. If the private plugin is now empty or near-empty, fold the remainder into the profile
   and retire it (the user's consolidation procedure applies, if they have one).

## 6. Verify

- Catalogue/plugin listing shows the public plugin with every moved skill, and the private
  one without them.
- Reading a public skill as served contains no personal detail.
- Run one real task through the public skill with the user's profile and confirm it
  behaves as the private skill did.

## Report

Public repo and release, marketplace, skills moved, profile path(s), what stayed private
and why, leak check result.

---
First worked through 2026-11-17: a private personal-WhatsApp plugin's send, new-number,
group, archive and health skills went out as a public WAHA plugin with per-account
profiles, so a second household member could use the same skills on their own account.
