<properties
    pageTitle="透過可預測方式在 Azure 中佈建和部署微服務"
    description="學習如何在 Azure App Service 中將包含微服務的應用程式佈建和部署為單一單位，並且使用 JSON 資源群組範本和 PowerShell 指令碼為可預測的方式。"
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2015"
    ms.author="cephalin"/>



# 透過可預測方式在 Azure 中佈建和部署微服務

本教學課程示範如何佈建和部署應用程式組成 [微服務](https://en.wikipedia.org/wiki/Microservices) 中 [Azure App Service](/services/app-service/) 當做單一單位，並使用 JSON 資源群組範本和 PowerShell 指令碼的可預測方式。

佈建和部署包含高低耦合微服務的高級別應用程式時，重複性和可預測性是成功的重要關鍵。 [Azure App Service](/services/app-service/) 可讓您建立微服務，包括 web 應用程式、 行動應用程式、 API 應用程式，以及邏輯應用程式。 [Azure 資源管理員](../resource-group-overview.md) 可讓您管理所有微服務視為一個單位，以及資源相依性，例如資料庫和原始檔控制設定。 現在，您也可以使用 JSON 範本和簡單 PowerShell 指令碼來部署這類應用程式。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 將執行的作業

在教學課程中，您將部署的應用程式包括：

-   兩個 Web 應用程式 (即兩個微服務)
-   後端 SQL Database
-   應用程式設定、連接字串和原始檔控制
-   Application insights、警示、自動調整設定

## 將使用的工具

在本教學課程中，您將使用下列工具。 這不是工具的完整討論，因此將著重在端對端案例，並且只對每個工具進行簡短介紹，以及您可以在哪裡找到更多資訊。

### Azure 資源管理員範本 (JSON)

例如，每次在 Azure App Service 中建立 Web 應用程式時，Azure 資源管理員都會使用 JSON 範本來建立具有元件資源的整個資源群組。 複雜的範本，從 [Marketplace<](/marketplace) 像 [Scalable WordPress](/marketplace/partners/wordpress/scalablewordpress/) 應用程式可以包括 MySQL 資料庫、 儲存體帳戶、 應用程式服務方案、 web 應用程式本身、 警示規則、 應用程式設定、 自動調整設定等等，而且您可以透過 PowerShell 使用所有這些範本。 如需如何下載和使用這些範本，請參閱 [使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。

如需有關 Azure 資源管理員範本的詳細資訊，請參閱 [編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)

### Azure SDK 2.6 for Visual Studio

最新 SDK 已增強 JSON 編輯器中的資源管理員範本支援。 您可以使用此項快速從頭開始建立資源群組範本，或開啟現有 JSON 範本 (例如已下載的資源庫範本) 進行修改、填入參數檔案，甚至直接從 Azure 資源群組方案部署資源群組。

如需詳細資訊，請參閱 [Azure SDK 2.6 for Visual Studio](/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/)。

### Azure PowerShell 0.8.0 或更新版本

自 0.8.0 版開始，Azure PowerShell 安裝除了 Azure 模組之外還包括 Azure 資源管理員模組。 這個新模組可讓您編寫資源群組部署的指令碼。

如需詳細資訊，請參閱 [使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)

### Azure 資源總管

這 [預覽工具](https://resources.azure.com) 可讓您瀏覽您的訂閱與個別資源中的所有資源群組的 JSON 定義。 在此工具中，您可以編輯資源的 JSON 定義、刪除整個階層的資源，以及建立新的資源。 此工具中立即可用的資訊對於範本撰寫十分有用，因為它會顯示您需要針對特定類型的資源所設定的屬性、正確值等。您甚至可以建立資源群組中的 [Azure 入口網站](https://portal.azure.com), ，然後檢查其 JSON 定義，在 [總管] 工具，可協助您建立資源群組的樣本。

### 部署至 Azure 按鈕

如果您使用 GitHub 進行原始檔控制時，您可以將 [部署至 Azure 按鈕](/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) 到您的讀我檔案。MD，啟用的轉換金鑰部署至 Azure 的 UI。 雖然您可以針對任何簡單 Web 應用程式執行這項作業，但是您可以將 azuredeploy.json 檔案放在儲存機制根目錄中，以擴充此項來啟用部署整個資源群組。 此 JSON 檔案包含資源群組範本，將供 [部署至 Azure] 按鈕用來建立資源群組。 如需範例，請參閱 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 範例中，您將在本教學課程中使用。

## 取得範例資源群組範本

因此，現在讓我們著手進行。

1.  瀏覽至 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service 範例。

2.   在 readme.md 中，按一下 [**部署至 Azure**]。

3.  您會進入 [部署至 azure](https://deploy.azure.com) 站台，並要求您輸入部署參數。 請注意，大部分的欄位都會填入儲存機制名稱以及一些隨機字串。 您可以視需要變更所有欄位，但唯一必須輸入的項目是 SQL Server 管理登入和密碼，然後按 [**下一步**]。

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)

4.  接著，按一下 [**部署**] 啟動部署程序。 處理程序執行之後完成，請按一下 [http://todoapp*XXXX*。 azure.websites.net 連結瀏覽已部署的應用程式。

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)

    在您第一次瀏覽至 UI 時，UI 會比較慢，因為應用程式剛剛啟動，但確信它是功能完整的應用程式。

5.  回到 [部署] 頁面，並按一下 [管理]**** 連結，以在 Azure 入口網站中查看新的應用程式。

6.  在 [**Essentials**] 下拉式清單中，按一下 [資源群組] 連結。 也請注意 Web 應用程式已連線至 [**外部專案**] 下的 GitHub 儲存機制。

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)

7.  在資源群組刀鋒視窗中，請注意，資源群組中已經有兩個 Web 應用程式和一個 SQL Database。

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

剛剛您在短短幾分鐘內看到的所有項目都是完整部署的雙微服務應用程式，以及所有元件、相依性、設定、資料庫和連續發行 (由 Azure 資源管理員中的自動化協調流程所設定)。 這項作業是透過兩件事完成：

-   [部署至 Azure] 按鈕
-   儲存機制根目錄中的 azuredeploy.json

您可以部署這個相同的應用程式數十次、數百次或數千次，而且每次的組態都完全相同。 這種方法的重複性和可預測性可讓您輕鬆並自信地部署高延展性應用程式。

## 檢查 (或編輯) AZUREDEPLOY.JSON

現在，讓我們看看已如何設定 GitHub 儲存機制。 您將在 Azure.NET SDK 中，使用 JSON 編輯器，如果您尚未安裝 [Azure.NET SDK 2.6](/downloads/), ，現在進行此動作。

1.  複製 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 使用慣用的 git 工具的儲存機制。 在下面的螢幕擷取畫面中，我將在 Visual Studio 2013 的 Team Explorer 中中進行這項作業。

    ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)

2.  在 Visual Studio 中，從儲存機制根目錄中開啟 azuredeploy.json。 如果您看不到 [JSON 大綱] 窗格，則需要安裝 Azure .NET SDK。

    ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

我不打算詳述 JSON 格式，但是 [其他資源](#resources) 小節具有了解資源群組範本語言的連結。 在這裡，我只會說明可協助您開始建立專屬自訂範本以進行應用程式部署的有趣功能。

### 參數

查看 parameters 區段，了解大部分這些參數都是 [**部署至 Azure**] 按鈕提示您輸入的參數。 [**部署至 Azure**] 按鈕後面的網站會使用 azuredeploy.json 中所定義的參數來填入輸入 UI。 這些參數用於整個資源定義 (例如資源名稱、屬性值等)。

### 資源

在 resources 節點中，您可以看到已定義 4 個最上層資源，包括 SQL Server 執行個體、App Service 方案和兩個 Web 應用程式。

#### App Service 方案

讓我們從 JSON 中的簡單根層級資源開始。 在 [JSON 大綱] 中，按一下名為 **[hostingPlanName]** 的 App Service 方案反白顯示對應的 JSON 程式碼。

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

請注意， `類型` 項目指定 App Service 方案 (它的伺服器陣列久以前稱為)、 字串和其他項目和屬性會填滿使用的 JSON 檔案中定義的參數及此資源不會有任何巢狀的資源。
>[AZURE.NOTE] 也請注意，值 `apiVersion` 告訴的 Azure 哪一個版本的 REST API 以使用 JSON 資源定義，而且它可能會影響資源內的格式化 `{}`。 

#### SQL Server

接下來，按一下 [JSON 大綱] 中名為 **SQLServer** 的 SQL Server 資源。

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

請注意反白顯示 JSON 程式碼的下列資訊：

-   參數的使用確保透過讓所建立的資源彼此一致的方式來命名和設定所建立的資源。
-   SQLServer 資源有兩個巢狀的資源，其各有不同的值給 `類型`。
-   內的巢狀的資源 `「 資源 」: [...]`, ，其中已定義資料庫和防火牆規則， `dependsOn` 項目，指定根層級 SQLServer 資源的資源識別碼。 這會告訴 Azure 資源管理員：「建立此資源之前，那個其他資源必須已經存在；如果那個其他資源定義在範本中，則請先建立它」。
    >[AZURE.NOTE] 如需如何使用詳細資訊 `resourceId()` 函數，請參閱 [Azure 資源管理員範本函數](../resource-group-template-functions.md)。

-   效果 `dependsOn` 項目是 Azure 資源管理員可以知道可平行建立哪些資源，以及必須依序建立哪些資源。

#### Web 應用程式

現在，讓我們繼續討論更為複雜的實際 Web 應用程式本身。 按一下 [ JSON 大綱] 中的 [variables(‘apiSiteName’)] Web 應用程式，以反白顯示其 JSON 程式碼。 您會發現事情變得越來越有趣。 因此，我將逐一討論所有功能：

##### 根資源

Web 應用程式與兩個不同的資源相依。 這表示只有在建立 App Service 方案和 SQL Server 執行個體之後，Azure 資源管理員才會建立 Web 應用程式。

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### 應用程式設定

應用程式設定也會定義為巢狀資源。

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

在 `屬性` 項目 `config/appsettings`, ，您會有兩個應用程式設定格式的 `「 < 名稱 > 」: 「 < 值 > 」`。

-   `專案` 是 [KUDU 設定](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) ，告訴 Azure 部署在多專案 Visual Studio 方案中使用哪個專案。 稍後會示範如何設定原始檔控制，但是，因為 ToDoApp 程式碼是在多專案 Visual Studio 方案中，所以我們需要這項設定。
-   `clientUrl` 是只要使用應用程式設定的應用程式程式碼。

##### 連接字串

連接字串也會定義為巢狀資源。

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

在 `屬性` 項目 `config/connectionstrings`, ，每個連接字串也會定義成名稱: 值配對，並且具有特定格式 `「 < 名稱 > 」: {"value":"..."、"type":"..."}`。如 `類型` 項目，可能的值為 `MySql`, ，`SQLServer`, ，`SQLAzure`, ，和 `自訂`。
>[AZURE.TIP] 連接字串型別的限定清單，請在 Azure PowerShell 中執行下列命令:
    \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")

##### 原始檔控制

原始檔控制也會定義為巢狀資源。 Azure 資源管理員會使用這項資源來設定持續發佈 (請參閱警告 `IsManualIntegration` 稍後)，也開始 JSON 檔案的處理期間自動部署的應用程式程式碼。

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` 和 `分支` 應該相當具直覺式，而且應該指向 Git 儲存機制以及從發行分支的名稱。 同樣地，這些是透過輸入參數所定義。

請注意，在 `dependsOn` 項目，除了 web 應用程式資源本身， `sourcecontrols /web` 也取決於 `config/appsettings` 和 `config/connectionstrings`。 這是因為一旦 `sourcecontrols /web` 是設定，Azure 部署程序將會自動嘗試部署、 建置和啟動應用程式程式碼。 因此，插入此相依性可協助您確保應用程式在執行應用程式程式碼之前，會有存取必要的應用程式設定和連接字串。 [TODO: 需要確認這是否為 true。]
>[AZURE.NOTE] 也請注意， `IsManualIntegration` 設為 `true`。 這個屬性是在本教學課程所需，因為您實際上未擁有 GitHub 儲存機制，因此無法實際權限授與設定持續發佈，從 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (也就是自動儲存機制更新推送至 Azure)。 您可以使用預設值 `false` 所指定的儲存機制設定中的擁有者的 GitHub 認證時，才 [Azure 預覽入口網站](https://portal.azure.com) 之前。 換句話說，如果您已設定 GitHub 或 BitBucket 中任何應用程式的原始檔控制 [Azure 入口網站](https://portal.azure.com) 之前，使用您的使用者認證，則 Azure 會記住認證，並在未來部署從 GitHub 或 BitBucket 的任何應用程式時使用它們。 不過，如果您還沒有這麼做，則 Azure 資源管理員嘗試設定 Web 應用程式的原始檔控制設定時，JSON 範本的部署會失敗。因為它無法使用儲存機制擁有者的認證登入 GitHub 或 BitBucket。

## 比較具有已部署資源群組的 JSON 範本

在這裡，您可以瀏覽中的所有 web 應用程式的刀鋒視窗 [Azure 入口網站](https://portal.azure.com), ，但還有另一個工具十分有用，不多。 移至 [Azure 資源總管](https://resources.azure.com) 預覽工具，可提供您的所有資源群組的 JSON 表示法中您的訂閱，因為它們實際存在於 Azure 後端。 您也可以看到 Azure 中資源群組的 JSON 階層如何與用來建立它的範本檔案中的階層對應。

例如，當我移至 [Azure 資源總管](https://resources.azure.com) 工具並展開 [檔案總管] 中的節點，可以看到資源群組以及收集在其個別資源型別下方的根層級資源。

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

如果您向下鑽研至 Web 應用程式，則應該可以看到與下面螢幕擷取畫面類似的 Web 應用程式組態詳細資料：

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

同樣地，巢狀資源的階層應該與 JSON 範本檔案中的階層極為類似，而且您應該會看到 JSON 窗格中正確反映的應用程式設定、連接字串等。 沒有這裡的設定可能表示 JSON 檔案發生問題，並可協助您疑難排解 JSON 範本檔案。

## 自行部署資源群組範本

[**部署至 Azure**] 按鈕不錯，但是，只有在已將 azuredeploy.json 推送至 GitHub 時，它才可讓您在 azuredeploy.json 中部署資源群組範本。 Azure .NET SDK 也提供工具，讓您直接從本機電腦部署任何 JSON 範本檔案。 若要這樣做，請遵循下面的步驟：

1.  在 Visual Studio 中，按一下 [**檔案**] > [**新增**] > [**專案**]。

2.  按一下 [**Visual C#**] > [**雲端**] > [**Azure 資源群組**]，然後按一下 [**確定**]。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)

3.  在 [**Select Azure 範本**] 中，選取 [**空白範本**]，然後按一下 [**確定**]。

4.  將 azuredeploy.json 拖曳至新專案的 [**樣本**] 資料夾。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)

5.  從 [方案總管]，開啟複製的 azuredeploy.json。

6.  按一下 [**加入資源**]，將一些標準 Application Insight 資源加入 JSON 檔案，但此作業僅供示範之用。 如果您只想部署 JSON 檔案，請跳至部署步驟。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)

7.  選取 [**Web Apps 的 Application Insights**]，並確定已選取現有 App Service 方案和 Web 應用程式，然後按一下 [**新增**]。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)

    您現在可以看到數個新資源 (根據資源和其作用而定) 與 App Service 方案或 Web 應用程式相依。 這些資源不是透過其現有定義所啟用，而您將變更這項行為。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)

8.  在 [JSON 大綱] 中，按一下 **appInsights AutoScale** 反白顯示其 JSON 程式碼。 這是您 App Service 方案的調整設定。

9.  在 [反白顯示的 JSON 程式碼，找出 `位置` 和 `啟用` 屬性並設定它們，如下所示。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)

10. 在 [JSON 大綱] 中，按一下 **CPUHigh appInsights** 反白顯示其 JSON 程式碼。 這是警示。

11. 找出 `位置` 和 `isEnabled` 屬性並設定它們，如下所示。 請對其他三個警示 (紫色燈泡) 執行相同的動作。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)

12. 您現在可以開始進行部署。 以滑鼠右鍵按一下專案，然後選取 [**部署**] > [**新增部署**]。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)

13. 登入 Azure 帳戶 (如果您尚未這樣做)。

14. 選取您訂用帳戶中的現有資源群組，或建立新的資源群組，並選取 **azuredeploy.json**，然後按一下 [**編輯參數**]。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)

    您現在可以透過好用的資料表編輯範本檔案中定義的所有參數。 定義預設值的參數已經有其預設值，而定義允許值清單的參數則會顯示為下拉式清單。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)

15. 填入所有空的參數，並使用 [GitHub address for ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) 中 **repoUrl**。 然後按一下 [**儲存**]。

    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    >[AZURE.NOTE] 自動調整是**標準**層級或更高層級所提供的功能，而計劃層級警示是**基本**層級或更高層級所提供的功能，您需要將 **sku** 參數設為**標準**或**進階**，才能看到所有新的 App Insights 資源都已啟動。

16. 按一下 [**部署**]。 如果您選取 [**儲存密碼**]，則會將密碼**以純文字**形式儲存至參數檔。 否則，系統會在部署過程要求您輸入資料庫密碼。

這樣就大功告成了！ 現在，您只需要移至 [Azure 入口網站](https://portal.azure.com) 和 [Azure 資源總管](https://resources.azure.com) 應用程式部署工具來檢查新的警示及加入 JSON 的自動調整設定。

您在本節中的步驟主要是完成下列作業：

1.  準備範本檔案
2.  建立要與範本檔案一起使用的參數檔案
3.  部署具有參數檔案的範本檔案

最後一個步驟是透過 PowerShell Cmdlet 輕鬆完成。 若要查看 Visual Studio 在部署您的應用程式時所執行的作業，請開啟 Scripts\Deploy-AzureResourceGroup.ps1。 在該處有很多程式碼，但我只想要討論部署具有參數檔案之範本檔案所需的所有相關程式碼。

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

最後一個指令程式， `New-azureresourcegroup`, ，就是實際執行動作。 所有這些動作都是在告訴您，在工具的協助下，透過可預測方式部署您的雲端應用程式相當簡單。 每次對具有相同參數檔案的相同範本執行 Cmdlet 時，結果都會相同。

## 摘要

在 DevOps 中，重複性和可預測性是任何成功部署包含微服務之高級別應用程式的關鍵。 在本教學課程中，您已使用 Azure 資源管理員範本將雙微服務應用程式部署至 Azure 以做為單一資源群組。 希望這可讓您了解如何開始將 Azure 中的應用程式轉換成範本，而且可以透過可預測方式進行佈建和部署。

## 後續步驟

了解如何 [套用敏捷式方法與連續發行微服務應用程式輕鬆地](app-service-agile-software-development.md) 和進階部署技術，如 [flighting 部署](app-service-web-test-in-production-controlled-test-flight.md) 輕鬆。

<a name="resources"></a>
## 其他資源

-   [Azure 資源管理員範本語言](../resource-group-authoring-templates.md)
-   [編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)
-   [Azure 資源管理員範本函數](../resource-group-template-functions.md)
-   [部署應用程式使用 Azure 資源管理員範本](../resource-group-template-deploy.md)
-   [使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)
-   [在 Azure 中的資源群組部署疑難排解](../resource-group-deploy-debug.md)










