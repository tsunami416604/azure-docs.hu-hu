<properties 
    pageTitle="什麼是 Logic Apps？" 
    description="深入了解應用程式服務 Logic Apps" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2015"
    ms.author="klam"/>

#什麼是 Logic Apps？

| 快速參考 |
| --------------- |
| [Logic Apps 定義語言](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Logic Apps 連接器文件](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Azure App Service 是專為專業開發人員提供的完全受管理的 PaaS (平台即服務)，能夠輕鬆地建置 Web、行動和整合應用程式。 Logic Apps 是此套件的一部分，可讓任何技術使用者或開發人員透過容易使用的視覺化設計工具，將商務程序執行和工作流程自動化。

最棒的邏輯應用程式可以結合 [連接器][connectors] 從 Marketplace 幫助您輕鬆地解決棘手的整合案例。

![流程應用程式設計工具](./media/app-service-logic-what-are-logic-apps/Designer.png)

如果您想要自動複寫 SQL DB 中的新記錄和郵件前臺，或自動尋找負評推文並將它們傳送至 slack 通道 

##為什麼要使用 Logic Apps？

Logic Apps 可讓開發人員設計從觸發程序啟動的工作流程，然後執行一系列的步驟，每個步驟都會叫用 App Service API 應用程式，同時安全地處理驗證和最佳作法，如檢查點和長期執行的工作流程。

如果您要自動執行任何商業程序 (例如尋找負評推文並張貼至內部 Slack 通道或從 SQL 將送達的新客戶記錄複寫道 CRM 系統中)，Logic Apps 可讓您輕鬆整合完全不同的資料來源 (從雲端至內部部署)。 請查看我們 [連接器][connectors] 更多靈感和 [開始][create] 現在來看看您做。 

什麼是多，我們 [BizTalk API 應用程式][biztalk] 可擴充至成熟的整合案例的威力與 [規則引擎][rules], ，[交易夥伴管理][tpm] 等等。

- **容易使用的設計工具** -邏輯應用程式可設計的端對端瀏覽器中。 從觸發程序啟動 - 從簡單的排程，到每次有關於貴公司的推文出現時。 然後，協調任何數量的動作，使用豐富的連接器組件庫。

- **輕鬆撰寫 SaaS** -即使撰寫工作，可輕鬆說明很難在程式碼中實作。 Logic Apps 可讓連接不同的系統變成簡單的作業。 想要在 CRM 軟體中建立以 Facebook 或 Twitter 帳戶中的活動為基礎的工作嗎？ 想要將您的雲端行銷解決方案連接到內部部署計費系統嗎？ 邏輯應用程式可用最快、最可靠方式，提供這些問題的解決方案。

- **從範本快速開始** -為了協助您開始，我們提供了 [範本庫][templates] 可讓您快速建立一些常見的解決方案。 從進階 BizTalk 解決方案到簡單的 SaaS 連線 (甚至只為了「好玩」) - 資源庫是了解Logic Apps 強大功能的最快方式。

- **擴充現成可用的** -看不到您所需要的連接器嗎？ Logic Apps 是應用程式服務套件的一部分，依設計可與 API 應用程式搭配使用；您可以輕鬆地建立您自己的 API 應用程式，以做為連接器。 建置您專屬的新應用程式，或在 Marketplace 中共用及銷售。

- **真正的整合馬力** -入門容易，並依需求擴充。 Logic Apps 可輕易地讓 Microsoft 領先業界的整合解決方案 BizTalk 發揮效益，使整合專業人員得以建置其所需的解決方案。 深入了解 [透過應用程式服務提供的 BizTalk 功能][biztalk]。

## 邏輯應用程式概念

以下是構成 Logic Apps 使用性的一些重要元素。 

- **工作流程** -邏輯應用程式提供圖形化的方式來建立商務程序為一系列的步驟或工作流程的模型。
- **連接器** -邏輯應用程式需要存取資料和服務。 連接器是特殊類型的 API 應用程式。 它是為了讓協助您連接及使用資料而特別設計的。 請參閱連接器中目前提供的清單 [使用連接器][connectors]。
- **觸發程序** -某些連接器也可以做為觸發程序。 觸發程序會根據特定的事件建立新的工作流程執行個體，例如在電子郵件送達或您的 Azure 儲存體帳戶有所變更時。
-  **動作** -每個工作流程中的觸發程序呼叫動作之後的步驟。 每個動作通常會對應至連接器或自訂 API 應用程式上的作業。
- **BizTalk** -更進階的整合案例，Azure 應用程式服務包含來自 Biztalk 的功能。 Biztalk 是 Microsoft 領先業界的整合平台。 BizTalk API 應用程式可讓您輕鬆地將驗證、轉換、規則等項目納入邏輯應用程式工作流程中。 進一步了解 [什麼是 BizTalk API 應用程式][biztalk]。

## 開始使用

若要開始使用邏輯應用程式，請遵循 [建立邏輯應用程式][create] 教學課程。

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][appservice]。

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[connectors]: app-service-logic-connectors-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md
 


