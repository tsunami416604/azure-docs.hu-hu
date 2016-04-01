<properties 
    pageTitle="在內部部署和雲端之間使用 Azure Data Factory 移動資料" 
    description="了解如何在內部部署和雲端之間使用資料管理閘道和 Azure Data Factory 移動資料。" 
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
    ms.date="10/29/2015" 
    ms.author="spelluru"/>

# 利用資料管理閘道在內部部署來源和雲端之間移動
現代資料整合的挑戰之一是順暢地在內部部署和雲端之間來回移動資料。 資料處理站會利用資料管理閘道順暢地進行整合。 資料處理站管理閘道器是您可以安裝在內部以啟用混合式管線的代理程式。

本文提供整合內部部署資料存放區與雲端資料存放區的整合，以及使用資料處理站進行雲端處理的概觀。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 文章和其他資料處理站核心概念文件。 下列概觀假設您已熟悉管線、活動、資料集和複製活動等資料處理站概念。

資料閘道提供下列功能：

1.  在相同資料處理站內建立內部部署資料來源和雲端資料來源的模型及移動資料。
2.  具有用於監視和管理的單一窗格，可利用資料處理站雲端儀表板看見閘道器的狀態。
3.  安全地管理內部部署資料來源的存取權。
    1. 不需要變更公司防火牆。 閘道器只會使輸出 HTTP 連線開啟網際網路。
    2. 利用您的憑證加密內部部署資料存放區的認證。
4.  有效率地移動資料 – 資料會以平行方式傳輸，且系統會採用自動重試邏輯，修復間歇性網路問題。

## 使用資料管理閘道的考量
1.  資料管理閘道器的單一執行個體可用於多個內部部署資料來源，但是請注意， **單一閘道執行個體繫結至一個 Azure data factory** 並不能與另一個 data factory 共用。
2.  您可以擁有 **只有一個執行個體的資料管理閘道器** 安裝在單一電腦上。 假設您有兩個需要存取內部部署資料來源的 Data Factory，您需要將繫結至不同 Data Factory 的閘道器個別安裝在兩部內部部署電腦上。
3.   **閘道不需要在相同的電腦做為資料來源上**, ，但越接近資料來源可縮短閘道連線到資料來源的時間。 建議您安裝閘道器的電腦不同於裝載內部部署資料來源的電腦，如此閘道器才不會與資料來源爭奪資源。
4.  您可以擁有 **連接到相同的內部部署資料來源的不同電腦上的多個閘道**。 例如，您可能有兩個閘道器用於服務兩個 Data Factory，但相同的內部部署資料來源都向這兩個 Data Factory 註冊。
5.  如果您已經有您的電腦上安裝一個閘道 **Power BI** 案例中，請安裝 **不同的 Azure Data Factory 的閘道** 另一部電腦上。
6.  您必須 **使用閘道，即使您使用 ExpressRoute**。 
7.  您應該將您的資料來源做為內部部署資料來源 （亦即在防火牆後面） 即使使用 **ExpressRoute** 和 **使用閘道** 來建置服務與資料來源之間的連線。 

## 閘道器安裝 - 必要條件
1.  支援 **作業系統** 版本是 Windows 7、 Windows 8/8.1、 Windows Server 2008 R2、 Windows Server 2012、 Windows Server 2012 R2。
2.  建議 **組態** 閘道機器是至少 2 GHz、 4 核心、 8 GB RAM 和 80 GB 磁碟。
3.  如果主機電腦休眠，閘道器即無法回應資料要求。 因此，設定適當 **電源計劃** 之前安裝閘道器的電腦上。 如果電腦已設定為休眠，安裝閘道器時會提示訊息。

因為複製活動執行會以特定的頻率發生，在電腦上的資源使用量 (CPU、記憶體) 也會遵循與尖峰和閒置時間相同的模式。 資源使用率也仰賴要移動的資料量。 當多個複製工作正在進行中時，您將觀察到資源使用量會在尖峰時段增加。 雖然上述情況是組態下限，根據資料移動的特定負載，您的組態最好還是要具備比上述組態下限還要多的資源。

## 安裝
資料管理閘道的安裝方式為從 Microsoft 下載中心下載 MSI 安裝套件。  MSI 也可用來將現有的資料管理閘道升級至最新的版本，並保留所有設定。 您可以遵循以下的逐步解說，以從 Azure 傳統入口網站尋找連結至 MSI 封裝的連結。

### 安裝最佳作法：
1.  為閘道器設定主機電腦上的電源計劃，使電腦不休眠。 如果主機電腦休眠，閘道器即無法回應資料要求。
2.  您應該備份與閘道器相關聯的憑證。

### 安裝疑難排解：
如果貴公司使用防火牆或 proxy 伺服器，如果資料管理閘道無法連接到 Microsoft 雲端服務，可能需要其他步驟。 

