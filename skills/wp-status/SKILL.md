---
name: wp-status
description: >-
  Read WordPress version, visitor PHP, and pending plugin and theme updates
  for one named site before any apply. Use when starting a site, or when
  someone asks what is on the site or which updates are pending.
---

# wp-status

Read-only. Do not update anything in this skill.

If the site name is unknown, ask. Do not invent a host.

## Run

On SSH:

```bash
wp core version
wp core check-update
wp plugin list --fields=name,status,version,update_version,update
wp theme list --fields=name,status,version,update_version,update
```

On Pantheon, prefix with `terminus wp <site>.<env> --`. Also list upstream updates. The dashboard upstream version and the WordPress.org version can differ. Report both.

## Show

1. **WordPress.** Installed version and every available bump, minor and major.
2. **PHP.** The version visitors run (Site Health, the host panel, Pantheon env info, or the WP Engine API). If the only number you have is `php -v` over SSH, label it CLI PHP and say visitor PHP is unknown. Do not present the CLI version as the visitor version.
3. **Plugins.** Rows with an update available. Rows the host marks unavailable are unregistered or unlicensed. Say that WP-CLI will not install those until they are licensed.
4. **Themes.** Same split: available versus unregistered.

If a private inventory lists plugins to skip, mention them. Do not treat that list as a decision for this run unless the person already said to skip them.

## Then stop

Wait until they say what to apply. Updating is `wp-apply`: one change at a time, then the public URL, then revert on failure.
