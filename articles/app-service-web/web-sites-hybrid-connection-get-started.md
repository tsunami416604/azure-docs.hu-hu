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


# 在 Azure App Service 中使用混合式連線存取內部部署資源

您可以在 Azure App Service 上將 Web 應用程式連接到使用靜態 TCP 連接埠的任何內部部署資源，例如 SQL Server、MySQL、HTTP Web API、行動服務及最高程度的自訂 Web 服務。 本文示範如何在 App Service 中的 Web 應用程式和內部部署的 SQL Server 資料庫之間建立混合式連線。
> [AZURE.NOTE] 混合式連線功能的 Web 應用程式部分是僅適用於 [Azure 入口網站](https://portal.azure.com)。 若要在 BizTalk 服務中建立的連線，請參閱 [混合式連線](http://go.microsoft.com/fwlink/p/?LinkID=397274)。  

## 必要條件

- Azure 訂用帳戶。 如需免費訂閱，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

- 若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。 建議在 SQL Server 上使用預設執行個體，因為其使用靜態連接埠 1433。 如需安裝及設定 SQL Server Express 以搭配混合式連線使用的資訊，請參閱 [從 Azure 網站使用混合式連線連接至內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 本文稍後將會針對安裝內部部署混合式連線管理員代理程式的電腦加以說明：

    - 必須能夠透過連接埠 5671 連線到 Azure
    - 必須能夠連繫內部部署資源的 *hostname*:*portnumber*。

> [AZURE.NOTE] 本文中的步驟假設您使用將主控內部部署混合式連線代理程式之電腦中的瀏覽器。


## 在 Azure 入口網站中建立 Web 應用程式

> [AZURE.NOTE] 如果您已經建立的 web 應用程式在 Azure 入口網站中您要用於此教學課程，您可以直接跳到 [建立混合式連線和 BizTalk 服務](#CreateHC) 並從那裡開始。

1. 中的左上角 [Azure 入口網站](https://portal.azure.com), ，按一下 [ **新增** > **Web + 行動** > **Web 應用程式**。

    ![新 Web 應用程式][newwebsite]

2. 在 [Web 應用程式]**** 刀鋒視窗上，提供 URL，然後按一下 [建立]****。

    ![Website name][websitecreationblade]

3. 經過一段時間之後，Web 應用程式會建立，並顯示它的 Web 應用程式分頁。 此分頁是垂直捲動的儀表板，可供您管理網站。

    ![Website running][websiterunningblade]

4. 若要確認網站是否已上線啟用，您可以按一下 [瀏覽]**** 圖示以顯示預設頁面。

    ![按一下 ][browse]

    ![預設 Web 應用程式頁面][defaultwebsitepage]

接著，您將為 Web 應用程式建立混合式連線和 BizTalk 服務。

<a name="CreateHC"></a>
## 建立混合式連線和 BizTalk 服務

1. 在您的 [Web 應用程式] 刀鋒視窗中按一下 [所有設定]**** > [網路]**** > [設定混合式連接端點]****。

    ![混合式連線][createhchcicon]

2. 在 [混合式連線] 分頁上，按一下 [新增]****。


3. [新增混合式連線]**** 分頁隨即開啟。 由於這是您的第一個混合式連線，因此會預先選取 [新增混合式連線]**** 選項，並為您開啟 [建立混合式連線]**** 分頁。

    ![Create a hybrid connection][twincreatehcblades]

    在 [建立混合式連線分頁]**** 上：
    - 在 [名稱]**** 中，提供連線的名稱。
    - 在 [主機名稱]**** 中，輸入主控資源的內部部署電腦名稱。
    - 在 [連接埠]**** 中，輸入內部部署資源使用的連接埠號碼 (若是 SQL Server 預設執行個體，請輸入 1433)。
    - 按一下 [Biz Talk 服務]****

4. [建立 BizTalk 服務]**** 分頁隨即開啟。 輸入 BizTalk 服務的名稱，然後按一下 [確定]****。

    ![Create BizTalk service][createhccreatebts]

    [建立 BizTalk 服務]**** 分頁隨即關閉，而您會回到 [建立混合式連線]**** 分頁。

5. 在 [建立混合式連線] 分頁上，按一下 [確定]****。

    ![Click OK][createbtscomplete]

6. 當程序完成時，入口網站中的通知區域會通知您已成功建立連線。

7. 在 Web 應用程式的刀鋒視窗上，[混合式連線]**** 圖示現在會顯示已建立 1 個混合式連線。

    ![One hybrid connection created][createhconeconnectioncreated]

至此，您已完成雲端混合式連線基礎結構的重要部分。 接下來，您將建立對應的內部部署部分。

<a name="InstallHCM"></a>
## 安裝內部部署混合式連線管理員以完成連線

1. 在 [Web 應用程式] 刀鋒視窗中按一下 [所有設定]**** > [網路]**** > [設定混合式連接端點]****。

    ![Hybrid connections icon][hcicon]

2. 在 [混合式連線]**** 分頁上，最近新增之端點的 [狀態]**** 欄會顯示為 [未連線]****。 請按一下連線加以設定。

    ![Not connected][notconnected]

    [混合式連線] 分頁隨即開啟。

    ![NotConnectedBlade][notconnectedblade]

3. 在此分頁上，按一下 [Listener Setup]****。

    ![Click Listener Setup][clicklistenersetup]

4. [混合式連線屬性]**** 分頁隨即開啟。 在 [內部部署混合式連線管理員]**** 下，選擇 [按一下此處進行安裝]****。

    ![Click here to install][clicktoinstallhcm]

5. 在 [應用程式執行] 安全性警告對話方塊中，選擇 [執行]**** 以繼續作業。

    ![Choose Run to continue][applicationrunwarning]

6.  在 [使用者帳戶控制]**** 對話方塊中，選擇 [是]****。

    ![Choose Yes][uac]

7. 系統會為您下載並安裝混合式連線管理員。

    ![安裝][hcminstalling]

8. 安裝完成後，請按一下 [關閉]****。

    ![Click Close][hcminstallcomplete]

    在 [混合式連線]**** 分頁上，[狀態]**** 欄此時會顯示為 [未連線]****。

    ![Connected Status][hcstatusconnected]

現在，您已完成混合式連線基礎結構，您可以使用它來建立混合式應用程式。
>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

<a name="NextSteps"></a>
## 後續步驟

- 如需建立使用混合式連線的 ASP.NET web 應用程式的詳細資訊，請參閱 [從 Azure 網站使用混合式連線連接至內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 如需行動服務中使用混合式連線，請參閱 [從 Azure 行動服務使用混合式連線連接至內部部署 SQL Server](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)。

### 其他資源

[混合式連線概觀](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist 介紹混合式連線 (第 9 頻道視訊)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合式連線網站](http://azure.microsoft.com/services/biztalk-services/)

[BizTalk 服務: 儀表板、 監視器、 調整、 設定和混合式連線索引標籤](../biztalk-dashboard-monitor-scale-tabs/)

[建置絕佳的應用程式可攜性 (第 9 頻道視訊) 與實際的混合式雲端](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[連接到內部部署 SQL Server，從 Azure 行動服務使用混合式連線 (第 9 頻道視訊)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)



[new]: ./media/web-sites-hybrid-connection-get-started/B01New.png 
[newwebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png 
[websitecreationblade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png 
[websiterunningblade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png 
[browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png 
[defaultwebsitepage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png 
[createhchcicon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png 
[createhcaddhc]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png 
[twincreatehcblades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png 
[createhccreatebts]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png 
[createbtscomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png 
[createhcsuccessnotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png 
[createhconeconnectioncreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png 
[hcicon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png 
[notconnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png 
[notconnectedblade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png 
[clicklistenersetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png 
[clicktoinstallhcm]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png 
[applicationrunwarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png 
[uac]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png 
[hcminstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png 
[hcminstallcomplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png 
[hcstatusconnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png 

