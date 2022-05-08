## Customizing the Centralized Login Page

When you integrate centralized login in your application, you redirect the user to the `/authorize` endpoint of your Auth0 tenant. If Auth0 needs to authenticate the user, it will show the default login page.

You can enable a custom Hosted Login Page by navigating to [Hosted Pages](${manage_url}/#/login_page) and enabling the **Customize Login Page** toggle.

![Hosted Login Page](/media/articles/hosted-pages/login.png)

## Customize Lock in the Hosted Login Page

The default login page for your tenant is a template that will use Lock to provide your users with an attractive interface and smooth authentication process. You can look over that template and use it as a starting point if you choose to customize it in any way. The default template uses Lock v11.

If you want to change any of Lock's [configurable options](/libraries/lock/customization), you can do so using the [Hosted Pages](${manage_url}/#/login_page) editor interface. These options can alter the behavior of Lock itself, or the look and feel of the widget using the theming options. See the [configuration documentation](/libraries/lock/customization) for details on how to customize Lock.

When you're done making changes to the code, click **Save** to persist the changes.

![Hosted Login Page](/media/articles/hosted-pages/hlp-lock.png)


