# Maintaining this repository

- Each skill lives in `skills/<name>/` with `SKILL.md` and `agents/openai.yaml`.
  The folder name equals the `name` in the skill's frontmatter.
- Each skill is listed in `.claude-plugin/plugin.json` (`skills`) and in the
  root `README.md`. Add, rename, or remove a skill in all three places at once.
- Keep real files under `skills/`: Codex drops symlinks when it installs a plugin.
- Skills reach each other by skill name through the Skill tool, and each shared
  reference lives inside the skill that owns it.
- Bump `version` in both `.claude-plugin/plugin.json` and the root
  `plugin.json` (Agent Plugins v1, read by Hermes) for every release; installed
  plugins update only when the version changes. Tag the release commit with an
  annotated `vX.Y.Z` tag matching that version and push the tag with it.
- Run `claude plugin validate . --strict` after editing `.claude-plugin/`.
- `.claude-plugin/marketplace.json` also lists plugins kept in their own
  repositories. Use a `url` source with the Git URL: it is the source type
  Claude Code, Codex and omp were each verified to install.
- Hermes scans a plugin before installing it and blocks a line that puts a
  modification verb next to an agent config filename (`AGENTS.md`,
  `CLAUDE.md`). Keep such a verb and the filename on separate lines when the
  sentence does not mean modifying that file.
- Repository artifacts are written in English.
