<properties 
   pageTitle="使用 Azure 入口網站管理 Azure 資料湖分析 | Azure" 
   description="瞭解如何管理資料湖分析帳戶、資料來源、使用者和作業。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/27/2015"
   ms.author="jgao"/>

# 使用 Azure 入口網站管理 Azure 資料湖分析

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

瞭解如何使用 Azure 入口網站管理 Azure 資料湖分析帳戶、資料來源、使用者和作業。 若要使用其他工具查看管理主題，請按一下上方的索引標籤選取器。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。




<!-- ################################ -->
<!-- ################################ -->
## 管理帳戶

您必須擁有資料湖分析帳戶，才能執行任何資料湖分析工作。 不同於 Azure HDInsight 中，不必付費分析帳戶時 
執行工作。  您只需支付執行工作時的費用。  如需詳細資訊，請參閱 
[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。  

**建立資料湖分析帳戶**

1. 登入新 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [ **新增**, ，按一下 [ **資料 + 分析**, ，然後按一下 [ **資料湖分析**。
6. 輸入或選取下列項目：

    ![Azure 資料湖分析入口網站刀鋒視窗](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **名稱**︰ 分析帳戶的名稱。
    - **資料湖存放區**︰ 每個資料湖分析帳戶有相依的 Azure 資料湖存放區帳戶。 資料湖分析帳戶和相依的資料湖存放區帳戶必須位於同一個 Azure 資料中心。 請依照指示來建立新的資料湖存放區帳戶，或選取現有的帳戶。
    - **訂閱**︰ 選擇用於分析帳戶的 Azure 訂閱。
    - **資源群組**。 選取現有的 Azure 資源群組，或建立一個新的群組。 Azure 資源管理員 (ARM) 可讓您將應用程式中的資源作為群組使用。 如需詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。 
    - **位置**。 為資料湖分析帳戶選取 Azure 資料中心。 

8. 按一下 [ **建立**。 其會帶領您前往入口網站主畫面。 新的磚會新增至「開始面板」，且具有顯示「部署 Azure 資料湖分析」的標籤。 建立資料湖分析帳戶需要幾分鐘的時間。 當建立帳戶之後，入口網站就會在新的刀鋒視窗上開啟帳戶。

    ![Azure 資料湖分析入口網站刀鋒視窗](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

<a name="access-adla-account"></a> **以 access 開啟資料湖分析帳戶**

1. 登入新 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [ **瀏覽** 左側的功能表，然後按一下 [ **資料湖分析**。
3. 按一下您要存取的資料湖分析帳戶。 該帳戶會在新的刀鋒視窗中開啟。

**刪除資料湖分析帳戶**

1. 開啟您要刪除的資料湖分析帳戶。 如需指示，請參閱 [存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [ **刪除** 從刀鋒視窗頂端的按鈕功能表中。
3. 輸入帳戶名稱，然後按一下 **刪除**。

刪除分析帳戶並不會刪除相依的資料湖儲存區帳戶。 如需相關指示，刪除
資料湖儲存體帳戶，請參閱 [刪除資料湖存放區帳戶](data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account)。




























<!-- ################################ -->
<!-- ################################ -->
## 管理帳戶資料來源

資料湖分析目前支援下列資料來源：

- [Azure 資料湖存放區](data-lake-store-overview.md)
- [Azure Blob 儲存體](storage-introduction.md)

當您建立資料湖分析帳戶時，您必須指定要設為預設的 Azure 資料湖市集帳戶 
子網域名稱。 預設的資料湖存放區帳戶是用來儲存工作中繼資料與工作稽核記錄。 當您有了 
建立資料湖分析帳戶，您可以加入其他資料湖存放區帳戶及/或 Azure 儲存體帳戶。 

<a name="default-adl-account"></a>**找不到預設資料湖儲存體帳戶**

- 開啟您要管理的資料湖分析帳戶。 如需指示，請參閱 [存取資料湖分析帳戶](#access-adla-account)。 預設資料湖存放區] 所示 **基本**:

    ![Azure 資料湖分析新增資料來源](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**新增其他資料來源**

1. 開啟您要管理的資料湖分析帳戶。 如需指示，請參閱 [存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [ **設定** 然後按一下 [ **資料來源**。 您應該會看到列出的預設資料湖存放區帳戶
那里。 
3. 按一下 [ **加入資料來源**。

    ![Azure 資料湖分析新增資料來源](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)

    若要新增 Azure 資料湖儲存區帳戶，您需要帳戶名稱及帳戶的存取權才可進行查詢。
    若要新增 Azure Blob 儲存體，您需要儲存體帳戶和帳戶金鑰，該金鑰可透過瀏覽至入口網站中的儲存體帳戶內找到。

<a name="explore-data-sources"></a>**若要瀏覽資料來源**  

1. 開啟您要管理的分析帳戶。 如需指示，請參閱 [存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [ **設定** 然後按一下 [ **資料總管**。 
 
    ![Azure 資料湖分析資料總管](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
    
3. 按一下資料湖儲存區帳戶以開啟。

    ![Azure 資料湖分析資料總管儲存體帳戶](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
    
    針對每一個資料湖儲存區帳戶，您可以：
    
    - **新的資料夾**︰ 加入新的資料夾。
    - **上傳**︰ 將檔案上傳至儲存體帳戶從您的工作站。
    - **存取**︰ 設定存取權限。
    - **重新命名資料夾**︰ 重新命名資料夾。
    - **資料夾屬性**︰ 顯示檔案或資料夾的內容，例如 WASB 路徑，WEBHDFS 路徑上次修改時間，依此類推。
    - **刪除資料夾**︰ 刪除資料夾。

<a name="upload-data-to-adls"></a> **檔案上傳至資料湖市集帳戶**

1. 從入口網站中，按一下 [* * 瀏覽 * * 從左的窗格中，然後按一下 [ **資料湖存放區**。
2. 按一下您要上傳資料的目標資料湖儲存區帳戶。 若要尋找的預設資料湖儲存體帳戶，請參閱 [這裡](#default-adl-account)。
3. 按一下 [ **資料總管** 從上方的功能表。
4. 按一下 [ **新目錄** 建立新的資料夾，或按一下 [變更資料夾的資料夾名稱。
6. 按一下 [ **上載** 從頂端功能表上傳的檔案。


<a name="upload-data-to-wasb"></a> **檔案上傳至 Azure Blob 儲存體帳戶**

請參閱 [hdinsight 的資料上傳](hdinsight-upload-data.md)。  此資訊適用於資料湖分析。


## 管理使用者

資料湖分析會運用 Azure Active Directory 執行角色存取控制。 當您建立資料湖分析 
帳戶的 「 訂閱管理員 」 角色加入至帳戶。 您可以新增其他的使用者和安全性群組與 
下列角色 ︰

|角色|說明|
|----|-----------|
|擁有者|可讓您管理所有項目，包括存取資源。|
|參與者|存取入口網站；提交及監視工作。 參與者也需要資料湖儲存區帳戶的讀取或寫入權限，才能提交工作。|
|DataLakeAnalyticsDeveloper | 使用者可以提交工作、監視所有的工作，但只能取消自己的工作。 他們也無法管理自己的帳戶，例如新增使用者、變更權限或刪除帳戶。 這些使用者需要資料湖儲存區帳戶的讀取或寫入權限，才能執行工作。     | 
|讀取者|可讓您檢視所有項目，但是無法進行變更。|  
|研發/測試實驗室使用者|可讓您檢視所有項目，以及連接、啟動、重新啟動和關閉虛擬機器。|  
|使用者存取系統管理員|可讓您管理 Azure 資源的使用者存取。|  

如需建立 Azure Active Directory 使用者和安全性群組的詳細資訊，請參閱 [什麼是 Azure Active Directory](active-directory-whatis.md)。

**新增使用者或安全性群組至分析帳戶**

1. 開啟您要管理的分析帳戶。 如需指示，請參閱 [存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [ **設定**, ，然後按一下 [ **使用者**。 您也可以按一下 **存取** 上 **Essentials** 標題列，如下列螢幕擷取畫面所示 ︰

    ![Azure 資料湖分析帳戶新增使用者](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. 從 **使用者** 刀鋒視窗中，按一下 [ **新增**。
4. 選取角色並新增使用者，然後按一下 [ **確定**。

**附註 ︰ 如果此使用者或安全性群組必須提交工作，他們必須在資料湖市集上授予權限。 如需詳細資訊，請參閱 [資料湖存放區中儲存資料的安全性](data-lake-store-secure-data.md)。**





<!-- ################################ -->
<!-- ################################ -->
## 管理工作

您必須擁有資料湖分析帳戶，才能執行工作。  如需詳細資訊，請參閱 [管理資料湖分析帳戶](#manage-data-lake-analytics-accounts)。

<a name="create-job"></a>**若要建立工作**

1. 開啟您要管理的分析帳戶。 如需指示，請參閱 [存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [ **新工作**。

    ![建立 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)

    您將看見類似下方的新刀鋒視窗：

    ![建立 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)

    針對每一作業，您可以設定：


  	|Name|Description|
  	|----|-----------|
  	|Job Name|Enter the name of the job.|
  	|Priority|Lower number is higher priority. If two jobs are both queued, the one with lower priority will be run first|
  	|Parallelism |Max number of compute processes that can happen at the same time. Increasing this number can improve performance but can also increase cost.|
  	|Script|Enter the U-SQL script for the job.|

    Using the same interface, you can also explore the link data sources, and add addtional files to the linked data sources. 
3. 按一下 [ **提交工作** 如果您想要提交工作。

**提交作業**

請參閱 [建立資料湖分析工作](#create-job)。

<a name="monitor-jobs"></a>**若要監控工作**

1. 開啟您要管理的分析帳戶。 如需指示，請參閱 
[存取資料湖分析帳戶](#access-adla-account)。 工作管理] 面板會顯示基本工作 
資訊 ︰

    ![管理 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)

3. 按一下 [ **作業管理** 一個螢幕擷取畫面所示。

    ![管理 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)

4. 按一下清單中的工作。 或按一下 **篩選** 可協助您找出工作 ︰

    ![篩選 Azure 資料湖分析 U-SQL 作業](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)

    您可以篩選由工作 **時間範圍**, ，**工作名稱**, ，和 **作者**。
5. 按一下 [ **重新提交** 如果您想要將工作重新提交。

**重新提交作業**

請參閱 [監視資料湖分析工作](#monitor-jobs)。

##監視帳戶使用情況

[簡介-我們必須說明條款，並連接項目。 ]

**監視帳戶使用情況**

1. 開啟您要管理的分析帳戶。 如需指示，請參閱 
[存取資料湖分析帳戶](#access-adla-account)。 [使用情況] 面板會顯示使用狀況：

    ![監視 Azure 資料湖分析使用情況](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)

2. 按兩下窗格以查看更多詳細資料。

##檢視 U-SQL 目錄

 [U SQL 目錄](data-lake-analytics-use-u-sql-catalog.md) 用來組織資料和程式碼，因此 U SQL 指令碼可以共用。 目錄可以讓 Azure 資料湖中的資料具有可能的最高效能。 您可從 Azure 入口網站檢視 U SQL 目錄。

**瀏覽 U-SQL 目錄**

1. 開啟您要管理的分析帳戶。 如需指示，請參閱 
[存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [ **資料總管** 從上方的功能表。
3. 展開 **目錄**, ，exapnd **主要**, ，展開 * * 資料表，或 **資料表值函式**, ，或 **組件**。 下列螢幕擷取畫面會顯示一個資料表值函式。

    ![Azure 資料湖分析資料總管儲存體帳戶](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)


<!-- ################################ -->
<!-- ################################ -->
## 使用 Azure 資源管理員群組

應用程式通常由許多元件，例如 web 應用程式、 資料庫、 資料庫伺服器、 存放裝置組成
和第 3 個合作對象的服務。 Azure 資源管理員 (ARM) 可讓您能夠使用您的應用程式中的資源 
為群組，稱為 「 Azure 資源群組。 您可以部署、 更新、 監視或刪除所有的 
您的應用程式在單一、 協調的作業中的資源。 您的部署，且可使用範本 
範本可以用於不同的環境，例如測試、 預備和生產環境。 您可以釐清計費 
為您的組織檢視整個群組的彙總成本。 如需詳細資訊，請參閱 [Azure
資源管理員概觀](resource-group-overview.md)。 

資料湖分析服務可包含下列元件：

- Azure 資料湖分析帳戶
- 必要的預設 Azure 資料湖儲存區帳戶
- 其他 Azure 資料湖儲存區帳戶
- 其他 Azure 儲存體帳戶

您可以在某個 ARM 群組下建立上述所有元件，這樣更容易管理。

![Azure 資料湖分析帳戶與儲存體](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

資料湖分析帳戶和相依的儲存體帳戶必須位於相同的 Azure 資料中心。
但 ARM 群組可位在不同的資料中心內。  



##另請參閱 

- [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
- [使用 Azure PowerShell 管理 Azure 資料湖分析](data-lake-analytics-use-powershell.md)
- [使用 Azure 入口網站監視和疑難排解 Azure 資料湖分析作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



