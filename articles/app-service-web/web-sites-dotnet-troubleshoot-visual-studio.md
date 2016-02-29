<properties 
    pageTitle="使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式" 
    description="了解如何使用 Visual Studio 2013 內建的遠端偵錯、追蹤和記錄工具，來疑難排解 Azure Web 應用程式。" 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/16/2015" 
    ms.author="tdykstra"/>

# 使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式

## 概觀

本教學課程示範如何使用 Visual Studio 工具可協助偵錯 web 應用程式執行 [應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714), ，在執行 [偵錯模式](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) 遠端或檢視應用程式記錄與 web 伺服器記錄。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

您將了解：

* Visual Studio 中提供的 Azure Web 應用程式管理功能有哪些。
* 如何使用 Visual Studio 遠端檢視，對遠端 Web 應用程式進行快速變更。
* 在 Azure 中執行專案時，如何同時針對 Web 應用程式和 WebJob 從遠端執行偵錯模式。
* 如何建立應用程式追蹤記錄，並在應用程式建立記錄時加以檢視。
* 如何檢視 Web 伺服器記錄，包括詳細的錯誤訊息與失敗的要求追蹤。
* 如何將診斷記錄傳送至 Azure 儲存體帳戶並在該處檢視。

如果您有 Visual Studio Ultimate，您也可以使用 [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) 進行偵錯。 本教學課程未涵蓋 IntelliTrace。

## <a name="prerequisites"></a>必要條件

本教學課程適用於開發環境、 web 專案和 Azure web 應用程式中設定 [開始使用 Azure 和 ASP.NET][GetStarted]。 針對 WebJobs 區段中，您必須在您建立的應用程式 [開始使用 Azure WebJobs SDK][GetStartedWJ]。

本教學課程中所提供的程式碼範例適用於 C# MVC Web 應用程式，但是疑難排解程序則是與 Visual Basic 和 Web Form 應用程式一樣。

遠端偵錯需要 Visual Studio 2013 或 Visual Studio 2012 含 Update 4。 遠端偵錯和 **伺服器總管** 功能需要 WebJobs [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) 或更新版本。 本教學課程中所示的其他功能也適用於 Visual Studio 2013 Express for Web 和 Visual Studio 2012 Express for Web。 

串流記錄功能僅適用於鎖定 .NET Framework 4 或更新版本的應用程式。

## <a name="sitemanagement"></a>Web 應用程式設定和管理

Visual Studio 提供的存取權的 web 應用程式管理功能和組態設定中可用的子集 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。 本節將說明可用的項目。