#### 使用事件檢視器查看閘道器記錄檔：

查到器組態管理員應用程式會顯示閘道器的狀態，像是「已中斷連接」或「連接中」。

如需詳細資訊，您可以查看 Windows 事件記錄檔中的閘道器記錄檔。 您可以使用 Windows 尋找 **事件檢視器** 下 **應用程式及服務記錄檔** > **資料管理閘道器** 而疑難排解閘道器的相關問題的錯誤層級事件在事件檢視器中尋找。


#### 防火牆相關問題的可能徵兆：

1. 當您嘗試註冊閘道器時，您會收到下列錯誤：「無法註冊閘道器金鑰。 再次嘗試註冊閘道器金鑰之前，請確認資料管理閘道已處於連線狀態，且已啟動資料管理閘道主機服務。」
2. 當您開啟組態管理員時，您會看到「已中斷連接」或「連接中」狀態。 檢視 Windows 事件記錄檔時，在 [事件檢視器] > [應用程式和服務記錄檔] > [資料管理閘道] 中，您會看到錯誤訊息，例如「無法連接到遠端伺服器」或「資料管理閘道的元件沒有回應，將會自動重新啟動。 元件名稱：閘道。」

這些錯誤的原因是防火牆或 proxy 伺服器的組態不正確，會阻止資料管理閘道連接到雲端服務進行驗證。

可能在範圍內的兩個防火牆為：在組織的中央路由器執行的公司防火牆，以及在安裝閘道器的本機電腦上設定為精靈的 Windows 防火牆。 以下是一些考量：

- 不需要變更公司防火牆的輸入原則。
- 公司防火牆及 Windows 防火牆應該啟用 TCP 連接埠的輸出規則：80、443，以及從 9350 到 9354。 Microsoft Azure 服務匯流排可使用這些項目建立雲端服務和資料管理閘道之間的連線。

MSI 安裝程式會自動設定閘道器電腦輸入連接埠的 Windows 防火牆規則 (請參閱上述的連接埠和安全性考量一節)。

但是安裝程式會假設本機電腦和公司防火牆會根據預設允許上述的輸出連接埠。 如果不是這樣，您必須啟用這些輸出連接埠。 如果您用協力廠商防火牆取代 Windows 防火牆，可能需要手動開啟這些連接埠。 

