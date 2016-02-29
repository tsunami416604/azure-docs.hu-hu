<properties
    pageTitle="Azure App Service 中 API Apps 的使用者驗證 | Microsoft Azure"
    description="了解如何藉由僅允許經過驗證的使用者存取，保護 Azure App Service 中的 API 應用程式。"
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="11/30/2015"
    ms.author="tdykstra"/>

# Azure App Service 中 API Apps 的使用者驗證

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概觀

本教學課程示範如何使用 Azure App Service 的驗證和授權功能來保護 API 應用程式，以及如何代表一般使用者取用 API 應用程式。 教學課程中所顯示的驗證提供者是 Azure Active Directory，API 是 ASP.NET Web API，用戶端則是在瀏覽器中執行的 AngularJS 單一頁面應用程式。

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
## App Service 中的驗證與授權

本教學課程中所使用的驗證功能的簡介，請參閱先前的教學課程，本系列 [Azure App Service 中 API 應用程式的驗證和授權](app-service-api-authentication.md)。

## 如何遵循本教學課程

本教學課程以您下載並建立的 API 應用程式中的範例應用程式 [的 API 應用程式和 ASP.NET 取得第一個教學課程開始系列](app-service-api-dotnet-get-started.md)。

## ContactsList.Angular.AAD 範例專案

在 [ContactsList 範例應用程式](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), ，ContactsList.Angular.AAD 專案會包含與 Azure Active Directory 使用的程式碼的 AngularJS 用戶端。 此程式碼基礎中可以找到 AAD 範例 [Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) 儲存機制。

ContactsList.Angular.AAD 專案中的程式碼結構與較簡單的 ContactsLists.Angular 專案不同。 呼叫 API 的程式碼位於 *app/scripts/contactsSvc.js* ContactsList.Angular.AAD 專案檔案中的。 

        angular.module('contactsListApp')
        .factory('contactsSvc', ['$http', function ($http) {
            //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
            var apiEndpoint = "https://localhost:44300";
        
            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 
        
            return {
                getItems: function () {
                    return $http.get(apiEndpoint + '/api/contacts');
                },
                getItem : function(id){
                    return $http.get(apiEndpoint + '/api/contacts/' + id);
                },
                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/contacts', item);
                },
                putItem : function(item){
                    return $http.put(apiEndpoint + '/api/contacts/', item);
                },
                deleteItem : function(id){
                    return $http({
                        method: 'DELETE',
                        url: apiEndpoint + '/api/contacts/' + id
                    });
                }
            };
        }]);

在這裡，`Get` 方法會標示為 `getItems`。  在控制器中 (*app/scripts/contactsCtrl.js*)， `getItems` 已達 `$scope.populate`。

        $scope.populate = function () {
            contactsSvc.getItems().then(function (results) {
                $scope.contactsList = results.data;
                $scope.loadingMessage = "";
            }, function (err) {
                $scope.loadingMessage = "";
                $scope.error = "Error: " + err;
            });
        };

在檢視 (*app/views/Contacts.html*)，$scope.populate 稱為初始化。

        <div ng-init="populate()">

記錄以及透過 API 要求的授權權杖的其他程式碼由 [Azure Active Directory Authentication Library for JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js), 中 *adal.js* 和 *adal-angular.js* 檔案。 

在 *app.js* 檔案中，程式碼會傳遞組態資訊和 `$http` 提供者 `adalProvider.init` 函式。 組態資訊包括適用於每個 API 端點的 AAD 應用程式用戶端識別碼和適用於此 AngularJS 應用程式的用戶端識別碼。 `init` 函式會對 `$http` 提供者新增攔截器，而這些攔截器會對要求新增授權權杖。

        var endpoints = { 
            //"https://{your api app name}.azurewebsites.net/": "{your client id}"
            "https://localhost:44300/": "{your client id}"
        };

        adalProvider.init(
            {
                instance: 'https://login.microsoftonline.com/', 
                tenant: '{your tenant url}',
                clientId: '{your client id}',
                extraQueryParameter: 'nux=1',
                endpoints: endpoints
                //cacheLocation: 'localStorage', // enable this for 
            },
            $httpProvider
            );

