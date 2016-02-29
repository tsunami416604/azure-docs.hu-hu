<properties 
    pageTitle="在 Azure App Service 中的 .NET Web 應用程式使用 New Relic 應用程式效能管理" 
    description="了解如何在 Azure App Service 上執行的 ASP.NET 應用程式使用 New Relic 的效能監視。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="07/30/2015" 
    ms.author="stepsic"/>



# 在 Azure App Service 中的 .NET Web 應用程式使用 New Relic 應用程式效能管理

本指南說明如何將 New Relic 的世界級效能
監視 web 應用程式中加入 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)。 我們將討論以快速又簡單的
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

New Relic Standard 供 Azure 使用者免費使用。
根據您所使用的網站模式及執行個體大小 (如果使用保留模式)，我們提供了多種 New Relic Pro 套件。

如需定價資訊，請參閱 [Azure Marketplace 的 New Relic 頁面](/marketplace/partners/newrelic/newrelic)。

> [AZURE.NOTE] 最多 10 個運算執行個體只列出定價。 若超過 10 個，請連絡 New Relic (sales@newrelic.com) 詢問批量價格。

Azure 客戶部署 New Relic 代理程式時享有 New Relic Pro 試用訂閱 2 週。

註冊使用 Azure Marketplace 的 New Relic
--

New Relic 與 Azure Web 角色、背景工作角色和 Azure App Service 緊密整合。

若要直接從 Azure Marketplace 市集註冊 New Relic，請依照下列四個簡單的步驟。

## 步驟 1. 建立 New Relic 帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com) 按一下 **新增** 角。
3. 按一下 [ **開發人員服務** > **New Relic APM**。
4. 指定下列命令，來設定 New Relic 帳戶，然後按一下 **建立**。
    - **名稱**
    - **定價層**
    - **資源群組**
    - **訂用帳戶**
    - **位置**
    - **法律條款**

11. 按一下之後 **建立**, ，New Relic 帳戶就會開始建立程序。 您可以監視的狀態，藉以 **通知** ] 按鈕。 建立之後，隨即會顯示 New Relic 帳戶的刀鋒視窗。

12. 若要擷取 New Relic 授權金鑰，看看 **Essentials** 刀鋒視窗的上方面板。 當您將 Web 應用程式與 New Relic 帳戶整合時，您的 Web 應用程式執行個體將在其應用程式設定中自動註冊此授權金鑰。

## 步驟 2：設定 Web 應用程式的 New Relic 整合

