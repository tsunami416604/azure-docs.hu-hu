<properties 
    pageTitle="從 .NET 用戶端使用 Azure App Service 中的 API 應用程式" 
    description="了解如何使用 App Service SDK 從 .NET 用戶端中的 API 應用程式。" 
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
    ms.date="09/22/2015" 
    ms.author="tdykstra"/>


# 從 .NET 用戶端使用 Azure App Service 中的 API 應用程式

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## 概觀

本教學課程示範如何使用 App Service SDK 撰寫程式碼，會呼叫 [API 應用程式](app-service-api-apps-why-best-platform.md) ，已針對設定 **公用 (匿名)** 或 **公用 (驗證)** 存取層級。 本文涵蓋以下範例案例：

- 從主控台應用程式呼叫**公用 (匿名)** API 應用程式
- 從 Windows 桌面應用程式呼叫**公用 (驗證)** API 應用程式

下列的教學課程章節各自獨立--您可以直接依照第二個案例的指示，而不需要完成的第一個案例的步驟。

如需如何呼叫 **內部** API 應用程式，請參閱 [取用內部 API 應用程式的.NET 用戶端](app-service-api-dotnet-consume-internal.md)。

## 必要條件

此教學課程假設您已熟悉如何建立專案，並在 Visual Studio 中，其中加入程式碼，以及如何 [管理 API 應用程式在 Azure 預覽入口網站中的](app-service-api-apps-manage-in-portal.md)。

在本文中的專案和程式碼範例，係根據您在這些文件中所建立、部署和保護的 API 應用程式專案：

