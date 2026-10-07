# How an agent should use WP-CLI

wp-cli-agent is a set of skills an AI agent follows when it runs WP-CLI on a live site. The agent names one site, reads status, and waits. It then edits content or updates core, plugins, and themes, loads the public URL, and reverts that change if the page fails. It is for developers who already reach WordPress over SSH or Terminus and want that work done without wp-admin. It does not update a whole fleet in one command.

Command syntax for search-replace, database export, cron, cache, and multisite lives in [WordPress/agent-skills](https://github.com/WordPress/agent-skills) (`wp-wpcli-and-ops`). Install that skill beside these. This repo does not copy it.

## For humans

You name one site you already reach with WP-CLI. The agent reads these skills, runs the commands, and checks the public URL. You keep the logins.

**What you need.** WP-CLI on that site, over SSH or Terminus. An agent that reads skills: Cursor, Claude Code, or Codex.

**Install, for every project on this machine:**

```bash
npx skills add veradigital/wp-cli-agent --skill wp-cli-agent wp-status wp-apply -g -y
npx skills add WordPress/agent-skills --skill wp-wpcli-and-ops -g -y
```

Drop `-g` to install into the current project only. Install both commands. The first is this procedure. The second is WordPress's command reference. Leave the files where the installer puts them.

To work inside this repo, clone it and open it. `.cursor/skills`, `.claude/skills`, and `.codex/skills` already point at `skills/`. `AGENTS.md` is already in effect. The files you edit are in `skills/`.

**What to say.** Name the site.

- "What's pending on example.com?"
- "Update plugins on example.com."
- "Change the hours on the contact page of example.com to weekdays 9–5."

The agent reports WordPress, visitor PHP, and pending updates, then waits. Say apply when you want it to write. After a write it loads the public URL. A critical error, a PHP fatal, or a blank page means it reverts that change and stops.

**What stays with you.** Hostnames, SSH users, and tokens live in your private inventory. This repo is only the procedure.

## For agents

Follow the files. The procedure is the skill, not a guess from this page.

1. Read `AGENTS.md`. Those rules stay on for the session.
2. When someone names a site or asks for WP-CLI, read `skills/wp-cli-agent/SKILL.md`.
3. Read `skills/wp-status/SKILL.md` and run it before any write. Report WordPress, the PHP version visitors get, and pending plugins and themes. Then stop.
4. Read `skills/wp-apply/SKILL.md` only after they say to apply, update, or deploy.
5. A content edit uses the content steps in `wp-cli-agent`. Fetch `post_content`, edit that copy, write it back, and load the public URL.
6. Search-replace, database export, cron, cache, and multisite: read the installed skill `wp-wpcli-and-ops`. Leave those commands there.
7. If SSH, Terminus, or the host WP-CLI gateway is missing, say so and stop.
8. Link `skills/<name>` into another project. Keep one copy of the markdown.

## What you can ask an agent to do

| Work | By hand in wp-admin | WP-CLI alone | With these skills |
| --- | --- | --- | --- |
| See WordPress, PHP, and pending updates | Open the dashboard | `wp core version`, `wp plugin list` | `wp-status` on one named site, then stop |
| Change page content | Edit in the builder | Possible, rarely done | Read the current post, write that copy, open the public URL |
| Update plugins, themes, or core | Click Update | `wp plugin update` | `wp-apply`: one item at a time, health check, revert on failure |
| Update every site you host | Repeat the clicks | Easy to over-reach | A full-fleet check is read-only. Updates stay on one named site |

## Skills

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
