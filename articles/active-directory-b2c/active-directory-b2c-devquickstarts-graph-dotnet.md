<properties
    pageTitle="Azure AD B2C 預覽：使用圖形 API | Microsoft Azure"
    description="如何對 B2C 租用戶呼叫圖形 API 並使用應用程式身分識別將過程自動化。"
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
    ms.date="10/08/2015"
    ms.author="dastrock"/>

# Azure AD B2C 預覽：使用圖形 API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Azure AD B2C 租用戶通常會很龐大，這表示許多常見的租用戶管理工作需要以程式設計方式執行。  
使用者管理的主要範例是-您可能需要將現有的使用者存放區移轉到 B2C 租用戶，或您或許想要主控件使用者 
在您自己的頁面上，在幕後的 Azure AD 中建立使用者帳戶進行註冊。  這類工作需要能夠建立，
讀取、 更新和刪除使用者帳戶-您可以使用 Azure AD Graph API。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
    
對於 B2C 租用戶，與圖形 API 通訊主要有兩種模式。  

- 對於互動式、執行一次的工作，您可能想要以 B2C 租用戶中的系統管理員帳戶來執行管理工作。
  此模式需要系統管理員先以認證登入，才能對圖形 API進行任何呼叫。
- 自動化、 持續的工作，您會想要執行使用某種類型的服務帳戶，您將授與必要的管理工作
權限。  在 Azure AD 中，您只要登錄應用程式，然後向 Azure AD 使用 「 應用程式識別碼 」，請使用
 [OAuth 2.0 用戶端認證授與](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)。  在此情況下， 
appilication 呼叫 Graph API 做為本身，而不任何特定使用者。  

在本文中，我們將示範如何執行後者的自動化使用案例。  為了示範，我們會建置 .NET 4.5 "B2CGraphClient" 來執行使用者 CRUD 作業。
為了方便試驗，用戶端會有 Windows 命令列介面讓您叫用各種方法。  不過，程式碼會寫入至 
非互動式、 自動化的方式運作。  現在就開始吧。

## 取得 Azure AD B2C 租用戶

您可以建立應用程式，使用者，或與所有 Azure AD 互動之前，您需要 Azure AD B2C 租用戶和全域系統管理員帳戶
在該租用戶。 如果您還沒有，請遵循該指南以 [開始使用 Azure AD B2C](active-directory-b2c-get-started.md)。

## 在租用戶中註冊服務應用程式

