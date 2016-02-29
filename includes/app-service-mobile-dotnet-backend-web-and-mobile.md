本主題將示範如何建立一個同時包含行動和 Web 用戶端的應用程式。 您將建立一個行動應用程式和一個 Web 應用程式，並為兩者使用相同的基礎資料庫。

首先您要建立新的行動裝置應用程式後端以及一個簡單 *待辦事項清單* 應用程式資料儲存在新的行動裝置應用程式後端應用程式。 行動應用程式後端使用支援的 .NET 語言，供伺服器端的商務邏輯使用。 用戶端應用程式可以使用行動應用程式支援的任何用戶端平台，包括 iOS、Windows、Xamarin iOS 和 Xamarin Android。

接著，您將使用與您的行動應用程式相同的資料庫，建立一個 Web 應用程式。 在本教學課程的結尾，您將擁有一個使用相同資料的 Web 用戶端和一個行動用戶端。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 建立新的行動應用程式後端和用戶端

* 依照本教學課程中的步驟 [Create a mobile app] 建立行動裝置應用程式後端和用戶端。 您可以使用行動應用程式支援的任何用戶端平台，包括 iOS、Windows、Xamarin iOS 和 Xamarin Android。

* 請確定您已將行動應用程式後端部署至 Azure，而且您將行動用戶端應用程式連接至託管的後端。 行動應用程式程式碼專案使用 Entity Framework Code First，並在行動用戶端應用程式的第一個 REST 要求之後初始化資料庫。

## 從 Visual Studio 發行 TodoList Web API

在本節中，您將使用範例 Web 應用程式方案，建立一個新的 Web 應用程式。 您將修改範例以使用相同的資料庫結構描述名稱和相同的連接字串做為行動應用程式。

>[AZURE.NOTE] 完成這些步驟之前，請確定已將用戶端連線到它，來初始化您的行動裝置應用程式後端資料庫。 否則，Web 應用程式將無法連線到相同的資料庫。

1. 在 [Azure 入口網站](https://portal.azure.com/), ，建立新的 web 應用程式，您的行動應用程式使用的相同資源群組和主控方案。

2. 下載範例方案 [MultiChannelToDo] 和 Visual Studio 中開啟。 此方案同時包含一個 Web API 專案和一個適用於 Web 用戶端 UI 的 Web 應用程式專案。

3. 在 Web API 專案中，編輯 MultiChannelToDoContext.cs。 在 `OnModelCreating` 中，將結構描述名稱更新成與您的行動應用程式名稱相同：

        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore

4. 接下來，我們將從 Azure 入口網站取得行動應用程式連接字串：

    - 在入口網站中選取行動應用程式，然後按一下組件標示為 **使用者程式碼**。

    - 在開啟的刀鋒視窗中選取 **所有設定**, ，然後 **應用程式設定**。

    - 在 **連接字串** 按一下 **顯示連接字串**。 複製設定值 **MS_TableConnectionString**。 這是您的行動應用程式用來連線到 SQL 資料庫的連接字串。

5. 在 Visual Studio 中，以滑鼠右鍵按一下 Web API 專案，然後選取 **發行**。 選取 **Azure Web Apps** 做為發佈目標，然後選取您先前建立的 web 應用程式。 按一下 [ **下一步** 直到您進入 **設定** 發行 Web 精靈] 的區段。

6. 在 **資料庫** 區段中，貼上行動應用程式連接字串做為值 **MultiChannelToDoContext**。 選取此核取方塊 **在執行階段使用此連接字串**。

7. 一旦您的 Web API 已成功發行至 Azure 之後，您將會看到一個確認頁面。 複製已發行之服務的 URL。

## 從 Visual Studio 發行 TodoList Web 用戶端 UI

在本節中，您將使用以 AngularJS 實作的範例 Web 用戶端應用程式。 接著，您將使用 Visual Studio 將專案發行至 Azure 中新託管的 App Service Web 應用程式。

1. 在 Visual Studio 中開啟專案 **MultiChannelToDo.Web**。 編輯檔案 `js/service/ToDoService.js`，將 URL 新增至您剛才發佈的 Web API 中：

        var apiPath = "https://your-web-api-site-name.azurewebsites.net";

2. 以滑鼠右鍵按一下專案 **MultiChannelToDo.Web** ，然後選取 **發行**。

3. 在 **發行 Web** 精靈中，選取 **Azure Web 應用程式** 做為發佈目標，並建立不含資料庫的新 web 應用程式。

4. 一旦成功發行您的專案之後，您將會在瀏覽器中看到 Web UI。

## 測試行動和 Web 應用程式

1. 在 Web UI 中，新增或編輯某些待辦項目。

    ![瀏覽器中的 Web 應用程式檢視](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)

2. 執行您在中建立的行動應用程式 [Create a mobile app] 教學課程。 您將會在 Web 應用程式看到相同的待辦項目。

    ![Xamarin 行動應用程式的檢視](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需舊入口網站變更為新入口網站的指南，請參閱: [瀏覽 Azure 入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[Create a mobile app]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md

