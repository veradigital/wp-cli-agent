---
name: wp-cli-agent
description: >-
  Run WordPress work through WP-CLI on one named live site. Use when someone
  names a WordPress site, asks an agent to check or edit it, or asks for core,
  plugin, or theme updates. Do not update every site at once.
---

# wp-cli-agent

WP-CLI does the work. This skill decides which site, when to write, and when to undo.

Reach the site the way that host already allows: SSH and `wp`, Terminus (`terminus wp <site>.<env> -- …`), or the host's WP-CLI gateway. If none of those work, say so and stop. Do not invent a wp-admin workflow.

For search-replace, database export and import, cron, cache, and multisite, follow the WordPress skill `wp-wpcli-and-ops` ([WordPress/agent-skills](https://github.com/WordPress/agent-skills)). Do not duplicate those steps here.

## Rules

1. Name one site. Do not invent a hostname, SSH user, or path.
2. Do not print tokens, private keys, or `.env` values.
3. Read status before any write, unless the person already said to apply a specific change.
4. Updates run on that site only. A loop over every site is allowed for read-only checks, not for `plugin update`, `theme update`, or `core update`.
5. After a write, load the public URL. A critical error, a PHP fatal, or a blank page means revert that change and stop.
6. Write a proven connection fact (port, WordPress path, WP-CLI binary, skipped plugin) in the private inventory for that site. Do not put client hostnames in this repo.

## Which skill

- Someone asks what is on the site, or this is the first step: follow `wp-status`. Then stop.
- They say to apply, update, or deploy pending updates: follow `wp-apply`.
- They ask to change page or post content: use the content steps below. Do not run a plugin update for a content edit.

## Content

1. Find the post or page (`wp post list`, by slug or URL).
2. Fetch the current `post_content`. Edit that copy. Builder markup is shortcodes or blocks in that field. Do not rebuild the page from memory.
3. Write it back with `wp post update`.
4. Load the public URL and confirm the change is visible.
5. If the page fails, restore the previous content.

## Host doors

- **Pantheon.** `terminus wp`. Core comes from the upstream, not `wp core update`. Prefer Dev, then a Test deploy, then Live. A git deploy can overwrite plugin files changed only on disk.
- **WP Engine.** SSH gateway. Core and PHP are changed in the User Portal. Apply plugins and themes on production unless asked for staging. Do not copy staging over production.
- **SSH (SiteGround, IONOS, GoDaddy cPanel, others).** `wp` in the WordPress root. Core updates use `wp core update` and `wp core update-db` when status shows a bump. GoDaddy Managed WordPress and IONOS plans with no shell are out of scope.
