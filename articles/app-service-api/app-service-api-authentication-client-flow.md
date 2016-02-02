<properties 
    pageTitle="從驗證的用戶端呼叫 API 應用程式" 
    description="了解如何從經過 Azure Active Directory 驗證的 Web 應用程式用戶端呼叫 Azure API 應用程式。" 
    keywords="app service,azure app service,authentication,azure authentication,api,authentication api"
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2015" 
    ms.author="tdykstra"/>


# 從經過 Azure Active Directory 驗證的 Web 應用程式用戶端呼叫 Azure API 應用程式

## 概觀

本教學課程示範如何呼叫受到 Azure Active Directory (AAD) 保護的 API 應用程式 (透過也受到 AAD 保護的 Web 應用程式)。 完成本教學課程時，您的 Azure 訂用帳戶中將執行一個 Web 應用程式和一個 API 應用程式。 Web 應用程式會顯示藉由呼叫 API 應用程式取得的資料，如下列螢幕擷取畫面所示。

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

您將學習如何從 Web 應用程式傳入 AAD 認證至 API 應用程式，以便 API 應用程式不會再次提示使用者進行登入。

您會執行下列步驟：

- 建立 API 應用程式，並將它設定為使用 AAD 驗證。
- 建立 Web 應用程式，並將它設定為使用 AAD 驗證。
- 加入程式碼至 Web 應用程式，以呼叫受保護的 API 應用程式。
- 將 Web 應用程式和 API 應用程式部署至 Azure。
- 測試以驗證 Web 應用程式可以呼叫 API 應用程式。

### 用戶端流程驗證

