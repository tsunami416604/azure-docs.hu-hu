<properties 
    pageTitle="API Apps 概觀" 
    description="了解 Azure App Service 為何是開發、發佈及裝載 RESTful API 的最佳平台。" 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/30/2015" 
    ms.author="tdykstra"/>

# API Apps 概觀

API 應用程式是四種應用程式類型所提供的其中一個 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

[應用程式服務](../app-service/app-service-value-prop-what-is.md) 是完全受管理的平台，可將一組豐富的功能帶入 web、 行動和整合案例。 App Service 中的 API Apps 提供各種功能，讓您更輕鬆地在雲端和內部部署當中建置、裝載和使用 API。 將您的 API 部署為應用程式服務中的 API 應用程式，並受益於企業級的安全性、 簡單的存取控制、 混合式連線、 自動 SDK 產生和完美整合與 [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)。

## 為何採用 API 應用程式？

API Apps 提供下列功能：

- **輕鬆使用** -整合支援 [Swagger API 中繼資料](#concepts) 讓各種用戶端都能輕鬆使用您的 Api。  使用各種語言 (包括 C#、Java 和 Javascript) 為您的 API 自動產生用戶端程式碼。 輕鬆地設定 [CORS](#concepts) 而不會變更您的程式碼。 如需詳細資訊，請參閱 [API 探索和程式碼產生的應用程式服務 API 應用程式中繼資料](app-service-api-metadata.md) 和 [從 JavaScript 使用 CORS 的 API 應用程式](app-service-api-cors-consume-javascript.md)。 

- **簡單的存取控制** -API 應用程式防止未驗證的存取，而不需變更，您的程式碼。 其他服務或代表使用者的用戶端存取 API 時，內建的驗證服務會提供保護。 支援的身分識別提供者包括 Azure Active Directory 和第三方提供者，例如 Facebook 和 Twitter。 用戶端可以使用 Active Directory Authentication Library (ADAL) 或行動應用程式 SDK。 如需詳細資訊，請參閱 [擴充應用程式服務驗證 / 授權](/blog/announcing-app-service-authentication-authorization/) 和 [應用程式服務 API 應用程式-變更的項目](app-service-api-whats-changed.md)。

- **Visual Studio 整合** -Visual Studio 中的專用的工具可簡化建立、 部署、 使用、 偵錯，以及管理 API 應用程式的工作。 如需詳細資訊，請參閱 [宣佈適用於.NET 的 Azure SDK 2.8.1](/blog/announcing-azure-sdk-2-8-1-for-net/)。

- **與邏輯應用程式整合** -可供您建立的 API 應用程式 [應用程式服務邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)。  了解如何在 [使用您的自訂 API 與邏輯應用程式的應用程式服務上主控](../app-service-logic/app-service-logic-custom-hosted-api.md)。 如何與邏輯應用程式整合 API 應用程式會不斷變化的相關資訊，請參閱 [應用程式服務 API 應用程式-變更的項目](app-service-api-whats-changed.md)。   

- **繼續使用現有的 API 集是** -您不需要變更任何現有 Api 利用 API 應用程式功能--只要將您的程式碼部署至 API 應用程式的程式碼。 您的 API 可以使用 App Service 支援的任何語言或架構，包括 ASP.NET 和 C#、Java、PHP、Node.js 和 Python。

此外，API Apps、Web Apps 以及 Mobile Apps 所提供的功能可彼此互換。 這表示 API Apps 的執行個體可利用 Web Apps 與 Mobile Apps 提供的 Web 與行動開發及主控功能。 反之亦然，例如您可以使用 Web 應用程式來裝載 API，同時仍可使用 Swagger 中繼資料執行用戶端程式碼產生，以及使用 CORS 進行跨網域瀏覽器存取。 如需詳細資訊，請參閱 [Web 應用程式的概觀](../app-service-web/app-service-web-overview.md) 和 [行動應用程式概觀](../app-service-mobile/app-service-mobile-value-prop.md)。

>[AZURE.NOTE] 您可以使用 [Azure API 管理](../api-management/api-management-key-concepts.md) 來控制用戶端存取所裝載的應用程式服務 API 應用程式的 Api。 雖然 API Apps 提供驗證服務，但有一些其他存取管理功能是它不提供而「API 管理」有提供的，例如端點合併和節流。

## API 應用程式概念 ##

- **Swagger** -文件和探索 RESTful API，依預設，API 應用程式中使用的架構。 如需詳細資訊，請參閱 [http://swagger.io/](http://swagger.io/)。
- **跨原始資源共用 (CORS)** -允許 JavaScript 瀏覽器中執行，才能裝載於不同的網域，比從載入網頁的 API 呼叫的機制。 如需詳細資訊，請參閱 [從 JavaScript 使用 CORS 的 API 應用程式](app-service-api-cors-consume-javascript.md)。 
- **觸發程序** -一種 REST API， [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md) 可以呼叫它以符合特定條件時初始化工作流程處理序。 例如，API 應用程式可以提供邏輯應用程式定期呼叫的方法，以在 Twitter 摘要中尋找特定詞組。 如需詳細資訊，請參閱 [API 應用程式觸發程序](app-service-api-dotnet-triggers.md)。
- **動作** -一種 REST API， [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md) 工作流程已由觸發程序啟動之後，可以呼叫來處理資料。 例如，API 應用程式可以提供一種方法，供邏輯應用程式呼叫，以回應 Twitter 觸發程序找到的推文。 動作是由 Swagger API 定義公開的 API 方法。

## 開始使用

若要開始使用 API 應用程式，請遵循 [開始使用 API 應用程式](app-service-api-dotnet-get-started.md) 教學課程。

若要查看 API 應用程式的已知問題的清單，請參閱 [的 API 應用程式已知問題的論壇文章](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps)。

如需 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。


