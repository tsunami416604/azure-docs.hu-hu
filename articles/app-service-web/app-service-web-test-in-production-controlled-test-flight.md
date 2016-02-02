<properties
    pageTitle="Azure App Service 中的試驗部署 (beta 測試)"
    description="了解如何在此端對端教學課程中試驗應用程式中的新功能，或對您的更新進行 beta 測試。它整合了 App Service 功能，例如持續性發佈、位置、流量路由及 Application Insights 整合。"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2015"
    ms.author="cephalin"/>

# Azure App Service 中的試驗部署 (beta 測試)

本教學課程會說明如何執行 *flighting 部署* 藉由整合的各種功能 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 和 [Azure Application Insights](/services/application-insights/)。

*Flighting* 是部署程序可驗證的新功能或變更數量有限的真實的客戶，而是主要生產環境中所用的測試 
案例。 它類似於 beta 測試，有時也稱為「受控試驗」。 許多大型企業中的與網站空間使用這個方法 
取得早期驗證其應用程式的更新，在他們的練習 [靈活的開發](https://en.wikipedia.org/wiki/Agile_software_development)。 Azure 應用程式 
服務可讓您在生產環境中的測試整合連續發行和 Application Insights 加入至實作相同的 DevOps 案例。 優點 
這種方法包括:

- **獲得真正的意見反應 _before_ 更新會發行至生產環境** -比獲得意見反應，當您釋放好的作法就取得 
在發行前的意見反應。 您可以在產品生命週期中，依需求及早以實際的使用者流量和行為來測試更新。
- * * 增強 [連續測試導向開發 (CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development)* *-藉由整合測試中 
使用者驗證產品生活中使用連續整合的生產環境或使用 Application Insights 檢測方法，是早期和自動 
循環。 這有助於減少在手動測試執行上投入的時間。
- **最佳化測試工作流程** -透過自動化測試使用連續監視檢測生產環境中的，您可能可以完成的目標 
各種測試在單一處理，例如 [整合](https://en.wikipedia.org/wiki/Integration_testing),， 
[迴歸](https://en.wikipedia.org/wiki/Regression_testing), ，[可用性](https://en.wikipedia.org/wiki/Usability_testing), ，協助工具、 當地語系化， 
[performance](https://en.wikipedia.org/wiki/Software_performance_testing), [security](https://en.wikipedia.org/wiki/Security_testing), and 
[acceptance](https://en.wikipedia.org/wiki/Acceptance_testing).

試驗部署牽涉到的不只是路由傳送即時流量而已。 在這類部署中您想要儘快，深入了解它是否會發生非預期的問題 
效能降低，使用者經驗方面的問題。 切記，您面對的是實際的客戶。 因此若要這麼做，您必須確定您已設定 
flighting 部署，以收集您要做出明智的決定，您的下一個步驟所需要的所有資料。 本教學課程會示範如何收集資料 
使用 Application Insights，但是您可以使用 New Relic 或其他技術適合您的案例。

## 將執行的作業

在本教學課程中，您將了解如何整合下列案例，以在生產環境中測試您的 App Service 應用程式：

- [生產流量路由傳送](app-service-web-test-in-production-get-start.md) beta 版應用程式
- [檢測您的應用程式](app-insights-web-track-usage.md) 取得有用的度量
- 持續部署 beta 應用程式並追蹤即時應用程式計量
- 比較生產應用程式和 beta 應用程式之間的計量，以觀察程式碼變更的結果為何

## 必要元件

-   一個 Azure 帳戶
-   A [GitHub](https://github.com/) account
- 您可以下載 visual Studio 2015- [Community edition](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx)。
-   Git Shell (隨 [GitHub for Windows](https://windows.github.com/)) -這可讓您在相同的工作階段中執行 Git 和 PowerShell 命令
-   最新 [PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi) 位元
-   下列項目的基本了解：
    -   [Azure 資源管理員](resource-group-overview.md) 範本部署 (請參閱 [部署複雜應用程式如預期般在 Azure 中的](app-service-deploy-complex-application-predictably.md))
    -   [Git](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] 要完成此教學課程，您必須要有 Azure 帳戶：
> + 您可以 [免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F) -取得點數可用來試用付費 Azure 服務，並甚至用完後，您可以保留帳戶，並使用免費的 Azure 服務，例如 Web 應用程式。
> + 您可以 [啟用 Visual Studio 訂閱者權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) -您的 Visual Studio 訂閱提供您額度，您可以使用 Azure 付費服務的每個月。
>
> 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 設定生產 Web 應用程式

>[AZURE.NOTE] 本教學課程中使用的指令碼會自動從 GitHub 儲存機制設定連續發行。 這需要您的 GitHub 認證已儲存在 Azure 中，否則，嘗試設定 Web 應用程式的原始檔控制設定時，指令碼部署會失敗。
>
>若要在 Azure 中儲存您的 GitHub 認證，建立 [web 應用程式中的 [Azure 入口網站](https://portal.azure.com) 和 [設定 GitHub 部署](web-sites-publish-source-control.md#Step7)。 您只需要做一次這個動作。

在一般 DevOps 案例中，應用程式是在 Azure 中即時執行，而且您想要透過連續發行對它進行變更。 在此案例中，您會將已開發及測試的範本部署至生產環境。

1.  建立的專屬分叉 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 儲存機制。 如需建立分叉的詳細資訊，請參閱 [分岔儲存機制](https://help.github.com/articles/fork-a-repo/)。 建立分叉之後，即可在瀏覽器中進行查看。

    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  開啟 Git Shell 工作階段。 如果您沒有 Git Shell，安裝 [GitHub for Windows](https://windows.github.com/) 現在。
3.  執行下列命令，以建立分叉的本機複製品：

        git clone https://github.com/<your_fork>/ToDoApp.git

4.  您的本機副本之後，請瀏覽至 *< repository_root >*\ARMTemplates，並執行 deploy.ps1 指令碼為唯一的尾碼，如下所示:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.  系統提示時，請輸入所需的使用者名稱和密碼來存取資料庫。 請記住您的資料庫認證，因為在更新資源群組時將必須再次加以指定。

    您應該會看到各種 Azure 資源的佈建進度。 部署完成時，指令碼會在瀏覽器中啟動應用程式，並提供合適的嗶聲。
    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.  回到 Git Shell 工作階段，並執行：

        .\swap –Name ToDoApp<your_suffix>

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.  當指令碼完成時，請回到瀏覽至前端的位址 (http://ToDoApp*< your_suffix >*.azurewebsites.net/) 若要查看在生產環境中執行的應用程式。
5.  登入 [Azure 入口網站](https://portal.azure.com) 就來看看一下建立的內容。

    您應該能夠看到相同的資源群組，一個使用中的兩個 web 應用程式 `Api` 後置字元的名稱。 如果您查看資源群組檢視，則也會看到 SQL Database 和伺服器、App Service 方案以及 Web 應用程式的預備位置。 瀏覽不同的資源，並比較它們與 *< repository_root >*\ARMTemplates\ProdAndStage.json 若要查看在範本中的設定方式。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

您已設定生產應用程式。 現在，我們假設您收到應用程式可用性不佳的意見反應。 因此，您決定加以調查。 你要檢測應用程式，以做出回應。

## 調查：檢測用戶端應用程式的監視/計量

5. 開啟 *< repository_root >*\src\MultiChannelToDo.sln Visual Studio 中的。
6. 以滑鼠右鍵按一下解決方案 > [管理解決方案的 NuGet 套件]**** > [還原]****，以還原所有的 Nuget 套件。
6. 以滑鼠右鍵按一下 **MultiChannelToDo.Web** > **Add Application Insights Telemetry** > **設定設定** > 變更資源群組，以 ToDoApp*< your_suffix >* > **將 Application Insights 加入專案**。
7. 在 Azure 入口網站中，開啟 **MultiChannelToDo.Web** Application Insight 資源的刀鋒視窗。 接著，在 [應用程式健全狀況]**** 部分中，按一下 [了解如何收集瀏覽器頁面載入資料]**** > [複製程式碼]。
7. 將複製的 JS 檢測程式碼加入 *< repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml，前面 `< 標題 >` 標記。它應包含您的 Application Insight 資源的唯一檢測金鑰。

     <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
         function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
     }({
         instrumentationKey:"<your_unique_instrumentation_key>"
     });
    
     window.appInsights=appInsights;
     appInsights.trackPageView();
     </script>

11. 將下列程式碼新增至本文底部，以將點按滑鼠的自訂事件傳送至 Application Insights：

    <script>
        $(document.body).find("*").click(function(event) {
    
            appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
        });
    </script>

此 JavaScript 程式碼片段會在每次使用者按一下 Web 應用程式中的任何位置時，將自訂事件傳送至 Application Insights。

12. 在 Git Shell 中，認可您的變更並將其發送至您在 GitHub 中的分岔。 然後，等待用戶端重新整理瀏覽器。

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.  將已部署的應用程式變更交換至生產環境：

        .\swap –Name ToDoApp<your_suffix>

13. 瀏覽至您所設定的 Application Insights 資源。 按一下 [自訂事件]。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    如果您未看見自訂事件的計量，請等候數分鐘，然後按一下 [重新整理]****。

假設您看見如下的圖表：

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

以及其下的事件方格：

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

根據您的 ToDoApp 應用程式程式碼，**BUTTON** 事件會對應至提交按鈕，而 **INPUT** 事件會對應至文字方塊。 到目前為止都沒什麼問題。 不過，點按次數似乎很多，但點按待辦事項 (**LI** 事件) 的次數卻很少。

根據這一點，您假設有部分使用者搞不清楚 UI 的哪個部分是可點按的，其原因是游標停留在清單項目及其圖示上時，會呈現為文字選取的樣式。

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

您可能會認為此範例是人為的。 不過，您可以藉此改進應用程式，並執行試驗部署，以取得實際客戶提供的使用意見。

### 檢測伺服器應用程式的監視/計量

這有一點離題，因為本教學課程中示範的案例只處理用戶端應用程式。 不過，基於完整性，您會設定伺服器端應用程式。

6. 以滑鼠右鍵按一下 **MultiChannelToDo** > **Add Application Insights Telemetry** > **設定設定** > 變更資源群組，以 ToDoApp*< your_suffix >* > **將 Application Insights 加入專案**。
12. 在 Git Shell 中，認可您的變更並將其發送至您在 GitHub 中的分岔。 然後，等待用戶端重新整理瀏覽器。

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.  將已部署的應用程式變更交換至生產環境：

        .\swap –Name ToDoApp<your_suffix>


就這麼簡單！

## 調查：將位置特定標記新增至您的用戶端應用程式計量

在本節中，您會設定不同的部署位置，將位置特定遙測傳送至相同的 Application Insights 資源。 如此，您即可比較來自不同位置 (部署環境) 的流量之間的遙測資料，輕鬆地檢視應用程式變更的影響。 相時，您可以區隔生產流量與其他流量，以便在必要時繼續監視您的生產應用程式。

因為您要收集資料用戶端行為，您會 [將遙測初始設定式加入至 JavaScript 程式碼](app-insights-api-custom-events-metrics.md#js-initializer) index.cshtml 中。 如果您想要測試伺服器端效能，例如，您也可以執行同樣的伺服器程式碼中 (請參閱 [自訂事件和度量的 Application Insights API]((app-insights-api-custom-events-metrics.md))。

1. 首先，加入程式碼 bewteen 這兩個 `/ /` 在 JavaScript 中的註解下封鎖您已加入至 `< 標題 >` 標記前面。

     window.appInsights = appInsights;
    
     // Begin new code
     appInsights.queue.push(function () {
         appInsights.context.addTelemetryInitializer(function (envelope) {
             var telemetryItem = envelope.data.baseData;
             telemetryItem.properties = telemetryItem.properties || {};
             telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
         });
     });
     // End new code
    
     appInsights.trackPageView();

 此初始設定式程式碼會造成 `appInsights` 要加入物件自訂內容稱為 `環境` 到它所傳送的遙測的每個片段。

2. 接下來，加入這個自訂屬性做為 [位置設定](web-sites-staged-publishing.md#AboutConfiguration) web 應用程式在 Azure 中。 若要這樣做，請在 Git Shell 工作階段中執行下列命令。

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    已經定義一個專案中的 Web.config `環境` 應用程式設定。 使用此設定，當您測試應用程式在本機，您的度量會加上 `VS 偵錯工具`。 不過，當您將變更推送至 Azure，Azure 會尋找並使用 `環境` 相反地，在 web 應用程式的組態中設定的應用程式和您的度量會標記為 `生產`。

3. 認可您的程式碼變更，並將其發送至 GitHub 上的分岔，然後等候使用者使用新的應用程式 (需要重新整理瀏覽器)。 它需要大約 15 分鐘在 Application Insights 中顯示為新屬性 `MultiChannelToDo.Web` 資源。

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. 現在，請移至 **自訂事件** 一次] 刀鋒視窗，並篩選出度量 `環境 = 生產`。 透過此篩選，現在您應可看見生產位置中所有新的自訂事件。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. 按一下 [我的最愛]**** 按鈕，將目前的 [計量瀏覽器] 設定儲存至 [自訂事件：生產]**** 之類的項目。 後續您可以在此檢視與部署位置檢視之間輕鬆切換。
    > [AZURE.TIP] 對於更強大的分析，請考慮 [與 Power BI 整合的 Application Insights 資源](app-insights-export-power-bi.md)。

### 將位置特定標記新增至您的伺服器應用程式計量

同樣地，基於完整性，您會設定伺服器端應用程式。 不同於以 JavaScript 進行檢測的用戶端應用程式，伺服器應用程式的位置特定標記會以 .NET 程式碼進行檢測。

1. 開啟 *< repository_root >*\src\MultiChannelToDo\Global.asax.cs。 將下列程式碼區塊新增至命名空間右括號前面。

     namespace MultiChannelToDo
     {
             ...
    
             // Begin new code
         public class ConfigInitializer
         : ITelemetryInitializer
         {
             void ITelemetryInitializer.Initialize(ITelemetry telemetry)
             {
                 telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
             }
         }
             // End new code
     }

2. 名稱解析錯誤更正加 `使用` 下方的陳述式開頭的檔案:

        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;

3. 將下列程式碼加入至開頭 `application_start ()` 方法:

        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. 認可您的程式碼變更，並將其發送至 GitHub 上的分岔，然後等候使用者使用新的應用程式 (需要重新整理瀏覽器)。 它需要大約 15 分鐘在 Application Insights 中顯示為新屬性 `MultiChannelToDo` 資源。

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master


## 更新：設定您的 Beta 分支：

2. 開啟 *< repository_root >*\ARMTemplates\ProdAndStagetest.json 和尋找 `appsettings` 資源 (搜尋 `"name":"appsettings"`)。 資源共有 4 項，分別用於各個位置。

2. 每個 `appsettings` 資源，加入  `「 環境 」: 「 [parameters('slotName')]"` 應用程式設定為結尾 `屬性` 陣列。 別忘了在上一行結尾處使用逗號。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)

    您剛才加入 `環境` 應用程式設定為在範本中的所有位置。

2. 在相同檔案中，尋找 `slotconfignames` 資源 (搜尋 `"name":"slotconfignames"`)。 資源共有 2 項，分別用於各個應用程式。

2. 每個 `slotconfignames` 資源，加入 `「 環境 」` 結尾 `appSettingNames` 陣列。 別忘了在上一行結尾處使用逗號。

    您剛才 `環境` 隨身碟設為兩個應用程式及其各自的部署位置的應用程式。

3. 在您的 Git Shell 工作階段中執行下列命令，其中具有您先前使用的相同資源群組後置詞。

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. 出現提示時，請指定與先前相同的 SQL 資料庫認證。 當系統要求您更新的資源群組，然後輸入 `Y`, ，然後 `ENTER`。

    指令碼完成後，原始資源群組中的所有資源都會保留下來，但會在其中建立名為 "beta" 的新位置，且其組態會與一開始建立的「預備」位置相同。
    >[AZURE.NOTE] 這個方法建立不同的部署環境的是不同的方法中 [敏捷式軟體開發使用 Azure App Service](app-service-agile-software-development.md)。 使用此方法時，您會建立具有部署位置的部署環境，而使用該方法時，則建立具有資源群組的部署環境。 使用資源群組來管理部署環境，可讓您將開發人員排除在生產環境以外，但不易在生產環境中進行測試，而這一點卻可以透過位置輕易辦到。

如果需要，也可以藉由下列程式碼建立 alpha 應用程式

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

在本教學課程，只要繼續使用 beta 應用程式即可。

## 更新：將您的更新發送至 beta 應用程式

回到您想要改善的應用程式。

1. 確定您此時位於 beta 分支中

        git checkout beta

2. 在 *< repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml，尋找 `< l i >` 標記，並新增 `樣式 = 資料指標: 指標 」` 屬性，如下所示。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. 認可並發送至 Azure。

4. 請確認該變更會立即反映 beta 插槽中瀏覽至 http://todoapp*< your_suffix >*-beta.azurewebsites.net/。 如果未看見變更，請重新整理瀏覽器以取得新的 javascript 程式碼。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

現在，您的變更已在 beta 位置中執行，因此已可執行試驗部署。

## 驗證：將流量路由傳送至 beta 應用程式

在本節中，您要將流量路由傳送至 beta 應用程式。 為了能夠清楚示範，請您將使用者流量的重要部分路由傳送至該應用程式。 實際上，您將路由傳送的流量會取決於您的特定情況。 例如，如果您的網站屬於 microsoft.com 層級，則您可能只需要不到 1% 總流量即可取得有用的資料。

1. 在 Git Shell 工作階段中執行下列命令，將半數的生產流量路由傳送至 beta 位置：

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  `ReroutePercentage = 50` 屬性會指定 50%的生產流量將會路由至 beta 版應用程式的 URL (所指定 `ActionHostName` 屬性)。

2. 現在瀏覽到 http://ToDoApp*< your_suffix >*。 azurewebsites.net。 50% 的流量現在應該會重新導向至 beta 位置。

3. 在您的 Application Insights 資源中，以 environment="beta" 來篩選計量。
    > [AZURE.NOTE] 如果您將這個篩選的檢視另存為我的最愛，您將可在生產與 beta 檢視之間輕鬆切換計量總管檢視。

假設您在 Application Insights 中看見如下的內容：

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

這不只顯示上有許多需按下滑鼠 `< l i >` 標記，但似乎的點按劇上 `< l i >` 標記。您接著可以推斷人發現新 `< l i >` 點選標記，並會立即清除所有先前已完成工作的應用程式中。

根據您試驗部署的資料，您認定新的 UI 已可用於生產環境。

## 實作：將新的程式碼移至生產環境

您現在已可將更新移至生產環境。 最棒的是，現在您知道您的更新在發送至生產環境_之前_已經過驗證。 因此，現在您可以安心地加以部署。 由於您是對 AngularJS 用戶端應用程式進行更新，因此只有用戶端程式碼受到驗證。 如果您要對後端 Web API 應用程式進行變更，您可以用同樣的方式輕鬆驗證變更。

1. 在 Git Shell 中執行下列命令，以移除流量路由規則：

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. 執行 Git 命令：

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. 等候幾分鐘，讓新的程式碼部署到預備位置，然後啟動 http://ToDoApp*< your_suffix >*-staging.azurewebsites.net 確認新的更新會接手預備位置中。 請記住，分叉的主要分支會連結至應用程式的預備位置。

3. 現在，將預備位置交換到生產環境中

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>


## 摘要

Azure App Service 可讓中小型企業輕鬆地在生產環境中測試其客戶端應用程式，這在過去只能在大型企業中執行。 希望本教學課程所提供的資訊可協助您結合 App Service 和 Application Insights 以順利進行試驗部署，甚或您在營運開發領域中的其他生產測試。

## 其他資源

-   [使用 Azure App Service 的敏捷式軟體開發](app-service-agile-software-development.md)
-   [設定預備環境，在 Azure App Service web 應用程式](web-sites-staged-publishing.md)
-   [將複雜的應用程式如預期般在 Azure 中部署](app-service-deploy-complex-application-predictably.md)
-   [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)
-   [JSONLint-JSON 驗證程式](http://jsonlint.com/)
-   [Git 分支-基本分支和合併](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [Azure PowerShell](powershell-install-configure.md)
-   [專案 Kudu Wiki](https://github.com/projectkudu/kudu/wiki)