- [建立 API 應用程式](app-service-dotnet-create-api-app.md)
- [部署 API 應用程式](app-service-dotnet-deploy-api-app.md)
- [保護 API 應用程式](../app-service-api-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

本教學課程需要 2.6 版或更新版本的 Azure SDK for.NET。

## 來自主控台應用程式的未驗證呼叫

本節中，您會建立主控台應用程式專案並在其中加入程式碼，以在不需要驗證的情況下呼叫 API 應用程式。

### 設定 API 應用程式並建立專案

1. 如果您尚未這麼做，請遵循 [部署 API 應用程式](app-service-dotnet-deploy-api-app.md) ContactsList 範例專案部署至 Azure 訂閱中的 API 應用程式。

    該教學課程會引導您在 Visual Studio 發佈對話方塊中將存取層級設定為 [**任何人皆可用**]，這相當於入口網站中的 [**公用 (匿名)**]。 不過，如果您沒有 [保護 API 應用程式](../app-service-api-dotnet-add-authentication.md) 之後的教學課程中，存取層級已經設為 **公用 (驗證)**, ，在此情況下您需要依照下列步驟的引導進行變更。

2. 在 [Azure 預覽入口網站](https://portal.azure.com/), ，請在 **API 應用程式** 您想要呼叫，請移至 API 應用程式刀鋒視窗 **設定 > 應用程式設定** ，並設定 **存取層級** 至 **公用 (匿名)**。

    ![](./media/app-service-api-dotnet-consume/setpublicanon.png)

2. 在 Visual Studio 中，建立主控台應用程式專案。

### <a id="addclient"></a>加入 App Service SDK 產生的用戶端程式碼

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### 加入呼叫 API 應用程式的程式碼

若要呼叫 API 應用程式，您只需要建立用戶端物件，然後對它呼叫方法，如下例所示：

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. 開啟 *Program.cs*, ，並新增下列程式碼 `Main` 方法。

     var client = new ContactsList();
    
     // Send GET request.
     var contacts = client.Contacts.Get();
     foreach (var c in contacts)
     {
         Console.WriteLine("{0}: {1} {2}",
             c.Id, c.Name, c.EmailAddress);
     }
    
     // Send POST request.
     client.Contacts.Post(new Models.Contact
     {
         EmailAddress = "lkahn@contoso.com",
         Name = "Loretta Kahn",
         Id = 4
     });
     Console.WriteLine("Finished");
     Console.ReadLine();

3. 按 CTRL+F5 執行應用程式。

    ![產生完成](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## 從 Windows 桌面應用程式發出經驗證的呼叫

本節中，您會建立 Windows 桌面應用程式專案並在其中加入程式碼，以在需要驗證的情況下呼叫 API 應用程式。

### 設定 API 應用程式並建立專案

1. 請依照下列 [保護 API 應用程式](../app-service-api-dotnet-add-authentication.md) 設定 API 應用程式的教學課程 **公用 (驗證)** 存取層級。

1. 在 Visual Studio 中，建立 Windows Forms 桌面專案。

2. 在表單設計工具中，加入下列控制項：

    * 按鈕控制項
    * 文字方塊控制項
    * 網頁瀏覽器控制項

3. 將文字方塊控制項設定為多行。

    您的表單外觀會如下例所示。

    ![](./media/app-service-api-dotnet-consume/form.png)

### 加入 App Service SDK 產生的用戶端程式碼

3. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，並依序選取 [**加入 > Azure API 應用程式用戶端**]。

3. 在 [**加入 Azure API 應用程式用戶端**] 對話方塊中，按一下 [**從 Azure API 應用程式下載**]。

5. 在下拉式清單中，選取要呼叫的 API 應用程式，然後按一下 [**確定**]。

### 加入呼叫 API 應用程式的程式碼

5. 在 Azure Preview 入口網站中，複製您的 API 應用程式閘道的 URL。 您將在下一個步驟中使用此值。

    ![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. 在 *Form1.cs* 原始程式碼中，新增下列程式碼之前 `Form1()` 建構函式，您在上一個步驟中複製的值取代 GATEWAY_URL 值。 請確定您包含結尾斜線 (/)。

        private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
        private const string URL_TOKEN = "#token=";

4. 在表單設計工具中，按兩下按鈕以加入 click 處理常式，然後在處理常式方法中加入程式碼以移至閘道的登入 URL，例如：

        webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

    以您在閘道中設定的身分識別服務提供者代碼來取代 "[authprovider]"，例如 "aad"、"twitter"、google"、"microsoftaccount" 或 "facebook"。 例如：

        webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. 新增 `DocumentCompleted` 事件處理常式的 web 瀏覽器控制項，並將下列程式碼加入至處理常式方法。

     if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
     {
         var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
         var decodedJson = Uri.UnescapeDataString(encodedJson);
         var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
         string userId = result.user.userId;
         string userToken = result.authenticationToken;
    
         var appServiceClient = new AppServiceClient(GATEWAY_URL);
         appServiceClient.SetCurrentUser(userId, userToken);
    
         var contactsListClient = appServiceClient.CreateContactsList();
         var contacts = contactsListClient.Contacts.Get();
         foreach (Contact contact in contacts)
         {
             textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
         }
         //appServiceClient.Logout();
         //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
     }

 會在使用者利用網頁瀏覽器控制項登入後，會執行已加入的程式碼。 成功登入之後，回應 URL 會包含使用者識別碼和密碼。 程式碼會從 URL 擷取這些值，並提供給 App Service 用戶端物件，然後使用該物件來建立 API 應用程式用戶端物件。 接著，您可以藉由對此 API 應用程式用戶端物件呼叫方法，來呼叫 API。

8. 按 CTRL+F5 執行應用程式。

9. 按一下按鈕，並當瀏覽器控制項顯示登入頁面時，輸入獲授權可以呼叫 API 應用程式的使用者認證。

    Azure 會驗證該認證，然後應用程式會呼叫 API 應用程式並顯示回應。

    ![](./media/app-service-api-dotnet-consume/formaftercall.png)

### <a id="client-flow"></a>伺服器流程和用戶端流程

範例應用程式會解說 [伺服器流程](../app-service/app-service-authentication-overview.md#server-flow), ，這表示閘道取得識別提供者存取權杖。 如 [用戶端流程](../app-service/app-service-authentication-overview.md#client-flow), ，其中用戶端應用程式直接從身分識別提供者取得存取權杖，並傳送至閘道，在您呼叫 `LoginAsync` 而 `SetCurrentUser`。

下列程式碼範例假設名為的字串變數的身分識別提供者的存取權杖 `providerAccessToken` 和身分識別提供者中的指示器 ("aad"、"microsoftaccount"、"google"、"twitter"或"facebook") 的字串變數，名為 `idProvider`:

        var appServiceClient = new AppServiceClient(GATEWAY_URL);
        var providerAccessTokenJSON = new JObject();
        providerAccessTokenJSON["access_token"] = providerAccessToken;
        var appServiceUser = await appServiceClient.LoginAsync(idProvider, providerAccessTokenJSON);
    
        var contactsListClient = appServiceClient.CreateContactsList();
        var contacts = contactsListClient.Contacts.Get();
        foreach (Contact contact in contacts)
        {
            textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
        }

## 後續步驟

本文示範對於存取層級設為 [**公用 (驗證)**] 和 [**公用 (匿名)**] 的 API 應用程式，如何從 .NET 用戶端使用 API 應用程式。

如需其他從.NET 用戶端呼叫 API 應用程式的程式碼範例，請下載 [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample) 範例應用程式。

如需如何在 API 應用程式中使用驗證資訊，請參閱 [API 應用程式和行動應用程式在 Azure App Service 中的驗證](../app-service/app-service-authentication-overview.md)。






