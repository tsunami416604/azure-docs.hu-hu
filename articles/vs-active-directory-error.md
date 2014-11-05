<properties title="Error During Authentication Detection" pageTitle="Error During Authentication Detection" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Error During Authentication Detection

While detecting previous authentication code, the wizard detected an incompatible authentication type.

##### What is being checked?

The wizard attempts to detect versions of authentication code that have been configured with previous versions of Visual Studio. If you received this error, it means your project contains an incompatible authentication type. The wizard detects the following types of authentication from previous versions of Visual Studio:

-   Windows Authentication
-   Individual User Accounts
-   Organizational Accounts

To detect Windows Authentication in an MVC project, the wizard looks for the `authentication` element from your **web.config** file.

<pre class="prettyprint">
    &lt;configuration&gt;
        &lt;system.web&gt;
            &lt;authentication mode=&quot;Windows&quot; /&gt;
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

To detect Windows Authentication in a Web API project, the wizard looks for the `IISExpressWindowsAuthentication` element from your project's **.csproj** file:

<pre class="prettyprint">
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            &lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
        &lt;/PropertyGroup&gt;
    &lt;/Project&gt;
</pre>

To detect Individual User Accounts authentication, the wizard looks for the package element from your **Packages.config** file.

<pre class="prettyprint">
    &lt;packages&gt;
        &lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
    &lt;/packages&gt;
</pre>

To detect an old form of Organizational Account authentication, the wizard looks for the following element from **web.config**:

<pre class="prettyprint">
    &lt;configuration*gt;
        &lt;appSettings&gt;
            &lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

To change the authentication type, remove the incompatible authentication type and run the wizard again.

For more information, see [Authentication Scenarios for Azure AD][Authentication Scenarios for Azure AD].

  [Authentication Scenarios for Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx
