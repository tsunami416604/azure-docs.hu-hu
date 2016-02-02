<properties
    pageTitle="App 模型 v2.0 .Net Web App|Microsoft Azure"
    description="如何建置可使用個人 Microsoft 帳戶及工作或學校帳戶登入使用者的 .NET MVC Web 應用程式。"
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>


# 應用程式模型 v2.0 預覽：將登入加入 .NET MVC Web 應用程式

有了 v2.0 應用程式模型，您就可以快速地將驗證加入 Web 應用程式，同時支援個人 Microsoft 帳戶以及工作或學校帳戶。 在 ASP.NET Web 應用程式中，您可以使用隨附於 .NET Framework 4.5 的 Microsoft OWIN 中介軟體來完成此項作業。
  > [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽版本。 如需如何公開上市的 Azure AD 整合服務，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

 這裡我們將使用 OWIN 來:
-   使用 Azure AD 和 v2.0 App 模型將使用者登入 App。
-   顯示使用者的一些相關資訊。
-   讓使用者登出 App。

為執行此作業，您必須執行下列動作：

1. 註冊應用程式。
2. 設定您的應用程式使用 OWIN 驗證管線。
3. 使用 OWIN 向 Azure AD 發出登入和登出要求。
4. 列印出使用者的相關資料。

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet)。 若要跟著做，您可以 [下載為.zip 的應用程式的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) 或再製基本架構:

`git 複製-分支基本架構 https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git`

本教學課程最後也會提供完整的應用程式。

## 1.註冊應用程式

建立新的應用程式在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ，或請遵循下列 [詳細步驟](active-directory-v2-app-registration.md)。 請確定：

- 將指派給您應用程式的**應用程式識別碼**複製起來，您很快會需要用到這些識別碼。
- 為您的應用程式新增 **Web** 平台。
- 輸入正確的**重新導向 URI**。 重新導向 uri 會向 Azure AD 在驗證回應應該重新導向-本教學課程中的預設值是指出 `https://localhost:44326 /`。

## 2.將您的應用程式設定為使用 OWIN 驗證管道

在這裡，我們將設定 OWIN 中介軟體使用 OpenID Connect 驗證通訊協定。 OWIN 將用來發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊等其他作業。

-   若要開始，請開啟 `web.config` 檔案位於專案的根目錄，並輸入您的應用程式中的組態值 `< g s >` 一節。
    -   `Ida: ClientId` 是 **應用程式識別碼** 指派給您的應用程式中註冊入口網站。
    -   `Ida: RedirectUri` 是 **重新導向 Uri** 您輸入在入口網站。

-   接下來，使用 Package Manager Console 將Next, add the OWIN 中介軟體 NuGet 套件新增到專案中。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   將 「 OWIN 啟動類別 」 專案呼叫 `Startup.cs`  --> [專案] 上的按一下向右 **新增** --> **新項目** --> 搜尋"OWIN"。 OWIN 中介軟體將會叫用 `Configuration(...)` 應用程式啟動時的方法。
-   將類別宣告變更為 `公用部分類別啟動` -我們已實作了此類別的部分為您在另一個檔案。 在 `Configuration(...)` 方法中，請呼叫 ConfigureAuth(...) 以設定 web 應用程式的驗證

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}```

-   Open the file `App_Start\Startup.Auth.cs` and implement the `ConfigureAuth(...)` method.  The parameters you provide in `OpenIdConnectAuthenticationOptions` will serve as coordinates for your app to communicate with Azure AD.  You'll also need to set up Cookie Authentication - the OpenID Connect middleware uses cookies underneath the covers.

```C#
public void ConfigureAuth(IAppBuilder app)
             {
                     app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

                     app.UseCookieAuthentication(new CookieAuthenticationOptions());

                     app.UseOpenIdConnectAuthentication(
                             new OpenIdConnectAuthenticationOptions
                             {
                                     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                                     // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                     // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                                     ClientId = clientId,
                                     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                     RedirectUri = redirectUri,
                                     Scope = "openid",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## 3.使用 OWIN 向 Azure AD 發出登入和登出要求

您的應用程式現在已正確設定，將使用 OpenID Connect 驗證通訊協定與 v2.0 端點通訊。 OWIN 已經處理所有製作驗證訊息、驗證 Azure AD 的權杖和維護使用者工作階段的瑣碎詳細資料。 剩餘的工作就是提供使用者一個登入和登出的方式。

- 您可以在控制器中使用授權標記，要求使用者在存取特定頁面時登入。 開啟 `controllers\ homecontroller.cs`, ，並新增 `Authorize` [關於] 控制器的標記。

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   您也可以使用 OWIN 從程式碼中直接發出驗證要求。 開啟 `controllers\ accountcontroller.cs`。 在 SignIn() 和 SignOut() 動作中，將分別發出 OpenID Connect 挑戰和登出要求。

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   現在，請開啟 `Views\Shared\_LoginPartial.cshtml`。 這裡是您向使用者顯示應用程式的登入和登出連結，以及在檢視中列印出使用者名稱的位置。

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## 4.顯示使用者資訊

V2.0 端點驗證時使用 OpenID Connect 使用者，傳回至應用程式，其中包含 id_token [宣告](active-directory-v2-tokens.md#id_tokens), ，或有關使用者判斷提示。 您可以使用這些宣告來個人化應用程式：

- 開啟 `Controllers\HomeController.cs` 檔案。 您可以透過在控制器中存取的使用者宣告 `ClaimsPrincipal.Current` 安全性主體物件。

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

最後，建置並執行您的應用程式！ 使用個人 Microsoft 帳戶或工作或學校帳戶登入，並注意上方導覽列中使用者身分識別的反映狀態。 您的 Web 應用程式現在使用業界標準的通訊協定保護，可以使用個人與工作/學校帳戶來驗證使用者。

(不含您的設定值) 已完成的範例供您參考 [依現狀的.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), ，或您可以從 GitHub 複製它:

`git 複製-分支完成 https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git`

## 後續步驟

您現在可以進入更進階的主題。 您可以嘗試：

[保護 Web API 與 2.0 版應用程式模型 >>](active-directory-devquickstarts-webapi-dotnet.md)

如需其他資源，請參閱：
- [應用程式模型 v2.0 預覽 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow [azure-active directory 的 「 標記 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)





