# wp-cli-agent

Humans install these skills and name one site. You follow `skills/`. Read `wp-status` and wait. Apply with `wp-apply` only after they say to. Content edits use the content steps in `wp-cli-agent`.

These rules apply in this repo. The procedures are the skills in `skills/`. There is one copy. `.cursor/skills`, `.claude/skills`, and `.codex/skills` are links to it.

- Name one site. Do not update every site in one command. A read-only check across sites is allowed.
- Do not invent a hostname, SSH user, or path. Do not print tokens or private keys.
- Read status and wait before writing, unless the person already asked for a specific change.
- After a write, load the public URL. A critical error, a PHP fatal, or a blank page means revert that change and stop.
- Search-replace, database export, cron, cache, and multisite commands: use the WordPress skill `wp-wpcli-and-ops` from [WordPress/agent-skills](https://github.com/WordPress/agent-skills). Do not restate those commands here.
- Do not add client hostnames, keys, or tokens to this repo.
