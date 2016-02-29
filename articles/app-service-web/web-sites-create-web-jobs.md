<properties 
    pageTitle="使用 WebJob 執行背景工作" 
    description="了解如何在 Azure Web 應用程式中執行背景工作。" 
    services="app-service" 
    documentationCenter="" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2015" 
    ms.author="tdykstra"/>

# 使用 WebJob 執行背景工作

## 概觀

您可以在 Webjob 中執行程式或指令碼程式 [應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714) 三種方式的 web 應用程式: 隨選、 連續或根據排程。 使用 WebJob 不會產生額外的費用。

本文將說明如何使用部署 WebJobs [Azure 入口網站](https://portal.azure.com)。 如需如何使用 Visual Studio 或連續傳遞程序部署的資訊，請參閱 [如何將 Azure Webjob 部署至 Web 應用程式](websites-dotnet-deploy-webjobs.md)。

Azure WebJobs SDK 能簡化許多 WebJobs 程式設計工作。 如需詳細資訊，請參閱 [什麼是 WebJobs SDK](websites-dotnet-webjobs-sdk.md)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="acceptablefiles"></a>指令碼或程式可接受的檔案類型

以下是可接受的檔案類型：

* .cmd、.bat、.exe (使用 Windows 命令提示字元)
* .ps1 (使用 PowerShell)
* .sh (使用 Bash)
* .php (使用 PHP)
* .py (使用 Python)
* .js (使用 Node)
* .jar (使用 java)

## <a name="CreateOnDemand"></a>在入口網站中建立依需求執行的 WebJob

1. 在 **Web 應用程式** 刀鋒視窗中的 [Azure 入口網站](http://portal.azure.com), ，按一下 [ **所有設定 > WebJobs** 顯示 **WebJobs** 刀鋒視窗。
    
    ![WebJob 刀鋒視窗](./media/web-sites-create-web-jobs/wjblade.png)
    
5. 按一下 [ **新增**。  **新增 WebJob** ] 對話方塊隨即出現。
    
    ![新增 WebJob 刀鋒視窗](./media/web-sites-create-web-jobs/addwjblade.png)
    
2. 在 **名稱**, ，提供 WebJob 的名稱。 名稱的開頭必須是字母或數字，而且不能含有 "-" 和 "_" 之外的任何特殊字元。
    
4. 在 **How to Run** 方塊中，選擇 [ **視需要執行**。
    
3. 在 **檔案上傳** ，按一下資料夾圖示，然後瀏覽至包含您指令碼的 zip 檔案。 該 zip 檔案應包含您的可執行檔 (.exe .cmd .bat .sh .php .py .js)，以及執行程式或指令碼所需的任何支援檔案。
    
5. 檢查 **建立** 指令碼上傳至您的 web 應用程式。 
    
    您為 WebJob 的清單中會出現在指定的名稱 **WebJobs** 刀鋒視窗。
    
6. 若要執行 WebJob，以滑鼠右鍵按一下其名稱清單中的，按一下 **執行**。
    
    ![執行 WebJob](./media/web-sites-create-web-jobs/runondemand.png)
    
## <a name="CreateContinuous"></a>建立連續執行的 WebJob

1. 若要建立連續執行的 WebJob，請遵循相同的步驟建立的 web 工作會執行一次，但在 **How to Run** 方塊中，選擇 [ **連續**。

2. 若要啟動或停止連續執行，請在清單中的 WebJob 上按一下滑鼠右鍵，按一下 **啟動** 或 **停止**。
    
> [AZURE.NOTE] 如果 web 應用程式在多個執行個體上執行，連續執行的 WebJob 將會在所有執行個體上執行。 依需求和排定的 WebJob 會在 Microsoft Azure 為負載平衡而選取的單一執行個體上執行。
    
> 若要讓連續 WebJobs 能夠在所有執行個體上可靠地執行，請對 Web 應用程式啟用 [永遠開啟] 組態設定，否則當 SCM 主機網站閒置太久時，其可能會停止執行。

## <a name="CreateScheduledCRON"></a>建立排程的 WebJob 使用 CRON 運算式

這項技術可用於在標準或高階模式中執行的 Web 應用程式，並需要 **Always On** 啟用應用程式設定。

若要將依需求 WebJob 設成排定的 WebJob，只要在 WebJob zip 檔案的根目錄加入 `settings.job` 檔案。 此 JSON 檔案應該包含 `schedule` 屬性 [CRON 運算式](https://en.wikipedia.org/wiki/Cron), ，每個以下的範例。

CRON 運算式由 6 個欄位組成: `{second} {minute} {hour} {day} {month} {day of the week}`。

例如，若要每隔 15 分鐘觸發 WebJob，您的 `settings.job` 必須有：

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

其他的 CRON 排程範例：

- 每隔一小時 (也就是每當分鐘的計數是 0)：`0 0 * * * *` 
- 上午 9 點到下午 5 點之間每隔一小時：`0 0 9-17 * * *` 
- 每天上午 9:30：`0 30 9 * * *`
- 每個工作日上午 9:30：`0 30 9 * * 1-5`

**請注意**: 在部署 webjob 的形式從 Visual Studio，請務必將標示您 `settings.job` 檔案做為 '複本才' 屬性。


## <a name="CreateScheduled"></a>建立使用 Azure 排程器排定的 WebJob

以下的替代技術會使用 Azure 排程器。 在此情況下，您的 WebJob 對排程一無所知。 反而是 Azure 排程器會被設定為依排程觸發 WebJob。 

Azure 入口網站尚未具備建立排定的 WebJob 的能力，但之前加入該功能可以執行使用 [傳統入口網站](http://manage.windowsazure.com)。

1. 在 [傳統入口網站](http://manage.windowsazure.com) 移至 WebJob 頁面，然後按一下 [ **新增**。

1. 在 **How to Run** 方塊中，選擇 [ **依排程執行**。
    
    ![新增排程工作][NewScheduledJob]
    
2. 選擇 **排程器區域** 為您的工作，然後按一下右下方，前進到下一個畫面] 對話方塊上的箭號。

3. 在 **建立工作** ] 對話方塊中，選擇的型別 **循環** 想: **一次工作** 或 **週期性工作**。
    
    ![排定週期][SchdRecurrence]
    
4. 也選擇 **起始** 時間: **現在** 或 **在特定時間**。
    
    ![排定開始時間][SchdStart]
    
5. 如果您想要在特定時間開始，選擇 [開始時間值在 **開始於**。
    
    ![排定於特定時間開始][SchdStartOn]
    
6. 如果您選擇週期性工作，您必須 **重複每個** 選項來指定發生的頻率和 **結束於** 選項來指定結束時間。
    
    ![排定週期][SchdRecurEvery]
    
7. 如果您選擇 **週**, ，您可以選取 **On a Particular Schedule** 方塊並指定您想要執行的作業一周天數。
    
    ![排定工作日][SchdWeeksOnParticular]
    
8. 如果您選擇 **月** ，然後選取 **On a Particular Schedule** ] 方塊中，您可以設定執行特定作業的 **天** 每個月。 
    
    ![排定每個月特定的日期][SchdMonthsOnPartDays]
    
9. 如果您選擇 **工作日**, ，您可以在工作上執行的當月選取哪天或每週天數。
    
    ![排定每個月的特定工作天][SchdMonthsOnPartWeekDays]
    
10. 最後，您也可以使用 **出現** 選項來選擇每個月的哪一週 (第一次，第二、 第三等) 要讓您指定工作日執行工作。
    
    ![排定每個月特定週的特定工作天][SchdMonthsOnPartWeekDaysOccurences]
    
11. 在建立一或多個工作之後，這些工作的名稱會連同狀態、排程類型及其他資訊一併顯示在 [WebJobs ] 索引標籤中。 會保留最後 30 個 Webjob 的歷程資訊。
    
    ![工作清單][WebJobsListWithSeveralJobs]
    
### <a name="Scheduler"></a>排程的工作和 Azure 排程器

Azure 排程器頁面中可以進一步設定排程的工作 [傳統入口網站](http://manage.windowsazure.com)。

1.  在 [WebJobs] 頁面中，按一下 [工作的 **排程** 連結以瀏覽至 Azure 排程器入口網站頁面。 
    
    ![連結至 Azure 排程器][LinkToScheduler]
    
2. 在 [排程器] 頁面中按一下工作。
    
    ![[排程器] 入口網站頁面中的工作][SchedulerPortal]
    
3.  **工作動作** 頁面隨即開啟，可以進一步設定工作。 
    
    ![工作動作 PageInScheduler][JobActionPageInScheduler]
    
## <a name="ViewJobHistory"></a>檢視工作歷程記錄

1. 若要檢視工作，包括使用 WebJobs SDK 時，所建立的執行歷程記錄中，按一下 [對應的連結底下 **記錄** Webjob 刀鋒視窗的資料行。 (如果需要，您可以使用剪貼簿圖示將記錄檔頁面的 URL 複製到剪貼簿。)
    
    ![記錄連結](./media/web-sites-create-web-jobs/wjbladelogslink.png)
        
2. 按一下連結即可開啟 WebJob 的詳細資料頁面。 此頁面能顯示執行之命令的名稱、上次執行時間及成功或失敗。 在 **最近執行的工作**, ，按一下可查看進一步的詳細資料的時間。
    
    ![WebJobDetails][WebJobDetails]
    
3.  **WebJob 執行詳細資料** ] 頁面隨即出現。 按一下 [ **切換輸出** 若要查看記錄內容的文字。 輸出記錄將會是文字格式。 
    
    ![網站工作執行詳細資料][WebJobRunDetails]
    
4. 若要查看輸出文字，在另一個瀏覽器視窗中的，按一下 [ **下載** 連結。 若要下載文字本身，請以滑鼠右鍵按一下連結，然後使用瀏覽器選項來儲存檔案內容。
    
    ![下載記錄輸出][DownloadLogOutput]
    
5.  **WebJobs** 在頁面頂端的連結可讓您方便您前往 Webjob 的歷程記錄] 儀表板清單。
    
    ![連結至 WebJobs 清單][WebJobsLinkToDashboardList]
    
    ![歷程記錄儀表板中的 WebJobs 清單][WebJobsListInJobsDashboard]
    
    按一下這些連結中的任一連結可帶您前往選取之工作的 [WebJob 詳細資料] 頁面。


## <a name="WHPNotes"></a>注意事項
    
- 如果沒有對 scm (部署) 網站提出任何要求，則免費模式的 Web 應用程式可能會在 20 分鐘之後逾時，且 Web 應用程式在 Azure 中的入口網站也不會開啟。 對實際網站的要求將不會重設此項目。
- 連續工作的程式碼需經過撰寫，才能以無限迴圈的形式執行。
- 唯有當 Web 應用程式處於運作狀態時，連續工作才能連續執行。
- [基本] 和 [標準] 模式能提供「永遠開啟」功能，此功能在啟用後能預防 Web 應用程式進入閒置狀態。
- 您僅可偵錯連續執行的 WebJobs。 不支援偵錯排程或隨選的 WebJobs。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## <a name="NextSteps"></a>後續步驟
 
如需詳細資訊，請參閱 [Azure WebJobs 建議資源][WebJobsRecommendedResources]。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需舊入口網站變更為新入口網站的指南，請參閱: [瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
 