如果貴公司使用 proxy 伺服器，您必須將 Microsoft Azure 新增至白名單。 您可以下載一份有效的 Microsoft Azure IP 位址，從 [Microsoft 下載中心](http://msdn.microsoft.com/library/windowsazure/dn175718.aspx)。

## 使用資料閘道器 – 逐步解說
在本逐步解說中，您可以使用將資料從內部部署 SQL Server 資料庫移至 Azure Blob 的管線，來建立一個 Data Factory。 

### 步驟 1：建立 Azure Data Factory
在此步驟中，您可以使用 Azure 傳統入口網站建立名為 Azure Data Factory 執行個體 **ADFTutorialOnPremDF**。 您也可以使用 Azure Data Factory Cmdlet 來建立 Data Factory。 

1.  登入後 [Azure 入口網站](https://portal.azure.com), ，按一下 [ **新增** 從左下角，選取 **資料分析** 中 **建立** 刀鋒視窗中，然後按一下 [ **Data Factory** 上 **資料分析** 刀鋒視窗。

    ![新增->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png) 
  
6. 在 **新增 data factory** 刀鋒視窗中 ︰
    1. 輸入 **ADFTutorialOnPremDF** 的 **名稱**。
    2. 按一下 [ **資源群組名稱** ，然後選取 **ADFTutorialResourceGroup**。 您可以選取現有的資源群組，或建立一個新的群組。 若要建立新的資源群組：
        1. 按一下 [ **建立新的資源群組**。
        2. 在 **建立資源群組分頁**, ，輸入 **名稱** 資源群組，然後按一下 [ **確定**。

7. 請注意， **新增至開始面板** 上已核取 **新增 data factory** 刀鋒視窗。

    ![新增至儀表板](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. 在 **新增 data factory** 刀鋒視窗中，按一下 [ **建立**。

    Azure Data Factory 的名稱在全域必須是唯一的。 如果您收到錯誤 ︰ **Data factory 名稱"ADFTutorialOnPremDF"沒有**, 、 變更資料處理站 (例如 yournameADFTutorialOnPremDF) 的名稱，然後嘗試重新建立。 執行此教學課程中的其餘步驟時，請使用此名稱來取代 ADFTutorialOnPremDF。  

9. 按一下查看來自建立程序的通知 **通知** 按鈕的標題列，如下圖所示。 再按一下按鈕可關閉通知視窗。 

    ![通知中樞](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. 建立完成之後，您會看到 **Data Factory** 分頁，如下所示 ︰

    ![Data Factory 首頁](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### 步驟 2：建立資料管理閘道
5. 在 **DATA FACTORY** 刀鋒視窗中，按一下 [ **作者和部署** ] 磚來啟動 **編輯器** 的 data factory。

    ![[製作和部署] 磚](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.  在 Data Factory 編輯器中，按一下 [ **...（省略符號）** 工具列，然後按一下 [ **新增資料閘道**。 

    ![工具列上的 [新增資料閘道]](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. 在 **建立** 刀鋒視窗中，輸入 **adftutorialgateway** 的 **名稱**, ，然後按一下 **確定**。    

    ![[建立閘道器] 刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. 在 **設定** 刀鋒視窗中，按一下 [ **直接在此電腦上安裝**。 這會下載閘道的安裝套件、在電腦上安裝、設定和註冊閘道。  

    > [AZURE.NOTE] 請使用 Internet Explorer 或 Microsoft ClickOnce 相容的 web 瀏覽器。

    ![閘道器 - [設定] 刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    這是最簡單的方式 (一鍵)，透過單一步驟即可下載、安裝、設定和註冊閘道。 您可以看到 **Microsoft 資料管理閘道組態管理員** 應用程式安裝在電腦上。 您也可以找到可執行檔 **ConfigManager.exe** 資料夾中 ︰ **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**。

    您可以同時下載並使用此刀鋒視窗中的連結，以手動方式安裝閘道器及註冊使用中顯示的金鑰 **REGISTER WITH KEY** 文字方塊。
    
    如需有關閘道器的詳細資訊 (包括最佳作法和重要考量)，請參閱本文開頭的章節。

    >[AZURE.NOTE] 您必須是系統管理員在本機電腦上的安裝及設定資料管理閘道器已成功。 您可以將其他使用者加入至資料管理閘道使用者本機 Windows 群組。 此群組的成員可以使用「資料管理閘道組態管理員」工具來設定閘道器。 

5. 等候數分鐘，並啟動 **資料管理閘道組態管理員** 應用程式在電腦上。 在 **搜尋** 視窗中，輸入 **資料管理閘道器** 來存取此公用程式。 您也可以找到可執行檔 **ConfigManager.exe** 資料夾中 ︰ **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared** 

    ![閘道器組態管理員](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. 請稍候，待這些值設定完成，如下所示：
    1. **狀態** 設為 **已啟動**。
    2. **閘道名稱** 設為 **adftutorialgateway**。
    3. **執行個體名稱** 設為 **adftutorialgateway**。
    4. **註冊** 設為 **註冊**。
    5. 狀態列底部會顯示 **連接到資料管理閘道雲端服務** 連同 **綠色核取記號**。

8. 切換至 **憑證** ] 索引標籤。 此索引標籤上指定的憑證可用來加密/解密您在入口網站指定之內部部署資料存放區的認證。 按一下 [ **變更** 改為使用您自己的憑證。 根據預設，閘道器會使用由 Data Factory 服務自動產生的憑證。

    ![閘道器憑證組態](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. （選擇性）切換至 **診斷** ] 索引標籤，核取 **啟用進行疑難排解的詳細資訊記錄** 選項，如果您想要啟用可用來疑難排解任何問題，閘道器的詳細資訊記錄。 記錄資訊可在 **事件檢視器** 下 **應用程式及服務記錄檔** ]-> [ **資料管理閘道器** 節點。 

    ![[診斷] 索引標籤](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    您也可以使用此頁面來 **測試連接** 使用閘道與內部部署資料來源。
10. 在 Azure 傳統入口網站中，按一下 [ **確定** 上 **設定** 刀鋒視窗，然後在 **新增資料閘道** 刀鋒視窗。
6. 您應該會看到 **adftutorialgateway** 下 **資料閘道** 左邊的樹狀檢視中。  如果按一下，應該會看到相關聯的 JSON。 
    

### 步驟 3：建立連結服務 
在此步驟中，您將建立兩個連結的服務 ︰ **StorageLinkedService** 和 **SqlServerLinkedService**。  **SqlServerLinkedService** 連結內部部署 SQL Server 資料庫和 **StorageLinkedService** 連結的服務會連結至 data factory 的 Azure blob 存放區。 稍後在本逐步解說中，您將建立可將內部部署 SQL Server 資料庫的資料複製到 Azure Blob 存放區的管線。 

#### 在內部部署 SQL Server 資料庫中新增連結服務
1.  在 **Data Factory 編輯器**, ，按一下 [ **新增資料存放區** 工具列，然後選取 **SQL Server**。 

    ![新增 SQL Server 連結服務](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.  在 **JSON 編輯器**, ，執行下列動作 ︰ 
    1. 如 **gatewayName**, ，指定 **adftutorialgateway**。 
    2. 如果您使用 Windows 驗證：
        1. 在 **connectionString**: 
            1. 設定 **整合式安全性** 至 **true**...
            2. 指定資料庫 **伺服器名稱** 和 **資料庫名稱**。 
            2. 移除 **使用者識別碼** 和 **密碼**。 
        3. 指定使用者名稱和密碼 **userName** 和 **密碼** 屬性。
        
                "typeProperties": {
                    "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
                    "gatewayName": "adftutorialgateway",
                    "userName": "<Specify user name if you are using Windows Authentication>",
                    "password": "<Specify password for the user account>"
                }

    4. 如果您使用 SQL 驗證：
        1. 指定資料庫 **伺服器名稱**, ，**資料庫名稱**, ，**使用者識別碼**, ，和 **密碼** 中 **connectionString**。       
        2. 移除最後兩個 JSON 內容- **userName** 和 **密碼** -從 JSON。
        3. 移除尾端 **, ，（逗號）** 在指定的值的行結尾處 **gatewayName** 屬性。 

                "typeProperties": {
                    "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
                    "gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
                }
       
2.  按一下 [ **部署** 命令列來部署 SQL Server 連結服務。 

#### 新增 Azure 儲存體帳戶的連結服務
 
1. 在 **Data Factory 編輯器**, ，按一下 [ **新增資料存放區** 命令列，然後按一下 [ **Azure 儲存體**。
2. 輸入您的 Azure 儲存體帳戶名稱 **帳戶名稱**。
3. 輸入您的 Azure 儲存體帳戶的金鑰 **帳戶金鑰**。
4. 按一下 [ **部署** 部署 **StorageLinkedService**。
   
 
### 步驟 4：建立輸入和輸出資料集
在此步驟中，您會建立代表複製作業的輸入和輸出資料的輸入和輸出資料集 (內部部署 SQL Server 資料庫 => Azure Blob 儲存體)。 在建立資料集或資料表 (矩形資料集) 之前，您必須執行下列動作 (清單後列有詳細步驟)：

- 建立名為 **emp** SQL Server 資料庫中您新增為連結服務的資料處理站和插入幾個範例項目插入資料表。
- 建立名為的 blob 容器 **adftutorial** Azure blob 儲存體帳戶加入做為連結服務的資料處理站中。

### 準備用於教學課程的內部部署 SQL Server

1. 在您指定的內部資料庫中 SQL Server 連結服務 (**SqlServerLinkedService**)，使用下列 SQL 指令碼建立 **emp** 資料庫資料表中的。


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL, 
            FirstName varchar(50),
            LastName varchar(50),
            CONSTRAINT PK_emp PRIMARY KEY (ID)
        )
        GO
 

2. 在資料表中插入一些範例： 


        INSERT INTO emp VALUES ('John', 'Doe')
        INSERT INTO emp VALUES ('Jane', 'Doe')



### 建立輸入資料表

1. 在 **Data Factory 編輯器**, ，按一下 [ **新的資料集** 命令列，然後按一下 [ **SQL Server 資料表**。 
2.  使用下列文字取代右窗格中的 JSON：    

        {
          "name": "EmpOnPremSQLTable",
          "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
              "tableName": "emp"
            },
            "external": true,
            "availability": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
            }
          }
        }

    請注意： 
    
    - **型別** 設為 **SqlServerTable**。
    - **tableName** 設為 **emp**。
    - **linkedServiceName** 設為 **SqlServerLinkedService** （您已在步驟 2 中建立此連結的服務）。
    - 針對不由 Azure Data Factory 中的另一個管線產生的輸入資料表，您必須將 **外部** 至 **true**。 它代表輸入資料產生於 Azure Data Factory 服務外部。 您可以選擇性地指定使用任何外部資料原則 **externalData** 中的項目 **原則** 一節。    

    如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2. 按一下 [ **部署** 命令列來部署資料集 （資料表是矩形的資料集）。 確認您看到訊息指出在標題列 **成功部署資料表**。 


### 建立輸出資料表

1.  在 **Data Factory 編輯器**, ，按一下 [ **新的資料集** 命令列，然後按一下 [ **Azure Blob 儲存體**。
2.  使用下列文字取代右窗格中的 JSON： 

        {
          "name": "OutputBlobTable",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "adftutorial/outfromonpremdf",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }
  
    請注意： 
    
    - **型別** 設為 **AzureBlob**。
    - **linkedServiceName** 設為 **StorageLinkedService** （您已在步驟 2 中建立此連結的服務）。
    - **folderPath** 設為 **adftutorial/outfromonpremdf** 其中 outfromonpremdf 是 adftutorial 容器中的資料夾。 您只需要建立 **adftutorial** 容器。
    -  **可用性** 設為 **每小時** (**頻率** 設 **小時** 和 **間隔** 設 **1**)。  Data Factory 服務會產生輸出資料配量中的每小時 **emp** Azure SQL Database 中的資料表。 

    如果您未指定 **fileName** 的 **輸入的資料表**, ，所有檔案 /blob 輸入資料夾 (**folderPath**) 都會被視為輸入。 如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。 如需範例，請參閱 [教學課程][adf-tutorial] 中的範例檔案。
 
    如果您未指定 **fileName** 的 **輸出資料表**, 、 中產生的檔案 **folderPath** 依照下列格式命名 ︰ 資料。<Guid>.txt (例如:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt。)。

    若要設定 **folderPath** 和 **fileName** 動態根據 **SliceStart** 時間，請使用 partitionedBy 屬性。 在下列範例中，folderPath 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。 例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

    如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2.  按一下 [ **部署** 命令列來部署資料集 （資料表是矩形的資料集）。 確認您看到訊息指出在標題列 **成功部署資料表**。
  

### 步驟 5：建立和執行管線
在此步驟中，您會建立 **管線** 以內 **複製活動** 使用 **EmpOnPremSQLTable** 做為輸入和 **OutputBlobTable** 做為輸出。

1.  在 **DATA FACTORY** 刀鋒視窗中，按一下 [ **作者和部署** ] 磚來啟動 **編輯器** 的 data factory。

    ![[製作和部署] 磚](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.  按一下 [ **新增管線** 命令列上。 如果看不到 [] 按鈕，按一下 [ **...（省略符號）** 若要展開命令列。
2.  使用下列文字取代右窗格中的 JSON：   


        {
          "name": "ADFTutorialPipelineOnPrem",
          "properties": {
            "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
            "activities": [
              {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-prem SQL server to blob",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "EmpOnPremSQLTable"
                  }
                ],
                "outputs": [
                  {
                    "name": "OutputBlobTable"
                  }
                ],
                "typeProperties": {
                  "source": {
                    "type": "SqlSource",
                    "sqlReaderQuery": "select * from emp"
                  },
                  "sink": {
                    "type": "BlobSink"
                  }
                },
                "Policy": {
                  "concurrency": 1,
                  "executionPriorityOrder": "NewestFirst",
                  "style": "StartOfInterval",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-02-13T00:00:00Z",
            "end": "2015-02-14T00:00:00Z",
            "isPaused": false
          }
        }

    Note the following:
 
    - In the activities section, there is only activity whose **type** is set to **Copy**.
    - **Input** for the activity is set to **EmpOnPremSQLTable** and **output** for the activity is set to **OutputBlobTable**.
    - In the **transformation** section, **SqlSource** is specified as the **source type** and **BlobSink **is specified as the **sink type**.
    - SQL query **select * from emp** is specified for the **sqlReaderQuery** property of **SqlSource**.

    Replace the value of the **start** property with the current day and **end** value with the next day. Both start and end datetimes must be in [ISO format](http://en.wikipedia.org/wiki/ISO_8601). For example: 2014-10-14T16:32:41Z. The **end** time is optional, but we will use it in this tutorial. 
    
    If you do not specify value for the **end** property, it is calculated as "**start + 48 hours**". To run the pipeline indefinitely, specify **9/9/9999** as the value for the **end** property. 
    
    You are defining the time duration in which the data slices will be processed based on the **Availability** properties that were defined for each Azure Data Factory table.
    
    In the example above, there will be 24 data slices as each data slice is produced hourly.
    
2. 按一下 [ **部署** 命令列來部署資料集 （資料表是矩形的資料集）。 確認您看到訊息指出在標題列 **成功部署管線**。  
5. 現在，關閉 **編輯器** 刀鋒視窗中，依序按一下 **X**。按一下 [ **X** ] 以關閉 [ADFTutorialDataFactory] 刀鋒視窗的工具列和樹狀檢視。 如果您看到 **將捨棄未儲存的編輯** 訊息中，按一下 **確定**。
6. 您應該會回到 **DATA FACTORY** 分頁 **ADFTutorialOnPremDF**。

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。

#### 在圖表檢視中檢視 Data Factory 
1. 在 **Azure 入口網站**, ，按一下 [ **圖表** 在首頁上的磚 **ADFTutorialOnPremDF** 資料處理站。 ：

    ![圖表連結](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. 您應該會看到如下所示的圖表：

    ![圖表檢視](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    您可以將管線和資料表放大、縮小、放大到 100%、縮放至適當比例和自動定位，以及顯示歷程資訊 (反白顯示所選取項目的上游和下游項目)。  您可以按兩下物件 (輸入/輸出資料表或管線) 查看其屬性。 

### 步驟 6：監視資料集和管線
在這個步驟中，您將使用 Azure 傳統入口網站來監視 Azure Data Factory 的運作情形。 您也可以使用 PowerShell Cmdlet 來監視資料集和管線。 如需有關監視的詳細資訊，請參閱 [監視和管理管線](monitor-manage-pipelines.md)。

1. 瀏覽至 **Azure 入口網站** （如果您已關閉它）
2. 如果分頁 **ADFTutorialOnPremDF** 尚未開啟，按一下以開啟 **ADFTutorialOnPremDF** 上 **儀表板**。
3. 您應該會看到 **計數** 和 **名稱** 之資料表和您在此刀鋒視窗建立的管線。

    ![Data Factory 首頁](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. 現在，按一下 [ **資料集** 並排顯示。
5. 在 **資料集** 刀鋒視窗中，按一下 [ **EmpOnPremSQLTable**。

    ![EmpOnPremSQLTable 配量](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. 請注意，截至目前為止的資料配量都已產生，而且它們是 **準備**。 這是因為您已將資料插入 SQL Server 資料庫中，而資料一直留存於其中。 確認任何配量顯示在 **問題配量** 底部區段。


    Both **Recently updated slices** and **Recently failed slices** lists are sorted by the **LAST UPDATE TIME**. The update time of a slice is changed in the following situations. 
    

    -  You update the status of the slice manually, for example, by using the **Set-AzureRmDataFactorySliceStatus** (or) by clicking **RUN** on the **SLICE** blade for the slice.
    -  The slice changes status due to an execution (e.g. a run started, a run ended and failed, a run ended and succeeded, etc).
 
    Click on the title of the lists or **... (ellipses)** to see the larger list of slices. Click **Filter** on the toolbar to filter the slices.  
    
    To view the data slices sorted by the slice start/end times instead, click **Data slices (by slice time)** tile.

7. 現在，在 **資料集** 刀鋒視窗中，按一下 [ **OutputBlobTable**。

    ![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. 確認已產生，截至目前為止的配量和 **準備**。 截至目前為止的配量的狀態設定為等候 **準備**。
9. 按一下從清單中的任何資料配量，您應該會看到 **資料配量** 刀鋒視窗。

    ![資料配量刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    如果配量不是處於 **準備** 狀態，您可以看到未就緒且阻礙目前配量中執行的上游配量 **未就緒的上游配量** 清單。

10. 按一下 [ **活動執行** 看到底部清單從 **活動執行詳細資料**。

    ![活動執行詳細資料刀鋒視窗][image-data-factory-activity-run-details]

11. 按一下 [ **X** ，直到您關閉所有刀鋒視窗 
12. 回到主要分頁 **ADFTutorialOnPremDF**。
14. （選擇性）按一下 [ **管線**, ，按一下 [ **ADFTutorialOnPremDF**, ，然後逐步深入輸入資料表 (**已使用**) 或輸出資料表 (**產生**)。
15. 使用工具，例如 **Azure 儲存體總管** 來驗證輸出。

    ![Azure 儲存體總管](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## 在電腦之間移動閘道器
本節提供將閘道器用戶端從一台電腦移至另一台電腦的步驟。 

2. 在入口網站中，瀏覽至 **Data Factory 首頁**, ，然後按一下 **連結服務** 並排顯示。 

    ![資料閘道連結](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. 選取您的閘道中 **資料閘道** 區段 **連結服務** 刀鋒視窗。
    
    ![[連結服務] 刀鋒視窗與所選取的閘道器](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. 在 **資料閘道** 刀鋒視窗中，按一下 [ **下載並安裝資料閘道**。
    
    ![下載閘道器連結](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. 在 **設定** 刀鋒視窗中，按一下 [ **下載並安裝資料閘道** 然後遵循指示來安裝在電腦上的資料閘道。 

    ![設定刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. 保留 **Microsoft 資料管理閘道組態管理員** 開啟。 
 
    ![組態管理員](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)  
7. 在 **設定** 刀鋒視窗中，在網站中，按一下 **重新建立索引鍵** 命令列，然後按一下 [ **是** 警告訊息。 按一下 [ **複製按鈕** 金鑰來將金鑰複製到剪貼簿文字旁邊。 請注意，一旦重新建立索引鍵，舊電腦上的閘道器便會停止運作。  
    
    ![重新建立索引鍵](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
     
8. 貼上 **金鑰** 中的文字方塊中 **註冊閘道** 頁面 **資料管理閘道組態管理員** 您的電腦上。 （選擇性）按一下 [ **顯示閘道器金鑰** 核取方塊，請參閱金鑰文字。 
 
    ![複製金鑰與註冊](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. 按一下 [ **註冊** 與雲端服務註冊閘道。
10. 在 **指定憑證** 頁面上，按一下 **瀏覽** 若要選取所使用的舊的閘道器相同的憑證，請輸入 **密碼**, ，然後按一下 **完成**。 
 
    ![指定憑證](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

    您可以從舊的閘道匯出憑證，方法如下 ︰ 在舊電腦上啟動資料管理閘道組態管理員，請切換至 **憑證** 索引標籤上，按一下 [ **匯出** 按鈕，然後遵循指示進行。 
10. 之後的閘道註冊成功，您應該會看到 **註冊** 設 **註冊** 和 **狀態** 設 **已啟動** 在首頁上的閘道組態管理員。 

## 設定認證和安全性

您也可以使用 [連結服務] 刀鋒視窗而不是使用 [Data Factory 編輯器] 建立 SQL Server 連結服務。 
 
3.  在 Data Factory 的首頁上，按一下 **連結服務** 並排顯示。 
4.  在 **連結服務** 刀鋒視窗中，按一下 [ **新增資料存放區** 命令列上。 
4.  輸入 **SqlServerLinkedService** 的 **名稱**。 
2.  按一下箭號旁 **類型**, ，然後選取 **SQL Server**。

    ![建立新的資料存放區](./media/data-factory-move-data-between-onprem-and-cloud/new-data-store.png)
3.  您應該更多設定下列 **類型** 設定。
4.  如 **資料閘道** 設定中，選取您剛建立的閘道。 

    ![SQL Server 設定](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-settings.png)
4.  輸入您的資料庫伺服器的名稱 **伺服器** 設定。
5.  輸入資料庫名稱 **資料庫** 設定。
6.  按一下箭號旁 **認證**。

    ![認證刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/credentials-dialog.png)
7.  在 **認證** 刀鋒視窗中，按一下 [ **按一下這裡以設定認證**。
8.  在 **設定認證** 對話方塊方塊中，執行下列動作 ︰

    ![設定認證對話方塊](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png)
    1.  選取 **驗證** 您想要用來連接到資料庫的 Data Factory 服務。 
    2.  輸入具有資料庫的存取權之使用者的名稱為 **USERNAME** 設定。 
    3.  輸入的使用者密碼 **密碼** 設定。  
    4.  按一下 [ **確定** 以關閉對話方塊。 
4. 按一下 [ **確定** 關閉 **認證** 刀鋒視窗。 
5. 按一下 [ **確定** 上 **新增資料存放區** 刀鋒視窗。    
6. 確認已選取的狀態 **SqlServerLinkedService** 連結服務刀鋒視窗中會設為 Online。
    ![SQL Server 連結服務狀態](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

如果您從閘道器電腦以外的另一台電腦存取入口網站，您必須確定「認證管理員」應用程式可以連接到閘道器電腦。 如果應用程式無法連接閘道器電腦，它將不允許您設定資料來源的認證，以及測試資料來源的連接。 

當您使用從 Azure 傳統入口網站啟動的「設定認證」應用程式，設定內部部署資料來源的認證時，入口網站會利用您在閘道器電腦上 [資料管理閘道器組態管理員] 的 [認證] 索引標籤中指定的憑證，來加密認證。 

如果您要尋找的 API 為基礎的方法來加密認證可以使用 [新增 AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell cmdlet 來加密認證。 此 cmdlet 會使用閘道器設定用來加密認證的憑證。 您可以由這個指令程式加密的認證並將它新增至所要使用的 JSON 檔案中 connectionString 的 EncryptedCredential 項目 [新增 AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) cmdlet 或 JSON 片段中 Data Factory 編輯器在入口網站中。 

    "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**注意 ︰** 如果您使用 「 設定認證 」 應用程式會自動設定加密的認證中的連結服務如上所示。

設定認證只能使用 Data Factory 編輯器，沒有其他方法。 如果您使用編輯器建立 SQL Server 連結服務，並且以純文字輸入認證，認證就會使用 Data Factory 服務所擁有的憑證加密，而不使用閘道器設定要使用的憑證。 雖然這種方法在某些情況下可能快一點，但也比較不安全。 因此，建議您只在開發/測試用途才採用此方法。 


## 使用 Azure PowerShell 建立和註冊閘道器 
本節說明如何使用 Azure PowerShell Cmdlet 建立和註冊閘道。 

1. 啟動 **PowerShell** 系統管理員模式。 
2. Azure Data Factory cmdlet 可用於 **AzureResourceManager** 模式。 執行下列命令以切換至 **AzureResourceManager** 模式。     

        switch-azuremode AzureResourceManager


2. 使用 **新增 AzureRmDataFactoryGateway** cmdlet 來建立邏輯閘道器，如下所示 ︰

        New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

    **範例命令和輸出**:


        PS C:\> New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

        Name              : MyGateway
        Description       : gateway for walkthrough
        Version           :
        Status            : NeedRegistration
        VersionStatus     : None
        CreateTime        : 9/28/2014 10:58:22
        RegisterTime      :
        LastConnectTime   :
        ExpiryTime        :
        ProvisioningState : Succeeded


3. 使用 **新增 AzureRmDataFactoryGatewayKey** 指令程式可產生註冊金鑰，為新建立的閘道，並將金鑰儲存在本機變數 **$Key**:

        New-AzureRmDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

    
    **範例命令輸出：**


        PS C:\> $Key = New-AzureRmDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

    
4. 在 Azure PowerShell 中，切換到資料夾: **C:\Program Files\Microsoft 資料管理 Gateway\1.0\PowerShellScript\** 並執行 **RegisterGateway.ps1** 與本機變數相關聯的指令碼 **$Key** 您稍早建立下列命令以註冊您的邏輯閘道器電腦上安裝用戶端代理程式中所示。

        PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
        
        Agent registration is successful!

5. 您可以使用 **Get AzureRmDataFactoryGateway** cmdlet 取得 data factory 中的閘道清單。 當 **狀態** 顯示 **線上**, ，這表示您的閘道是可供使用。

        Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

您可以移除閘道，使用 **移除 AzureRmDataFactoryGateway** 閘道使用的 cmdlet，並更新描述 **組 AzureRmDataFactoryGateway** 指令程式。 如需這些 Cmdlet 的語法及其他詳細資訊，請參閱 Data Factory Cmdlet 參考文件。  


## 使用資料管理閘道進行複製的資料流
當您使用資料管線中的複製活動，將內部部署資料擷取至雲端以進行進一步的處理，或者匯出雲端中的結果資料回到內部部署資料存放區，複製活動都會在內部使用閘道器將資料從內部部署資料來源傳輸回雲端，反之亦然。

這裡的高層級的資料流和複製資料閘道的步驟摘要 ︰
![使用閘道器的資料流](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.  資料開發人員建立新的閘道，使用 Azure Data Factory 的 [Azure 傳統入口網站](http://portal.azure.com) 或 [PowerShell Cmdlet](https://msdn.microsoft.com/library/dn820234.aspx)。 
2.  資料開發人員會使用「連結服務」面板，透過閘道器定義內部部署資料存放區的新連結服務。 在設定連結服務資料的過程中，開發人員會使用設定認證應用程式指定驗證類型和認證，如逐步解說所示。  設定認證應用程式對話方塊將會與資料存放區進行通訊以測試要儲存認證的連線與閘道器。
3.  在雲端中儲存認證之前，閘道器會利用與閘道器 (由資料開發人員提供) 相關聯的憑證加密認證。
4.  資料處理站移動服務會和閘道器進行通訊，以透過使用共用 Azure 服務匯流排佇列的控制通道，進行工作的排程和管理。 必須開始複製活動工作時，資料處理站會將要求和認證資訊一起排入佇列。 輪詢佇列之後，閘道器隨即啟動。
5.  閘道器會利用相同的憑證解密認證，然後利用適當的驗證類行連接到內部部署資料存放區。
6.  閘道器會根據複製活動在資料管線中的設定方式，將資料從內部部署存放區複製到雲端儲存體，或從雲端儲存體複製到內部部署資料存放區。 請注意：在這個步驟中，閘道器會透過安全 (HTTPS) 通道直接與以雲端為基礎的儲存體服務 (例如 Azure Blob、Azure SQL 等) 通訊。

### 連接埠和安全性考量

1. 如同上方逐步解說所述，有多種方式可以利用資料處理站為內部部署資料存放區設定認證。 這些選項的連接埠考量不盡相同。 

    - 使用 **設定認證** 應用程式 ︰ 資料管理閘道器安裝程式預設會開啟 **8050** 和 **8051** 防火牆在閘道器電腦的本機 windows 上的連接埠。 設定認證應用程式會使用這些連接埠將認證轉送至閘道器。 這些連接埠址會在本機 Windows 防火牆上的電腦才會開啟。 網際網路無法連接至這些連接埠，您不需要在全公司防火牆中開啟這些連接埠。
    2.  使用 [新增 AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) powershell commandlet:。  如果您使用 powershell 命令來加密認證，而且您最後不想閘道器安裝程式在 Windows 防火牆中開啟輸入連接埠，您可以在安裝期間使用下列命令即可辦到：
    
            msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
3.  如果您使用 **設定認證** 應用程式必須能夠連接到資料管理閘道器，可以設定資料來源的認證，以及測試資料來源連接的電腦上啟動。
4.  在內部部署 SQL Server 資料庫和 Azure SQL Database 之間往返複製資料時，請確定下列各項：   
    -   閘道器電腦上的防火牆允許連出 TCP 通訊上 **TCP** 連接埠 **1433年**。
    -   設定 [SQL Azure 防火牆設定](https://msdn.microsoft.com/library/azure/jj553530.aspx) 新增 **的閘道 IP 位址** 電腦到 **允許的 IP 位址**。
5.  當複製資料至/從內部部署 SQL Server 至任何目的地，但閘道器和 SQL Server 電腦不同時，執行下列動作 ︰ [設定 Windows 防火牆](https://msdn.microsoft.com/library/ms175043.aspx) 在 SQL Server 電腦上，讓閘道可以透過存取資料庫的 SQL Server 執行個體接聽的連接埠。 在預設執行個體中，這是連接埠 1433。