## 在 Azure 中設定驗證與授權

1. 在 [Azure 入口網站](https://portal.azure.com/), ，瀏覽至 **API 應用程式** 刀鋒視窗中，您想要保護，以便僅限通過驗證使用者的 API 應用程式可以呼叫它。 (在本教學課程中，請選擇 ContactsList.API 專案所部署到的 API 應用程式)。

2. 按一下 [ **設定**

2. 尋找 **功能** 區段，然後再按一下 **驗證 / 授權**。

    ![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. 在 **驗證 / 授權** 刀鋒視窗中，按一下 [ **上**。

4. 在 **不會驗證要求時要採取的動作** 下拉式清單中，選取 **登入 Azure Active Directory**。

5. 在 **驗證提供者**, ，按一下 [ **Azure Active Directory**。

    ![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. 在 **Azure Active Directory 設定** 刀鋒視窗中，按一下 [ **Express**

    ![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

    在這邊，「快速」是表示 Azure 會自動在 AAD 租用戶中建立 AAD 應用程式。 請記下新的 AAD 應用程式的名稱，因為您稍後在移至 Azure 傳統入口網站以取得其用戶端識別碼時需要選取它。

7. 按一下 [ **確定**。

10. 在 **驗證 / 授權** 刀鋒視窗中，按一下 [ **儲存**。

8. 若要驗證 API 應用程式現已受到保護，請移至 API 應用程式的 URL + `/swagger`如同您在第一個教學課程中一樣使用 Swagger UI。 

    這次系統會將您重新導向至登入頁面。

    ![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，請移至 **Azure Active Directory**。

    您必須移至傳統入口網站，因為現行的 Azure 入口網站尚未提供您需要存取的特定 Azure Active Directory 設定。

12. 在 **目錄** 索引標籤上，按一下您的 AAD 租用戶。

    ![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. 按一下 [ **應用程式 > 我公司所擁有的應用程式**, ，然後按一下核取記號。

    您可能還必須重新整理頁面，才能看見新的應用程式。

15. 在應用程式清單中，按一下當您針對 API 應用程式啟用驗證時 Azure 為您建立的應用程式名稱。

    ![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. 按一下 [ **設定**。

    ![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. 在頁面底部，按一下 [ **管理資訊清單 > 下載資訊清單**, ，並將檔案儲存在其中您可以編輯它的位置。

16. 在下載的資訊清單檔案中搜尋 「  `oauth2AllowImplicitFlow` 屬性。 將這個屬性的值從 `false` 變更為 `true`，然後儲存檔案。

    必須要有這項設定才能從 JavaScript 單一頁面應用程式進行存取。 它可讓 Oauth 2.0 持有人權杖傳回 URL 片段中。

16. 按一下 [ **管理資訊清單 > 上傳資訊清單**, ，並上傳您在前述步驟中更新的檔案。

17. 一直保持頁面開啟，讓您可以複製並貼上值，並且在稍後的教學課程步驟中更新頁面上的值。

## 設定 ContactsList.Angular.AAD 專案以呼叫 Azure API 應用程式

下列指示說明如何在 Azure 中部署並執行應用程式，但只要略做變更，您就能在本機執行。 範例程式碼包含 localhost URL 端點。 如果您想在本機執行，請將專案設定為使用 SSL，在專案程式碼中使用 localhost SSL URL，並在 AAD 應用程式設定中使用 localhost SSL URL。 在本機執行時，AngularJS 程式碼只會允許已登入的使用者呼叫 API，但來自其他用戶端的未經驗證呼叫者可以呼叫 API。

1. 在 ContactsList.Angular.AAD 專案中，開啟 *app/scripts/app.js* 檔案。

8. 在設定 `endpoints` 變數的程式碼中，將 localhost 端點註解化，並取消註解 Azure 端點。

10. 將 「 yourclientid 」 與實際值的 AAD 應用程式從傳統入口網站的用戶端識別碼 **設定** AAD 應用程式] 索引標籤。

2. 將 "{您的 API 應用程式名稱}" 替換為 ContactsList.API 專案所部署到之 API 應用程式的名稱。

    程式碼現在類似下列範例。

        var endpoints = {
            "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
            //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
        };

9. 此外，在 *app.js*, ，呼叫中 `adalProvider.init`, ，取代 「 {您租用戶 url}"和"{用戶端識別碼} 」 的實際值。

    程式碼如下列範例所示：

        adalProvider.init(
            {
                instance: 'https://login.microsoftonline.com/', 
                tenant: 'contoso.onmicrosoft.com',
                clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
                extraQueryParameter: 'nux=1',
                endpoints: endpoints
            },
            $httpProvider
            );

1. 在 *app/scripts/contactsSvc.js* 檔案，請在相同的端點 URL 從 localhost 變更為稍早在 API 應用程式 URL *app.js*。

    程式碼現在類似下列範例。

        var apiEndpoint = "https://contactslistapi.azurewebsites.net";
        //var apiEndpoint = "https://localhost:44300";

## 針對 Azure Web 應用程式設定 AAD 應用程式

1. 在 **設定** AAD 應用程式，在傳統入口網站中，索引標籤中 **登入 URL** 欄位中，刪除已經存在的 URL 並取代 web 應用程式的基底 URL，包括結尾的斜線。 (請注意，這是將要執行 AngularJS 程式碼之 Web 應用程式的 URL，而不是 API 應用程式的 URL)。

    ![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. 在 **回覆 URL** 欄位中，已經有 web 應用程式的基底 url 的 URL 取代。

    ![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. 按一下 [ **儲存**。

## 將 ContactsList.Angular.AAD 專案部署至 Azure

8. 在 **方案總管] 中**, ，ContactsList.Angular.AAD 專案上按一下滑鼠右鍵，然後按一下 **發行**。

9. 按一下 [ **Microsoft Azure App Service**。

10. 在 **應用程式服務** 對話方塊中，於 **訂閱** 下拉式清單中，選擇您的訂閱。

11. 展開您為本教學課程建立的資源群組，然後選取您在第二個教學課程中建立的 Web 應用程式。

    ![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. 按一下 [ **確定**。

12. 在 **發行 Web** 精靈] 中，按一下 **連接** ] 索引標籤，然後在 **目的地 URL** 方塊變更 `http://` 至 `https://`

    ![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

    此設定會決定在部署成功後要將預設瀏覽器開啟在哪個 URL。

12. 在 **發行 Web** 精靈] 中，按一下 **設定** 索引標籤上，依序展開 **檔案發行選項**, ，然後選取 **移除目的端的其他檔案** 核取方塊。

7. 按一下 [ **發行**。

    Visual Studio 會部署專案並將瀏覽器開啟至應用程式的首頁。

## 在 Azure 中測試 AngularJS Web 應用程式

8. 按一下 [ **連絡人** ] 索引標籤。

    系統會提示您登入。

9. 使用 AAD 租用戶中使用者的認證進行登入。

10.  **連絡人** ] 頁面隨即出現。

    ![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

前端現在可以代表經過驗證的使用者呼叫 API，但未經驗證的使用者無法呼叫 API。

## 後續步驟

在本教學課程中，您使用 App Service 的驗證和授權功能來限制 API 應用程式存取，讓只有經過驗證的使用者才可以呼叫它。 數列中的下一個教學課程中，您將學習如何 [限制存取服務-案例的 API 應用程式](app-service-api-dotnet-service-principal-auth.md)。