1. 開啟 web 應用程式的刀鋒視窗中 [Azure 入口網站](https://portal.azure.com)。
2. 按一下"..." 在分頁，然後選取頂端的功能表 **新增磚**。
3. 在 **監視** ] 索引標籤選取 **應用程式摘要** 並將它拖曳到想要顯示在 web 應用程式的刀鋒視窗上的磚。
4. 按一下 [完成] 以完成加入磚。
5. 按一下 [ **應用程式監視** 磚，然後選取 **New Relic**。
6. 選取您在上一個步驟中建立的帳戶，然後按一下 [ **確定**。 

    ![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

    一旦儲存操作完成後，按一下 [ **所有設定** 在 web 應用程式的刀鋒視窗中，然後按一下 [ **應用程式設定**。 您應該會看到 **NEWRELIC\_LICENSEKEY** 設定新增至 **應用程式設定** 刀鋒視窗，以支援 New Relic 的區段:

    >[AZURE.NOTE] 可能需要長達 30 秒，新的應用程式設定才會生效。 若要強制設定立即生效，請重新啟動 Web 應用程式。

## 步驟 3：發佈 ASP.NET Web 應用程式

使用 Visual Studio 來發佈 Web 應用程式。 如果您先前已發佈 Web 應用程式，再次發佈以便 Web 應用程式執行個體新增必要的 New Relic NuGet 封裝以啟用 New Relic 監視。

## 步驟 4. 在 New Relic 中檢查應用程式的效能。

檢視 New Relic 儀表板：

2. 開啟 web 應用程式的刀鋒視窗中 [Azure 入口網站](https://portal.azure.com)。
3. 按一下 [ **應用程式監視** > **應用程式名稱** > **在 New Relic 檢視**。

    ![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. 如果這是您首次使用帳戶，請設定您的帳戶資訊。
3. 從 New Relic 功能表列中，選取 **應用程式 > (應用程式的名稱)**。

     **監視 > 概觀** 儀表板] 會自動出現。

    ![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

    在選取清單中的應用程式之後您 **應用程式** ] 功能表上， **概觀** 儀表板會顯示目前的應用程式伺服器和瀏覽器資訊。

### <a id="using-new-relic"></a>使用 New Relic

從 [Applications] 功能表的清單中選取應用程式之後，[Overview] 儀表板會顯示目前的應用程式伺服器和瀏覽器資訊。 若要將兩個檢視之間切換，請按一下 [ **應用程式伺服器** 或 **瀏覽器** ] 按鈕。

除了 <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">標準 New Relic UI</a> 和 <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">儀表板向下鑽取</a> 功能以外，[應用程式概觀] 儀表板還有其他功能。

<table border="1">
  <thead>
    <tr>
      <th><b>如果您想要...</b></th>
      <th><b>執行此動作...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>顯示所選取應用程式的伺服器或瀏覽器的儀表板資訊</td>
       <td>按一下 <b>[應用程式伺服器]</b> 或 <b>[瀏覽器]</b> 按鈕。</td>
    </tr>
     <tr>
       <td>檢視應用程式的臨界值等級 <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> 分數</td>
       <td>指向 Apdex 分數 <b>?<b> 圖示。</b></b></td>
    </tr>
    <tr>
       <td>檢視全球 Apdex 詳細資料</td>
       <td>從 [概觀] 的 <b>[瀏覽器]</b> 檢視中，指向 Global Apdex 地圖上。<br /><b>秘訣：</b> 若要直接移至選取的應用程式 <a href="https://newrelic.com/docs/site/geography" target="_blank">[地理位置]</a>儀表板，請按一下 <b>[全球 Apdex]</b> 標題，或按一下 [全球 Apdex] 地圖上的任一處。</td>
    </tr>
    <tr>
       <td>檢視 <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">[Web 交易]</a> 儀表板</td>
       <td>按一下 [Applications Overview] 儀表板的 [Web Transactions] 資料表。 或者，若要檢視特定 Web 交易 (包括 <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>) 的詳細資料，請按一下其名稱。</td>
    </tr>
    <tr>
       <td>檢視 <a href="https://newrelic.com/docs/site/errors" target="_blank">錯誤數</a> 儀表板</td>
       <td>按一下 [Error rate] 圖表 & #39; s 應用程式概觀] 儀表板的標題。<br /><b>秘訣：</b> 您可以從 <b>應用程式</b> & gt;(您的應用程式) (& s) gt;事件與 gt;發生錯誤。</td>
    </tr>
    <tr>
       <td>檢視應用程式的伺服器詳細資料</td>
       <td><p>執行下列任何動作：<p>
        <ul>
          <li>在主機的資料表檢視或每個主機的分組度量詳細資料之間切換。</li>
          <li>按一下個別伺服器的名稱。</li>
          <li>指向個別伺服器的 Apdex 分數。</li>
          <li>按一下個別伺服器的 CPU 使用率或記憶體。</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

以下是選取 [Browser] 檢視時的 [Applications Overview] 儀表板範例。

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## 後續步驟

如需詳細資訊，請參閱下列其他資源：

 * [為 Azure 網站安裝.NET 代理程式](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-websites#manual): New Relic.NET 代理程式安裝程序 
 * [New Relic 使用者介面](https://newrelic.com/docs/site/the-new-relic-ui): 
New Relic UI,、設定使用者權限和設定檔，以及使用標準功能和儀表板向下鑽研詳細資料的概觀
 * [應用程式概觀](https://newrelic.com/docs/site/applications-overview): 特性及功能時使用 New Relic 的應用程式概觀儀表板
 * [Apdex](https://newrelic.com/docs/site/apdex): Apdex 如何衡量應用程式的使用者滿意度的概觀
 * [實際使用者監控](https://newrelic.com/docs/features/real-user-monitoring): 概觀 RUM 如何詳細資料時所需為您的使用者 
使用者來自何處，和使用者使用什麼瀏覽器的概觀
 * [尋找說明](https://newrelic.com/docs/site/finding-help): New Relic 線上說明中心提供的資源

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)


[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
 
