The first step in adding authentication to your iOS application is to provide a way for your users to log in. The fastest, most secure, and most feature-rich way to do this with Auth0 is to use the [login page](https://auth0.com/docs/hosted-pages/login).

<div class="phone-mockup"><img src="/media/articles/native-platforms/ios-swift/lock_centralized_login.png" alt="Hosted Login Page"></div>

<%= include('_dependency_centralized') %>

## Adding the callback

Auth0 will need to handle the callback of this authentication, add the following to your `AppDelegate`:

First, import the `Auth0` module:

${snippet(meta.snippets.setup)}

Then, add the following `UIApplicationDelegate` method:

```swift
// AppDelegate.swift

func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any]) -> Bool {
    return Auth0.resumeAuth(url, options: options)
}
```

::: note
Please ensure you have configured your callback URL as demonstrated in [Configure Callback](/quickstart/native/ios-swift/getting-started#configure-callback-urls).
:::

## Implement the Login

First, import the `Auth0` module in the file where you want to present the hosted login page:

${snippet(meta.snippets.setup)}

Then present the hosted login screen, like this:

${snippet(meta.snippets.use)}

Upon successful authentication the user's `credentials` will be returned.

::: note
For further reference on the `credentials` object, please see
[Credentials](https://github.com/auth0/Auth0.swift/blob/master/Auth0/Credentials.swift). We will cover the storage of the user's credentials in a later chapter.  By default Auth0 will not store this for you.
:::

## Embedded Login

Auth0's hosted login page provides the fastest, most secure, and most feature-rich way to implement authentication in your app. If required, the Lock widget can also be embedded directly into your application, but certain features such as single sign-on won't be accessible. It is highly recommended that you use this login (as covered in this tutorial), but if you wish to embed the Lock widget directly in your application, you can follow the [Embedded Login sample](https://github.com/auth0-samples/auth0-ios-swift-sample/tree/embedded-login/01-Embedded-Login).