您將加入至 web 應用程式的程式碼會實作程序呼叫 [用戶端流程](../app-service/app-service-authentication-overview.md#client-flow) 驗證。 下圖說明取得 AAD 存取權杖，並交換該存取權杖以取得 API 應用程式 (Zumo) 權杖的程序。

![](./media/app-service-api-authentication-client-flow/clientflow.png)

如果您不熟悉驗證的 API 應用程式中的 API 應用程式閘道的角色，請參閱 [API 應用程式和行動應用程式驗證](../app-service/app-service-authentication-overview.md)。

## 必要條件

開始本教學課程之前, 請確定您有 [Visual Studio 2015](https://www.visualstudio.com/) 和 [Azure SDK for.NET](http://go.microsoft.com/fwlink/?linkid=518003) 安裝。 相同的指示也適用於 Visual Studio 2013。

本教學課程假設您知道如何在 Visual Studio 中使用 Web 專案。

## 使用 AAD 建立和保護 API 應用程式

1. 建立或下載 API 應用程式專案。

    * 若要建立專案時，請遵照 [建立 API 應用程式](app-service-dotnet-create-api-app.md)。

    * 若要下載的專案，取得從 [ContactsList GitHub 儲存機制](https://github.com/tdykstra/ContactsList)。

    您現在有可傳回連絡資料的 Web API 專案。

    ![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. 將 API 應用程式專案部署到 Azure 中的新 API 應用程式。

    請遵照 [部署 API 應用程式](app-service-dotnet-deploy-api-app.md)。

    在 [Azure 預覽入口網站，] **API 定義** API 應用程式刀鋒視窗現在會顯示在 Get 和 Post 方法，使 Web api 專案您部署。

    ![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. 藉由使用 Azure Active Directory (AAD) 做為身分識別提供者來保護 API 應用程式。

    請依照指示在 AAD 的 [保護 API 應用程式](app-service-api-dotnet-add-authentication.md)。 您不必進行教學課程的**使用 Postman 傳送 Post 要求**一節。

    當您完成時，API 應用程式的 **Azure Active Directory** [Azure 預覽入口網站] 中的分頁具有 AAD 應用程式的用戶端識別碼和 AAD 租用戶。

    ![](./media/app-service-api-authentication-client-flow/aadblade.png)

    AAD 應用程式和 **設定** [Azure 入口網站] 索引標籤具有 API 應用程式的應用程式識別碼 URL 和回覆 URL。

    ![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## 使用 AAD 建立和保護 Web 應用程式

在本節中，您可以下載並設定為 AAD 驗證設定的 Web 專案。 專案具有**關於**頁面，顯示所登入使用者的宣告資訊。

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

稍後您會透過加入區段用於顯示從 API 應用程式擷取的連絡資訊來修改此頁面。

1. 下載 web 專案從 [WebApp-GroupClaims-DotNet 儲存機制](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)

2. 依照指示 **如何執行範例** 中 [讀我檔案](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md), ，但有下列例外狀況:

    * 雖然讀我檔案說明要使用 Visual Studio 2013，但您可以使用 Visual Studio 2015。

    * 使用您已建立的 AAD 應用程式而不是建立一個新應用程式。

    * 保留您的 AAD 應用程式已經擁有的相同**應用程式識別碼 URI**。 (不要將它變更為讀我檔案中指定的格式。)

    * 依照指示變更其他 AAD 應用程式設定；特別是將登入和回覆 URL 設為範例應用程式的基底 URL。

您會保留為 API 應用程式建立的相同的應用程式識別碼 URI，以便您可以對 Web 應用程式和 API 應用程式使用相同的 AAD 存取權杖。 如果您將應用程式識別碼 URI 變更為讀我檔案所規定的格式，如此能存取 Web 應用程式，但無法存取 API 應用程式。 您將無法將 AAD 權杖傳遞至 API 應用程式閘道器來取得 Zumo 權杖，因為閘道器會預期應用程式識別碼 URI 的權杖是由閘道器 URL 加上 "/login/aad" 組成。

## 為 API 應用程式加入產生的用戶端程式碼

在本節中，您會為呼叫 API 應用程式的具型別介面加入自動產生的程式碼。

8.  在 Visual studio **方案總管] 中**, ，研究 GroupClaims WebApp DotNet 專案上按一下滑鼠右鍵，然後按一下 **加入 > Azure API 應用程式用戶端**。

9.  在 [加入 Microsoft Azure API 應用程式用戶端]**** 對話方塊中，選取以 AAD 保護的 API 應用程式。

    程式碼產生完成之後，您會在 [**方案總管**] 中看到該 API 應用程式名稱的新資料夾。 此資料夾包含實作用戶端類別和資料模型的程式碼。

10. 修正模稜兩可的參考所產生的程式碼造成 *ContactsList/ContactsExtensions.cs*: 變更兩個執行個體 `Task.Factory.StartNew` 至 `System.Threading.Tasks.Task.Factory.StartNew`。

## 加入程式碼來交換 Zumo 權杖的 AAD 權杖

1.  在 HomeController.cs 中加入 `使用` 陳述式中的 App Service SDK 和 JSON 序列化程式。

        using Microsoft.Azure.AppService;
        using Newtonsoft.Json.Linq;

2. 加入閘道器 URL 和 AAD 應用程式的應用程式識別碼 URI 的常數。 請確定閘道器 URL 是 https 不是 http。

        private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
        private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.  加入會取得用於呼叫 API 應用程式的用戶端物件的方法。

    public async Task<AppServiceClient> GetAppServiceClient()
    {
        var appServiceClient = new AppServiceClient(GATEWAY_URL);
        string userObjectID = ClaimsPrincipal.Current.FindFirst
            ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    
        var authContext = new AuthenticationContext
            (ConfigHelper.Authority, new TokenDbCache(userObjectID));
    
        ClientCredential credential = new ClientCredential
            (ConfigHelper.ClientId, ConfigHelper.AppKey);
    
        // Get the AAD token.
        AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
        var aadToken = new JObject();
        aadToken["access_token"] = result.AccessToken;
    
        // Send the AAD token to the gateway and get a Zumo token
        var appServiceUser = await appServiceClient.LoginAsync
            ("aad", aadToken).ConfigureAwait(false);
    
        return appServiceClient;
    }

在此程式碼 `ConfigHelper.Authority` 會解析為 「 https://login.microsoftonline.com/ {tenant}"，例如: 「 https://login.microsoftonline.com/contoso.onmicrosoft.com 」。

2.  加入程式碼前，立即 `傳回加` 陳述式的結尾 `有關` 方法以呼叫 API 應用程式。 (您將在下一個步驟中加入程式碼 `有關` 檢視來顯示傳回的資料。)

        var appServiceClient = await GetAppServiceClient();
        var client = appServiceClient.CreateContactsList();
        var contacts = client.Contacts.Get();
        ViewData["contacts"] = contacts;

3. 在 *Views/Home/About.cshtml*, ，將程式碼加入之後立即 `h2` 標題以顯示的連絡資訊。

     <h3>Contacts</h3>
     <table class="table table-striped table-bordered table-condensed table-hover">
         <tr>
             <th>Name</th>
             <th>Email</th>
         </tr>
    
         @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
         {
             <tr>
                 <td>@contact.Name</td>
                 <td>@contact.EmailAddress</td>
             </tr>
         }
    
     </table>

3. 在應用程式 Web.config 檔案中，新增下列繫結重新導向後 `< assemblyBinding >` 標記。

        <dependentAssembly>
          <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
          <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
        </dependentAssembly>

    若沒有這個繫結重新導向，應用程式將會失敗，因為 App Service SDK 包含 System.Net.Http.Primitives 1.5.0.0 版的相依性，但專案所使用的版本為 4.2.28.0。

3. 依照 **將此範例部署至 Azure** 中 [讀我檔案](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)。

5. 執行應用程式。

    ![](./media/app-service-api-authentication-client-flow/homepage.png)

6. 按一下 [登入]****，然後輸入您正用於此應用程式的 AAD 網域中的使用者認證。

    ![](./media/app-service-api-authentication-client-flow/signedin.png)

7. 按一下 [關於]****。

    您加入到 [關於] 控制項和檢視的程式碼會執行並顯示您已成功向 API 應用程式驗證並呼叫其 Get 方法。

    ![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## 疑難排解

### HTTP 400 錯誤

請確定閘道器 URL 是 https 不是 http。 直接從 Azure Preview 入口網站複製時，它可能會指定 http。

### SQL Server 錯誤

應用程式需要以 GroupClaimContext 連接字串識別的 SQL Server 資料庫的存取權。 請確定已部署之網站中的連接字串指向 SQL 資料庫執行個體。 您可以將正確的連接字串放在已部署的 Web.config 或是在 Azure 執行階段組態設定中。

## 通知

由於 Govind S Yadav ([@govindsyadav](https://twitter.com/govindsyadav)) 開發本教學課程的說明。

## 後續步驟

您已了解如何為 App Service API 應用程式執行用戶端流程驗證。 其他的方式處理 API 應用程式中的驗證的相關資訊，請參閱 [API 應用程式和行動應用程式驗證](../app-service/app-service-authentication-overview.md)。


[azure portal]: https://manage.windowsazure.com/ 
[azure preview portal]: https://portal.azure.com/ 

