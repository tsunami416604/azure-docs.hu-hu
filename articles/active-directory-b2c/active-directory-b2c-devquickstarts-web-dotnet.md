<properties
    pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
    description="如何使用 Azure AD B2C 建置支援登入、註冊和設定檔管理的 Web 應用程式。"
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/22/2015"
    ms.author="dastrock"/>

# Azure AD B2C 預覽：建置 .NET Web 應用程式

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入至 Web 應用程式，只要幾個簡短步驟即可完成。  這篇文章將示範如何
若要建立的.NET MVC web 應用程式，包括使用者註冊、 登入與設定檔管理。  其中包含支援註冊與登入使用者名稱
或電子郵件，以及社交帳戶，例如 Facebook 和 Google。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。  目錄為所有使用者、應用程式、群組等項目的容器。  如果您沒有
其中一個，請移 [建立 B2C 目錄](active-directory-b2c-get-started.md) 之後再繼續。

## 2.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。  若要建立應用程式
請依照下列 [這些指示](active-directory-b2c-app-registration.md)。  請務必

- 包含 **web 應用程式/web api** 應用程式中
- 輸入 `https://localhost:44316/` 為 **回覆 URL** -它是此程式碼範例的預設 URL。
- 複製 **應用程式識別碼** ，其指派給您的應用程式。  稍後您將會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3.建立您的原則

在 Azure AD B2C，每個使用者經驗來定義 [**原則**](active-directory-b2c-reference-policies.md)。  此程式碼範例包含三種 
識別發生位註冊、 登入，並編輯設定檔。  您必須建立的每個類型，一個原則中所述 
[原則參考文件](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)。  建立您的三個原則時，請務必：

- 選擇 **使用者識別碼註冊** 或 **電子郵件註冊** 身分識別提供者] 刀鋒視窗中。
- 選擇 **顯示名稱** 和您註冊的原則中的其他幾個註冊屬性。
- 選擇 **顯示名稱** 宣告為每個原則的應用程式宣告。  您也可以選擇其他宣告。
- 複製 **名稱** 的每個原則建立之後。  稍後您將需要這些原則名稱。 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立三個原則後，就可以開始建置您的應用程式。

## 4.下載程式碼和設定驗證

此範例的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet)。  當您建置範例，您可以 
[下載為.zip 的基本架構專案](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) 或再製基本架構 ︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

已完成範例也 [成為.zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) 或
`complete` 相同的儲存機制分支。

下載範例程式碼後，請開啟 Visual Studio `.sln` 檔案開始進行。

您的應用程式與 Azure AD B2C 通訊時會傳送 HTTP 驗證要求，並指定它想要在要求中執行的原則。
.NET web 應用程式，您可以使用 Microsoft OWIN 程式庫來傳送 OpenID Connect 驗證要求、 執行原則、 管理使用者
工作階段，依此類推。

首先，請使用 Visual Studio Package Manager Console，將 OWIN 中介軟體 NuGet 封裝加入至專案。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

接下來，開啟專案根目錄中的 `web.config` 檔案，在 `<appSettings>` 區段中輸入應用程式的組態值。

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="[Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com]" /> 
    <add key="ida:ClientId" value="[Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

現在，將「OWIN 啟動類別」加入名為 `Startup.cs` 的專案。  以滑鼠右鍵按一下專案--> **新增** --> **新項目** --> 搜尋"OWIN"。  
將類別宣告變更為 `public partial class Startup`，我們已為您在另一個檔案中實作了此類別的一部分。  
OWIN 中介軟體將會叫用 `Configuration(...)` 方法啟動應用程式-這種方法時呼叫 ConfigureAuth(...)，其中我們
會為您的應用程式的驗證設定。

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

