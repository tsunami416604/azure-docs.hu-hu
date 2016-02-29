<properties
    pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
    description="如何使用 Azure AD B2C 建置支援登入、註冊和設定檔管理的 Windows 桌面應用程式。"
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

# Azure AD B2C 預覽：建置 Windows 桌面應用程式

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入至桌面應用程式，只要幾個簡短步驟即可完成。  這篇文章將示範如何
若要建立 「 待辦事項清單 」 應用程式的.NET WPF 包含使用者註冊、 登入和管理設定檔。  其中包含支援註冊與登入使用者名稱
或電子郵件，以及社交帳戶，例如 Facebook 和 Google。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。  目錄為所有使用者、應用程式、群組等項目的容器。  如果您沒有
其中一個，請移 [建立 B2C 目錄](active-directory-b2c-get-started.md) 之後再繼續。

## 2.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。  若要建立應用程式
請依照下列 [這些指示](active-directory-b2c-app-registration.md)。  請務必

- 包含 **原生用戶端** 應用程式中
- 複製 **重新導向 Uri** `urn:ietf:wg:oauth:2.0:oob` -它是此程式碼範例的預設 URL。
- 複製 **應用程式識別碼** ，其指派給您的應用程式。  稍後您將會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3.建立您的原則

在 Azure AD B2C，每個使用者經驗來定義 [**原則**](active-directory-b2c-reference-policies.md)。  此程式碼範例包含三種 
識別發生位註冊、 登入，並編輯設定檔。  您必須建立的每個類型，一個原則中所述 
[原則參考文件](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)。  建立您的三個原則時，請務必：

- 選擇 **使用者識別碼註冊** 或 **電子郵件註冊** 身分識別提供者] 刀鋒視窗中。
- 選擇 **顯示名稱** 和您註冊的原則中的其他幾個註冊屬性。
- 選擇 **顯示名稱** 和 **物件識別碼** 宣告做為應用程式宣告每個原則。  您也可以選擇其他宣告。
- 複製 **名稱** 的每個原則建立之後。  其前置詞應該為 `b2c_1_`。  稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)] 

當您成功建立三個原則後，就可以開始建置您的應用程式。

## 4.下載程式碼

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet)。  當您建置範例，您可以 
[下載為.zip 的基本架構專案](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip) 或再製基本架構:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

完成應用程式也是 [成為.zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) 或
`complete` 相同的儲存機制分支。

下載範例程式碼後，請開啟 Visual Studio `.sln` 檔案開始進行。  您會看到方案中有兩個專案：`TaskClient` 專案和 `TaskService` 專案。   `TaskClient` 是 WPF 
與使用者互動的桌面應用程式。  `TaskService` 是應用程式的後端 Web API，儲存每個使用者的待辦事項清單。  同時 `TaskClient` 和 `TaskService` 會由單一 **應用程式識別碼**
在此情況下，因為它們都包含一個邏輯應用程式。 

## 5.設定工作服務

當 `TaskService` 收到 `TaskClient` 的要求時，它會檢查存取權杖是否有效可驗證要求。  若要驗證的存取語彙基元 
您必須提供 `TaskService` 一些您的應用程式相關資訊。  在 `TaskService` 專案中，開啟 `web.config` 根目錄中的檔案 
在專案中的值取代和 `<appSettings>` 區段:

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
  
