<properties 
    pageTitle="在 Azure App Service 中調整定價層" 
    description="了解如何在 Azure App Service 中調整 Web、Mobile、API 和 Logic Apps，包括自動調整。" 
    services="app-service" 
    documentationCenter="" 
    authors="stepsic-microsoft-com" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/29/2015" 
    ms.author="stepsic"/>

# 在 Azure App Service 中調整定價層

為了增加的效能和輸送量的 Microsoft Azure 上的應用程式，您可以使用 [Azure 入口網站](https://portal.azure.com/) 以向上擴充您的應用程式服務計劃從 **免費** 至 **共用**, ，**基本**, ，**標準**, ，或 **高階**。 

應用程式服務方案的服務層級根據 [*定價層* 計劃的](/pricing/details/app-service/)。 更高的定價層，如 **標準** 和 **高階**, ，提供更健全、 更彈性決定如何使用您在 Azure 上的資源中。 變更定價層會影響您的服務具有的核心和記憶體數量，以及這指 *向上擴充* (或 *向下調整*)。

除了相應增加定價層以外，也可以增加您的服務具有的執行個體數目。 這指 *向外延展* 或 *中的縮放比例*。 請參閱 [手動或自動調整執行個體計數](../insights-how-to-scale.md) 若要深入了解 *向外延展* 和 *縮放*。

如需 App Service 方案的相關資訊，請參閱 [什麼是 App Service 方案？](../web-sites-web-hosting-plan-overview.md) 和 [Azure App Service 方案深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md)。 定價資訊及個別應用程式服務方案的功能，請參閱 [App Service 定價詳細資料](/pricing/details/app-service/)。

最後，調整的運作方式不同如果您想要使用的專用 [App Service 環境](app-service-app-service-environment-intro.md)。 請參閱 [App Service 環境中調整 web 應用程式](app-service-web-scale-a-web-app-in-an-app-service-environment.md) 如需詳細資訊。

> [AZURE.NOTE] 從切換之前 **免費** 模式 **基本**, ，**標準**, ，或 **高階** 模式中，您必須先移除的支出上限就地 Azure App Service 訂用帳戶。 若要檢視或變更 Microsoft Azure 應用程式服務訂閱的選項，請參閱 [Microsoft Azure 訂閱][azuresubscriptions]。

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## 調整定價層

1. 在瀏覽器中開啟 [Azure 入口網站][portal] 並瀏覽至您想要調整的應用程式。
    
2. 在 **Essentials** 應用程式，然後按一下 [ **App Service 方案/定價層** 連結。

3. 按一下 [ **定價層**, ，則您會看到您的計劃可能服務層級的清單。 每一層都附有估價，以便您大致了解該層的平均成本。 
    
    ![選擇方案](./media/app-service-scale/ChoosePricingTier.png)
    
4. 一旦您選擇定價層，按一下 [ **選取**。
    
     **通知** ] 索引標籤便會閃爍綠色 **成功** 一旦作業已完成。
 
您也可以了解不同的 Azure 中的計算層 [這裡](http://go.microsoft.com/fwlink/?LinkId=309169)。
    
<a name="ScalingSQLServer"></a>
##調整相關資源
如果您的應用程式相依於其他服務 (如 SQL 或儲存體)，您也可以根據需求調整這些服務。

1. 在 **Essentials**, ，按一下 [ **資源群組** 連結。

2. 然後，在 **摘要** 的資源群組分頁中，按一下其中一個資料庫 （或任何其他您想要調整的資源） 的一部分。

    ![連結的資料庫](./media/app-service-scale/ResourceGroup.png)
    
3. 在該連結的資源刀鋒視窗中，按一下 [ **定價層** 組件中，選取其中一層，根據您的效能需求，然後按一下 [ **選取**。 
    
    ![調整您的 SQL Database](./media/app-service-scale/ScaleDatabase.png)
    
4. 如果您的應用程式使用儲存體，則異地複寫會在您選擇支援異地複寫的定價層時自動設定。 另一方面，對於 SQL，您需要手動設定異地複寫，以增加 SQL 資料庫的高可用性和災害復原功能。 若要這樣做，請按一下 [ **異地複寫** 組件。
    
    ![針對 SQL Database 設定異地複寫](./media/app-service-scale/GeoReplication.png)
    
<a name="devfeatures"></a>
## 開發人員功能
視定價層而定，可使用下列以開發人員為導向的功能：

### 位元 ###

-  **基本**, ，**標準**, ，和 **高階** 層支援 64 位元和 32 位元應用程式。
-  **免費** 和 **共用** 層支援 32 位元應用程式只計劃。

### 偵錯工具支援 ###

- 偵錯工具支援適用於 **免費**, ，**共用**, ，和 **基本** 1 個同時連線每個 App Service 方案模式。
- 偵錯工具支援適用於 **標準** 和 **高階** 5 個同時連線每個 App Service 方案模式。

<a name="OtherFeatures"></a>
## 其他功能

- 所有應用程式服務中的其餘功能的詳細資訊的方案，包括定價和功能適用於所有使用者 （包括開發人員），請參閱 [App Service 定價詳細資料](/pricing/details/web-sites/)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

<a name="Next Steps"></a>   
## 後續步驟

- 若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](/pricing/free-trial/)。
- 如需定價、支援及 SLA 的相關資訊，請參閱以下連結：
    
    [資料傳輸定價詳細資料](/pricing/details/data-transfers/)
    
    [Azure 支援方案](/support/plans/)
    
    [服務等級協定](/support/legal/sla/)
    
    [SQL Database 定價詳細資料](/pricing/details/sql-database/)
    
    [Microsoft Azure 的虛擬機器和雲端服務大小][vmsizes]
    
    [應用程式服務定價詳細資料](/pricing/details/app-service/)
    
    [應用程式服務定價詳細資料 - SSL 連線](/pricing/details/web-sites/#ssl-connections)

- 如需 Azure App Service 最佳作法，包括建置可調整且具彈性的架構，請參閱 [最佳作法 ︰ Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)。

- 調整 Web Apps 規模的相關影片：
    
    - [何時該調整 Azure 網站，與 Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
    - [自動調整 Azure 網站、CPU 或排程，與 Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
    - [如何調整 Azure 網站，與 Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

