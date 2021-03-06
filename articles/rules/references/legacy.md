---
description: Learn what Rules are and how you can use them to customize and extend Auth0's capabilities.
toc: true
topics:
  - rules
  - extensibility
contentType:
  - reference
  - how-to
useCase: extensibility-rules
---

# Rules

::: version-warning
This document covers an outdated version of the Auth0 authentication pipeline and the way rules can be used. We recommend you use the latest version. For more on the latest authentication pipeline refer to [Introducing OIDC Conformant Authentication](/api-auth/intro).
:::

**Rules** are functions written in JavaScript that are executed when a user authenticates to your application. They run once the authentication process is complete and you can use them to customize and extend Auth0's capabilities. They can be chained together for modular coding and can be turned on and off individually.

![Rules Flow](/media/articles/rules/flow.png)

1. An app initiates an authentication request to Auth0.
1. Auth0 routes the request to an Identity Provider through a configured connection.
1. The user authenticates successfully.
1. The `user` object representing the logged in user is passed through the Rules pipeline, and returned to the app.

Among many possibilities, Rules can be used to:

* __Profile enrichment__: query for information on the user from a database/API, and add it to the user profile object.
* Create __authorization rules__ based on complex logic (anything that can be written in JavaScript).
* __Normalize attributes__ from different providers beyond what is provided by Auth0.
* Reuse information from existing databases or APIs for migration scenarios.
* Keep a __white-list of users__ and deny access based on email.
* __Notify__ other systems through an API when a login happens in real-time.
* Enable counters or persist other information. (For information on storing user data, see: [Metadata in Rules](/rules/metadata-in-rules).)
* Enable __multifactor__ authentication, based on context (such as last login, IP address of the user, location, and so on).

## Video: Using Rules

Watch this video learn all about rules in just a few minutes.

<%= include('../../_includes/_video', { id: 'g7dy1fpwc3' }) %>

## Rule Syntax

A Rule is a function with the following arguments:

* `user`: the user object as it comes from the identity provider. For a complete list of the user properties, see [User Profile Structure](/users/references/user-profile-structure).

* `context`: an object containing contextual information of the current authentication transaction, such as user's IP address, application, location. For a complete list of context properties, see [Context Argument Properties in Rules](/rules/references/context-object).

::: note
Due to the asynchronous nature of Node.js, you __must__ call the `callback` function. If not, this acts as a blocker and the next rule will not execute. The entire rules sequence must complete within __20 seconds__, otherwise the process times out.
:::

### Global Functions

If you need to use a function in multiple Rules, you can place the shared function in its own Rule. Within this rule, assign the function to the global object so that you can later call the function in a separate rule.

```js
if (!global.foo) {
  global.foo = function () { };
}

if (!global.bar) {
  global.bar = function (baz) { };
}
```

