---
name: wp-apply
description: >-
  Apply WordPress core, plugin, and theme updates on one named site with a
  backup, a health check, a browser load, and a revert on failure. Use when
  someone says update, apply, or deploy the pending updates after status.
---

# wp-apply

One named site. The public page is the check: catch a fatal or a broken homepage, not a design review.

Run `wp-status` first if you do not already have the pending list.

Search-replace, database import, and cron stay in the WordPress skill `wp-wpcli-and-ops`. This skill is the live update.

## What to include

Include available core (minor and major), plugins with an update available, and themes with an update available, unless they named something to hold or a private inventory says to skip it.

Skip unregistered or unlicensed rows. WP-CLI cannot install those.

Pantheon core is an upstream apply on Dev, done before plugins. SSH hosts run `wp core update` and `wp core update-db` first when core is behind, then health, then plugins and themes.

## Health

After every core, plugin, or theme change, and after each Pantheon deploy:

1. WP-CLI boots, `wp option get siteurl` works, and the site URL returns HTTP 200, 301, or 302 without a critical-error body.
2. Open that URL in a browser. Fail on a WordPress critical-error screen, a PHP fatal, a blank page, or a 5xx document.
3. Fail on an uncaught exception in the console. Ignore third-party tracker noise.
4. The homepage is enough. This is an error check.

Pantheon Dev and Test use the `pantheonsite.io` URL. Live and SSH hosts use the public URL.

CLI or browser failure: revert that change and check the URL again. Do not continue.

## Backup

Take a backup before the first write. Stop if it fails.

- **Pantheon.** Environment backup of code, database, and files on Live before a Live deploy. Do not also export the database with a plugin.
- **Anywhere else.** If WP Migrate DB is active, export a gzipped SQL file outside the web root. That is the database only. Code undo is still reinstalling the previous plugin or theme version.
- **No backup command and no export plugin.** Say so, then continue. Revert is reinstalling the old version.

## Pantheon

One plugin per Dev commit. One Test deploy. One Live deploy.

1. Backup Live.
2. Upstream on Dev if core is in the set. Health and browser.
3. On Dev, each plugin: record the old version, update, health and browser. Commit, or reinstall the old version on failure. Then themes.
4. Deploy Test. Health and browser. On failure, stop. Do not deploy Live.
5. Deploy Live. Health and browser. On failure, restore the Live backup or deactivate the plugin that just went out.
6. Deploy note: only what changed, for example `updated wp to 7.1, plugins and theme`. Do not name a plugin. Do not say the note is a copy from Dev.

## SSH, including WP Engine

Production, unless they asked for staging. Staging is a second apply of the same slugs. Do not copy staging onto production.

1. Backup, as above.
2. Core first if it is behind: `wp core update`, `wp core update-db`, health, browser. WP Engine core is the User Portal, not `wp core update`.
3. Each plugin or theme: record the old version, update, health and browser. On failure, `wp plugin install <slug> --version=<old> --force` (or the theme equivalent), activate if it was active, health and browser again, then skip that slug.
4. There is no Dev or Test hop. The files are already on the environment you updated.

## After the site is healthy

If Broken Link Checker is installed, list undismissed broken URLs.

- A redirect: update the link in the post to the destination, then dismiss the row.
- The same URL opens: dismiss the row.
- A 404 or 410: search the site. One clear match gets retargeted. Otherwise unlink the anchor and keep the text, then dismiss the row and report what was unlinked.
- A row you could not verify: report it. Do not guess an edit.

If Broken Link Checker is not installed, skip it.

Report each plugin or theme as passed or reverted, whether the browser check passed, and where the backup went. Do not mention Terminus on an SSH host.
