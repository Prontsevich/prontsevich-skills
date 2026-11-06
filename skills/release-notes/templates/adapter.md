# Release notes

<!-- Written by release-notes. Edit freely; the skill reads this file before every list. -->

**Reader:** <who reads the list, and the language of its prose>.
**Triggered by:** <deploy skill, command, script or CI job that ends with the list>.

## Range

<How a version's range is found. Name the release branch and the exact commands
that give the current and the previous marker, for example:>

```
git log --first-parent --format='%h %s' --grep='<bump subject pattern>' -2 <release branch>
git log --no-merges --format='%h %s%n%b' <previous>..<current>
```

<What to do for a rollback or a redeploy of an older version.>

## Names

<Where the product's user-facing names live: templates, menu registries, string
catalogues. The list takes labels from there.>

## Exclusions

<Project-specific commit types or paths with no visible effect, beyond the
skill's defaults. Delete the section when there are none.>

## Accepted example

<The first list the reader accepted, verbatim, and one line on what its range
also held that the list left out.>