開啟檔案 `App_Start\Startup.Auth.cs` 並實作 `ConfigureAuth(...)` 方法。  在您所提供的參數 `OpenIdConnectAuthenticationOptions` 
將做為您的應用程式與 Azure AD 進行通訊的座標。  您也需要設定 Cookie 驗證-OpenID Connect 中介軟體 
您可以使用 cookie 來維護使用者工作階段，等其他作業。

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // The ACR claim is used to indicate which policy was executed
    public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
    public const string PolicyKey = "b2cpolicy";
    public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
        {
            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            { 
                AuthenticationFailed = AuthenticationFailed,
                RedirectToIdentityProvider = OnRedirectToIdentityProvider,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
            // endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);
            
    }
```

## 5.將驗證要求傳送至 Azure AD
您的應用程式現在已正確設定，將使用 OpenID Connect 驗證通訊協定與 Azure AD B2C 進行通訊。  OWIN 已經處理所有的瑣碎詳細資料
製作驗證訊息、 驗證 Azure ad 的權杖和維護使用者工作階段。  剩下的就是啟動每個使用者流程。

當使用者在 Web 應用程式中按一下 [註冊]、[登入] 或 [編輯設定檔] 按鈕時，`Controllers\AccountController.cs` 中會叫用相關聯的動作。
在每個案例中，您可以使用內建的 OWIN 方法來觸發正確的原則：

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties using the PolicyKey provided.
    
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties (
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.SignInPolicyId}
                })
            { 
                RedirectUri = "/", 
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.SignUpPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.ProfilePolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

您也可以在控制器中使用自訂的 `PolicyAuthorize` 標記，要求如果使用者尚未登入，則執行特定的原則。
開啟 `Controllers\HomeController.cs`，將 `[PolicyAuthorize]` 標記加入「宣告」控制器。  請務必將包含的原則範例
使用您自己的登入原則。

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed into the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

您也可以使用 OWIN 將使用者登出應用程式。  回到 `Controllers\AccountController.cs`：  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string> 
            { 
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

根據預設，OWIN 不會將您在 `AuthenticationProperties` 中指定的原則傳送至 Azure AD。  不過，您可以在 `RedirectToIdentityProvider` 通知中編輯 OWIN 產生的要求。
在 `App_Start\Startup.Auth.cs` 中使用這個通知，從原則的中繼資料擷取每項原則的適用端點。  針對您的應用程式想要執行的每一個原則，如此可確保正確的要求會傳送至 Azure AD。   

```C#
// App_Start\Startup.Auth.cs

private async Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    PolicyConfigurationManager mgr = notification.Options.ConfigurationManager as PolicyConfigurationManager;
    if (notification.ProtocolMessage.RequestType == OpenIdConnectRequestType.LogoutRequest)
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseRevoke.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.EndSessionEndpoint;
    }
    else
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseChallenge.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.AuthorizationEndpoint;
    }
}
``` 

## 6.顯示使用者資訊
Azure AD 驗證時使用 OpenID Connect 使用者，傳回至應用程式，其中包含 id_token **宣告**, ，或有關使用者判斷提示。  您可以使用這些宣告來個人化應用程式。  

開啟 `Controllers\HomeController.cs` 檔案。  您可以透過 `ClaimsPrincipal.Current` 安全性主體物件來存取控制器中的使用者宣告。

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

您可以用相同方式存取您的應用程式所接收的任何宣告。  應用程式收到的所有宣告的清單已印在宣告頁面供您檢閱。

## 7.執行範例應用程式

最後，建置並執行您的應用程式！   使用電子郵件地址或使用者名稱來註冊應用程式。  登出，再以相同的使用者身分重新登入。  編輯該使用者的設定檔。  登出，再註冊
使用不同的使用者。  請注意如何將資訊顯示在 **宣告** ] 索引標籤對應到您在您的原則設定的資訊。

## 8.新增社交 IDP

目前，應用程式僅支援使用者註冊和使用所謂的登入 **本機帳戶** -使用使用者名稱與密碼 B2C 目錄中儲存的帳戶。  使用 Azure AD B2C，
您可以新增其他支援 **身分識別提供者**, ，或 Idp，而不變更任何程式碼。

若要將社交 IDP 加入至應用程式，請依照下面一篇或兩篇文章中的詳細指示開始。  您想要支援每個的 IDP，您必須註冊應用程式
在系統中，並取得用戶端識別碼。

- [將 Facebook 設定為 IDP](active-directory-b2c-setup-fb-app.md)
- [將 Google 設定為 IDP](active-directory-b2c-setup-goog-app.md)
- [將 Amazon 設定為 IDP](active-directory-b2c-setup-amzn-app.md)
- [將 LinkedIn 設定為 IDP](active-directory-b2c-setup-li-app.md) 

當您已將身分識別提供者加入 B2C 目錄時，您需要返回並編輯每個要包含新的 Idp，三個原則
中所述 [原則參考文件](active-directory-b2c-reference-policies.md)。  儲存您的原則之後，重新執行應用程式。  您應該會看到
做為登入與註冊的選項，在每個身分識別經驗中加入新的 Idp。

您可以自由試驗您的原則，並觀察範例應用程式上有何效果 - 新增/移除 IDP、操作應用程式宣告、變更註冊屬性。
試驗到您開始了解原則、驗證要求和 OWIN 如何結合在一起為止。

（不含您的設定值） 已完成的範例供您參考 [依現狀的.zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip),，
或者，您可以從 GitHub 複製它 ︰

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->


