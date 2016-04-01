<properties
    pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
    description="如何使用 Azure AD B2C 建置 .NET Web API，並使用 OAuth 2.0 存取權杖進行驗證加以保護。"
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
    
# Azure AD B2C 預覽：建置 .NET Web API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

有了 Azure AD B2C，您可以使用 OAuth 2.0 存取權杖保護 Web API，讓您的用戶端應用程式使用 Azure AD B2C 向 API 驗證。  這篇文章將示範如何
若要建立 「 待辦事項清單 」 應用程式的.NET MVC，包括註冊、 登入的使用者和管理設定檔。  每位使用者的待辦事項清單將會儲存在工作服務-web API，可讓已驗證
使用者建立及讀取其待辦事項清單中的工作。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。  目錄為所有使用者、應用程式、群組等項目的容器。  如果您沒有
其中一個，請移 [建立 B2C 目錄](active-directory-b2c-get-started.md) 之後再繼續。

## 2.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。  若要建立應用程式
請依照下列 [這些指示](active-directory-b2c-app-registration.md)。  請務必

- 包含 **web 應用程式 / web api** 應用程式中。
- Web 應用程式中，使用 **重新導向 Uri** `https://localhost:44316/` -它是 web 應用程式用戶端，此程式碼範例的預設位置。
- 複製 **應用程式識別碼** ，其指派給您的應用程式。  稍後您將會用到此資訊。

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3.建立您的原則

在 Azure AD B2C，每個使用者經驗來定義 [**原則**](active-directory-b2c-reference-policies.md)。  此程式碼範例中的用戶端包含三種 
識別發生位註冊、 登入，並編輯設定檔。  您必須建立的每個類型，一個原則中所述 
[原則參考文件](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)。  建立您的三個原則時，請務必：

- 選擇 [ **使用者識別碼註冊** 或 **電子郵件註冊** 身分識別提供者] 刀鋒視窗中。
- 選擇 **顯示名稱** 和您註冊的原則中的其他幾個註冊屬性。
- 選擇 **顯示名稱** 和 **物件識別碼** 宣告做為應用程式宣告每個原則。  您也可以選擇其他宣告。
- 複製 **名稱** 的每個原則建立之後。  稍後您將需要這些原則名稱。 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立三個原則後，就可以開始建置您的應用程式。

## 4.下載程式碼

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet)。  當您建置範例，您可以 
[下載為.zip 的基本架構專案](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip) 或再製基本架構 ︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

完成應用程式也是 [成為.zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) 或
`complete` 相同的儲存機制分支。

下載範例程式碼後，請開啟 Visual Studio `.sln` 檔案開始進行。  您會看到方案中有兩個專案：`TaskWebApp` 專案和 `TaskService` 專案。   `TaskWebApp` 是 MVC 
與使用者互動的 web 應用程式。   `TaskService` 是應用程式的後端 web API 儲存每個使用者的待辦事項清單。 

## 5.設定工作 Web 應用程式

使用者與 `TaskWebApp` 互動時，用戶端會傳送要求給 Azure AD，然後收到可用於呼叫 `TaskService` Web API 的權杖。
為了讓使用者登入並取得權杖，您必須提供應用程式的部分資訊給 `TaskWebApp`。  在 `TaskWebApp` 專案中，開啟 `web.config` 根目錄中的檔案 
在專案中的值取代和 `<appSettings>` 區段 ︰

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

另外有兩個 `[PolicyAuthorize]` 裝飾項目，需要在其中提供您的登入原則名稱。   `[PolicyAuthorize]` 屬性用來叫用特定的
當使用者嘗試存取的網頁需要驗證的應用程式中的原則。

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

如果您想要了解如何在 web 應用程式，例如 `TaskWebApp` 會使用 Azure AD B2C，看看我們
[Web 應用程式標誌在開始使用文件](active-directory-b2c-devquickstarts-web-dotnet.md)。

## 6.保護 API

既然現在已有一個代替使用者呼叫 API 的用戶端，您可以使用 OAuth 2.0 持有人權杖保護 `TaskService`。  您的 API 可以接受和驗證 
使用 Microsoft OWIN 程式庫的權杖。

#### 安裝 OWIN
首先要安裝 OWIN OAuth 驗證管線：

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

#### 輸入 B2C 詳細資料
開啟 `TaskService` 專案根目錄中的 `web.config` 檔案，取代 `<appSettings>` 區段中的值。  這些值將在整個 API 和
OWIN 程式庫。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings> 
```

#### 新增 OWIN 啟動類別
將 OWIN 啟動類別加入名為 `Startup.cs` 的 `TaskService` 專案。  以滑鼠右鍵按一下專案--> **新增** --> **新項目** --> 搜尋"OWIN"。
  

```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

#### 設定 OAuth 2.0 驗證
開啟檔案 `App_Start\Startup.Auth.cs` 並實作 `ConfigureAuth(...)` 方法：

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
    private const string discoverySuffix = ".well-known/openid-configuration";

    public void ConfigureAuth(IAppBuilder app)
    {   
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
        };

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {   
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
        });
    }
}
```

#### 保護工作控制器
既然應用程式已設定為使用 OAuth 2.0 驗證，再來只需要將 `[Authorize]` 標記加入至工作控制器，就可保護您的 Web API。
所有待辦事項清單操作都在此控制器中進行，因此我們將在類別層級保護整個控制器。
您也可以將 `[Authorize]` 標記加入個別的動作，以進行更細微的控制。

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

#### 從權杖取得使用者資訊
`TaskController` 會將工作儲存在資料庫中，而每一項工作都有一個「擁有」此工作的相關聯使用者。  擁有者由使用者的 **物件識別碼** （其中
就是為什麼您在為應用程式中加入的物件識別碼在所有的原則中的宣告):

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## 7.執行範例應用程式

最後，建置並執行 `TaskWebApp` 和 `TaskService`。  使用電子郵件地址或使用者名稱來註冊應用程式。  在使用者的待辦事項清單中，建立一些工作
並注意如何在保存 API 中即使您停止並重新啟動用戶端。

## 8.編輯您的原則

既然您已經使用 Azure AD B2C 保護 API，您可以測試一下應用程式的原則，檢視對 API 產生的效果 (或沒有效果)。  您可以 <!--add **identity providers**
to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->操作 **應用程式宣告** 中原則，以及變更
使用 Web API 中的使用者資訊。  如上所述，您加入的其他任何宣告都可在 `ClaimsPrincipal` 物件中供您的 .NET MVC Web API 使用。

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->


