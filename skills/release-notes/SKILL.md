---
name: release-notes
description: Write the short, user-facing list of what a deployed or released version changed, as one flat list of terse bullets with no plan, wave or commit names. Use after a deploy or release step (a project's deploy skill can end with it), when asked what a version contains, or to set up the project's release-notes adapter `docs/agents/release-notes.md`.
---

# Release Notes

Tell the reader what changed for the people who use the product in one version,
and nothing else. The reader is usually the project owner or a tester deciding
what to look at, so the list has to be short enough to read at a glance and
precise enough to find each change in the product.

## The adapter

`docs/agents/release-notes.md` records this project's choices:

- how a version's range is found (release markers, branch, first parent or not);
- who reads the list and in which language;
- where the product's own names live (templates, menus, string catalogues);
- project-specific exclusions;
- one accepted example.

Read it first when it exists. The adapter wins over the defaults below, and the
format rules below still apply.

Without an adapter, discover the range with the defaults, write the list, and
offer to record the discovered choices as the adapter. When `project-docs`
bootstraps or adapts documentation, it asks this skill to write the adapter: run
**Set up** below without producing a list.

## Find the range

A version is everything between its release marker and the previous one. Look
for markers in this order and use the first that the history actually carries:

1. Version tags (`vX.Y.Z` or the project's pattern): `git describe --tags --abbrev=0 <ref>^`
   gives the previous one.
2. Version bump commits of the package manifest (`package.json`, `pyproject.toml`,
   `Cargo.toml`, …), on the release branch's first parent.
3. A changelog heading or a deploy log that names the previous version.

Neither tags nor bumps nor a log: ask which commit the previous release was.
When the user says an intermediate version never reached its environment, widen
the range to the last version that did.

```
git log --no-merges --format='%h %s%n%b' <previous-marker>..<this-marker>
```

Read the subjects and bodies first. Open a diff, a plan, or a tracker item only
when a subject does not say what the user sees.

## What makes the list

- **Kept:** every change a user or tester can see or feel. That covers a new or
  redrawn screen, page or dialog, a changed control, a fixed bug, a removed
  feature, and a speed or loading change.
- **Dropped:** anything with no visible effect. That covers documentation, plans,
  design boards and mockups, tests, chores, build and tooling, refactors with no
  behaviour change, and merge commits.
- Several commits that land one visible change make **one** bullet.
- Nothing visible in the range: say so in one line, and name what the range did
  carry instead (for example, "documentation and design boards only").

## The format

- One flat bulleted list headed by the version in bold. No groups, sub-bullets
  or plan, wave or package names. No commit hashes, file, component or service
  names.
- One line per bullet: `Screen or area: what changed`, at most about twelve
  words.
- Say what the user sees now, not how it was done.
- Order: new and redrawn screens first, then behaviour changes, then fixes.
- Nothing after the list: no summary, offer or follow-up question.

### Names stay as the product writes them

The prose follows the reader's language: the adapter's choice, otherwise the
language of the conversation. Names stay exactly as the product writes them, in
the product's language. That covers screens, dialogs, panels, buttons, fields,
columns, and product and technical terms.

Do not translate a name, and do not coin one for something the product leaves
unnamed. A translated name is a guess, and the reader then has to work out which
screen it means. Take a button or field label from the template or string
catalogue, not from memory.

## Example

A product written in English, with notes for a Russian-speaking owner:

```markdown
**2.4.0**
- Invoices: новый экран со списком и фильтром Status.
- Settings: раздел Notifications открывается одной страницей, без вкладок.
- Export: кнопка Download CSV больше не зависает на больших отчётах.
```

The range also held a plan, a design board and test fixes. None of those is in
the product, so none of them is in the list.

## Set up

Write `docs/agents/release-notes.md` from
[the adapter template](templates/adapter.md):

1. Find the release markers the history carries and the branch releases are cut
   from. Record the exact commands that list the previous and the current marker.
2. Find what triggers a release: a deploy skill, command or script, a CI job.
   Note it in the adapter, and add one line to that entry point's report step
   asking for this skill on success, when the user agrees to edit it.
3. Record who reads the list and in which language. Ask only when the repository
   and the conversation do not settle it.
4. Name the places that hold the product's user-facing names.
5. Leave the example empty until the reader accepts a first list; then record it.

When the adapter exists, repair what drifted instead of rewriting it. Writing the
adapter is a local documentation change: show it in the report and commit it
only with the user's approval.