既然您已經有 B2C 租用戶，您需要使用 Azure AD Powershell Cmdlet 建立服務應用程式。
首先，下載並安裝 [Microsoft Online Services 登入小幫手](http://go.microsoft.com/fwlink/?LinkID=286152)。  然後您可以下載
（& s) 安裝 [64 位元 Azure Active Directory 的 Windows Powershell 模組](http://go.microsoft.com/fwlink/p/?linkid=236297)。

> [AZURE.NOTE]
若要使用圖形 API 搭配 B2C 租用戶，您必須使用 Powershell 註冊專用的應用程式，並遵循下列指示。  您不能重複使用已經在 Azure 入口網站中註冊的現有 B2C 應用程式。  這是 Azure AD B2C 預覽的一項限制，將在不久的將來移除，屆時我們將會更新本文章。 

安裝 Powershell 模組後，開啟 Powershell 並連線到 B2C 租用戶。  執行之後 `Get-Credential`, ，您將會是
提示您輸入使用者名稱和密碼-輸入與您 B2C 租用戶系統管理員帳戶。

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
``` 

建立應用程式之前，您必須產生新的「用戶端密碼」。  應用程式將使用用戶端密碼來向 Azure
AD，並取得存取權杖。  您可以在 Powershell 中產生有效的密碼：

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
``` 

上述最後一個命令應該會印出新的用戶端密碼。  將它複製到某個安全的地方，稍後需要用到。 現在您可以建立程式 
應用程式，提供新的用戶端密碼做為認證的應用程式 ︰

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

如果成功建立應用程式，應該會印出應用程式的某些屬性，如上所示。  您將需要這兩個 `ObjectId` 和
 `AppPrincipalId`, ，因此也向下複製這些值。

既然您已經在 B2C 租用戶中建立應用程式，您需要將執行使用者 CRUD 作業所需的權限指派給它。  您必須
若要將應用程式的三個不同角色指派 ︰ 目錄讀取器 （如讀取使用者）、 （適用於建立和更新使用者，） 目錄寫入器和使用者帳戶
管理員 （適用於刪除使用者）。  這些角色會有已知的識別項，因此您可以執行下列命令，取代 `-RoleMemberObjectId`
參數與 `ObjectId` 上述。  若要查看所有目錄角色的清單，請嘗試執行 `Get-MsolRole`。

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

您現在的應用程式有權從 B2C 租用戶建立、讀取、更新和刪除使用者，我們就撰寫一些程式碼來使用它。

## 下載、設定和建置範例程式碼

首先，下載範例程式碼並開始執行。  然後，我們可以看看幕後運作情形。  您可以 
[下載為.zip 的範例程式碼](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) 或將它複製
到您所選擇的目錄 ︰

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

在 Visual Studio 中開啟 `B2CGraphClient\B2CGraphClient.sln` Visual Studio 方案。  在 `B2CGraphClient` 專案中，開啟 `App.config` 檔案。  將
三個應用程式設定為您自己的值，就像這樣 ︰

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

現在，以滑鼠右鍵按一下 `B2CGraphClient` 方案並重建範例。  如果成功，您現在應該有一個可執行檔 `B2C.exe` 位於 `B2CGraphClient\bin\Debug`。

## 使用圖形 API 執行使用者 CRUD

若要使用 B2CGraphClient，請開啟 cmd Windows 命令提示字元，cd 到 `Debug` 目錄。  然後執行 `B2C Help` 命令。

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

這會顯示每個命令的簡短描述。  當您叫用任何這些命令時，B2CGraphClient 會對 Azure AD 圖形 API 發出要求。

### 取得存取權杖

對圖形 API 發出任何要求時，需要有存取權杖進行驗證。  B2CGraphClient 使用開放原始碼 Active Directory 驗證程式庫 (ADAL) 來協助取得存取權杖。
您當然不一定要使用 ADAL 來取得權杖，您可以製作自己的 HTTP 要求來取得權杖。  ADAL 可讓權杖取得更加容易藉由提供簡單的 API，以及負責
重要的詳細資訊，例如，快取的存取權杖。

> [AZURE.NOTE]
    此程式碼範例刻意使用 ADAL v2 (ADAL 公開上市版本)。  「不」使用 ADAL v4，因為這是專門用於 Azure AD B2C 的預覽版本。
    在 Azure AD B2C 預覽中，您必須使用 ADAL v2 才能與圖形 API 進行通訊。  經過一段時間，我們將使圖形 API 存取，且 ADAL v4，如此您不需要使用兩個
    不同版本的 ADAL 中完整的 Azure AD B2C 解決方案。

B2CGraphClient 執行時會建立 `B2CGraphClient` 類別的執行個體。  這個類別的建構函式會設定 ADAL 的驗證架構：

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are 
    // provided to Azure AD in order to receive an access_token using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

讓我們以 `B2C Get-User` 命令為例。  叫用 `Get-User` 而沒有任何其他輸入時，CLI 會呼叫 `B2CGraphClient.GetAllUsers(...)` 方法。
這個方法會呼叫 `B2CGraphClient.SendGraphGetRequest(...)`，後者會送出 HTTP GET 要求給圖形 API。  在傳送之前的 GET 要求，它第一次取得存取權
使用 ADAL 的權杖 ︰

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ... 

```

如您所見，您可以呼叫 ADAL 的 `AuthenticationContext.AcquireToken(...)` 方法，取得圖形 API 的存取權杖。  ADAL 會傳回代表 access_token
應用程式的身分識別。

### 讀取使用者

當您想要從圖形 API 取得使用者清單或取得特定的使用者時，您可以傳送 HTTP GET 要求給 `/users` 端點。 租用戶中的所有使用者的要求
應該像這樣 ︰

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

若要查看此要求的運作情形，請嘗試執行：

 ```
 > B2C Get-User
 ``` 
 
這裡有兩個重點值得注意：

- 透過 ADAL 取得的存取權杖已利用 `Bearer` 配置加入 `Authorization` 標頭。
- 對於 B2C 租用戶，您必須使用查詢參數 `api-version=beta`。


> [AZURE.NOTE]
    Azure AD 圖形 API 的 Beta 版提供預覽功能。  請參閱 
    [此圖形 API 團隊部落格文章](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) 
    如需有關 beta 版。

這兩個細節都在 `B2CGraphClient.SendGraphGetRequest(...)` 方法中處理：

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...
    
    // For B2C user managment, be sure to use the beta Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }
    
    // Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);
    
    ... 
```
        
### 建立取用者的使用者帳戶 

在 B2C 租用戶中建立使用者帳戶時，您可以傳送 HTTP POST 要求給 `/users` 端點：

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // These properties are all required for creating consumer users.
     
    "accountEnabled": true,
    "alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "NameCoexistence",          // always set to 'NameCoexistence'
    "displayName": "Joe Consumer",              // a value that can be used for diplaying to the end-user
    "mailNickname": "joec",                     // a mail alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

建立取用者使用者時，需要以上要求中包含的每個屬性。   `//` 註解已經包含於圖-不執行
請將它們包含在實際的要求。

若要查看此要求的運作情形，請嘗試執行下列其中一個命令：

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 命令接受 `.json` 檔案作為輸入參數，其中包含使用者物件的 JSON 表示法。  有兩個範例 `.json` 檔案
包含範例程式碼- `usertemplate-email.json` 和 `usertemplate-username.json` -您可以修改以符合您的需求。  除了 
必要的欄位之上，有數個您可以使用這些檔案中包含的選擇性欄位。  可以在這些其他欄位的詳細資料
在中找到 [Azure AD Graph API 實體參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity)。

您可以在 `B2CGraphClient.SendGraphPostRequest(...)`中看到如何建構此 POST 要求，它會：

- 將存取權杖附加至要求的 `Authorization` 標頭。
- 設定 `api-version=beta`。
- 將 JSON 使用者物件加入要求主體中。

### 更新取用者的使用者帳戶

更新使用者物件非常類似於建立使用者物件，但使用 HTTP PATCH 動詞命令：

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request only updates the user's displayName
}
```

您可以嘗試以新資料更新 JSON 檔案，並使用 B2CGraphClient 執行下列其中一個命令，以更新使用者：

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```
    
