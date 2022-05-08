---
section: libraries
title: Migrating from Lock v10 to v11
description: How to migrate from Lock v10 to v11
toc: true
---
# Migrating from Lock v10 to v11

This guide includes all the information you need to update your Lock v10 application to [Lock v11](/libraries/lock).

## Migration steps

<%= include('../../_includes/_get_lock_latest_version') %>
<%= include('../../_includes/_configure_embedded_login', { library : 'Lock v11'}) %>
<%= include('../../_includes/_change_get_profile') %>
<%= include('../../_includes/_oidc_conformant') %>

## Behavioral changes in Lock v11

<%= include('../../_includes/_hosted_pages') %>
<%= include('../../_includes/_popup_mode') %>
<%= include('../../_includes/_last_logged_in_window') %>
<%= include('../../_includes/_ip_ranges') %>
<%= include('../../_includes/_default_values_lock') %>
