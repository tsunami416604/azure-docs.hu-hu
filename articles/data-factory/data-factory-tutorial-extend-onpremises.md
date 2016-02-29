<properties 
    pageTitle="請將輸出資料複製到內部部署 SQL Server 資料庫 (Azure 傳統入口網站)。" 
    description="本逐步解說使用 Azure 傳統入口網站中的 Data Factory 編輯器擴充教學課程，如此可讓管線將輸出資料複製到 SQL Server Database。"
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/12/2015" 
    ms.author="spelluru"/>


# 逐步解說：將行銷活動有效性資料複製到內部部署 SQL Server 資料庫 
在此逐步解說中，您將學習如何設定環境以啟用管線，來處理您的內部部署資料。
 
在記錄檔處理案例中的最後一個步驟，從第一個逐步解說與分割 -> 充實 -> 分析工作流程，行銷活動有效性輸出已複製到 Azure SQL 資料庫。 您也可以在組織內，將此資料移動到內部部署 SQL Server 以進行分析。
 
為了從 Azure Blob 將行銷活動有效性資料複製到內部部署 SQL Server，您需要使用與在第一個逐步解說中所使用的一組相同 Cmdlet 建立額外的內部部署連結的服務、資料表和管線。

## 必要條件

您 **必須** 執行中的逐步解說 [教學課程: 移動和處理記錄檔使用 Data Factory][datafactorytutorial] 之前執行此逐步解說，本文中。 

**(建議選項)** 檢閱並練習中的逐步解說 [讓您的管線使用內部部署資料][useonpremisesdatasources] 文章的逐步解說建立管線，將資料從內部部署 SQL Server 至 Azure blob 存放區。


在本逐步解說中，您將執行下列步驟： 

