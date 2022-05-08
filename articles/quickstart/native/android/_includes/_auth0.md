## Add the Auth0 Android Dependency

Your first step is to add the [Auth0 Android](https://github.com/auth0/Auth0.Android) SDK into your project. The library makes requests to the Auth0's Authentication and Management APIs.

### Gradle

Inside the `build.gradle` dependencies section add:

```xml
apply plugin: 'com.android.application'
android {
  //..
}
dependencies {
  compile 'com.auth0.android:auth0:1.+'
}
```

::: note
You can check for the latest version on the repository [Readme](https://github.com/auth0/auth0.android#installation), in [Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22auth0%22%20g%3A%22com.auth0.android%22), or in [JCenter](https://bintray.com/auth0/android/auth0).
:::

Then, run **Sync Project with Gradle Files** inside Android Studio or `./gradlew clean assembleDebug` from the command line.

::: note
For more information about Gradle usage, check [their official documentation](https://gradle.org/getting-started-android-build/).
:::
