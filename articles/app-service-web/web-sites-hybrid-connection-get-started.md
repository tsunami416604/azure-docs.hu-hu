<properties 
    pageTitle="在 Azure App Service 中使用混合式連線存取內部部署資源" 
    description="在 Azure App Service 中的 Web 應用程式和使用靜態 TCP 連接埠的內部部署資源之間建立連線" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/24/2015" 
    ms.author="cephalin"/>

#在 Azure App Service 中使用混合式連線存取內部部署資源

您可以在 Azure App Service 上將 Web 應用程式連接到使用靜態 TCP 連接埠的任何內部部署資源，例如 SQL Server、MySQL、HTTP Web API、行動服務及最高程度的自訂 Web 服務。 本文示範如何在 App Service 中的 Web 應用程式和內部部署的 SQL Server 資料庫之間建立混合式連線。

> [AZURE.NOTE] 混合式連線功能的 Web 應用程式部分是僅適用於 [Azure 入口網站](https://portal.azure.com)。 若要在 BizTalk 服務中建立的連線，請參閱 [混合式連線](http://go.microsoft.com/fwlink/p/?LinkID=397274)。  

## 必要條件
- Azure 訂用帳戶。 如需免費訂閱，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。 

- 若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。 建議在 SQL Server 上使用預設執行個體，因為其使用靜態連接埠 1433。 如需安裝及設定 SQL Server Express 以搭配混合式連線使用的資訊，請參閱 [從 Azure 網站使用混合式連線連接至內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 本文稍後將會針對安裝內部部署混合式連線管理員代理程式的電腦加以說明：

    - 必須能夠透過連接埠 5671 連線到 Azure
    - 必須能夠連 *hostname*:*portnumber* 在內部部署資源。 

> [AZURE.NOTE] 這篇文章中的步驟假設您使用將主控內部部署混合式連線代理程式的電腦中的瀏覽器。


## 在 Azure 入口網站中建立 Web 應用程式 ##

> [AZURE.NOTE] 如果您已經建立的 web 應用程式在 Azure 入口網站中您要用於此教學課程，您可以直接跳到 [建立混合式連線和 BizTalk 服務](#CreateHC) 並從那裡開始。

1. 中的左上角 [Azure 入口網站](https://portal.azure.com), ，按一下 [ **新增** > **Web + 行動** > **Web 應用程式**。
    
    ![新的 web 應用程式][新網站]
    
2. 在 **Web 應用程式** 刀鋒視窗中，提供 URL，然後按一下 [ **建立**。 
    
    ![網站名稱][] WebsiteCreationBlade
    
3. 經過一段時間之後，Web 應用程式會建立，並顯示它的 Web 應用程式分頁。 此分頁是垂直捲動的儀表板，可供您管理網站。
    
    !執行 [網站][] WebSiteRunningBlade
    
4. 若要確認網站是否已上線啟用，您可以按一下 **瀏覽** 圖示以顯示預設頁面。
    
    ![按一下以查看您的 web 應用程式的瀏覽][瀏覽]
    
    ![預設 web 應用程式頁面][] DefaultWebSitePage
    
接著，您將為 Web 應用程式建立混合式連線和 BizTalk 服務。

<a name="CreateHC"></a>
## 建立混合式連線和 BizTalk 服務 ##

1. 在您的 web 應用程式刀鋒視窗按一下 **所有設定** > **網路** > **設定混合式連接端點**。
    
    ![混合式連線][] CreateHCHCIcon
    
2. 在 [混合式連線] 分頁中，按一下 [ **新增**。
    
    <!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
    
3.  **新增混合式連線** 分頁隨即開啟。  由於這是您第一次的混合式連線， **新增混合式連線** 已預先選取選項，而 **建立混合式連線** 刀鋒視窗會開啟。
    
    ![建立混合式連線][] TwinCreateHCBlades
    
    在 **建立混合式連線分頁]**:
    - 如 **名稱**, ，提供連線的名稱。
    - 如 **Hostname**, ，輸入主控資源的內部部署電腦的名稱。
    - 如 **連接埠**, ，輸入您的內部部署資源使用 (SQL Server 預設執行個體 1433) 連接埠號碼。
    - 按一下 [ **Biz Talk 服務]**


4.  **建立 BizTalk 服務** 分頁隨即開啟。 輸入 BizTalk 服務的名稱，然後按一下 **確定**。
    
    ![建立 BizTalk 服務][] CreateHCCreateBTS
    
     **建立 BizTalk 服務** 分頁隨即關閉，而您會回到 **建立混合式連線** 刀鋒視窗。
    
5. 在 [建立混合式連線] 分頁中，按一下 [ **確定**。 
    
    ![按一下 [確定][] CreateBTScomplete
    
6. 當程序完成時，入口網站中的通知區域會通知您已成功建立連線。
    <!-TODO

    在這個步驟中，所有項目都會失敗。 我無法在狗食入口網站中建立 BizTalk 服務。 切換到傳統的入口網站
    (完整的入口網站) 並建立 BizTalk 服務，但似乎無法讓您連接它們 - 當您完成
    「建立混合式連線」步驟時，您看見下列錯誤：
    無法建立混合式連線 RelecIoudHC。 Auch die Eigenschaften 
    資源類型無法在 
    「適用於 API 版本 2014-06-01 的 Microsoft.BizTaIkServices」命名空間中找到。
    
    此錯誤表示找不到該類型，而不是該執行個體。
    ![成功通知][] CreateHCSuccessNotification
    -->
7. 在 web 應用程式的刀鋒視窗中， **混合式連線** 圖示現在會顯示已建立的 1 個混合式連線。
    
    ![建立一個混合式連線][] CreateHCOneConnectionCreated
    
至此，您已完成雲端混合式連線基礎結構的重要部分。 接下來，您將建立對應的內部部署部分。

<a name="InstallHCM"></a>
## 安裝內部部署混合式連線管理員以完成連線 ##

1. 在 web 應用程式的刀鋒視窗中，按一下 [ **所有設定** > **網路** > **設定混合式連接端點**。 
    
    ![混合式連線] 圖示][] HCIcon
    
2. 在 **混合式連線** 刀鋒視窗中， **狀態** ，最近新增之端點所示的資料行 **未連接**。 請按一下連線加以設定。
    
    ![未連線][] NotConnected
    
    [混合式連線] 分頁隨即開啟。
    
    ![] NotConnectedBlade[] NotConnectedBlade
    
3. 在刀鋒視窗中，按一下 [ **接聽程式設定**。
    
    ![按一下接聽程式設定][] ClickListenerSetup
    
4.  **混合式連線屬性** 分頁隨即開啟。 在 **內部部署混合式連線管理員**, ，選擇 [ **按這裡安裝**。
    
    ![按一下此處進行安裝][] ClickToInstallHCM
    
5. 在應用程式執行安全性警告對話方塊中，選擇 **執行** 以繼續。
    
    ![選擇要繼續執行][] ApplicationRunWarning
    
6.  在 **使用者帳戶控制** ] 對話方塊中，選擇 [ **是**。
    
    ![選擇 [是][UAC]
    
7. 系統會為您下載並安裝混合式連線管理員。 
    
    ![安裝][] HCMInstalling
    
8. 安裝完成後，按一下 [ **關閉**。
    
    ![按一下 [關閉][] HCMInstallComplete
    
    在 **混合式連線** 刀鋒視窗中， **狀態** 欄此時會顯示 **連線**。 
    
    ![連線的狀態][] HCStatusConnected

現在，您已完成混合式連線基礎結構，您可以使用它來建立混合式應用程式。 

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

<a name="NextSteps"></a>
## 後續步驟 ##

- 如需建立使用混合式連線的 ASP.NET web 應用程式的詳細資訊，請參閱 [從 Azure 網站使用混合式連線連接至內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 如需行動服務中使用混合式連線，請參閱 [從 Azure 行動服務使用混合式連線連接至內部部署 SQL Server](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)。

### 其他資源

[混合式連線概觀](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist 介紹混合式連線 (第 9 頻道視訊)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合式連線網站](http://azure.microsoft.com/services/biztalk-services/)

[BizTalk 服務：儀表板、監視、調整、設定和混合式連線索引標籤](../biztalk-dashboard-monitor-scale-tabs/)

[透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 
