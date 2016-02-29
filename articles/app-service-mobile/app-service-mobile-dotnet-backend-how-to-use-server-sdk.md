<properties
    pageTitle="如何使用適用於行動應用程式的 .NET 後端伺服器 SDK | Azure App Service"
    description="了解如何使用適用於 Azure App Service 行動應用程式的 .NET 後端伺服器 SDK。"
    keywords="app service, azure app service, mobile app, mobile service, scale, scalable, app deployment, azure app deployment"
    services="app-service\mobile"
    documentationCenter=""
    authors="ggailey777" 
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="glenga"/>

# 使用適用於 Azure 行動應用程式的 .NET 後端伺服器 SDK

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何在主要的 Azure App Service 行動應用程式案例中使用 .NET 後端伺服器 SDK Azure 行動應用程式 SDK 可協助您從 ASP.NET 應用程式使用行動用戶端。

>[AZURE.TIP]  [.NET 伺服器 SDK for Azure Mobile Apps](https://github.com/Azure/azure-mobile-apps-net-server) 是 GitHub 上的開放原始碼。 儲存機制包含整個伺服器 SDK 單元測試組件以及一些範例專案。

## 參考文件

伺服器 SDK 的參考文件位於此處: [Azure 行動應用程式的.NET 參考](https://msdn.microsoft.com/library/azure/dn961176.aspx)。

## <a name="create-app"></a>如何：為您的行動應用程式建立 .NET 後端

如果您開始新的專案，您可以建立應用程式服務應用程式使用 [Azure portal] 或 Visual Studio。 本章節將協助您使用其中一個項目來建立新的行動應用程式後端，它會裝載簡單的待辦事項清單 API。 您可以在本機執行此作業，或將專案發佈至雲端架構 App Service 行動應用程式。

如果您的行動功能新增至現有的專案，請參閱 [下載並初始化 SDK](#install-sdk) 下一節。

### 使用 Azure 入口網站建立 .NET 後端

您可以建立新的行動應用程式權限在 [Azure portal]。 您可以依照下列步驟，或建立新的用戶端和伺服器一起依照 [建立行動應用程式](app-service-mobile-ios-get-started.md) 教學課程。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9. 回到 _開始_ 刀鋒視窗底下 **建立資料表 API**, ，選擇 **C#** 做為您 **的後端語言**。

&nbsp;&nbsp;10. 按一下 [下載]，將壓縮的專案檔案解壓縮至您的本機電腦，並在 Visual Studio 中開啟方案。

### 使用 Visual Studio 2013 和 Visual Studio 2015 建立 .NET 後端

若要在 Visual Studio 建立行動應用程式專案，您必須安裝 [Azure SDK for.NET](https://azure.microsoft.com/downloads/), ，2.8.1 版或更新版本。 當您安裝 SDK 之後，建立新的 ASP.NET 應用程式：

1. 開啟 **新的專案** 對話方塊 (從 *檔案* > **新增** > **專案...**)。

2. 展開 **範本** > **Visual C#**, ，然後選取 **Web**。

3. 選取 **ASP.NET Web 應用程式**。

4. 填入專案名稱。 然後按一下 [ **確定**。

5. 在 _ASP.NET 4.5.2 範本_, ，請選取 **Azure 行動應用程式**。 檢查 **定域機組中的主機** 定域機組，您可以發行這個專案中建立新的行動裝置應用程式。

6. 按一下 [ **確定**。 您的應用程式會建立並且在 [方案總管] 中顯示。

## <a name="install-sdk"></a>做法：下載並初始化 SDK

一套 SDK 可在 [NuGet.org]。 此封裝包含開始使用 SDK 所需的基本功能。 若要初始化 SDK，您需要上執行的動作 **HttpConfiguration** 物件。 

###安裝 SDK

若要安裝 SDK，以滑鼠右鍵按一下伺服器專案在 Visual Studio 中，選取 **管理 NuGet 封裝**, ，搜尋 [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 套件，然後按一下 [ **安裝**。  

###<a name="server-project-setup"></a> 初始化伺服器專案

初始化 .NET 後端伺服器專案的方式類似其他 ASP.NET 專案，可藉由包含 OWIN 啟動類別來完成。 請確定您已參考 NuGet 封裝 `Microsoft.Owin.Host.SystemWeb`。 若要在 Visual Studio 中加入這個類別，以滑鼠右鍵按一下伺服器專案，然後選取 **新增** ]-> [ **新項目**, ，然後 **Web** ]-> [ **一般** ]-> [ **OWIN 啟動類別**。

這將會產生具有下列屬性的類別：

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

在 `Configuration()` OWIN 啟動類別，設定伺服器專案使用的方法 **HttpConfiguration** 物件，以代表服務的組態選項。 下列範例會初始化伺服器專案，不新增任何功能： 

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();
       
        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);  
        
        app.UseWebApi(config);
    }

若要啟用個別功能，您必須呼叫擴充方法 **MobileAppConfiguration** 物件之前，先呼叫 **ApplyTo**。 例如，下列程式碼會在初始化期間，將預設路由加入具有屬性 `[MobileAppController]` 的所有 API 控制器中：

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

請注意，`MapApiControllers` 只會對應具有屬性 `[MobileAppController]` 的控制器。

許多功能的擴充方法都可透過其他您可以包含的 NuGet 封裝提供使用，於下節中說明。 

從 Azure 入口網站呼叫伺服器快速入門 **UseDefaultConfiguration()**。 此命令相當於下列設定：
    
        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);


### SDK 延伸模組

下列 NuGet 型擴充套件提供了許多您應用程式可以使用的行動功能。 在初始化期間啟用擴充功能使用 **MobileAppConfiguration** 物件。

- [Microsoft.Azure.Mobile.Server.Quickstart] 
    支援基本的行動應用程式設定。 藉由呼叫加入至組態 **UseDefaultConfiguration** 在初始化期間的擴充方法。 此延伸模組包含下列延伸模組：通知、驗證、實體、資料表，Crossdomain 與首頁封裝。 這就相當於您從 Azure 入口網站下載的快速入門伺服器專案。

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/)   
    實作預設 *此行動應用程式已啟動並執行頁面* 網站根目錄。 藉由呼叫新增至設定 **AddMobileAppHomeController** 擴充方法。

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/)  
    包含處理資料的類別並設定總資料管線。 藉由呼叫新增至設定 **AddTables** 擴充方法。

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/)   
    可讓 Entity Framework 存取 SQL 資料庫中的資料。 藉由呼叫新增至設定 **AddTablesWithEntityFramework** 擴充方法。

- [Microsoft.Azure.Mobile.Server.Authentication] 
   啟用驗證並設定總 OWIN 中介軟體用來驗證權杖。 藉由呼叫新增至設定 **AddAppServiceAuthentication** 和 **IAppBuilder**。**UseMobileAppAuthentication** 擴充方法。

- [Microsoft.Azure.Mobile.Server.Notifications]
   啟用推播通知，並定義推播註冊端點。 藉由呼叫新增至設定 **AddPushNotifications** 擴充方法。

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/)  
    建立提供資料至舊版 web 瀏覽器，從您的行動應用程式的控制站。 藉由呼叫新增至設定 **MapLegacyCrossDomainController** 擴充方法。

- [Microsoft.Azure.Mobile.Server.Login]
    透過 MobileAppLoginHandler.CreateToken() 方法的自訂驗證提供預覽支援。 這是靜態方法，不需要在組態中啟用。

## 做法：發佈伺服器專案

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

您也可以使用其他任何方法中涵蓋 [Azure App Service 部署文件](../app-service-web/web-site-deploy.md)。

## 做法：定義資料表控制器

資料表控制器提供資料表型資料存放區 (例如 SQL Database 或 Azure 資料表儲存體) 中實體資料的存取權。 資料表控制器繼承自 **TableController** 泛型類別，其中的泛型型別是代表資料表結構描述，如下所示的模型中的實體:

    public class TodoItemController : TableController<TodoItem>
    {  
        //...
    }

透過使用資料表控制器初始化 **AddTables** 擴充方法。 這會將路由加入 `TableController` 所有子類別的 `/tables/` 底下。 

下列範例會初始化使用 Entity Framework 進行資料存取的資料表控制器：

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
如需使用 Entity Framework 來存取資料，從 Azure SQL Database 資料表控制器的範例，請參閱 **TodoItemController** 的快速入門伺服器專案下載的 Azure 入口網站中的類別。

## 做法：定義自訂 API 控制器

自訂 API 控制器透過公開端點，提供最基本的功能給您的行動應用程式後端。 您可以使用屬性 `MobileAppControllerAttribute` 來註冊行動裝置特定 API 控制器。 這個屬性會註冊路由，也會設定 Mobile Apps JSON 序列化程式。 

1. 在 Visual Studio 中，以滑鼠右鍵按一下 Controllers 資料夾，然後按一下 [ **新增** > **控制器**, ，請選取 **Web API 2 控制器 & mdash;空白** 按一下 **新增**。

2. 提供 **控制器名稱**, ，例如 `CustomController`, ，然後按一下 **新增**。 這會建立新 **CustomController** 類別繼承自 **ApiController**。   

3. 在新的控制器類別檔案中，新增下列 Using 陳述式：

        using Microsoft.Azure.Mobile.Server.Config;

4. 套用 **MobileAppControllerAttribute** API 控制器類別定義，如下列範例所示:

        [MobileAppController] 
        public class CustomController : ApiController
        {
              //...
        }

4. 在 App_Start/Startup.MobileApp.cs 檔案中，新增呼叫至  **MapApiControllers** 擴充方法，如下列範例所示:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);
    
    請注意，您不需要呼叫 **MapApiControllers** 如果改為呼叫 **UseDefaultConfiguration**, ，這會初始化所有的功能。 

沒有任何控制器 **MobileAppControllerAttribute** 套用仍然可以存取用戶端，但它可能不正確供用戶端使用任何行動裝置應用程式用戶端 SDK。 


## 做法：將驗證新增至伺服器專案

您可以將驗證新增至您的伺服器專案擴充 **MobileAppConfiguration** 物件和設定 OWIN 中介軟體。 當您安裝 [Microsoft.Azure.Mobile.Server.Quickstart] 封裝並呼叫 **UseDefaultConfiguration** 擴充方法，您可以跳到步驟 3。

1. 在 Visual Studio 中安裝 [Microsoft.Azure.Mobile.Server.Authentication] 封裝。 

2. Startup.cs 專案檔案中新增下列程式碼行的開頭 **組態** 方法:

        app.UseMobileAppAuthentication(config);

    這樣會新增讓您的 Azure 行動應用程式可驗證相關聯 App Service 閘道器發出之權杖的 OWIN 中介軟體元件。

3. 將 `[Authorize]` 屬性加入任何需要驗證的控制器或方法中。 現在必須驗證使用者才能存取該端點或特定 API。

若要深入了解如何驗證您的行動應用程式後端的用戶端，請參閱 [將驗證新增至您的應用程式](app-service-mobile-ios-get-started-users.md)。

## <a name="custom-auth"></a>做法：針對應用程式使用自訂驗證

如果您不想要使用其中一個 App Service 驗證/授權提供者，您可以選擇提供自己的登入系統。 若要這樣做，請安裝 [Microsoft.Azure.Mobile.Server.Login] 封裝。

您必須提供您自己的邏輯來判斷使用者是否應該登入。 例如，您可以針對資料庫中的 salted 和雜湊密碼進行檢查。 在下列範例中，`isValidAssertion()` 方法會負責這些檢查，並在其他地方定義。 

自訂驗證會公開，方法是建立新的 ApiController 並且公開如下的註冊和登入動作。 用戶端可以嘗試登入，方法是向使用者收集相關資訊，然後將 HTTPS POST 提交給在本文中具有使用者資訊的 API。 驗證這項資訊之後，可以使用 `MobileAppLoginHandler.CreateToken()` 方法發行權杖。 

可能的範例登入動作為：

        public HttpResponseMessage Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = MobileAppLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return this.Request.CreateResponse(HttpStatusCode.OK, new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

在上述範例中，LoginResult 和 LoginResultUser 只是公開顯示屬性的簡單物件。 用戶端預期有以下格式的 JSON 物件的登入回應：

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

 `MobileAppLoginHandler.CreateToken()` 方法包含 _觀眾_ 和 _簽發者_ 參數。 這兩個參數通常會使用 HTTPS 配置設定為應用程式根目錄的 URL。 同樣地，您應該設定 _secretKey_ 是您的應用程式的價值的簽署金鑰。 這是機密值，永遠不應共用或包含於用戶端。 您可以取得此值同時參考 App Service 中裝載 _WEBSITE_AUTH_SIGNING_KEY_ 環境變數。 如果需要在本機偵錯內容中，請依照下列中的指示 [以驗證本機偵錯](#local-debug) 擷取索引鍵，並將它儲存為應用程式設定] 區段。

您也必須提供已發行權杖的存留期，以及您想要包含的任何宣告。 您必須提供主體宣告，如範例程式碼所示。

## 做法：將推播通知新增至伺服器專案

您可以新增推播通知至您的伺服器專案延伸 **MobileAppConfiguration** 物件及建立通知中樞用戶端。 當您安裝 [Microsoft.Azure.Mobile.Server.Quickstart] 封裝並呼叫 **UseDefaultConfiguration** 擴充方法，您可以直接跳到步驟 3。

1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案中，按一下 [ **管理 NuGet 封裝**, 、 搜尋 Microsoft.Azure.Mobile.Server.Notifications'，然後按一下 [ **安裝**。 這會安裝 [Microsoft.Azure.Mobile.Server.Notifications] 封裝。
 
3. 重複這個步驟以安裝 `Microsoft.Azure.NotificationHubs` 封裝，其中包含通知中樞用戶端程式庫。 

2. 在 App_Start/Startup.MobileApp.cs，並新增呼叫 **AddPushNotifications** 擴充方法，在初始化期間，如下列所示:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);

    這會在您的伺服器專案中建立推播通知註冊端點。 用戶端會使用此端點來註冊相關聯的通知中樞。 現在您需要新增用來傳送通知的通知中樞用戶端。

3. 在您想要傳送推播通知的控制器中，新增下列 Using 陳述式：

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. 新增下列建立通知中樞用戶端的程式碼：

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

目前您可以使用「通知中樞」用戶端將推播通知傳送到已註冊的裝置。 如需詳細資訊，請參閱 [將推播通知新增至您的應用程式](app-service-mobile-ios-get-started-push.md)。 若要深入了解所有您可以使用通知中心，請參閱 [通知中心概觀](../notification-hubs/notification-hubs-overview.md)。

##<a name="tags"></a>做法：將標記新增至裝置安裝以啟用發送到標記

依照上述 **How to: 定義自訂的 API 控制器**, ，您會想要將設定為使用通知中心，將標記加入至特定裝置安裝在您的後端上的自訂 API。 請確定您已傳遞儲存在用戶端本機儲存體的裝置識別碼和您想加入的標記 (可省略，因為您也可以直接在您的後端指定標記)。 下列程式碼片段應新增至您的控制器，才能使用通知中樞新增標記至裝置安裝識別碼。

使用 [Azure 通知中樞 NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ([參考](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

        var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

        hub.PatchInstallation("my-installation-id", new[]
        {
            new PartialUpdateOperation
            {
                Operation = UpdateOperationType.Add,
                Path = "/tags",
                Value = "{my-tag}"
            }
        });

若要將推送至這些標記，使用 [通知中樞 Api](https://msdn.microsoft.com/library/azure/dn495101.aspx)。

您也可以獨立出自訂 API 以使用通知中樞直接在您的後端上註冊裝置安裝。

## 做法：偵錯和疑難排解 .NET 伺服器 SDK

Azure App Service 提供了數個適用於 ASP.NET 應用程式的偵錯和疑難排解技術：

- [監視 Azure App Service](../app-service-web/web-sites-monitor.md)
- [在 Azure App Service 中啟用診斷記錄](../app-service-web/web-sites-enable-diagnostic-log.md)
- [在 Visual Studio 中疑難排解 Azure App Service](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### 記錄

您可以使用標準的 ASP.NET 追蹤寫入來寫入 App Service 診斷記錄：

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");  

### <a name="local-debug"></a>本機偵錯使用驗證

您可以在將變更發佈至雲端之前，在本機執行您的應用程式以測試變更。 許多應用程式，則只需按 *F5* Visual Studio 中。 不過，使用驗證時有一些其他考量。

您必須擁有雲端式行動應用程式並且已設定 App Service 驗證/授權，而且您的用戶端必須有指定的雲端端點做為替代登入主機。 請參閱您所選的用戶端的平台的文件 ([iOS](app-service-mobile-ios-how-to-use-client-library.md), ，[Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)) 取得所需的特定步驟。

請確定您的應用程式具有 [Microsoft.Azure.Mobile.Server.Authentication] 安裝。 然後，在 `MobileAppConfiguration` 已套用至您的 `HttpConfiguration` 之後，在您的應用程式的 OWIN 啟動類別中加入下列項目：
        
        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetMobileAppTokenHandler()
        });

在上述範例中，您應該設定 _authAudience_ 和 _authIssuer_ Web.config 中的應用程式設定檔至每個應用程式根目錄的 url 使用 HTTPS 配置。 同樣地，您應該設定 _authSigningKey_ 是您的應用程式的價值的簽署金鑰。 這是機密值，永遠不應共用或包含於用戶端。 若要取得它，瀏覽至您的應用程式內 [Azure portal] 按一下 **工具**。 然後選取 **Kudu** 按一下 **移**。 這樣會帶您前往您的網站的 Kudu 管理端點。 按一下 [ **環境** 並尋找下的值 _WEBSITE_AUTH_SIGNING_KEY_。 這是您應使用的值 _authSigningKey_ 在本機應用程式組態中。

您的本機執行伺服器現在已裝備，可以驗證用戶端從雲端式端點取得的權杖。


[Azure portal]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

