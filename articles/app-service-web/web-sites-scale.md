<properties 
    pageTitle="在 Azure App Service 中調整 Web 應用程式規模" 
    description="了解如何在 Azure App Service 中相應增加及相應減少 Web 應用程式，包括自動調整。" 
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
    ms.date="12/10/2015" 
    ms.author="cephalin"/>


# 在 Azure App Service 中調整 Web 應用程式規模

為了增加的效能和輸送量，您在 Microsoft Azure 上的 web 應用程式，您可以使用 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715) 來調整您 [應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714) 從計劃 **免費** 模式 **共用**, ，**基本**, ，**標準**, ，或 **高階** 模式。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

調升 Azure Web 應用程式規模牽涉到兩個相關動作：將 App Service 方案模式變更為較高的服務層級，以及在切換為較高的服務層級後設定特定設定。 本文章涵蓋以上兩個主題。 較高的服務層級 (如 [標準]**** 和 [高階]**** 模式) 能讓您以更健全、更彈性的方式決定 Azure 中的資源使用情況。

這些調整設定只需幾秒鐘便能套用，且影響範圍遍及 App Service 方案內的所有 Web 應用程式。 您不需要變更程式碼或重新部署應用程式。

如需 App Service 方案的相關資訊，請參閱 [什麼是 App Service 方案?](../web-sites-web-hosting-plan-overview.md) 和 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 定價資訊及個別應用程式服務方案的功能，請參閱 [App Service 定價詳細資料](/pricing/details/web-sites/)。
> [AZURE.NOTE] 將 Web 應用程式從 [免費]**** 模式切換為 [基本]****、[標準]**** 或 [高階]**** 模式之前，您必須先適當地移除 Azure App Service 訂用帳戶的支出費用上限。 若要檢視或變更 Microsoft Azure 應用程式服務訂閱的選項，請參閱 [Microsoft Azure 訂用帳戶 ][azuresubscriptions]。

<a name="scalingsharedorbasic"></a>

## 調整為共用或基本模式

1. 在瀏覽器中開啟 [Azure 入口網站 ][portal]。

2. 在您的 Web 應用程式刀鋒視窗中，依序按一下 [所有設定]****、[級別]****、[從免費方案升級以新增執行個體並提升效能]****。

    ![選擇方案][choosewhp]

4. 在 [選擇定價層]**** 刀鋒視窗中，選擇 [共用]**** 或 [基本]****  模式，然後按一下 [選取]****。

    當操作完成時，[通知]**** 索引標籤會有綠色的「成功」****字樣閃爍顯示。

5. 將 [執行個體]**** 列從左滑動到右以增加執行個體的數量，然後按一下命令列中的 [儲存]****。 「共用」****模式未提供執行個體大小選項。 如需有關這些執行個體大小的詳細資訊，請參閱 [虛擬機器和雲端服務大小適用於 Microsoft Azure ][vmsizes]。

    ![基本模式的執行個體大小][choosebasicinstances]

    當操作完成時，[通知]**** 索引標籤會有綠色的「成功」****字樣閃爍顯示。

<a name="scalingstandard"></a>

## 調整為標準或高階模式

> [AZURE.NOTE] 在將 App Service 方案切換為 [標準]**** 或 [高階]**** 模式之前，應該針對您的 Microsoft Azure App Service 訂用帳戶適當地移除支出費用上限。 以免在帳單期間還未結束之前，Web 應用程式就因為已達支出上限而變得無法使用。 若要檢視或變更 Microsoft Azure 應用程式服務訂閱的選項，請參閱 [Microsoft Azure 訂用帳戶 ][azuresubscriptions]。

1. 若要調整為 [標準]**** 或 [高階]**** 模式，請依照與調整為 [共用]**** 或 [基本]**** 時相同的初始步驟執行，接著在 [選擇定價層]**** 中選擇 [標準]**** 或 [高階]**** 模式，然後按一下 [選取]****。

    當操作完成時，[通知]**** 索引標籤會有綠色的「成功」****字樣閃爍顯示，並且會啟用 [自動調整]****。

    ![在標準或高階模式中調整規模][scalestandard]

    您仍可滑動 [執行個體]**** 列以手動調整為更多執行個體，就像在前述的 [基本]**** 模式中一樣。 但您將在此了解到如何使用 [自動調整模式]****。

2. 在 [自動調整模式]**** 中選取 [效能]****，可根據效能度量自動調整。

    ![將自動調整模式設為效能][autoscale]

3. 在 [執行個體範圍]**** 中移動兩個滑桿，可定義要為應用程式服務計劃自動調整的執行個體數量下限和上限。 在本教學課程中，請將最大滑桿移至 **6** 個執行個體。

4. 按一下命令列中的 [儲存]****。

4. 在 [目標度量]**** 下，按一下 **>** 以設定預設度量的自動調整規則。

    ![設定目標度量][settargetmetrics]

    您可以設定不同效能度量的自動調整規則，包括 CPU、記憶體、磁碟佇列、HTTP 佇列和資料流程。 在此，您將為處於下列狀況的 CPU 百分比設定自動調整：

    - 如果 CPU 使用率在過去 10 分鐘內高於 70%，則相應增加 1 個執行個體
    - 如果 CPU 使用率在過去 5 分鐘內高於 90%，則相應增加 3 個執行個體
    - 如果 CPU 使用率在過去 30 分鐘內低於 50%，則相應減少 1 個執行個體

4. 將 [度量]**** 下拉式清單保留為 [CPU 百分比]****。