1. 如果您未登入至 Azure，Visual Studio 中，按一下 [ **連接到 Azure** 按鈕 **伺服器總管**。

    替代方式為安裝可讓您存取帳戶的管理憑證。 如果您選擇安裝憑證，以滑鼠右鍵按一下 **Azure** 節點 **伺服器總管**, ，然後按一下 [ **管理訂閱** 內容功能表中。 在 **管理 Azure 訂閱** 對話方塊中，按一下 [ **憑證** 索引標籤，然後再按一下 **匯入**。 請依照指示下載，然後再匯入的訂閱檔案 (也稱為 *.publishsettings* 檔案) 您的 Azure 帳戶。

    > [AZURE.NOTE]
    > 如果您下載了訂閱檔案，請將其儲存到原始程式碼目錄以外的資料夾 (例如在 Downloads 資料夾)，然後在匯入完成後刪除該檔案。 惡意使用者一旦能夠存取此訂用帳戶檔案，就能夠編輯、建立和刪除您的 Azure 服務。

    如需有關如何從 Visual Studio 連線至 Azure 資源的詳細資訊，請參閱 [管理帳戶、 訂閱和系統管理角色](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)。

2. 在 **伺服器總管**, ，依序展開 **Azure** 展開 **應用程式服務**。

3. 展開包含您在中建立的 web 應用程式的資源群組 [開始使用 Azure 和 ASP.NET][GetStarted], ，然後以滑鼠右鍵按一下 web 應用程式節點，再按一下 **檢視設定**。

    ![在伺服器總管中檢視設定](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

     **Azure Web 應用程式** ] 索引標籤隨即出現，而您會看到 web 應用程式管理與組態工作，可在 Visual Studio 中。

    ![Azure Web 應用程式視窗](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    在本教學課程中，您將使用記錄與追蹤下拉式清單。 您也會使用遠端偵錯功能，但是將以不同的方式來加以啟用。
   
    在此視窗中的應用程式設定和連接字串方塊的詳細資訊，請參閱 [Azure Web Apps: 應用程式字串與連接字串的運作](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx)。

    如果您想要執行無法在此視窗中完成的 web 應用程式管理工作，按一下 [ **管理入口網站中開啟** 開啟 Azure 傳統入口網站的瀏覽器視窗。 如需詳細資訊，請參閱 [如何設定 Web 應用程式](/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig)。

## <a name="remoteview"></a>在伺服器總管中存取 Web 應用程式檔案

部署 Web 專案時，通常會將 Web.config 檔案中的 `customErrors` 旗標設為 `On` 或 `RemoteOnly`，這表示出現問題時，您將不會收到有用的錯誤訊息。 對許多錯誤而言，您只會看到如下列之一的頁面。

**'/' 應用程式中有伺服器錯誤：**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**發生錯誤：**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**網站無法顯示頁面**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

要找到錯誤原因最簡單的方式，往往就是啟用詳細的錯誤訊息，而以上第一個螢幕擷取畫面說明的是其做法。 該做法需要在部署的 Web.config 檔案中進行變更。 您可以編輯 *Web.config* 檔案中的專案並重新部署專案，或建立 [Web.config 轉換](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) 並部署偵錯組建，但還有更快的方法: 在 **方案總管] 中** 直接檢視及編輯遠端 web 應用程式中的檔案使用 *遠端檢視* 功能。

1. 在 **伺服器總管**, ，展開 **Azure**, ，依序展開 **應用程式服務**, 、 依序展開您的 web 應用程式所在的資源群組和 web 應用程式的節點。

    您會看到可供您存取 Web 應用程式內容檔案與記錄檔的節點。

2. 展開 **檔案** 節點，然後按兩下 *Web.config* 檔案。

    ![開啟 Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio 會從遠端 Web 應用程式開啟 Web.config 檔案，然後在標題列中的檔案名稱旁邊顯示 [遠端]。

3. 將下列字行新增至 `system.web` 元素：

    `<customErrors mode="off"></customErrors>`

    ![編輯 Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. 重新整理顯示沒有幫助的錯誤訊息的瀏覽器，現在您就會看到詳細的錯誤訊息，例如以下範例：

    ![詳細的錯誤訊息](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (顯示的錯誤以紅色字行來建立 *Views\Home\Index.cshtml*。)

能夠讀取與編輯您的 Azure Web 應用程式上的檔案，使得疑難排解作業更為輕鬆，編輯 Web.config 檔案僅僅是其中一個範例案例。

## <a name="remotedebug"></a>遠端偵錯 Web 應用程式

如果詳細的錯誤訊息無法提供足夠的資訊，而且您無法在本機重現錯誤，另一個疑難排解的方式則是在遠端於偵錯模式中執行。 您可以設定中斷點、直接操作記憶體、逐步執行程式碼，甚至變更程式碼路徑。 

遠端偵錯無法在 Visual Studio 的 Express 版本中運作。

本節說明如何從遠端使用您在中建立的專案進行偵錯 [開始使用 Azure 和 ASP.NET][GetStarted]。

1. 開啟您在中建立的 web 專案 [開始使用 Azure 和 ASP.NET][GetStarted]。

1. 開啟 *controllers\ homecontroller.cs*。

2. 刪除 `About()` 方法，然後插入以下程式碼加以取代。

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [設定中斷點](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) 上 `ViewBag.Message` 列。

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下專案，然後按一下 **發行**。

2. 在 **設定檔** 下拉式清單中，選取您在中使用的相同設定檔 [開始使用 Azure 和 ASP.NET][GetStarted]。

3. 按一下 [ **設定** 索引標籤，然後變更 **組態** 至 **偵錯**, ，然後按一下 [ **發行**。

    ![於偵錯模式中發行](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. 當部署完成且您的瀏覽器開啟至 Web 應用程式的 Azure URL 之後，請關閉瀏覽器。

5. Visual Studio 2013: 在 **伺服器總管**, ，以滑鼠右鍵按一下 web 應用程式，然後按一下 **附加偵錯工具**。 

    ![連結偵錯工具](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    瀏覽器會自動開啟至您在 Azure 中執行的首頁。 您可能需要等候 20 秒左右的時間，讓 Azure 設定要偵錯的伺服器。 通常只有當您第一次在 Web 應用程式上執行偵錯模式時，才會出現這個延遲現象。 後續 48 小時內再次啟動的偵錯程序將不會再出現任何延遲。

6. 針對 Visual Studio 2012 Update 4 或更新版本:<a id="vs2012"></a>

    * 在 Azure 傳統入口網站，請移至 **設定 > 應用程式設定** 針對您的 web 應用程式，然後向下的捲動至 **偵錯** 一節。

    * 設定 **遠端偵錯** 到 **上**, ，並設定 **遠端 Visual Studio 版本** 至 **2012年**。

    * 在 Visual Studio **偵錯** ] 功能表上，按一下 [ **附加至處理序**。

    * 在 **限定詞** 方塊中，輸入 URL 的 web 應用程式不含 `http://` 前置詞。 

    * 選取 **顯示來自所有使用者的處理程序**。

    * 當系統提示您輸入認證時，請輸入具有將內容部署至 Web 應用程式權限的使用者名稱與密碼。 若要取得這些認證，請移至傳統入口網站中 web 應用程式的 [儀表板] 索引標籤，並按一下 **下載發行設定檔**。 在文字編輯器中，開啟檔案，您會發現 [使用者名稱和密碼之後的第一個出現的 **userName =** 和 **userPWD =**。 

    * 當處理程序出現在 **可用的處理序** 資料表中，選取 **w3wp.exe**, ，然後按一下 [ **附加**。

    * 開啟瀏覽器至您 Web 應用程式的 URL。

    您可能需要等候 20 秒左右的時間，讓 Azure 設定要偵錯的伺服器。 通常只有當您第一次在 Web 應用程式上執行偵錯模式時，才會出現這個延遲現象。 後續 48 小時內再次啟動的偵錯程序將不會再出現任何延遲。

6. 按一下 [ **有關** 功能表中。

    Visual Studio 會在中斷點處停止，而程式碼是在 Azure 中執行，而不是在您的本機電腦上執行。

7. 將滑鼠移至 `currentTime`變數上，以查看時間值。

    ![檢視 Azure 偵錯模式中執行的變數](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    您在 Azure 伺服器時間上看到的時間，可能與您的本機電腦所在的時區不同。

8. 為 `currentTime` 變數輸入新的值，例如「現在於 Azure 中執行」。

5. 按 F5 繼續執行。

    Azure 中執行的 [關於] 頁面會顯示您輸入到 currentTime 變數中的新值。

    ![含有新值的關於頁面](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> WebJobs 的遠端偵錯

本節說明如何從遠端使用您在建立專案和 web 應用程式進行偵錯 [開始使用 Azure WebJobs SDK](websites-dotnet-webjobs-sdk.md)。 本節顯示的功能僅適用於 Visual Studio 2013 含 Update 4 或更新版本。 遠端偵錯僅適用於連續的 WebJobs。 排程與隨選 WebJobs 不支援偵錯。

1. 開啟您在中建立的 web 專案 [開始使用 Azure WebJobs SDK][GetStartedWJ]。

1. 在 ContosoAdsWebJob 專案中，開啟 *Functions.cs*。

2. [設定中斷點](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) 中的第一個陳述式上 `GnerateThumbnail` 方法。

    ![設定中斷點](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 web 專案 (不是 WebJob 專案)，然後按一下 **發行**。

2. 在 **設定檔** 下拉式清單中，選取您在中使用的相同設定檔 [開始使用 Azure WebJobs SDK](websites-dotnet-webjobs-sdk.md)。

3. 按一下 [ **設定** 索引標籤，然後變更 **組態** 至 **偵錯**, ，然後按一下 [ **發行**。

    Visual Studio 會部署 Web 和 WebJob 專案，且在瀏覽器中開啟您 Web 應用程式的 Azure URL。

5. 在 **伺服器總管** 展開 **Azure > App Service > 資源群組 > web 應用程式 > WebJobs > 連續**, ，然後以滑鼠右鍵按一下 **ContosoAdsWebJob**。

7. 按一下 [ **附加偵錯工具**。 

    ![連結偵錯工具](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    瀏覽器會自動開啟至您在 Azure 中執行的首頁。 您可能需要等候 20 秒左右的時間，讓 Azure 設定要偵錯的伺服器。 通常只有當您第一次在 Web 應用程式上執行偵錯模式時，才會出現這個延遲現象。 如果您在 48 小時內執行，則下一次連結偵錯工具時將不會出現延遲。

6. 在開啟至 Contoso 廣告首頁的 Web 瀏覽器中，建立新的廣告。 

    建立廣告會建立將由 WebJob 挑選並處理的佇列訊息。 當 WebJobs SDK 呼叫函數來處理佇列訊息時，程式碼便會叫用中斷點。

7. 當偵錯工具在中斷點出現中斷時，您可以檢查並變更在雲端執行程式時的變數值。 在下圖中，偵錯工具顯示已傳遞到 GenerateThumbnail 方法的 blobInfo 物件內容。

    ![偵錯工具中的 blobInfo 物件](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. 按 F5 繼續執行。

    GenerateThumbnail 方法完成建立縮圖。

6. 在瀏覽器中重新整理索引頁面，您便會看見縮圖。

6. 在 Visual Studio 中，按 SHIFT+F5 停止偵錯。

7. 在 **伺服器總管**, ，以滑鼠右鍵按一下 ContosoAdsWebJob 節點，然後按一下 **檢視儀表板**。

8. 使用您的 Azure 認證登入，然後按一下 WebJob 名稱，可前往 WebJob 的頁面。 

    ![按一下 ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

    儀表板會顯示最近執行的 GenerateThumbnail 函數。

    (下次按一下 [ **檢視儀表板**, 、 您沒有登入，而且瀏覽器便會直接進入頁面在您的 WebJob。)

9. 按一下函數名稱可查看執行函數的詳細資料。

    ![函數詳細資料](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

如果您的函式 [撰寫記錄](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs), ，您可以按一下 **ToggleOutput** 才能查看它們。

## 遠端偵錯注意事項

* 不建議在生產環境中執行偵錯模式。 如果您的生產 Web 應用程式並未調升規模到多個伺服器執行個體，偵錯就會防止 Web 伺服器回應其他要求。 如果您不具備多個 Web 伺服器執行個體，當您連結至偵錯工具時，會取得一個隨機產生的執行個體，而且您將無法確認後續瀏覽器要求是否會通往該執行個體。 此外，通常我們不會將偵錯組建部署到生產環境中，而且版本組建的編譯器最佳化作業將會使系統無法逐行顯示您的原始程式碼中所發生的事情。 針對生產環境問題的疑難排解，您的最佳資源將是應用程式追蹤與 Web 伺服器記錄。

* 進行遠端偵錯時，避免長時間在中斷點停止運作。 Azure 會將停止超過幾分鐘的處理序視為無回應的處理序，並將其關閉。

* 在偵錯期間，伺服器會將資料傳送至 Visual Studio，進而影響頻寬付費情況。 如需關於頻寬費率的詳細資訊，請參閱 [Azure 定價](/pricing/calculator/)。

* 請確定 `debug` 屬性 `compilation` 中的項目 *Web.config* 檔案設定為 true。 在發行偵錯組建組態時，該值預設會設為 true。

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* 如果您發現偵錯工具無法逐步執行您要偵錯的程式碼，可能需要變更「Just My Code」設定。  如需詳細資訊，請參閱 [限制於 Just My Code 逐步執行](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code)。

* 當您啟用遠端偵錯功能時，伺服器上會啟動計時器，並在 48 小時後自動關閉此功能。 此 48 小時的限制是為了安全與效能起見而設計的功能。 若需要，您可以輕鬆開啟這項功能，次數不限。 當您不需要偵錯時，建議您將其保持為停用。

* 您可以手動將偵錯工具附加至任何處理序，不僅止於 Web 應用程式處理序 (w3wp.exe)。 如需如何使用 Visual Studio 中偵錯模式的詳細資訊，請參閱 [Visual Studio 偵錯](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx)。

## <a name="logsoverview"></a>診斷記錄概觀

在 Azure Web 應用程式中執行的 ASP.NET 應用程式，可建立下列各種記錄：

* **應用程式追蹤記錄**<br/>
  應用程式呼叫的方法來建立這些記錄檔 [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) 類別。
* **Web 伺服器記錄**<br/>
  Web 伺服器會為每個對於 Web 應用程式的 HTTP 要求建立記錄項目。
* **詳細的錯誤訊息記錄**<br/>
  Web 伺服器會針對失敗的 HTTP 要求 (產生狀態碼 400 或以上的要求) 建立含有一些額外資訊的 HTML 頁面。 
* **失敗要求追蹤記錄**<br/>
  Web 伺服器會針對失敗的 HTTP 要求建立含有詳細追蹤資訊的 XML 檔案。 Web 伺服器會一併提供 XSL 檔案，在瀏覽器中格式化 XML。
  
記錄功能會影響 Web 應用程式效能，因此 Azure 可讓您視需要啟用或停用每一種記錄類型。 對於應用程式記錄，您可以指定只寫入高於特定嚴重性層級的記錄。 當您建立新的 Web 應用程式時，預設會停用所有記錄功能。

記錄檔中的檔案會寫入 *LogFiles* web 應用程式和可以透過 FTP 存取的檔案系統中的資料夾。 Web 伺服器記錄與應用程式記錄也可以寫入至 Azure 儲存體帳戶。 您可以在儲存體帳戶中保留大於檔案系統可容納的記錄檔數量。 當您使用檔案系統時，最大的記錄檔大小為 100 MB。 (檔案系統記錄僅適用於短期保留之用。 Azure 會在達到此上限之後刪除舊的記錄檔以騰出空間給新的記錄檔使用。)  

## <a name="apptracelogs"></a>建立並檢視應用程式追蹤記錄

您將在本節執行下列工作：

* 將追蹤陳述式加入至您在建立 web 專案 [開始使用 Azure 和 ASP.NET][GetStarted]。
* 當您在本機上執行專案時檢視記錄。
* 依原樣檢視 Azure 中執行的應用程式所產生的記錄。 

如需有關如何建立應用程式資訊記錄在 WebJobs 中，請參閱 [如何使用 Azure 佇列儲存體使用 WebJobs SDK-如何寫入記錄檔](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)。 下列有關在 Azure 中檢視記錄和控制記錄儲存方式的指示也同樣適用於 WebJobs 所建立的應用程式記錄。 

### 將追蹤陳述式新增至應用程式

1. 開啟 *controllers\ homecontroller.cs*, ，並以下列程式碼取代檔案內容，以便加入 `Trace` 陳述式和 `using` 陳述式 `System.Diagnostics`:

        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Diagnostics;
        using System.Linq;
        using System.Web;
        using System.Web.Configuration;
        using System.Web.Mvc;
        namespace MyExample.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    Trace.WriteLine("Entering Index method");
                    ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
                    Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
                    Trace.WriteLine("Leaving Index method");
                    return View();
                }
        
                public ActionResult About()
                {
                    Trace.WriteLine("Entering About method");
                    ViewBag.Message = "Your app description page.";
                    Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
                    Trace.WriteLine("Leaving About method");
                    return View();
                }
        
                public ActionResult Contact()
                {
                    Trace.WriteLine("Entering Contact method");
                    ViewBag.Message = "Your contact page.";
                    Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
                    Trace.WriteLine("Leaving Contact method");
                    return View();
                }
            }
        }
        
                
### 在本機檢視追蹤輸出

3. 按 F5 以在偵錯模式中執行應用程式。

    預設的追蹤接聽項寫入至的所有追蹤輸出 **輸出** ] 視窗中的，連同其他偵錯輸出。 下圖顯示您加入 `Index` 方法的追蹤陳述式的輸出。

    ![偵錯視窗中的追蹤](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    以下步驟說明如何在網頁中檢視追蹤輸出，而不需在偵錯模式中編譯。

2. 開啟應用程式 Web.config 檔 (專案資料夾中的那個) 並將 `<system.diagnostics>` 元素新增至檔案結尾 `</configuration>` 元素關閉處前：

        <system.diagnostics>
            <trace>
              <listeners>
                <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
              </listeners>
            </trace>
          </system.diagnostics>

    `WebPageTraceListener` 可讓您藉由瀏覽至 `/trace.axd` 來檢視追蹤輸出。

3. 在 Web.config 檔案的 `<system.web>` 下方，加入<a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">追蹤元素</a>，如以下範例所示：

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. 按 CTRL+F5 執行應用程式。

4. 在瀏覽器視窗網址列中加入 *trace.axd* 至 URL，然後按 Enter (URL 會類似於 http://localhost:53370/trace.axd)。

5. 在 **應用程式追蹤** 頁面上，按一下 **檢視詳細資料** 的第一行 (不是 BrowserLink 行)。

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

     **要求詳細資料** ] 頁面隨即出現，並在 **追蹤資訊** 區段，您會看到您加入的追蹤陳述式輸出 `Index` 方法。

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    根據預設，`trace.axd` 只能在本機使用。 如果您想讓其可從遠端 web 應用程式，您可以將 `localOnly="false"` 到 `trace` 中的項目 *Web.config* 檔案，如下列範例所示:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    但是，為了安全起見，通常不建議在生產 Web 應用程式中啟用 `trace.axd`，而以下各節將為您說明如何用更簡易的方式，在 Azure Web 應用程式中讀取追蹤記錄。

### 在 Azure 中檢視追蹤輸出

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 web 專案，然後按一下 **發行**。

2. 在 **發行 Web** 對話方塊中，按一下 [ **發行**。

    Visual Studio 成功發行您的更新後，將會開啟瀏覽器視窗至您的首頁 (假設您並未清除 **目的地 URL** 上 **連接** ] 索引標籤)。

3. 在 **伺服器總管**, ，以滑鼠右鍵按一下 web 應用程式，然後選取 **檢視串流記錄**。 

    ![在內容功能表中檢視串流記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

     **輸出** 視窗會顯示您已連線至記錄串流服務，並沒有要顯示的記錄每一分鐘新增一行通知文字。

    ![在內容功能表中檢視串流記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. 在瀏覽器視窗，顯示您應用程式的首頁上，按一下 [ **連絡人**。

    在幾秒內的錯誤層級追蹤輸出加入至您 `Contact` 方法會出現在 **輸出** 視窗。

    ![輸出視窗中的錯誤追蹤](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio 只會顯示錯誤層級追蹤，因為那是您在啟用記錄監視服務時的預設設定。 建立新的 Azure Web 應用程式時，預設會停用所有記錄功能，正如同稍早您在開啟設定頁面時所見：

    ![應用程式記錄功能關閉](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


    However, when you selected **View Streaming Logs**, Visual Studio automatically changed **Application Logging(File System)** to **Error**, which means error-level logs get reported. In order to see all of your tracing logs, you can change this setting to **Verbose**. When you select a severity level lower than error, all logs for higher severity levels are also reported. So when you select verbose, you also see information, warning, and error logs.  

4. 在 **伺服器總管**, ，以滑鼠右鍵按一下 web 應用程式，然後按一下 **檢視設定** 如先前般。

5. 變更 **Application Logging (File System)** 至 **Verbose**, ，然後按一下 [ **儲存**。
 
    ![將追蹤層級設定為詳細資訊](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. 瀏覽器視窗，現在會顯示您 **連絡人** 頁面上，按一下 **首頁**, ，然後按一下 [ **有關**, ，然後按一下 [ **連絡人**。

    在幾秒鐘之內， **輸出** ] 視窗會顯示的所有追蹤輸出。

    ![詳細資訊追蹤輸出](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    在本節中，您已使用 Azure Web 應用程式設定來啟用與停用記錄功能。 您也可以修改 Web.config 檔案，來啟用與停用追蹤接聽程式。 不過，修改 Web.config 檔案會導致應用程式網域回收，而透過 Web 應用程式設定來啟用記錄功能則不會有這種現象。 如果此問題需要經過長時間才會重現或是間歇性出現，則回收應用程式網域可能「修正」此問題，並強迫您等候問題再次發生。 在 Azure 中啟用診斷功能不會出現此情況，因此您可以立即開始擷取錯誤資訊。

### 輸出視窗功能

 **Azure 記錄檔** ] 索引標籤的 **輸出** 視窗有數個按鈕和文字方塊:

![記錄索引標籤按鈕](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

這些物件可執行下列功能：

* 清除 **輸出** 視窗。
* 啟用或停用自動換行。
* 啟動或停止監視記錄。
* 指定要監視的記錄。
* 下載記錄。
* 依據搜尋字串或規則運算式篩選記錄。
* 關閉 **輸出** 視窗。

如果您輸入搜尋字串或是規則運算式，則 Visual Studio 會篩選用戶端的記錄資訊。 這表示在顯示記錄後，您可以輸入準則 **輸出** ] 視窗中，您可以變更篩選條件而不必重新產生記錄。

## <a name="webserverlogs"></a>檢視 Web 伺服器記錄

Web 伺服器記錄會記下 Web 應用程式的所有 HTTP 活動。 若要查看在 **輸出** 視窗，您必須針對 web 應用程式啟用它們，然後告訴 Visual Studio 您想要監視它們。 

1. 在 **Azure Web 應用程式設定** ] 索引標籤中開啟 **伺服器總管**, ，變更 Web 伺服器記錄 **上**, ，然後按一下 [ **儲存**。

    ![啟用 Web 伺服器記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. 在 **輸出** ] 視窗中，按一下 [ **指定要監視的 Azure 記錄** ] 按鈕。
    
    ![指定要監視的 Azure 記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. 在 **Azure 記錄選項** 對話方塊中，選取 **Web 伺服器記錄**, ，然後按一下 [ **確定**。

    ![監視 Web 伺服器記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. 在瀏覽器視窗中顯示 web 應用程式，按一下 [ **首頁**, ，然後按一下 [ **有關**, ，然後按一下 [ **連絡人**。

    通常會先產生應用程式記錄，然後才是 Web 伺服器記錄。 您可能需要等候一小段時間，記錄才會顯示。 

    ![輸出視窗中的 Web 伺服器記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


根據預設，當您第一次使用 Visual Studio 啟用 Web 伺服器記錄時，Azure 會將記錄寫入檔案系統。 作為替代方式，您可以使用傳統入口網站來指定應該將 Web 伺服器記錄寫入儲存體帳戶中的某個 Blob 容器。 如需詳細資訊，請參閱 **網站診斷** 一節中 [如何設定網站](/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig)。 

如果您使用傳統入口網站對 Azure 儲存體帳戶啟用 Web 伺服器記錄功能，然後在 Visual Studio 中停用記錄功能，當您在 Visual Studio 中重新啟用記錄功能時，將會還原您的儲存體帳戶設定。 

## <a name="detailederrorlogs"></a>檢視詳細的錯誤訊息記錄

針對導致錯誤回應代碼 (400 或以上) 之 HTTP 要求，詳細的錯誤訊息記錄可提供部分額外資訊。 若要查看在 **輸出** ] 視窗中，您必須針對 web 應用程式啟用它們，然後告訴 Visual Studio 您想要監視它們。

1. 在 **Azure Web 應用程式設定** ] 索引標籤中開啟 **伺服器總管**, ，變更 **詳細的錯誤訊息** 到 **上**, ，然後按一下 [ **儲存**。

    ![啟用詳細的錯誤訊息](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. 在 **輸出** ] 視窗中，按一下 [ **指定要監視的 Azure 記錄** ] 按鈕。

3. 在 **Azure 記錄選項** 對話方塊中，按一下 [ **所有記錄檔**, ，然後按一下 [ **確定**。

    ![監視所有記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. 在瀏覽器視窗的網址列中，為 URL 加入額外的字元以引發 404 錯誤 (例如，`http://localhost:53370/Home/Contactx`)，然後按 Enter 鍵。

    幾秒鐘之後詳細的錯誤記錄會出現在 Visual Studio **輸出** 視窗。

    ![輸出視窗中的詳細的錯誤記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    按住 Ctrl 鍵並按一下該連結，可在瀏覽器中查看格式化的記錄輸出：

    ![瀏覽器視窗中的詳細的錯誤記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>下載檔案系統記錄

您可以監視中的任何記錄檔 **輸出** 視窗也能下載為 *.zip* 檔案。 

1. 在 **輸出** ] 視窗中，按一下 [ **下載串流記錄**。

    ![記錄索引標籤按鈕](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    檔案總管] 會開啟至您 *下載* 資料夾，並選取下載的檔案。

    ![下載的檔案](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. 擷取 *.zip* 檔案，而且您會看到下列資料夾結構:

    ![下載的檔案](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

    * 應用程式追蹤記錄位於 *.txt* 檔案中 *.txt* 資料夾。
    * Web 伺服器記錄位於 *.log* 檔案中 *.log* 資料夾。 您可以使用一種工具，例如 [Log Parser](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) 檢視和管理這些檔案。
    * 詳細的錯誤訊息記錄位於 *.html* 檔案中 *.html* 資料夾。

    ( *部署* 資料夾則用於原始檔控制發行; 所建立的檔案沒有與 Visual Studio 發行功能相關的任何項目。  *Git* 資料夾則用於原始檔控制相關的追蹤發行，以及記錄檔案串流服務。)  

## <a name="storagelogs"></a>檢視儲存體記錄

應用程式追蹤記錄也可以傳送至 Azure 儲存體帳戶，以便您在 Visual Studio 中加以檢視。 若要建立儲存體帳戶，啟用儲存體記錄，在傳統的入口網站，並中檢視它們 **記錄** ] 索引標籤的 **Azure Web 應用程式** 視窗。

您可以將記錄傳送至以下任意或所有目的地 (共三個)：

* 檔案系統。
* 儲存體帳戶資料表。
* 儲存體帳戶 Blob。

您可以為每個目的地指定不同的嚴重性層級。 

資料表可讓您輕鬆地在線上檢視記錄的詳細資料，而且資料表還支援串流；您可以查詢資料表中的記錄，並即時看到正在建立的新記錄。 Blob 則可讓您輕鬆地將記錄下載到檔案，並運用 HDInsight 加以分析，因為 HDInsight 知道如何處理 Blob 儲存體。 如需詳細資訊，請參閱 **Hadoop 與 MapReduce** 中 [資料儲存體選項 (建置真實世界雲端應用程式與 Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options)。

您目前已將檔案系統記錄設為詳細資訊層級；以下步驟將帶您逐步設定資訊層級記錄，以便傳送至儲存體帳戶資料表。 資訊層級代表所有藉由呼叫 `Trace.TraceInformation`、`Trace.TraceWarning` 與 `Trace.TraceError` 的記錄都會顯示出來，但不會顯示藉由呼叫 `Trace.WriteLine` 所建立的記錄。

與檔案系統相較之下，儲存體帳戶可提供更多的儲存體與較長的記錄保留時間。 將應用程式追蹤記錄傳送至儲存體的另一項好處，就是您可以從每個記錄中獲得更多的額外資訊，而檔案系統記錄則無法提供。

5. 以滑鼠右鍵按一下 **儲存體** Azure 節點，然後按下 **建立儲存體帳戶**。

![建立儲存體帳戶](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. 在 **建立儲存體帳戶** ] 對話方塊中，輸入儲存體帳戶的名稱。 

    這個名稱必須是唯一的 (其他 Azure 儲存體帳戶不可以有相同的名稱)。 如果您輸入的名稱已在使用中，您可以變更此名稱。

    若要存取儲存體帳戶的 URL 會是 *{name}*。.core.windows.net。 

5. 設定 **區域或同質群組** 到最接近您區域的下拉式清單。

    此設定會指定哪個 Azure 資料中心將會主控您的儲存體帳戶。 在本教學課程中，您的決定並不會造成明顯的差異，但對於生產 Web 應用程式而言，您會希望 Web 伺服器和儲存體帳戶是在相同區域內，以便將延遲和資料輸出費用降到最低。 您稍後將建立的 Web 應用程式的執行區域應該盡可能接近可存取您 Web 應用程式的瀏覽器，以便將延遲降至最低。

6. 設定 **複寫** 下拉式清單來 **本機備援**。 

    對儲存體帳戶啟用地理區域複寫時，儲存內容會複寫至次要資料中心，以便能在主要位置發生嚴重災難時容錯移轉至該位置。 地理區域複寫會引發額外成本。 對於測試和開發帳戶，您通常不會想要付費使用地理區域複寫功能。 如需詳細資訊，請參閱 [建立、 管理或刪除儲存體帳戶](../storage-create-storage-account/#replication-options)。

5. 按一下 [ **建立**。 

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)  

1. 在 Visual Studio **Azure Web 應用程式** ] 視窗中，按一下 [ **記錄** 索引標籤，然後再按一下 **設定管理入口網站中的記錄**。

    <!-- todo:screenshot of new portal if the VS page link goes to new portal -->
    ![設定記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    這會開啟 **設定** 在傳統的入口網站，web 應用程式] 索引標籤。

2. 在傳統入口網站 **設定** ] 索引標籤上，向下捲動 [應用程式診斷] 區段中，然後再變更 **Application Logging (Table Storage)** 到 **上**。

3. 變更 **記錄層級** 至 **資訊**。

4. 按一下 [ **管理資料表儲存體**。

    ![按一下 [管理資料表儲存體]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    在 **管理資料表儲存體應用程式診斷** ] 方塊中，您可以選擇儲存體帳戶，如果您有一個以上。 您可以建立新的資料表，或是使用現有的資料表。

    ![管理資料表儲存體](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. 在 **管理資料表儲存體應用程式診斷** 方塊中，按一下核取記號，以關閉該方塊。

6. 在傳統入口網站 **設定** 索引標籤上，按一下 [ **儲存**。

7. 在瀏覽器視窗顯示應用程式的 web 應用程式中，按一下 [ **首頁**, ，然後按一下 [ **有關**, ，然後按一下 [ **連絡人**。

    瀏覽這些網頁所產生的記錄資訊將會寫入儲存體帳戶。

8. 在 **記錄** ] 索引標籤的 **Azure Web 應用程式** ] 視窗，在 Visual Studio 中，按一下 [ **重新整理** 下 **診斷摘要**。

    ![按一下 [重新整理]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     **診斷摘要** 區段顯示最後 15 分鐘的預設記錄。 您可以變更期間以檢視更多記錄。 

    (如果您收到 「 找不到資料表 」 錯誤，請確認您所進行追蹤的作業，在您啟用的網頁瀏覽 **Application Logging (Storage)** 並在您按一下之後 **儲存**。)

    ![儲存體記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

    請注意，在此檢視中您會看到 **處理序識別碼** 和 **Thread ID** 針對每個記錄檔，您不在檔案系統記錄中取得。 您可以直接檢視 Azure 儲存體資料表來查看其他欄位。

8. 按一下 [ **檢視所有應用程式記錄檔**。

    追蹤記錄資料表會顯示在 Azure 儲存體資料表檢視器中。
   
    (如果您收到 「 序列包含任何項目 」 的錯誤，請開啟 **伺服器總管**, ，展開您的儲存體帳戶的節點 **Azure** 節點，然後再以滑鼠右鍵按一下 **資料表** 按一下 **重新整理**。)

    ![資料表檢視中的儲存體記錄](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

    此檢視會顯示在其他任何檢視中都不會看到的額外欄位。 此檢視還可讓您藉由特殊的查詢產生器 UI 來篩選記錄，以便建構查詢。 如需詳細資訊，請參閱 「 使用表格資源-篩選實體中的 [使用伺服器總管瀏覽儲存體資源](http://msdn.microsoft.com/library/ff683677.aspx)。

7. 若要查看單一資料列的詳細資訊，請按兩下其中一個資料列。

    ![伺服器總管中的追蹤資料表](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

## <a name="failedrequestlogs"></a>檢視失敗要求追蹤記錄

當您需要了解 IIS 如何處理 HTTP 要求的詳細資料時，例如在 URL 重新寫入或是出現驗證問題等情況，失敗要求追蹤記錄就會很有用。 

Azure Web 應用程式會使用 IIS 7.0 及更新版本所提供的相同失敗要求追蹤功能。 但您無法存取用來設定哪些錯誤會被記錄下來的 IIS 設定。 當您啟用失敗要求追蹤時，將擷取所有錯誤。 

您可以使用 Visual Studio 來啟用失敗要求追蹤，但是您無法在 Visual Studio 中加以檢視。 這些記錄是 XML 檔案。 串流記錄服務只會監視被當作可在純文字模式中讀取的檔案:  *.txt*, ，*.html*, ，和 *.log* 檔案。

您可以直接在瀏覽器中透過 FTP，或是在本機使用 FTP 工具將記錄下載到本機電腦中之後檢視失敗要求追蹤記錄。 在本節中，您將在瀏覽器中直接檢視。

1. 在 **組態** ] 索引標籤的 **Azure Web 應用程式** ] 視窗中開啟 **伺服器總管**, ，變更 **失敗要求追蹤** 到 **上**, ，然後按一下 [ **儲存**。

    ![啟用失敗要求追蹤](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. 在顯示 Web 應用程式的瀏覽器視窗網址列中，將額外字元新增至 URL 並按 Enter 鍵以引發 404 錯誤。

    這麼做會讓系統建立失敗要求追蹤記錄，以下步驟將說明如何檢視或下載記錄。

2. 在 Visual Studio 中，在 **組態** ] 索引標籤的 **Azure Web 應用程式** ] 視窗中，按一下 [ **管理入口網站中開啟**。

3. 在您的 web 應用程式的 Azure 入口網站 (https://portal.azure.com) 分頁中，按一下 [ **設定 > 部署認證**, ，然後輸入新的使用者名稱和密碼。

    ![新的 FTP 使用者名稱與密碼](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    **當您登入時，必須使用此完整使用者名稱搭配 Web 應用程式名稱前置詞。 例如，若您輸入使用者名稱 "myid"，而網站為 "myexample"，則會登入為 "myexample\myid"。

5. 在新的瀏覽器視窗中，移至的 URL 會顯示在下方 **FTP 主機名稱** 或 **FTPS 主機名稱** 中 **Web 應用程式** web 應用程式的刀鋒視窗。 

6. 使用您先前建立的 FTP 認證登入 (包括該使用者名稱的 Web 應用程式名稱前置詞)。

    瀏覽器會顯示 Web 應用程式的根資料夾。

6. 開啟 *LogFiles* 資料夾。

    ![開啟 LogFiles 資料夾](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. 開啟名為 W3SVC 並加上數值的資料夾。

    ![開啟 W3SVC 資料夾](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    該資料夾包含了一些 XML 檔案 (內含任何您在啟用失敗要求追蹤功能後所記錄的錯誤)，以及一個可供瀏覽器用來格式化該 XML 檔案的 XSL 檔案。

    ![W3SVC 資料夾](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. 按一下 XML 檔案，以取得您想要檢視其追蹤資訊的失敗要求。

    下圖顯示錯誤範例追蹤資訊的片段。

    ![瀏覽器中的失敗要求追蹤](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


## <a name="nextsteps"></a>後續步驟

在了解 Visual Studio 如何讓您輕鬆地檢視 Azure Web 應用程式所建立的記錄之後， 下列各節提供相關主題的更多資源連結：

* Azure Web 應用程式疑難排解
* 在 Visual Studio 中偵錯 
* 在 Azure 中遠端偵錯
* 在 ASP.NET 應用程式中追蹤
* 分析 Web 伺服器記錄
* 分析失敗要求追蹤記錄
* 偵錯雲端服務

### Azure Web 應用程式疑難排解

如需在 Azure App Service 中疑難排解 Web 應用程式的詳細資訊，請參閱下列資源：

* [如何監視 Web 應用程式](/manage/services/web-sites/how-to-monitor-websites/)
* [調查 Azure Web 應用程式，使用 Visual Studio 2013 中的記憶體流失](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx)。 Microsoft ALM 部落格文章，討論 Visual Studio 中分析 Managed 記憶體問題的功能。
* [您應該了解 azure 的 web 應用程式線上工具](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/)。 取自 Amit Apple 的部落格文章。

如需特定疑難排解問題的說明，請在下列任一個論壇中開啟一段討論串：

* [ASP.NET 網站上的 Azure 論壇](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)。
* [MSDN 上的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/)。
* [StackOverflow.com](http://www.stackoverflow.com)。

### 在 Visual Studio 中偵錯 

如需如何使用 Visual Studio 中偵錯模式的詳細資訊，請參閱 [Visual Studio 偵錯](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) MSDN 主題與 [Visual Studio 2010 的偵錯秘訣](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)。

### 在 Azure 中遠端偵錯

如需針對 Azure Web 應用程式與 WebJob 進行遠端偵錯的詳細資訊，請參閱下列資源：

* [遠端偵錯 Azure App Service Web Apps 的簡介](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/)。
* [遠端偵錯 Azure App Service Web Apps 的簡介第 2 部分 - 內部遠端偵錯](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [遠端偵錯 Azure App Service Web Apps 的簡介第 3 部分 - 多重執行個體環境和 GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs 偵錯 (影片)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

如果您的 web 應用程式使用 Azure Web API 或是行動服務後端，而您需要偵錯，請參閱 [Visual Studio 中偵錯.NET 後端](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx)。

### 在 ASP.NET 應用程式中追蹤

網際網路上找不到關於 ASP.NET 追蹤功能詳盡且具時效性的說明。 您所能做的，就是從專為 Web Form 所撰寫的一些舊有簡介資料下手，因為 MVC 是最近才問世的技術，並以著重在特定議題的較新的部落格文章來做為補充。 以下資源是您開始了解這項技術的一些好去處：

* [監視與遙測 (運用 Azure 建置真實世界的雲端應用程式)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)。<br> 
  針對追蹤 Azure 雲端應用程式所建議的電子書章節。
* [ASP.NET 追蹤](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  雖然舊但仍是該主題的基本簡介的良好資源。
* [追蹤接聽程式](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  追蹤接聽程式的相關資訊，但是沒有提到 [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx)。
* [逐步解說: 整合 ASP.NET 追蹤與 System.Diagnostics 追蹤](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  此資訊同樣很舊，但包含了簡介文章沒有提到的一些其他資訊。
* [追蹤 ASP.NET MVC Razor 檢視](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  除了追蹤 Razor 檢視之外，該文同時說明了如何建立錯誤篩選條件以便記錄 MVC 應用程式所出現的所有未處理的例外。 如需如何記錄 Web Form 應用程式中的所有未處理的例外狀況資訊，請參閱中的 Global.asax 範例 [的錯誤處理常式的完整範例](http://msdn.microsoft.com/library/bb397417.aspx) MSDN 上。 無論是 MVC 還是 Web Form，如果您想要記錄特定例外，但是讓預設的架構處理功能生效，則您可以如以下範例所示捕捉並重新擲回這些例外：

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [串流診斷追蹤記錄從 Azure 命令列 (加上 Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  如何使用命令列來執行本教學課程所示範的 Visual Studio 步驟。 [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) 是用於偵錯 ASP.NET 應用程式的工具。 
* [使用 Web 應用程式記錄與診斷功能-與 David ebbo 一起](/documentation/videos/azure-web-site-logging-and-diagnostics/) 和 [從 Web 應用程式-與 David ebbo 一起串流記錄](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Scott Hanselman 和 David Ebbo 提供的影片。

針對錯誤記錄，撰寫您自己的追蹤程式碼的替代方式是使用開放原始碼的記錄架構，例如 [ELMAH](http://nuget.org/packages/elmah/)。 如需詳細資訊，請參閱 [關於 ELMAH Scott Hanselman 的部落格文章](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)。

此外，如果您想要從 Azure 取得串流記錄，則您不需要使用 ASP.NET 或 System.Diagnostics 追蹤功能。 Azure web 應用程式串流記錄服務會串流任何 *.txt*, ，*.html*, ，或 *.log* 檔案中找到 *LogFiles* 資料夾。 因此，您可以建立自己的記錄系統以寫入 Web 應用程式的檔案系統，而您的檔案將自動進行串流與下載。 您只需要會建立相關檔案中的寫入應用程式碼 *d:\home\logfiles* 資料夾。 

### 分析 Web 伺服器記錄

如需分析 Web 伺服器記錄的詳細資訊，請參閱下列資源：

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  在 web 伺服器記錄中檢視資料的工具 (*.log* 檔案)。
* [疑難排解 IIS 效能問題或使用 LogParser 的應用程式錯誤 ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  介紹可用來分析 Web 伺服器記錄的記錄檔剖析器工具。
* [關於使用 LogParser Robert mcmurray 的部落格文章](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [IIS 7.0、IIS 7.5 與 IIS 8.0 中的 HTTP 狀態碼](http://support.microsoft.com/kb/943891)

### 分析失敗要求追蹤記錄

Microsoft TechNet 網站內 [使用失敗要求追蹤](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) 小節可能有助您了解如何使用這些記錄檔。 不過，本文主要著重在 IIS 內設定失敗要求追蹤功能，這是您無法在 Azure Web Apps 中執行的功能。

### 偵錯雲端服務

如果您想要偵錯 Azure 雲端服務，而不是 web 應用程式，請參閱 [偵錯雲端服務](http://msdn.microsoft.com/library/windowsazure/ee405479.aspx)。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需 Azure 入口網站變更為 Azure 預覽入口網站的指南，請參閱: [瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[GetStarted]: web-sites-dotnet-get-started.md
[GetStartedWJ]: websites-dotnet-webjobs-sdk.md
 