如需有關如何傳送此要求的詳細資訊，請檢閱 `B2CGraphClient.SendGraphPatchRequest(...)` 方法。

### 刪除使用者

刪除使用者很簡單 - 只要使用 HTTP DELETE 動詞命令，並以正確的物件識別碼建構 URL：

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看範例，請嘗試下列命令並檢視主控台印出的 DELETE 要求：

```
> B2C Delete-User <object-id-of-user>
```

如需有關如何傳送此要求的詳細資訊，請檢閱 `B2CGraphClient.SendGraphDeleteRequest(...)` 方法。

除了使用者管理，Azure AD 圖形 API 還可讓您執行其他許多動作。  Auch die Eigenschaften 
[Azure AD Graph API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 提供範例要求以及每個動作的詳細資料。  


## 使用自訂屬性

幾乎每個消費者應用程式都需要儲存某種自訂使用者設定檔資訊。  方法之一是 B2C 租用中定義的自訂屬性
它可讓您將使用者物件上的該屬性就像任何其他內容。  您可以更新的屬性，刪除屬性，以查詢屬性 
傳送的宣告中的登入權杖，等屬性。  

若要定義自訂屬性 B2C 租用戶中，請參閱 [B2C 預覽自訂的屬性參考](active-directory-b2c-reference-custom-attr.md)。

您可以使用 B2CGraphClient 來檢視 B2C 租用戶中定義的自訂屬性：

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

這些函式的輸出會顯示每個自訂屬性的詳細資料，例如：

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

您可以使用完整名稱作為使用者物件的屬性，例如 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`。  只會更新您 `.json` 與新的檔案
屬性的值屬性，然後執行 ︰

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

就這麼簡單！  透過 B2CGraphClient，您現在有一個服務應用程式可以用程式設計方式管理 B2C 租用戶使用者。  它會使用它自己的應用程式識別 
驗證 Azure AD Graph API，並取得使用 client_secret 語彙基元。 當您將這項功能合併至自己的應用程式時，請記住您的幾個重點
B2C 應用程式 ︰

- 您需要將租用戶中的適當權限授與應用程式
- 現在，您需要使用 ADAL v2 取得存取權杖 (也可以直接傳送通訊協定訊息，而不使用程式庫)
- 當呼叫 Graph API，使用 [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx)。
- 當建立和更新取用者使用者，有幾個必要的屬性，如上所述。

> [AZURE.IMPORTANT]
您將需要帳戶複寫的特性目錄服務基礎的 Azure AD B2C (讀取 [這](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx) 文章以進一步) B2C 應用程式中使用 Azure AD Graph API 時。 取用者使用 B2C 應用程式註冊之後 **註冊** 原則，如果您立即反過來，嘗試讀取應用程式中使用 Azure AD Graph API 的使用者物件，它可能無法使用。 您必須等候數秒鐘讓複寫程序完成。 在正式發行的版本中，我們將發行關於 Azure AD 圖形 API 和目錄服務提供的「讀寫一致性保證 」的更具體指引。

對於您想要使用圖形 API 在 B2C 租用戶上執行的動作，如果您有任何問題或要求，我們洗耳恭聽！  請在發行項上留下註解
或檔案的程式碼範例 GitHub 儲存機制中的問題。


