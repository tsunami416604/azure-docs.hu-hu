<properties 
    pageTitle="將 New Relic 與 Azure 搭配使用 | Microsoft Azure" 
    description="了解如何使用 New Relic 服務來管理與監控 Azure 應用程式。" 
    services="" 
    documentationCenter=".net" 
    authors="nickfloyd" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="03/16/2015" 
    ms.author="nickfloyd@newrelic.com"/>



# Azure 上的 New Relic 應用程式效能管理

本指南說明如何將 New Relic 的世界級效能
監視加入 Azure 託管的應用程式。 我們將討論以快速又簡單的
程序將 New Relic 加入至應用程式，並介紹一些
New Relic 功能。 如需有關使用 New Relic 的詳細資訊，請參閱 [使用 New Relic](#using-new-relic)。

## 什麼是 New Relic？

New Relic 是監視生產應用程式的開發人員導向工具
並提供其效能和可靠性的深入分析。 當您 [顯示選項] 並檢視所選取的憑證時，它
是設計用來快速識別並診斷效能問題，並
提供輕鬆解決這些問題所需的效能。

New Relic 會追蹤 Web 交易的載入時間和輸送量，包括來自
伺服器和使用者瀏覽器。 它會顯示您花多少時間在
資料庫上、分析緩慢查詢和 Web 要求、提供正常運作時間監視和
警示、追蹤應用程式例外狀況等等。

## 透過 Azure 市集享受 New Relic 優惠價格


New Relic Standard 供 Azure 使用者免費使用
New Relic Pro 是依據 Azure 雲端服務的執行個體大小提供。

如需定價資訊，請參閱 [Azure 市集的 New Relic 頁面](http://azure.microsoft.com/marketplace/partners/newrelic/newrelic)。

> [AZURE.NOTE] 最多 10 個運算執行個體只列出定價。 若超過 10 個，請連絡 New Relic (sales@newrelic.com) 詢問批量價格。

Azure 客戶部署 New Relic 代理程式時享有 New Relic Pro 試用訂閱 2 週。

## 使用 Azure 市集註冊 New Relic

New Relic 與 Azure Web 角色和背景工作角色緊密整合。

若要直接從 Azure 市集註冊 New Relic，請依照下列三個簡單的步驟。

### 步驟 1. 透過 Azure 市集註冊

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在管理入口網站的下方窗格中，按一下 [ **新增**。
3. 按一下 [ **存放區**。
4. 在 **選擇附加元件** 對話方塊中，選取 **New Relic** 按一下 **下一步**。
5. 在 **個人化附加元件** ] 對話方塊中，選取您想要的 New Relic 方案。
6. 輸入促銷代碼 (若有的話)。
7. 輸入要在 Azure 設定中顯示的 New Relic 服務名稱，
   設定或使用預設值 **NewRelic**。 這個名稱
   在您訂閱的 Azure 市集項目清單必須是唯一的。
8. 選擇地區值例如， **美國西部**。
9. 按一下 [ **下一步**。
10. 在 **檢閱購買** ] 對話方塊中，檢閱方案和價格資訊，
    並檢閱法律條款。 如果您同意條款，請按一下 [ **購買**。
11. 按一下之後 **購買**, ，New Relic 帳戶就會開始建立程序。 您可以在 Azure 管理入口網站中監視狀態。
12. 若要擷取 New Relic 授權金鑰，請按一下 [ **輸出值**。 
13. 複製出現的授權金鑰。 安裝 New Relic Nuget 套件時需要輸入此金鑰。

### 步驟 2. 安裝 Nuget 封裝

1. 開啟 Visual Studio 方案，或選取 [檔案] > [新增] >
   **檔案 > 新增 > 專案**。

    ![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. 如果您還沒有 Azure 雲端服務專案程式
   方案中，以滑鼠右鍵按一下 [方案總管] 中的應用程式和
   選取 **加入 Azure 雲端服務專案**。

    ![Create cloud service](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. 選取以開啟 [Package Manager console **工具 > Library Package Manager] >
   封裝管理員主控台**。 在 [NuGet 封裝管理員主控台] 視窗的頂端中，
   將您的專案設定為預設專案。

    ![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. 在 [封裝管理員命令提示字元中輸入 `Install-Package
   NewRelicWindowsAzure` 按 **Enter**。

    ![install in package manager](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. 在授權金鑰提示上，輸入您從 Azure 市集取得的授權金鑰。

    ![enter license key](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. 選擇性: 在應用程式名稱提示中，輸入您的應用程式名稱，它將
   顯示的應用程式名稱。 或者，使用方案名稱做為預設值。

    ![enter application name](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. 從 [方案總管] 中，以滑鼠右鍵按一下 Azure 雲端服務專案，然後選取 **發行**。

    ![public the cloud project](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**注意:** 如果這是您第一次將此應用程式部署至 Azure，系統會提示您輸入您 
Azure 認證。 如需詳細資訊，請參閱 [ASP.NET Web 應用程式部署至 Azure 網站](app-service-web\web-sites-dotnet-get-started.md)

![publish settings](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### 步驟 3. 在 New Relic 中檢查應用程式的效能。

檢視 New Relic 儀表板：

1. 從 Azure 入口網站中，按一下 [ **管理** ] 按鈕。
2. 以您的 New Relic 帳戶電子郵件和密碼登入。
3. 從 New Relic 功能表列中，選取 **應用程式 > (應用程式的名稱)**。

     **監視 > 概觀** 儀表板] 會自動出現。

    ![New Relic monitoring dashboard](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

    在選取清單中的應用程式之後您 **應用程式** ] 功能表上， **概觀** 儀表板會顯示目前的應用程式伺服器和瀏覽器資訊。

### <a id="using-new-relic"></a>使用 New Relic

從 [Applications] 功能表的清單中選取應用程式之後，[Overview] 儀表板會顯示目前的應用程式伺服器和瀏覽器資訊。 若要將兩個檢視之間切換，請按一下 [ **應用程式伺服器** 或 **瀏覽器** ] 按鈕。

除了[標準 New Relic UI](https://newrelic.com/docs/site/the-new-relic-ui#functions")和[儀表板向下鑽研](https://newrelic.com/docs/site/the-new-relic-ui#drilldown)功能，[Applications Overview] 儀表板還有其他功能。

| 如果您想要... | 執行此動作... |
| ----------------- | ---------- |
| 顯示所選取應用程式的伺服器或瀏覽器的儀表板資訊。 | 按一下 [ **應用程式伺服器** 或 **瀏覽器** ] 按鈕。 |
| 檢視您的應用程式的臨界值層級 [Apdex](https://newrelic.com/docs/site/apdex) 分數。 | 指向 Apdex 分數 **?** 圖示。 |
| 檢視全球 Apdex 詳細資料。 | 從 [Overview] 的 **瀏覽器** 檢視中，指向 Global Apdex 地圖上。 **提示:** ，直接移至選取的應用程式 [Geography](https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard") 儀表板，按一下 [ **Global Apdex** 標題，或按一下 Global Apdex 地圖的任意處。 |
| 檢視 [Web 交易](https://newrelic.com/docs/applications-dashboards/web-transactions) 儀表板。 | 按一下 [Applications Overview] 儀表板的 [Web Transactions] 資料表。 或者，若要檢視特定 web 交易的詳細資料 (包括 [Key Transactions](https://newrelic.com/docs/site/key-transactions"), ，按一下其名稱。 |
| 檢視 [錯誤](https://newrelic.com/docs/site/errors) 儀表板。 | 按一下 [Applications Overview] 儀表板的 [Error rate] 圖表標題。 **提示:** 您也可以檢視從 **應用程式** > (您的應用程式) > 事件 > 錯誤。 |


此外，如果您想要檢視應用程式的伺服器詳細資料，請執行下列任何動作：

- 在主機的資料表檢視或每個主機的分組度量詳細資料之間切換。
- 按一下個別伺服器的名稱。
- 指向個別伺服器的 Apdex 分數。
- 按一下個別伺服器的 CPU 使用率或記憶體。

以下是選取 [Browser] 檢視時的 [Applications Overview] 儀表板範例。

![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## 後續步驟

如需詳細資訊，請參閱下列其他資源：

 * [在 Azure 上安裝.NET 代理程式](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): New Relic.NET 代理程式安裝程序 
 * [New Relic 使用者介面](https://newrelic.com/docs/site/the-new-relic-ui): 
New Relic UI,、設定使用者權限和設定檔，以及使用標準功能和儀表板向下鑽研詳細資料的概觀
 * [應用程式概觀](https://newrelic.com/docs/site/applications-overview): 特性及功能時使用 New Relic 的應用程式概觀儀表板
 * [Apdex](https://newrelic.com/docs/site/apdex): Apdex 如何衡量應用程式的使用者滿意度的概觀
 * [實際使用者監控](https://newrelic.com/docs/features/real-user-monitoring): 概觀 RUM 如何詳細資料時所需為您的使用者 
使用者來自何處，和使用者使用什麼瀏覽器的概觀
 * [尋找說明](https://newrelic.com/docs/site/finding-help): New Relic 線上說明中心提供的資源

