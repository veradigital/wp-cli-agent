# How an agent should use WP-CLI

wp-cli-agent is a set of skills an AI agent follows when it runs WP-CLI on a live site. The agent names one site, reads status, and waits. It then edits content or updates core, plugins, and themes, loads the public URL, and reverts that change if the page fails. It is for developers who already reach WordPress over SSH or Terminus and want that work done without wp-admin. It does not update a whole fleet in one command.

Command syntax for search-replace, database export, cron, cache, and multisite lives in [WordPress/agent-skills](https://github.com/WordPress/agent-skills) (`wp-wpcli-and-ops`). Install that skill beside these. This repo does not copy it.

## What you can ask an agent to do

| Work | By hand in wp-admin | WP-CLI alone | With these skills |
| --- | --- | --- | --- |
| See WordPress, PHP, and pending updates | Open the dashboard | `wp core version`, `wp plugin list` | `wp-status` on one named site, then stop |
| Change page content | Edit in the builder | Possible, rarely done | Read the current post, write that copy, open the public URL |
| Update plugins, themes, or core | Click Update | `wp plugin update` | `wp-apply`: one item at a time, health check, revert on failure |
| Update every site you host | Repeat the clicks | Easy to over-reach | Not allowed. A full-fleet check is read-only |

## Skills

The files you edit are in `skills/`. Cursor, Claude Code, and Codex each look in a different folder. Those folders are links to `skills/`, so there is one copy.

| Skill | When it applies |
| --- | --- |
| `wp-cli-agent` | Someone names a WordPress site, or asks an agent to use WP-CLI |
| `wp-status` | First step. Read-only. Then wait |
| `wp-apply` | They said to apply updates on that site |

| Tool | Path in this repo |
| --- | --- |
| Cursor | `.cursor/skills/` |
| Claude Code | `.claude/skills/` |
| Codex | `.codex/skills/` |

Short rules that should always be in effect are in `AGENTS.md`. Cursor and Codex read that file. Claude Code reads it when the repo has no `CLAUDE.md`.

To use the skills in another project, link `skills/<name>` into that tool's skills directory. Do not keep a second copy of the markdown.

Install the WordPress command skill next to these, from their repo:

```bash
npx skills add WordPress/agent-skills --skill wp-wpcli-and-ops
```

## How an agent should behave

1. Name one site. If the name is unknown, ask. Do not invent a hostname.
2. Run status. Report WordPress, the PHP version visitors get, and pending plugins and themes. Wait until someone says to apply.
3. Edit content or apply updates through WP-CLI. One plugin or theme at a time.
4. Load the public URL. A critical error, a PHP fatal, or a blank page is a failure.
5. On failure, revert that change and stop.
6. Pantheon core comes from the platform upstream. WP Engine core is changed in the User Portal. SSH hosts use `wp core update`.

## Frequently Asked Questions About wp-cli-agent

### Can an AI agent update WordPress plugins without wp-admin?

Yes, if WP-CLI is already reachable over SSH or Terminus. The agent updates one plugin, loads the public URL, and reverts that plugin if the page fails.

### Can an agent edit WordPress content with WP-CLI?

Yes. Page and post content can be changed with WP-CLI, including builder markup stored in the post. Fetch the current content, edit that copy, then load the public URL and confirm the edit is there.

### Should an agent update every site at once?

No. Updates run on one named site. Checking several sites is allowed only when the commands do not write.

### What if the update breaks the site?

The agent reverts that plugin or theme to the version it recorded and checks the public URL again. It does not continue to the next update.

### Does this replace WP-CLI?

No. WP-CLI still does the work. These skills tell the agent which command to run, when to stop, and when to undo.

### Which hosts does this cover?

Any host where WP-CLI runs. Pantheon is reached with Terminus, and core updates come from the Pantheon upstream. WP Engine is reached over its SSH gateway, and core updates are done in the WP Engine User Portal. SiteGround, IONOS, GoDaddy cPanel, and other SSH hosts use `wp core update` when a core update is pending. A plan with no shell stays on the dashboard.

### Do I put client sites in this repo?

No. Keep hostnames, SSH users, and tokens in a private inventory. This repo only has the procedure.

### Does this only work in Cursor?

No. The same `SKILL.md` files are linked for Cursor, Claude Code, and Codex. `AGENTS.md` holds the short rules those tools read.

## License

[MIT](LICENSE)