5. 在 [相應增加規則]**** 中設定第一項規則，方法是將 [條件]**** 設為 [大於]****、[臨界值]**** 設為 **70** (%)、[經歷時間]**** 設為 **10** (分鐘)、[相應增加值]**** 設為 **1** (執行個體)，並將 [回復期間]**** 設為 **10** (分鐘)。

    ![設定第一個自動調整規則][setfirstrule]
    >[AZURE.NOTE] [回復期間]**** 設定會指定在前次調整動作之後，此規則要再次執行調整前所應等待的時間。

6. 按一下 [新增相應增加規則]**/**，然後設定第二項規則，方法是將 [條件]**** 設為 [大於]****、[臨界值]**** 設為 **90** (%)、[經歷時間]**** 設為 **1** (分鐘)、[相應增加值]**** 設為 **3** (執行個體)，並將 [回復期間]**** 設為 **1** (分鐘)。

    ![設定第二個自動調整規則][setsecondrule]

5. 在 [相應減少規則]**** 中設定第三項規則，方法是將 [條件]**** 設為 [小於]****、[臨界值]**** 設為 **50** (%)、[經歷時間]**** 設為 **30** (分鐘)、[相應減少值]**** 設為 **1** (執行個體)，並將 [回復期間]**** 設為 **60** (分鐘)。

    ![設定第三個自動調整規則][setthirdrule]

7. 按一下命令列中的 [儲存]****。 自動調整規則現在應該會反映在 [級別]**** 刀鋒視窗中。

    ![設定自動調整規則結果][setrulesfinal]

<a name="ScalingSQLServer"></a>
## 調整與您的 Web 應用程式連接的 SQL Server 資料庫

如果您有一或多個連結至 Web 應用程式的 SQL Server 資料庫 (無論 App Service 方案模式為何)，您都可以根據本身的需求快速加以調整。

1. 若要調整其中一個連結的資料庫，請開啟 web 應用程式的刀鋒視窗中 [Azure 入口網站 ][portal]。 在 [Essentials]**** 可摺疊的下拉式清單中，按一下 [資源群組]**** 連結。 然後，在資源群組刀鋒視窗的 [摘要]**** 組件，按一下其中一個連結的資料庫。

    ![連結的資料庫][resourcegroup]

2. 在您連結的 SQL Database 刀鋒視窗中，按一下 [定價層]**** 組件，根據您的效能需求選取其中一層，然後按一下 [選取]****。

    ![調整您的 SQL Database][scaledatabase]

3. 您也可以設定異地複寫，以增加 SQL Database 的高可用性和災害復原功能。 若要這樣做，請按一下 [異地複寫]**** 組件。

    ![針對 SQL Database 設定異地複寫][georeplication]

<a name="devfeatures"></a>
## 開發人員功能

視 Web 應用程式的模式而定，可使用下列以開發人員為導向的功能：

### 位元

- [基本]****、[標準]**** 及 [高階]**** 模式支援 64 位元和 32 位元的應用程式。
- [免費]**** 和 [共用]**** 等方案模式僅支援 32 位元的應用程式。

### 偵錯工具支援

- 在每個 App Service 方案 1 個同時連線的情況下，偵錯工具支援適用於 [免費]****、[共用]**** 及 [基本]**** 模式。
- 在每個 App Service 方案有 5 個同時連線的情況下，偵錯工具支援適用於 [標準]**** 和 [高階]**** 模式。

<a name="OtherFeatures"></a>
## 其他功能

### Web 端點監視

- Web 端點監視適用於 [基本]****、[標準]**** 及 [高階]**** 模式。 如需 web 端點監視的詳細資訊，請參閱 [如何監視 Web 應用程式](web-sites-monitor.md)。

- 所有應用程式服務中的其餘功能的詳細資訊的方案，包括定價和功能適用於所有使用者 (包括開發人員)，請參閱 [App Service 定價詳細資料](/pricing/details/web-sites/)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

<a name="Next Steps"></a>
## 後續步驟

- 若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](/pricing/free-trial/)。
- 如需定價、支援及 SLA 的相關資訊，請參閱以下連結：

    [資料傳輸定價詳細資料](/pricing/details/data-transfers/)

    [Microsoft Azure 支援方案](/support/plans/)

    [服務等級協定](/support/legal/sla/)

    [SQL Database 定價詳細資料](/pricing/details/sql-database/)

    [虛擬機器和雲端服務大小適用於 Microsoft Azure ][vmsizes]

    [應用程式服務定價詳細資料](/pricing/details/web-sites/)

    [應用程式服務定價詳細資料-SSL 連線](/pricing/details/web-sites/#ssl-connections)

- 如需 Azure App Service 最佳作法，包括建置可調整且具彈性的架構，請參閱 [最佳作法: Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)。

- 調整 Web Apps 規模的相關影片：

    - [何時該調整 Azure 網站-Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
    - [自動調整 Azure 網站、 CPU 或排程-Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
    - [如何調整 Azure 網站-Stefan schackow](/documentation/videos/how-azure-web-sites-scale/)

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)




[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169 
[sqlaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930 
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288 
[portal]: https://portal.azure.com/ 
[choosewhp]: ./media/web-sites-scale/scale1ChooseWHP.png 
[choosebasicinstances]: ./media/web-sites-scale/scale2InstancesBasic.png 
[savebutton]: ./media/web-sites-scale/05SaveButton.png 
[basiccomplete]: ./media/web-sites-scale/06BasicComplete.png 
[scalestandard]: ./media/web-sites-scale/scale3InstancesStandard.png 
[autoscale]: ./media/web-sites-scale/scale4AutoScale.png 
[settargetmetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png 
[setfirstrule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png 
[setsecondrule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png 
[setthirdrule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png 
[setrulesfinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png 
[resourcegroup]: ./media/web-sites-scale/scale10ResourceGroup.png 
[scaledatabase]: ./media/web-sites-scale/scale11SQLScale.png 
[georeplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png 

