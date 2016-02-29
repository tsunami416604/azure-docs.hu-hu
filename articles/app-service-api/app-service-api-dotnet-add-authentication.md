<properties 
    pageTitle="保護 Azure API 應用程式" 
    description="了解如何使用 Visual Studio 保護 Azure API 應用程式。" 
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
    ms.date="10/27/2015" 
    ms.author="tdykstra"/>

# 保護 API 應用程式：新增 Azure Active Directory 或社交提供者驗證

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## 概觀

本教學課程示範如何保護 API 應用程式，如此一來，只有經過驗證的使用者才可加以存取。 本教學課程也將提供您可用於 ASP.NET API 的程式碼，以便擷取已登入使用者的相關資訊。

您會執行下列步驟：

- 呼叫 API 應用程式，確認它正在運作。
- 將驗證規則套用至 API 應用程式。
- 再次呼叫 API 應用程式，確認它會拒絕未經驗證的要求。
- 登入已設定的提供者。
- 再次呼叫 API 應用程式，確認經過驗證的存取有作用。
- 撰寫及測試程式碼，以擷取已登入使用者的宣告。

如需 Azure App Service 中驗證的詳細資訊，請參閱 [API 應用程式和行動應用程式驗證](../app-service/app-service-authentication-overview.md)。

## 先決條件

本教學課程會與您在建立 API 應用程式 [建立 API 應用程式](app-service-dotnet-create-api-app.md) 並部署在 [部署 API 應用程式](app-service-dotnet-deploy-api-app.md)。

## 使用瀏覽器來呼叫 API 應用程式 

驗證您的 API 應用程式是否可公開存取的最簡單方法就是從瀏覽器呼叫它。

1. 在瀏覽器中，移至 [Azure preview portal]。

3. 從首頁按一下 **瀏覽 > API 應用程式** ，然後按一下您想要保護的 API 應用程式的名稱。

    ![瀏覽](./media/app-service-api-dotnet-add-authentication/browse.png)

    ![選取 API 應用程式](./media/app-service-api-dotnet-add-authentication/select.png)

