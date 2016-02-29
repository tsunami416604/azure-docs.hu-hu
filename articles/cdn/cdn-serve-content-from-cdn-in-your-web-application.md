<properties 
    pageTitle="在 Web 應用程式中從 Azure CDN 提供內容" 
    description="指導如何使用 CDN 的內容提升 Web 應用程式效能的教學課程。" 
    services="cdn" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="cdn" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/08/2015" 
    ms.author="cephalin"/>

# 在 Web 應用程式中從 Azure CDN 提供內容 #

本教學課程說明如何利用 Azure CDN 來提升 Web 應用程式的存取性和效能。 在下列情況中，Azure CDN 有助於提升 Web 應用程式的效能：

- 您有許多連結指向頁面上的靜態或半靜態內容
- 您的應用程式由全球的用戶端存取
- 您想要卸載 Web 伺服器的流量
- 您想要減少 Web 伺服器的用戶端同時連線數目 (沒有在探討 [統合和縮製](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)) 
- 您想要增加可察覺的頁面載入/重新整理時間

## 學習目標 ##

在本教學課程中，您將了解如何執行下列工作：

-   [從 Azure CDN 端點提供靜態內容](#deploy)
-   [自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點](#upload)
-   [設定 CDN 快取來重新導向所需的內容更新](#update)
-   [使用查詢字串來立即提供最新內容](#query)

## 必要元件 ##

本教學課程有下列先決條件：

-   使用中 [Microsoft Azure 帳戶](/account/)。 您可以註冊試用帳戶
-   Visual Studio 2013 [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) 適用於 blob 管理 GUI
-   [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (由 [自動從 ASP.NET 應用程式至 CDN 端點內容上的傳](#upload))

> [AZURE.NOTE] 您需要 Azure 帳戶才能完成此教學課程:
> + 您可以 [免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F) -取得點數可用來試用付費 Azure 服務，並甚至用後可保留帳戶，並使用免費的 Azure 服務，例如網站。
> + 您可以 [啟用 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/) -您的 MSDN 訂閱提供您額度，您可以使用 Azure 付費服務的每個月。

<a name="static"></a>
## 從 Azure CDN 端點提供靜態內容 ##

在這一節的教學課程中，您將了解如何建立 CDN 並用它來提供靜態內容。 相關的主要步驟如下：

1. 建立儲存體帳戶
2. 建立連結至儲存體帳戶的 CDN
3. 在儲存體帳戶中建立 Blob 容器
4. 將內容上傳至 Blob 容器
5. 使用 CDN URL 連結至您上傳的內容

讓我們開始吧！ 遵循下列步驟來開始使用 Azure CDN：

1. 若要建立 CDN 端點，請登入您 [Azure 管理入口網站](http://manage.windowsazure.com/)。 
1. 按一下 [建立儲存體帳戶 **新增 > Data Services > 儲存體 > [快速建立**。 指定 URL、 位置，然後按一下 [ **建立儲存體帳戶**。 

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

    >[AZURE.NOTE] 請注意，我使用 [東亞] 作為區域，因為這地點夠遠讓測試我稍後從北美洲 CDN。

2. 當新儲存體帳戶的狀態為 **線上**, ，建立新的 CDN 端點會繫結至您建立的儲存體帳戶。 按一下 [ **新增 > 應用程式服務 > CDN > [快速建立**。 選取您建立的儲存體帳戶，然後按一下 **建立**。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

    >[AZURE.NOTE] 一旦建立 CDN 之後，Azure 入口網站會顯示其 URL 及它所繫結至原始網域。 不過，需要花費一些時間，CDN 端點的設定才能完全傳播至所有節點位置。  

3. 利用 Ping 來測試 CDN 端點，確定它已在線上。 如果 CDN 端點未傳播至所有節點，您會看到類似下列的訊息。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

    請多等一小時，再重新測試。 當 CDN 端點完成傳播至節點之後，就應該會回應 Ping。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

4. 此時，您已看出 CDN 端點決定哪裡是最靠近您的 CDN 節點。 從我的桌上型電腦，回應的 IP 位址是 **93.184.215.201**。 插入之類的網站 [www.ip-address.org](http://www.ip-address.org) 和伺服器位於華盛頓特區

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

    如需所有 CDN 節點位置的清單，請參閱 [Azure 內容傳遞網路 (CDN) 節點位置](http://msdn.microsoft.com/library/azure/gg680302.aspx)。

3. 回到 Azure 入口網站中，在 **CDN** 索引標籤上，按一下您剛才建立的 CDN 端點的名稱。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

3. 按一下 [ **啟用查詢字串** 啟用 Azure CDN 快取中的查詢字串。 啟用此選項後，將以個別項目來快取以不同查詢字串存取的相同連結。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

    >[AZURE.NOTE] 雖然啟用查詢字串並不需要此部分的教學課程，您會想要這樣做越好，但為了方便起見任何變更後這裡是長時間才能傳播至剩餘的節點，您不希望任何非字串-啟用查詢的內容塞滿 CDN 快取 (更新 CDN 內容將會稍後討論)。 您將了解如何利用這個在 [提供最新的內容，透過查詢字串來立即](#query)。

6. 在 Visual Studio 2013，在伺服器總管] 中，按一下 [ **連線到 Microsoft Azure** ] 按鈕。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

7.  依照提示來登入 Azure 帳戶。 
8.  一旦登入後，依序展開 **Microsoft Azure > 儲存體 > 儲存體帳戶**。 以滑鼠右鍵按一下 **Blob** ，然後選取 **Create Blob Container**。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

8.  指定 blob 容器名稱，然後按一下 **確定**。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

9.  在 [伺服器總管] 中，按兩下您建立的 Blob 容器來管理它。 您應該會在中央窗格中看到管理介面。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

10. 按一下 [ **上傳 Blob** 按鈕來上傳映像、 指令碼或樣式表，可供您的網頁至 blob 容器。 上傳進度會顯示在 **Azure 活動記錄檔**, ，並上傳的 blob 會出現在容器檢視。 

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

11. 您現在已上傳 Blob，必須將它們公開才能存取它們。 在 [伺服器總管] 中，以滑鼠右鍵按一下容器，然後選取 **屬性**。 設定 **公用讀取權限** 屬性 **Blob**。

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

12. 在瀏覽器中瀏覽至其中一個 Blob 的 URL，測試 Blob 的公用存取權。 例如，我可以使用 `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png` 測試已上傳清單中的第一個影像。

    請注意，我並未使用 Visual Studio 中的 Blob 管理介面所提供的 HTTPS 位址。 利用 HTTP 才能測試內容是否可公開存取，而這是 Azure CDN 的一項要求。

13. 如果您看到 Blob 適當地在瀏覽器中轉譯，請將 URL 從 `http://<yourStorageAccountName>.blob.core.windows.net` 變更為 Azure CDN 的 URL。 以我的情況來說，我會使用 `http://az623979.vo.msecnd.net/cdn/cephas_lin.png` 在 CDN 端點上測試第一個影像。

    >[AZURE.NOTE] 在 Azure 管理入口網站中，在 [CDN] 索引標籤中，您可以找到 CDN 端點的 URL。

    如果您比較直接 Blob 存取與 CDN 存取的效能，則可以發現使用 Azure CDN 的效能較高。 以下的 Internet Explorer 11 F12 開發人員工具螢幕擷取畫面是以 Blob URL 存取我的影像：

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

    以 CDN URL 存取同一張影像 

    ![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

    請注意的數字 **要求** 時間而定，也就是第一個位元組時間或傳送要求，並從伺服器接收第一個回應所花費的時間。 當我存取 Blob 時 (託管於 [東亞] 區域)，耗時 266 亳秒，因為要求必須橫渡整個太平洋才能到達伺服器。 不過，當我存取 Azure CDN 時，花了 16 毫秒，也就是幾乎 **20-fold 提升的效能**!
    
15. 現在，只要在網頁中使用新的連結即可。 例如，我可以加入下列影像標籤：

        <img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

在本節中，您已了解如何建立 CDN 端點、將內容上傳給它，以及從任何網頁連結至 CDN 內容。

<a name="upload"></a>
## 自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點 ##

如果您想要輕鬆地將上傳至 CDN 端點，ASP.NET Web 應用程式中的靜態內容的所有或如果您部署 Web 應用程式使用連續傳遞 (如需範例，請參閱 [Azure 中雲端服務的連續傳遞](../cloud-services/cloud-services-dotnet-continuous-delivery.md))，您可以使用 Azure PowerShell，在每次部署 Web 應用程式時，自動同步的最新的內容檔案至 Azure blob。 例如，您可以執行指令碼在 [將內容檔案上傳至 Azure Blob 的 ASP.NET 應用程式從](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a) 上傳 ASP.NET 應用程式中的所有內容檔案。 若要使用此指令碼：

4. 從 **開始** 功能表中，執行 **Microsoft Azure PowerShell**。
5. 在 Azure PowerShell 的視窗中，執行 `Get-AzurePublishSettingsFile`，為 Azure 帳戶下載發佈設定檔案。
6. 下載發行設定檔案之後，執行下列命令： 

        Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

    >[AZURE.NOTE] 當您匯入您發行設定檔，它就會成為預設 Azure 帳戶用於所有 Azure PowerShell 工作階段。 這表示以上的步驟只需要執行一次。
    
1. 下載的指令碼 [下載頁面](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a)。 將它儲存至 ASP.NET 應用程式的專案資料夾。
2. 下載的指令碼上按一下滑鼠右鍵，然後按一下 [ **屬性**。
3. 按一下 [ **解除封鎖**。
4. 開啟 PowerShell 視窗並執行下列命令：

        cd <ProjectFolder>
        .\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

此指令碼會從所有的檔案上傳您 *\Content* 和 *\Scripts* 資料夾，以指定儲存體帳戶和容器。 優點如下：

-   自動複寫 Visual Studio 專案的檔案結構
-   視需要自動建立 Blob 容器
-   多個 Web 應用程式可重複使用相同的 Azure 儲存體帳戶和 CDN 端點，各自在不同的 Blob 容器中
-   輕鬆地以新的內容更新 Azure CDN。 如需有關更新內容的詳細資訊，請參閱 [設定 CDN 快取來反映所需的內容更新](#update)。

針對 `-StorageContainer` 參數，必須使用 Web 應用程式名稱或 Visual Studio 的專案名稱。 我先前使用一般的 "cdn" 作為容器名稱，但使用 Web 應用程式的名稱可將相關內容組織在同一個很容易辨識的容器中。

一旦完成上傳內容，您可以將它連結到任何項目中您 *\Content* 和 *\Scripts* HTML 程式碼中，例如在.cshtml 檔案，使用資料夾 `http://<yourCDNName>.vo.msecnd.net/<containerName>`。 以下是我在 Razor 檢視中可使用某個項目的例子： 

    <img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

如需 PowerShell 指令碼整合至連續傳遞設定的範例，請參閱 [Azure 中雲端服務的連續傳遞](../cloud-services/cloud-services-dotnet-continuous-delivery.md)。 

<a name="update"></a>
## 設定 CDN 快取來重新導向所需的內容更新 ##

現在，假設您已從 Web 應用程式中將靜態檔案上傳至 Blob 容器，您變更專案的其中一個檔案，然後重新上傳至 Blob 容器。 您可能認為它會自動更新至 CDN 端點，但實際上在您存取內容的 CDN URL 時，卻無法理解為什麼沒有看到更新反映出來。 

其實 CDN 並不會真的自動從 Blob 儲存體來更新，而是這樣做時會對內容套用預設的 7 天快取規則。 這表示當 CDN 節點從 Blob 儲存體提取內容時，同樣的內容要等到在快取中過期時才會重新整理。

好消息是您可以自訂快取到期時間。 與大部分瀏覽器相似，Azure CDN 也遵守內容的 Cache-Control 標頭中指定的到期時間。 您可以在 Azure 入口網站中瀏覽至 Blob 容器，然後編輯 Blob 屬性來指定 Cache-Control 標頭值。 以下的螢幕擷取畫面顯示快取到期時間設為 1 小時 (3600 秒)。 

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

您也可以在 PowerShell 指令碼中這樣做，可設定所有 Blob 的 Cache-Control 標頭。 在指令碼 [自動從 ASP.NET 應用程式至 CDN 端點內容上的傳](#upload), ，尋找下列程式碼片段:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

修改如下：  

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

使用新的 Cache-Control 標頭時，您可能仍然需要等待 Azure CDN 上整整 7 天的快取內容到期之後，才能提取新的內容。 事實上，如果您希望內容更新立即生效，例如 JavaScript 或 CSS 更新，自訂快取值並沒有幫助。 不過，您可以透過查詢字串來重新命名檔案或控制檔案的版本設定，以解決此問題。 如需詳細資訊，請參閱 [提供最新的內容使用查詢字串來立即](#query)。

當然會有適合快取的天時與地利。 例如，您的內容可能不需要經常更新，例如即將開打的世界盃球賽 (可每 3 小時重新整理一次)，但會產生足夠的全球流量而需要從您自己的 Web 伺服器卸載。 這就是一個適合使用 Azure CDN 快取的最佳例子。

<a name="query"></a>
## 使用查詢字串來立即提供最新內容 ##

在 Azure CDN 中，您可以啟用查詢字串，以個別快取從 URL 中以特定查詢字串傳回的內容。 如果您想要立即將某些內容更新推送至用戶端瀏覽器，而不想等待快取的 CDN 內容到過，則這會是一個很實用的功能。 假設我在 URL 中加上版本號碼來發佈網頁。
  
    <link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0" rel="stylesheet"/>

當我發行 CSS 更新並在 CSS URL 中使用不同版本號碼時：  

    <link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1" rel="stylesheet"/>

已啟用查詢字串的 CDN 端點，這兩個 Url 彼此，而且將會對新的要求我的網頁伺服器來擷取新 *bootstrap.css*。 不需要啟用查詢字串的 CDN 端點，不過，這些就是相同的 URL，並會直接提供快取 *bootstrap.css*。 

竅門就在於自動更新版本號碼。 在 Visual Studio 中，要這樣做很簡單。 在我想使用上述連結的 .cshtml 檔案中，我可以根據組件號碼來指定版本號碼。  

    @{
        var cdnVersion = System.Reflection.Assembly.GetAssembly(
            typeof(MyMvcApp.Controllers.HomeController))
            .GetName().Version.ToString();
    }
    
    ...
    
    <link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=@cdnVersion" rel="stylesheet"/>

如果您在每次的發行週期中變更組件號碼，則同樣可確定每次發行 Web 應用程式時會取得唯一的版本號碼，而這在下次發行週期之前會保持不變。 或者，您可以讓 Visual Studio 每次建置 Web 應用程式開啟時就自動遞增組件版本號碼 *properties\ assemblyinfo.cs* 在 Visual Studio 專案中，並使用 `*` 中 `AssemblyVersion`。 例如：

    [assembly: AssemblyVersion("1.0.0.*")]

## 關於 ASP.NET 中統合的指令碼和樣式表 ##

使用 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 和 [Azure 雲端服務](/services/cloud-services/), ，取得與最佳的 Azure CDN 整合 [ASP.NET 統合和縮製](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)。 

Azure App Service 與 Azure CDN 或 Azure 雲端服務與 Azure CDN 整合將帶給您下列好處：

- 將內容部署 (影像、 指令碼和樣式表) 整合 Azure web 應用程式的 [連續部署](../web-sites-publish-source-control.md) 程序
- 輕鬆地升級 CDN 提供的 NuGet 套件，例如 jQuery 或 Bootstrap 版本 
- 從相同的 Visual Studio 介面來管理 Web 應用程式和 CDN 提供的內容

相關教學課程，請參閱：
- [在 Azure App Service 中使用 Azure CDN](../cdn-websites-with-cdn.md)
- [整合雲端服務與 Azure CDN](cdn-cloud-service-with-cdn.md)

即使未與 Azure App Service Web Apps 或 Azure 雲端服務整合，還是可以對指令碼套件組合使用 Azure CDN，但要注意下列幾點：

- 您必須手動將統合的指令碼上傳至 Blob 儲存體。 以程式設計方式解決方案 [stackoverflow](http://stackoverflow.com/a/13736433)。
- 在 .cshtml 檔案中，將轉譯的指令碼/CSS 標頭改為使用 Azure CDN。 例如：

        @Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

## 相關資訊 ##
- [Azure 內容傳遞網路 (CDN) 概觀](cdn-overview.md)
- [在 Azure App Service 中使用 Azure CDN](../cdn-websites-with-cdn.md)
- [整合雲端服務與 Azure CDN](cdn-cloud-service-with-cdn.md)
- [如何將內容傳遞網路 (CDN) 內容對應至自訂網域](http://msdn.microsoft.com/library/azure/gg680307.aspx)
- [使用 Azure 的 CDN](cdn-how-to-use-cdn.md)
 