1. [步驟 1: 建立資料管理閘道器](#OnPremStep1)。 資料管理閘道器是用戶端代理程式可讓您組織中的內部部署資料來源從雲端存取。 閘道可以讓您在內部部署 SQL Server 和 Azure 的資料存放區之間傳送資料。  

    您必須在公司環境中至少安裝一個閘道，以及將它向其註冊 Azure Data Factory 註冊，之後才將內部部署 SQL Server 資料庫新增為 Azure Data Factory 的連結的服務。

2. [步驟 2: 建立內部部署 SQL Server 的連結的服務](#OnPremStep2)。 在此步驟中，先建立您的內部部署 SQL Server 電腦上的資料庫和資料表，然後建立連結的服務: **OnPremSqlLinkedService**。  
3. [步驟 3: 建立資料表和管線](#OnPremStep3)。 在此步驟中，您將建立資料表 **MarketingCampaignEffectivenessOnPremSQLTable** 和管線 **EgressDataToOnPremPipeline**。 

4. [步驟 4: 監視管線和檢視結果](#OnPremStep4)。 在此步驟中，您將使用 Azure 入口網站來監視管線、資料表和資料配量。


## <a name="OnPremStep1"></a> 步驟 1: 建立資料管理閘道器

資料管理閘道是可讓您從雲端存取組織中內部部署資料來源的用戶端代理程式。 閘道可以讓您在內部部署 SQL Server 和 Azure 的資料存放區之間傳送資料。
  
您必須在公司環境中至少安裝一個閘道，以及將它向其註冊 Azure Data Factory 註冊，之後才將內部部署 SQL Server 資料庫新增為 Azure Data Factory 的連結的服務。

如果您有可以使用的現有資料閘道，請略過此步驟。

1.  建立邏輯資料閘道。 在 **Azure 入口網站**, ，按一下 [ **連結服務** 上 **DATA FACTORY** data factory] 分頁。
2.  按一下 [ **新增 (+) 資料閘道** 命令列上。  
3.  在 **新增資料閘道** 刀鋒視窗中，按一下 [ **建立**。
4.  在 **建立** 刀鋒視窗中，輸入 **MyGateway** 做為資料閘道 **名稱**。
5.  按一下 [ **選擇區域** 並視需要予以變更。 
6.  按一下 [ **確定** 中 **建立** 刀鋒視窗。 
7.  您應該會看到 **設定** 刀鋒視窗。 
8.  在 **設定** 刀鋒視窗中，按一下 [ **直接在此電腦上安裝**。 這會在您的電腦上下載、安裝及設定閘道，並向服務註冊閘道。 如果在您想要連結至這個邏輯閘道入口網站的電腦上已安裝現有的閘道，使用此分頁中的金鑰，使用資料管理閘道組態管理員 (預覽) 工具重新註冊您的閘道。

    ![資料管理閘道組態管理員][image-data-factory-datamanagementgateway-configuration-manager]

9. 按一下 **[確定]** 關閉 **設定** 分頁和 **確定** 關閉 **建立** 刀鋒視窗。 等到狀態 **MyGateway** 中 **連結服務** 刀鋒視窗中變更為 **好**。 您也可以啟動 **資料管理閘道組態管理員 (預覽)** 工具來確認閘道的名稱符合入口網站中的名稱和 **狀態** 是 **註冊**。 您可能必須關閉再重新開啟 [連結的服務] 分頁，以查看最新狀態。 可能需要幾分鐘的時間，畫面才會重新整理為最新狀態。 

## <a name="OnPremStep2"></a> 步驟 2: 建立內部部署 SQL Server 的連結的服務

在此步驟中，您可以先在內部部署 SQL Server 電腦上建立所需的資料庫和資料表，然後再建立連結的服務。

### 準備內部部署資料庫和資料表

若要開始，您需要建立 SQL Server 資料庫、資料表、使用者定義型別和預存程序。 這些會用來移動 **MarketingCampaignEffectiveness** 結果從 Azure blob 複製到 SQL Server 資料庫。

1.  在 **Windows 檔案總管**, ，瀏覽至 **OnPremises** 子資料夾中的 **C:\ADFWalkthrough** (或您解壓縮範例的位置)。
2.  開啟 **Prepareonpremdatabase&table.ps1 & Table.ps1** 您喜好的編輯器，在 SQL Server 資訊取代反白顯示，並儲存檔案 (請提供 **SQL 驗證** 詳細資料)。 針對教學課程的目的，為您的資料庫啟用 SQL 驗證。 
            
        $dbServerName = "<servername>"
        $dbUserName = "<username>"
        $dbPassword = "<password>"

3. 在 **PowerShell**, ，瀏覽至 **C:\ADFWalkthrough\OnPremises** 資料夾。
4.  執行 **Prepareonpremdatabase&table.ps1 & Table.ps1** **(可能是與在雙引號或如下所示)**。
            
        & '.\prepareOnPremDatabase&Table.ps1'

5. 一旦指令碼執行成功，您會看到下列項目：   
            
        PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
        6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
        6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
        6/10/2014 10:12:33 PM Connecting as user [sa]
        6/10/2014 10:12:33 PM Summary:
        6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
        6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### 建立連結的服務

1.  在 **Azure 入口網站**, ，按一下 [ **製作和部署** 磚 **DATA FACTORY** 分頁 **LogProcessingFactory**。
2.  在 **Data Factory 編輯器**, ，按一下 [ **新增資料存放區** 工具列，然後選取 **內部部署 SQL Server 資料庫**。
3.  在 JSON 指令碼中，執行下列動作： 
    1.  取代 **<servername>** 裝載 SQL Server 資料庫的伺服器名稱。
    2.  取代 **<databasename>** 與 **MarketingCampaigns**。
    3.  如果您使用 **SQL 驗證**
        1.  指定 **<username>** 和 **<password>** 中 **connectionString**。
        2.  移除最後兩個資料列 (**username** 和 **密碼** JSON 屬性必要的只有當您使用 Windows 驗證)。 
        3.  移除 * *，(逗號) * * 結尾 **gatewayName** 資料列。
        
        **如果您使用 Windows 驗證:**
        1. 設定的值 **整合式安全性** 至 **True** 中 **connectionString**。 移除 「**使用者識別碼 =<username>;密碼 =<password>;**「 從連接字串。 
        2. 指定的使用者具有存取權的資料庫名稱 **username** 屬性。 
        3. 指定 **密碼** 使用者帳戶。   
    4. 指定閘道的名稱 (**MyGateway**) gatewayName 屬性。             
3.  按一下 [ **部署** 部署連結的服務] 工具列上。 

## <a name="OnPremStep3"></a> 步驟 3: 建立資料表和管線

### 建立內部部署邏輯資料表

1.  在 **Data Factory 編輯器**, ，按一下 [ **新的資料集** 從工具列中，然後選取 **內部 SQL**。 
2. JSON，取代右窗格中的 JSON 指令碼 **MarketingCampaignEffectivenessOnPremSQLTable.json** 檔案從 **C:\ADFWalkthrough\OnPremises** 資料夾。
3. 變更連結的服務名稱 (**linkedServiceName** 屬性) 從 **OnPremSqlServerLinkedService** 至  **SqlServerLinkedService**。
4. 按一下 [ **部署** 部署資料表] 工具列上。 
     
#### 建立將資料從 Azure Blob 複製到 SQL Server 的管線

1.  1. 在 **Data Factory 編輯器**, ，按一下 [ **新增管線** 工具列上的按鈕。 如果沒看到此按鈕，請按一下工具列 **...(省略符號)**。 或者，您可以以滑鼠右鍵按一下 **管線** 樹狀檢視中按一下 **新增管線**。
2. JSON，取代右窗格中的 JSON 指令碼 **EgressDataToOnPremPipeline.json** 檔案從 **C:\ADFWalkthrough\OnPremises** 資料夾。
3. 新增 **逗號 ('，')** 結尾 **右方括弧 ('] ')** 在 JSON 中，然後在右方括弧之後加入下列三行。 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

    請注意， **啟動** 和 **結束** 時間設為 05/01/2014年和 05/05/2014年，因為在這個逐步解說的範例資料是從 05/01/2014年到 05/05/2014年。 
 
3. 按一下 [ **部署** 建立並部署管線] 工具列上。 確認您看到 **已成功建立管線** 編輯器的標題列上的訊息。
    
## <a name="OnPremStep4"></a> 步驟 4: 監視管線和檢視結果

您現在可以使用相同的步驟中導入 **監視管線和資料配量** 區段 [主要教學課程][datafactorytutorial] 來監視新的管線和新的內部部署 ADF 資料表的資料配量。
 
當您看到資料表的配量狀態 **MarketingCampaignEffectivenessOnPremSQLTable** 變成已備妥，這表示管線已完成配量執行。 若要檢視結果，查詢 **MarketingCampaignEffectiveness** 資料表中 **MarketingCampaigns** SQL Server 資料庫中的。
 
恭喜！ 您已成功完成使用內部部署資料來源的逐步解說。 
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-move-data-between-onprem-and-cloud.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises/DataManagementGatewayConfigurationManager.png

 