3. 在 **API 應用程式** 刀鋒視窗中，按一下 [ **URL** 開啟可呼叫 API 應用程式的瀏覽器視窗。

    ![API 應用程式刀鋒視窗](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. 將 `/api/contacts/get/` 新增至瀏覽器網址列中的 URL。

    例如，如果您的 API 應用程式的 URL 如下：

        https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

    完整的 URL 會如下所示：

        https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

    不同的瀏覽器會以不同方式處理 API 呼叫。 下圖顯示從 Chrome 瀏覽器進行的成功呼叫。

    ![Chrome Get 回應](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. 儲存您使用的 URL；您稍後將在本教學課程中再次使用它。

## 保護 API 應用程式

當您部署 API 應用程式時，您會將它部署到資源群組。 您可以將 Web 應用程式和其他 API 應用程式加入至相同的資源群組，而資源群組內的每個 API 應用程式可以有下列其中一個可存取性設定：
<!--todo: diagram showing different accessibility settings-->

- **公用 (匿名)** -未登入的情況下，任何人都可以呼叫 API 應用程式從資源群組外部。
- **公用 (驗證)** -已驗證的使用者才可以呼叫 API 應用程式從資源群組外部。
- **內部** -只有在相同的資源群組中其他 API 應用程式可以呼叫 API 應用程式。 (從 Web 應用程式呼叫會被視為外部呼叫，即使 Web 應用程式位於相同的資源群組也一樣。)

當 Visual Studio 為您建立資源群組時，它也會建立 *閘道*。  閘道是一個特別的 Web 應用程式，可處理以資源群組中的 API 應用程式為目標的所有要求。

當您移至資源群組刀鋒視窗中 [Azure preview portal], ，您可以看到您的 API 應用程式和圖表中的閘道。

![資源群組圖](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

### <a id="apiapp"></a>將 API 應用程式設定為需要驗證

若要設定您的 API 應用程式只接受已驗證的要求，會將其存取範圍設定為 **公用 (驗證)** 而且您會設定為需要從提供者，例如 Azure Active Directory、 Google 或 Facebook 驗證閘道。

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

您現已保護 API 應用程式，使其免於遭受未經驗證的存取。 接下來，您必須設定閘道來指定要使用的驗證提供者。

### <a id="gateway"></a>將閘道設定為使用驗證提供者

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## 確認驗證可以運作

**注意:** 如果您有問題，執行下列步驟時發生登入，請嘗試開啟私人或匿名視窗。
 
1. 開啟瀏覽器視窗，並在網址列中輸入可呼叫 API 應用程式之 `Get` 方法的 URL (如同您先前所為)。

    這次嘗試存取 API 應用程式會導致錯誤訊息。

    ![Chrome Get 回應失敗](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. 在瀏覽器中，移至登入 URL。 URL 遵循以下模式： 

        http://[gatewayurl]/login/[providername]

    您可以取得閘道 URL，從 **閘道** 刀鋒視窗中的 [Azure preview portal]。 (若要取得 **閘道** 刀鋒視窗中，按一下顯示在圖表中的閘道 **資源群組** 刀鋒視窗。)

    ![閘道 URL](./media/app-service-api-dotnet-add-authentication/gatewayurl.png)

    [providername] 的值必須是以下其中一項：
    
    * "microsoftaccount"
    * "facebook"
    * "twitter"
    * "google"
    * "aad"

    這裡是 Azure Active Directory 的範例登入 URL：

        https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

    請注意，不同於先前的 URL，此 url 不包含您的 API 應用程式名稱: 閘道正在驗證您無法將 API 應用程式。  閘道會處理資源群組中所有 API 應用程式的驗證。

3. 在瀏覽器顯示登入頁面時輸入您的認證。 
 
    如果您設定 Azure Active Directory 登入，使用其中一個列出的使用者 **使用者** 您建立的 Azure Active Directory 索引標籤中的應用程式] 索引標籤 [Azure portal], ，例如 admin@contoso.onmicrosoft.com。

    ![AAD 使用者](./media/app-service-api-dotnet-add-authentication/aadusers.png)

    ![登入頁面](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. 出現「登入完成」訊息時，再次輸入 API 應用程式之 Get 方法的 URL。

    此時因為您已通過驗證，所以呼叫會成功。 閘道會將您視為已驗證的使用者，並將您的要求傳遞到您的 API 應用程式。

    ![登入完成](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

    ![Chrome Get 回應](./media/app-service-api-dotnet-add-authentication/chromeget.png)

    如果您已經啟用 Swagger UI，您現在也可以移至 Swagger UI 頁面。 不過，您會看到紅色 **錯誤** 圖示右下角] 頁面上，，如果您按一下圖示即可看到訊息，說明 Swagger JSON 檔案無法存取。 這是因為 Swagger 進行 AJAX 呼叫嘗試擷取 JSON 檔案，但未包含 Zumo 權杖。 這不會造成 Swagger UI 頁面無法運作。

## 使用 Postman 傳送 Post 要求

當您登入閘道時，閘道會傳回驗證權杖。  此權杖必須隨附於來自通過閘道之外部來源的所有要求。 當您使用瀏覽器存取 API 時，瀏覽器通常會在 cookie 中儲存權杖，並將它連同所有後續的呼叫一起傳送至 API。

您可以看到背景中發生的事情，教學課程的本節中您會使用瀏覽器工具來建立和提交 Post 要求，然後從 Cookie 取得授權權杖並將它包含在 HTTP 標頭中。 本節是選擇性：在前一節中，您已確認 API 應用程式只接受已驗證的存取。

這些指示說明如何在 Chrome 瀏覽器中使用 Postman 工具，但是您可以利用任何 REST 用戶端工具和瀏覽器開發人員工具來執行相同的作業。

1. 在 Chrome 瀏覽器視窗中，執行上一節中所示的步驟來進行驗證，然後開啟開發人員工具 (F12)。

    ![移至 [資源] 索引標籤](./media/app-service-api-dotnet-add-authentication/resources.png)

3. 在 **資源** 索引標籤上的 Chrome 開發人員工具，尋找 cookie 閘道器，然後按三下的值 **x zumo 驗證** cookie 來選取它的全部。

    **注意:**  請確定您取得的所有 cookie 的值。 如果按兩下，只會取得它的第一個部分。

5. 以滑鼠右鍵按一下 **值** 的 **x zumo 驗證** cookie，然後再按一下 **複製**。

    ![複製驗證權杖](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. 如果您還沒這麼做，請在 Chrome 瀏覽器中安裝 Postman 延伸模組。

6. 開啟 Postman 延伸模組。

7. 在 [要求 URL] 欄位中，輸入您先前使用的 API 應用程式的 Get 方法 URL，但省略結尾的 `get/`。
 
        http://[apiappurl]/api/contacts
    
8. 按一下 [ **標頭**, ，然後新增 *x zumo 驗證* 標頭。 貼上剪貼簿中的權杖值 **值** 欄位。

9. 新增 *Content-type* 標頭值 *application/json*。

10. 按一下 [ **表單資料**, ，然後新增 *連絡* 金鑰具有下列值:

        {   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. 按一下 [傳送]。

    API 應用程式會傳回 *201 已建立* 回應。

    ![新增標頭和內文](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. 若要驗證此要求是否沒有驗證權杖就無法運作，請刪除驗證標頭，再按一下 [傳送]。

    您取得 *403 禁止* 回應。

    ![[403 禁止] 回應](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## 取得已登入使用者的相關資訊

本節將說明如何變更 ContactsList API 應用程式中的程式碼，以擷取並傳回已登入使用者的名稱和電子郵件地址。  

1. 在 Visual Studio 中，開啟您在部署 API 應用程式專案 [部署 API 應用程式](app-service-dotnet-deploy-api-app.md) 和已即本教學課程。

3. 開啟 apiapp.json 檔案，並加入一行，說明 API 應用程式是使用 Azure Active Directory 驗證。

        "authentication": [{"type": "aad"}]

    最後的 apiapp.json 檔案會類似以下範例所示：

        {
            "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
            "id": "ContactsList",
            "namespace": "microsoft.com",
            "gateway": "2015-01-14",
            "version": "1.0.0",
            "title": "ContactsList",
            "summary": "",
            "author": "",
            "endpoints": {
                "apiDefinition": "/swagger/docs/v1",
                "status": null
            },
            "authentication": [{"type": "aad"}]
        }

    本教學課程會使用 Azure Active Directory 做為範例。 若是其他提供者，則應改用適當的識別碼。 有效的提供者值如下：

    * "aad"
    * "microsoftaccount"
    * "google"
    * "twitter"
    * "facebook" 

3. 在 *ContactsController.cs* 檔案中，新增 `using` 陳述式，在檔案頂端。

        using Microsoft.Azure.AppService.ApiApps.Service;

2. 中的程式碼來取代 `Get` 為下列程式碼的方法。

        var runtime = Runtime.FromAppSettings(Request);
        var user = runtime.CurrentUser;
        TokenResult token = await user.GetRawTokenAsync("aad");
        var name = (string)token.Claims["name"];
        var email = (string)token.Claims["http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"];
        return new Contact[]
        {
            new Contact { Id = 1, EmailAddress = email, Name = name }
        };

    程式碼會傳回已登入使用者的連絡資訊，而不是三個範例連絡人。 

    範例程式碼會使用 Azure Active Directory。 若是其他提供者，則應依照上一個步驟所述，使用適當的權杖名稱與宣告識別碼。

    可用的 Azure Active Directory 宣告的相關資訊，請參閱 [支援的權杖和宣告類型](https://msdn.microsoft.com/library/dn195587.aspx)。

3. 為 `Microsoft.Azure.AppService.ApiApps.Service` 新增 using 陳述式：

        using Microsoft.Azure.AppService.ApiApps.Service;

3. 重新部署專案。  

    Visual Studio 會記住您部署專案時設定 [部署](app-service-dotnet-deploy-api-app.md) 教學課程。  以滑鼠右鍵按一下專案，請按一下 **發行**, ，然後按一下 [ **發行** 中 **發行 Web** ] 對話方塊。

6. 依照稍早執行過的程序，將 Get 要求傳送至受保護的 API 應用程式。 

    回應訊息會顯示您用來登入的身分識別名稱與識別碼。

    ![有關已登入使用者的回應訊息](./media/app-service-api-dotnet-add-authentication/chromegetuserinfo.png)

## 後續步驟

您已了解如何透過 Azure Active Directory 或社交提供者驗證來保護 Azure API 應用程式。 如需詳細資訊，請參閱 [API 應用程式和行動應用程式驗證](../app-service/app-service-authentication-overview.md)。 

[Azure portal]: https://manage.windowsazure.com/
[Azure preview portal]: https://portal.azure.com/