Rules containing shared functions should be placed at the top of the [Rules list in the Management Dashboard](${manage_url}/#/rules). If this is not the case, calling these functions results in an undefined function error when the Rules execute.

## Examples

To create a Rule, or try the examples below, go to [New Rule](${manage_url}/#/rules/create) in the Rule Editor on the dashboard.

### *Hello World*

This rule will add a `hello` attribute to all users authenticating through any provider.

```js
function (user, context, callback) {
  user.hello = 'world';
  console.log('===> set "hello" for ' + user.name);
  callback(null, user, context);
}
```

::: note
You can add `console.log` lines for [debugging](#debugging) or use the [Real-time Webtask Logs Extension](/extensions/realtime-webtask-logs).
:::


### Add roles to a user

In this example, all authenticated users will get a **guest** role, but `johnfoo@gmail.com` will also be an **admin**:

```js
function (user, context, callback) {
  user.roles = [];
  // only johnfoo is admin
  if (user.email === 'johnfoo@gmail.com') {
    user.roles.push('admin');
  }

  // all users are guest
  user.roles.push('guest');

  callback(null, user, context);
}
```

At the beginning of the rules pipeline, John's `user` object will be:

```js
{
  "email": "johnfoo@gmail.com",
  "family_name": "Foo",
  "user_id": "google-oauth2|103547991597142817347"
  //... other properties ...
}
```

The `context` object will be:

```js
{
  "clientID": "...client_id_of_the_app...",
  "clientName": "my app",
  "clientMetadata": {
    "myKey1": "myValue2",
    "myKey2": "myValue2"
  }
  "connection": "google-oauth2"
}
```

After the rule executes, the output that the application will receive is the following `user` object:

```js
{
  "email": "johnfoo@gmail.com",
  "family_name": "Foo",
  "user_id": "google-oauth2|103547991597142817347",

  //... other props ...

  "roles": ["guest", "admin"]  // NEW PROPERTY ADDED BY THE RULE
}
```

::: note
Properties added in a rule are __not persisted__ in the Auth0 user store. Persisting properties requires calling the Auth0 Management API.
:::

### Deny access based on a condition

In addition to adding and removing properties from the user object, you can return an *access denied* error.

```js
function (user, context, callback) {
  if (user.roles.indexOf('admin') === -1) {
    return callback(new UnauthorizedError('Only admins can use this'));
  }

  callback(null, user, context);
}
```

This will cause a redirect to your callback url with an `error` querystring parameter containing the message you set. (such as `https://yourapp.com/callback?error=unauthorized&error_description=Only%20admins%20can%20use%20this`). Make sure to call the callback with an instance of `UnauthorizedError` (not `Error`).

::: note
Error reporting to the app depends on the protocol. OpenID Connect apps will receive the error in the querystring. SAML apps will receive the error in a `SAMLResponse`.
:::

## Create Rules with the Management API

Rules can also be created by creating a POST request to `/api/v2/rules` using the [Management APIv2](/api/management/v2#!/Rules/post_rules).

This will creates a new rule according to the following input arguments:

- ``name``: The name of the rule. It can only contain alphanumeric characters, spaces and '-', and cannot start nor end with '-' or spaces.

- ``script`` : ??he script that contains the rule's code. This is the same as what you would enter when creating a new rule using the [dashboard](${manage_url}/#/rules/create).

- ``order``: This field is optional and contains a `number`. This number represents the rule's order in relation to other rules. A rule with a lower order than another rule executes first. If no order is provided it will automatically be one greater than the current maximum.

- ``enabled``: This field can contain an optional `boolean`. If `true`, the rule will be enabled, if it's `false` it will be disabled.

Example of a body schema:

```
{
  "name": "my-rule",
  "script": "function (user, context, callback) {\n  callback(null, user, context);\n}",
  "order": 2,
  "enabled": true
}
```
Use this to create the POST request:

```har
{
  "method": "POST",
  "url": "https://${account.namespace}/api/v2/rules",
  "headers": [{
    "name": "Content-Type",
    "value": "application/json"
  }],
  "postData": {
    "mimeType": "application/json",
    "text": "{\"name\":\"my-rule\",\"script\":\"function (user, context, callback) {callback(null, user, context);}\",\"order\":2,\"enabled\":true}"
  }
}
```

::: note
You can use the [auth0-rules-testharness library](https://www.npmjs.com/package/auth0-rules-testharness) to deploy, execute, and test the output of Rules using a Webtask sandbox environment.
:::

## How to Debug Rules

You can add `console.log` lines in the rule's code for debugging. The [Rule Editor](${manage_url}/#/rules/create)  provides two ways for seeing the output:

![Rules Editor](/media/articles/rules/rule-editor.png)

1. **TRY THIS RULE**: opens a pop-up where you can run a rule in isolation. The tool provides a mock **user** and **context** objects. Clicking **TRY** will result on the Rule being run with those two objects as input. `console.log` output will be displayed too.

![Try this Rule](/media/articles/rules/try-rule.png)

2. **REALTIME LOGS**: an [extension](${manage_url}/#/extensions) that displays all logs in real-time for all custom code in your account. This includes all `console.log` output, and exceptions.

3. **DEBUG RULE**: similar to the above, displays instructions for installing, configuring and running the [webtask CLI](https://github.com/auth0/wt-cli) for debugging rules. Paste these commands into a terminal to see the `console.log` output and any unhandled exceptions that occur during Rule execution.

  For example:

  ```sh
  ~  npm install -g wt-cli
  ~  wt init --container "youraccount" --url "https://sandbox.it.auth0.com" --token "eyJhbGci...WMPGI" -p "youraccount-default-logs"
  ~  wt logs -p "youraccount-default-logs"
  [18:45:38.179Z]  INFO wt: connected to streaming logs (container=youraccount)
  [18:47:37.954Z]  INFO wt: webtask container assigned
  [18:47:38.167Z]  INFO wt: ---- checking email_verified for some-user@mail.com! ----
  ```

  This debugging method works for rules tried from the dashboard and those actually running during user authentication.

## Cache expensive resources

The code sandbox Rules run on allows storing _expensive_ resources that will survive individual execution.

This example, shows how to use the `global` object to keep a mongodb connection:

```js

  ...

  //If the db object is there, use it.
  if (global.db){
    return query(global.db, callback);
  }

  //If not, get the db (mongodb in this case)
  mongo('mongodb://user:pass@mymongoserver.com/my-db',  function (db){
    global.db = db;
    return query(db, callback);
  });

  //Do the actual work
  function query(db, cb){
    //Do something with db
    ...
  });

  ...

```

Notice that the code sandbox in which Rules run on, can be recycled at any time. So your code __must__ always check `global` to contain what you expect.

## Available modules

For security reasons, the Rules code runs in a JavaScript sandbox based on [webtask.io](https://webtask.io) where you can use the full power of the ECMAScript 5 language.

For a list of currently supported sandbox modules, see: [Modules Supported by the Sandbox](https://auth0-extensions.github.io/canirequire/).

## Read more

* [Redirecting users from within rules](/rules/redirect)
