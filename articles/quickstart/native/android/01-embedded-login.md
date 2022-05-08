---
title: Embedded Login
description: This tutorial will show you how to integrate Lock v2 in your Android project in order to present an Embedded Login screen.
seo_alias: android
budicon: 448
---

This tutorial will show you how to integrate Lock v2 in your Android project in order to present an Embedded Login screen.

<%= include('../../../_includes/_package', {
  org: 'auth0-samples',
  repo: 'auth0-android-sample',
  branch: 'embedded-login',
  path: '01-Embedded-Login',
  requirements: [
    'Android Studio 2.3',
    'Android SDK 25',
    'Emulator - Nexus 5X - Android 6.0'
  ]
}) %>

<%= include('_includes/_lock') %>

<%= include('_includes/_manifest') %>

<%= include('_includes/_lock_login') %>

### Optional: Log In with Social Connections

To have a simple login mechanism through social connections, all you have to do is enable them in your account's [dashboard](${manage_url}/#/connections/social). Every social connection you switch on there, will appear in the login screen of your app.
