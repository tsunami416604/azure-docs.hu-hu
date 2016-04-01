<properties 
    pageTitle="使用 Visual Studio 部署 WebJob" 
    description="了解如何使用 Visual Studio，將 Azure WebJob 部署至 Azure App Service Web Apps。" 
    services="app-service" 
    documentationCenter="" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="na" 
    ms.date="09/22/2015" 
    ms.author="tdykstra"/>

# 使用 Visual Studio 部署 WebJob

## 概觀

本主題說明如何使用 Visual Studio 主控台應用程式專案部署至 web 應用程式中 [應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714) 為 [Azure WebJob](http://go.microsoft.com/fwlink/?LinkId=390226)。 如需如何使用部署 WebJobs [Azure 入口網站](https://portal.azure.com), ，請參閱 [使用 WebJobs 執行背景工作](web-sites-create-web-jobs.md)。

當 Visual Studio 部署具有 WebJobs 功能的主控台應用程式專案時，它會執行兩個工作：

* 將執行階段檔案複製到 web 應用程式中的適當資料夾 (*App_Data/工作/continuous* 用於連續 WebJobs， *App_Data/工作/triggered* 的排程和隨選 WebJobs)。
* 設定 [Azure 排程器工作](#scheduler) 的已排定在特定時間執行的 WebJobs。 (無需為連續 WebJobs 執行此動作。)

具有 WebJobs 功能的專案會新增下列項目：

*  [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝。
* A [webjob 發行 settings.json](#publishsettings) 包含部署和排程器設定的檔案。 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

您可以將這些項目新增至現有的主控台應用程式專案，或使用範本建立具有 WebJobs 功能的新主控台應用程式專案。 

以 WebJob 的方式自我部署專案，或將專案連結到 Web 專案，因此每當您要部署 Web 專案時，專案便會自動部署。 若要連結專案，Visual Studio 會包含具有 WebJobs 功能的專案名稱 [webjobs-list.json](#webjobslist) web 專案中的檔案。

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)
 


## 必要條件

安裝 Azure SDK 2.4 版或更新版本後，您便可在 Visual Studio 2013 中使用 WebJobs 部署功能：

* [Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)。

也會包含 WebJobs 部署功能 [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) 和更新版本的更新。

## <a id="convert"></a>啟用現有主控台應用程式專案的 WebJobs 部署

您有兩個選擇：

* [透過 web 專案啟用自動部署](#convertlink)。

    設定現有主控台應用程式專案，以便在您部署 Web 專案時，它會以 WebJob 的方式自動部署。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

* [不透過 web 專案啟用部署](#convertnolink)。

    設定現有主控台應用程式專案以 WebJob 的方式自我部署，且不具 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。

### <a id="convertlink"></a> 透過 Web 專案啟用自動 WebJobs 部署
  
1. 以滑鼠右鍵按一下 web 專案中的 **方案總管] 中**, ，然後按一下 [ **新增** > **Existing Project as Azure WebJob**。

    ![Existing Project as Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
    
     [Add Azure WebJob](#configure) ] 對話方塊隨即出現。

1. 在 **專案名稱** 下拉式清單中，選取要新增為 WebJob 的主控台應用程式專案。

    ![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. 完成 [Add Azure WebJob](#configure) ] 對話方塊中，然後再按一下 **確定**。 

### <a id="convertnolink"></a> 不透過 Web 專案啟用 WebJobs 部署
  
1. 以滑鼠右鍵按一下 [主控台應用程式專案中的 **方案總管] 中**, ，然後按一下 [ **Publish as Azure WebJob**。 

    ![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
    
     [Add Azure WebJob](#configure) ] 對話方塊隨即出現，而且在選取的專案 **專案名稱** 方塊。

2.  完成 [Add Azure WebJob](#configure) ] 對話方塊中，然後按一下 **確定**。

     **發行 Web** 精靈] 隨即出現。  如果您不打算立即發行，請關閉精靈。 當您真的想您所輸入的設定會儲存供 [部署專案](#deploy)。

## <a id="create"></a>建立具有 WebJobs 功能的新專案

若要建立新專案，您可以使用主控台應用程式專案範本，並在中所述來啟用 WebJobs 部署 [上一節](#convert)。 另一種方式是，您可以使用 WebJobs 新專案範本：

* [在獨立的 WebJob 中使用 WebJobs 新專案範本](#createnolink)

    建立專案，並將專案設定為以 WebJob 的方式自我部署，且不具 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。

* [在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本](#createlink)

    建立專案，並設定在針對位於相同解決方案中的 Web 專案進行部署時，會自動以 WebJob 的方式部署此專案。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

在 SDK 2.4 版本中，WebJobs 新專案範本並不會比建立主控台應用程式專案並啟用 WebJobs 部署來得容易。 日後，WebJobs 新專案範本將會更方便 [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) 開發，因為它會自動安裝適當的 WebJobs SDK NuGet 封裝。 在那之前，您可以設定專案使用 WebJobs SDK 中所示的手動安裝封裝 [WebJobs SDK 教學課程](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)。 


### <a id="createnolink"></a> 在獨立的 WebJob 中使用 WebJobs 新專案範本
  
1. 按一下 [ **檔案** > **新的專案**, ，然後在 **新的專案** 對話方塊中，按一下 **定域機組** > **Microsoft Azure WebJob**。

    ![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
    
2. 請依照稍早所示的指示 [主控台應用程式專案設定成獨立的 WebJobs 專案](#convertnolink)。

### <a id="createlink"></a> 在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本

1. 以滑鼠右鍵按一下 web 專案中的 **方案總管] 中**, ，然後按一下 [ **新增** > **New Azure WebJob Project**。

    ![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)

     [Add Azure WebJob](#configure) ] 對話方塊隨即出現。

2. 完成 [Add Azure WebJob](#configure) ] 對話方塊中，然後按一下 **確定**。

## <a id="configure"></a>[新增 Azure WebJob] 對話方塊

 **Add Azure WebJob** 對話方塊可讓您輸入 WebJob 的名稱和排程設定。 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

在此對話方塊中的欄位對應到欄位 **新工作** 的 Azure 入口網站] 對話方塊。 如需詳細資訊，請參閱 [使用 WebJobs 執行背景工作](web-sites-create-web-jobs.md)。

若是排程 WebJob （不適用於連續 WebJobs)，Visual Studio 會建立 [Azure 排程器](/services/scheduler/) 工作集合，如果其中一個不存在，而且在集合中建立工作 ︰

* 排程器工作集合名為 *WebJobs-{regionname}* 其中 *{regionname}* 指的是 web 應用程式中裝載的區域。 例如：WebJobs-WestUS。
* 名為排程器工作 *{webappname}-{webjobname}*。 例如：MyWebApp-MyWebJob。 
 
>[AZURE.NOTE]
> 
>* 如需命令列部署的相關資訊，請參閱 [啟用命令列或連續傳遞 Azure WebJobs 的](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)。
>* 如果您設定 **週期性工作** 並將週期頻率設為分鐘數，Azure 排程器服務不是免費。 其他頻率 (時數、天數等) 可免費提供。
>* 如果您部署了 WebJob，但之後決定變更 WebJob 的類型並重新部署，就必須刪除 webjobs-publish-settings.json 檔案。 這樣會讓 Visual Studio 再次顯示發佈選項，您才能夠變更 WebJob 的類型。
>* 如果部署 WebJob，並在稍後將執行模式從連續變更為非連續 (或相反情形)，則在您重新部署時，Visual Studio 會在 Azure 中建立新的 WebJob。 如果您變更其他排程設定但保持執行模式不變，或在排程和隨選模式之間切換，則 Visual Studio 會更新現有的工作，而非建立新的工作。

## <a id="publishsettings"></a>webjob-publish-settings.json

當您設定 WebJobs 部署的主控台應用程式時，Visual Studio 會安裝 [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝 
及排程資訊儲存區 *webjob 發行 settings.json* 專案檔案中的 *屬性* WebJobs 專案的資料夾。 以下是該檔案的範例：

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。 檔案結構描述會儲存在 [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) 和可在該處檢視。  

>[AZURE.NOTE]
>
>* 如果您設定 **週期性工作** 並將週期頻率設為分鐘數，Azure 排程器服務不是免費。 其他頻率 (時數、天數等) 可免費提供。

## <a id="webjobslist"></a>webjobs-list.json

當您將具有 WebJobs 功能的專案連結至 web 專案時，Visual Studio 會儲存在 WebJobs 專案的名稱 *webjobs-list.json* web 專案檔中的 *屬性* 資料夾。 此清單可能包含多個 WebJobs 專案，如以下範例所示：

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。 檔案結構描述會儲存在 [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) 和可在該處檢視。
  
## <a id="deploy"></a>部署 WebJobs 專案

已連結到 Web 專案的 WebJobs 專案會透過 Web 專案自動部署。 如需 web 專案部署的相關資訊，請參閱 [如何部署至 Web Apps](web-sites-deploy.md)。

若要自我部署 WebJobs 專案，以滑鼠右鍵按一下專案中的 **方案總管] 中**, ，然後按一下 **Publish as Azure WebJob**。 

![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
    
在獨立的 WebJob，相同 **發行 Web** web 專案隨即出現，但其中幾個設定可以變更使用的精靈。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## <a id="nextsteps"></a>後續步驟

本文說明如何使用 Visual Studio 部署 WebJobs。 如需有關如何部署 Azure WebJobs 的 Visual Studio 使用連續傳遞程序的資訊，請參閱 [Azure WebJobs-建議資源-部署](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying)。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)