[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

如果您想要了解 web API 如何安全地驗證使用 Azure AD B2C 要求，請參閱我們
[Web API 快速入門文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

## 6.執行原則
既然 `TaskService` 已準備好驗證要求，我們便可以實作 `TaskClient`。  您的應用程式會傳送 HTTP 驗證要求，與 Azure AD B2C 通訊
也就是希望執行過程中要求的指定原則。  .NET 桌面應用程式，您可以使用 **Active Directory 驗證程式庫 (ADAL)**
若要傳送 OAuth 2.0 驗證訊息，執行原則，並取得權杖呼叫 web Api。

#### 安裝 ADAL
首先，使用 Visual Studio Package Manager Console 將 ADAL 新增到 TaskClient 專案中。

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### 輸入 B2C 詳細資料
開啟檔案 `Globals.cs`，並使用您自己的值來取代每個屬性值。  整個 `TaskClient` 都會使用這個類別來參考常用的值。

```C#
public static class Globals
{
    public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
    public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
    public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
    public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
    public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

    public static string taskServiceUrl = "https://localhost:44332";
    public static string aadInstance = "https://login.microsoftonline.com/";
    public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
``` 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


#### 建立 AuthenticationContext
ADAL 的主要類別是 `AuthenticationContext`它代表您的應用程式與 B2C 目錄的連接。  當應用程式啟動時，
建立的執行個體 `AuthenticationContext` 中 `MainWindow.xaml.cs`, ，其可用於整個視窗。

```C#
public partial class MainWindow : Window
{
    private HttpClient httpClient = new HttpClient();
    private AuthenticationContext authContext = null;

    protected async override void OnInitialized(EventArgs e)
    {
        base.OnInitialized(e);

        // The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
        // ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the ADAL TokenCache and created a simple FileCache in this app.
        authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
        ...
    ...
```

#### 起始註冊流程
當使用者按一下 [註冊] 按鈕時，我們要使用您所建立的註冊原則來起始註冊流程。  有了 ADAL，您只需要呼叫 
`authContext.AcquireTokenAsync(...)`。  您要傳遞的參數 `AcquireTokenAsync(...)` 可判斷何種語彙基元您接收，使用中的原則 
驗證要求，依此類推。

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
        // Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
        // Pass in the name of your sign-up policy to execute the sign-up experience.
        result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
            null, Globals.clientId, new Uri(Globals.redirectUri),
            new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

        // Indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;
        
        // When the request completes successfully, you can get user information form the AuthenticationResult
        UsernameLabel.Content = result.UserInfo.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }
    
    // Handle any exeptions that occurred during execution of the policy.
    catch (AdalException ex)
    {
        if (ex.ErrorCode == "authentication_canceled")
        {
            MessageBox.Show("Sign up was canceled by the user");
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

#### 起始登入流程
登入流程的起始方式與註冊流程相同。  當使用者按一下 [登入] 按鈕時，對 ADAL 發出相同的呼叫，但這次使用登入原則：

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
        ...         
```

#### 起始設定檔編輯流程
同樣地，您可以用相同的方式執行您的編輯設定檔原則：

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

在這些案例中，ADAL 會在其 `AuthenticationResult` 中傳回權杖，或擲回例外狀況。  每當您從 ADAL，取得權杖 
您可以使用 `AuthenticationResult.UserInfo` 來更新應用程式，例如 UI 中的使用者資料的物件。  ADAL 也會快取的權杖，供使用 
在應用程式的其他部分。

## 7.呼叫 API
我們已經使用 ADAL 來執行原則並取得權杖。  不過，在許多情況下，您只想要檢查現有快取的權杖，而不執行任何原則。
例如，當應用程式嘗試從 `TaskService` 擷取使用者的待辦事項清單時。  您可以使用相同 `authContext.AcquireTokenAsync(...)` 方法來執行這項操作，一旦
再次使用 `clientId` 做為範圍參數，但這次使用 `PromptBehavior.Never`:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token 
        // could not be acquired from the cache, rather than automatically prompting the user to sign in. 
        result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
            null, Globals.clientId, new Uri(Globals.redirectUri),
            new PlatformParameters(PromptBehavior.Never, null), existingPolicy);
    
    }

    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (AdalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "user_interaction_required")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;

        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

當呼叫 `AcquireTokenAsync(...)` 成功和快取中找到權杖，您可以加入要的語彙基元 `Authorization` HTTP 要求標頭，讓 `TaskService` 可以驗證
要求讀取使用者的待辦事項清單: 

```C#
    ...
    // Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
    ...
``` 

每當您想要檢查權杖快取中的權杖時，就可以採用這種相同的模式，而不需要提示使用者登入。  執行個體的應用程式啟動時，
我們想要檢查 `FileCache` 任何現有的權杖，如此才能保持使用者的登入工作階段每次應用程式執行。  您可以看到相同
中的程式碼 `MainWindow`的 `OnInitialized` 事件，會處理此首次執行情況。

## 8.登出使用者
最後，當使用者按一下 [登出] 按鈕時，您可以使用 ADAL 來結束使用者與應用程式之間的工作階段。  有了 ADAL，很簡單，只要清除所有的權杖
權杖快取中:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    authContext.TokenCache.Clear();

    // This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## 9.執行範例應用程式

最後，建置並執行 `TaskClient` 和 `TaskService`。  使用電子郵件地址或使用者名稱來註冊應用程式。  登出，再以相同的使用者身分重新登入。  編輯該使用者的設定檔。  登出，再註冊
使用不同的使用者。

## 10.新增社交 IDP

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
試驗到您開始了解原則、驗證要求和 ADAL 如何結合在一起為止。

已完成的範例供您參考 [依現狀的.zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip),，
或者，您可以從 GitHub 複製它:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## 後續步驟

您現在可以進入更進階的 B2C 主題。  您可以嘗試：

[從 Web 應用程式呼叫 Web API >>]()

[自訂 B2C 應用程式的 UX >>]()

-->

