---
title: Upgrade Guide
---

General Upgrade Notes
---------------------

The OnDemand module for Open XDMoD should be upgraded at the same time as the
main Open XDMoD software. The upgrade procedure is documented on the [Open
XDMoD upgrade page](https://open.xdmod.org/upgrade.html). Downloads of RPMs and
source packages for the OnDemand module for Open XDMoD are available from
[GitHub][github-release].

Additional 11.5.0 Upgrade Notes
-------------------

The `request_path` column of the `modw_ondemand.staging` table has been
narrowed to `varchar(255)`, and the `reverse_proxy_port` column of the
`modw_ondemand.normalized` table has been changed to `smallint unsigned`.
Both tables are truncated at the start of every ingest, before their
definitions are brought in line with the new configuration, so the columns are
always altered while the tables are empty and no existing data can conflict
with the new types. No action is required.

[github-release]: https://github.com/ubccr/xdmod-ondemand/releases/tag/v{{ page.rpm_version }}
