   <properties
   pageTitle="將資料載入 SQL 資料倉儲 | Microsoft Azure"
   description="了解 SQL 資料倉儲的常見資料載入情況"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/02/2015"
   ms.author="lodipalm;barbkess"/>

# 將資料載入 SQL 資料倉儲
SQL 資料倉儲提供許多選項，供載入資料，包括：

- PolyBase
- Azure Data Factory
- BCP 命令列公用程式
- SQL Server Integration Services (SSIS)
- 協力廠商資料載入工具

雖然所有上述方法可以搭配 SQL 資料倉儲使用，但 PolyBase 能明確地平行處理 Azure Blob 儲存體的負載，這讓它成為載入資料最快的工具。  查看更多有關如何 [使用 PolyBase 載入][]。  此外，由於許多使用者都在尋求從內部部署來源初始載入 100 秒 GB 到 10 秒 TB，因此在以下各節中，我們將針對初始資料的載入提供一些指引。  

## 從 SQL Server 初始載入至 SQL 資料倉儲的 
從內部部署 SQL Server 執行個體載入至 SQL 資料倉儲時，建議採用下列步驟：

1. **BCP** 成一般檔案的 SQL Server 資料 
2. 使用 **AZCopy** 或 **匯入/匯出** （適用於大型資料集） 將檔案移至 Azure
3. 將 PolyBase 設定為從您的儲存體帳戶讀取您的檔案
4. 建立新的資料表並載入資料與 **PolyBase**

在下列各節中，我們將深入探討每個步驟，並提供程序的範例。

> [AZURE.NOTE] 之前的系統，例如 SQL Server 移動資料時，我們建議您檢閱 [移轉結構描述][] 和 [移轉程式碼][] 文件的文件。 

## 使用 BCP 匯出檔案

若要準備將您的檔案移動到 Azure，您必須將它們匯出至一般檔案。  最佳作法就是使用 BCP 命令列公用程式。  如果您沒有此公用程式還可以下載與 [Microsoft SQL server 的命令列公用程式][]。  範例 BCP 命令看起來可能如下所示：

```
bcp "<Directory>\<File>" -c -T -S <Server Name> -d <Database Name>
```

此命令將取得查詢的結果，並將它們匯出至您所選擇的目錄中的檔案。 您可以藉由執行多個 BCP 命令對個別資料表一次這可以讓您執行您的伺服器的每個核心一個 BCP 處理序平行處理程序，我們的建議是嘗試幾個較小的作業，在不同的組態，以便查看何者最適用於您的環境。

此外，當我們使用 PolyBase 載入時，請注意，PolyBase 尚未支援 UTF-16，因此所有檔案必須以 UTF-8 表示。  可以輕鬆地完成此作業，方法是在您的 BCP 命令中包含 '-c' 旗標，或者，您也可以使用下列程式碼，將一般檔案從 UTF-16 轉換為 UTF-8：

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```
 
一旦順利地將您的資料匯出至檔案，就可以開始將它們移至 Azure。  完成此作業的方法為使用 AZCopy 或使用匯入/匯出服務，如下節所述。  

## 使用 AZCopy 或匯入/匯出載入至 Azure
如果您要移動 5-10 tb 範圍中的資料或以上所述，我們建議您使用我們的磁碟運送服務 [匯入/匯出][] 以便完成移動。  不過，在我們的研究中，我們已經能夠使用公用網際網路搭配 AZCopy，輕鬆地移動單一數字 TB 範圍中的資料。  此程序也可以加速或透過 ExpressRoute 擴充。

下列步驟將詳細說明如何使用 AZCopy 將資料從內部部署移入 Azure 儲存體帳戶。  如果您沒有 Azure 儲存體帳戶位於相同區域中您可以建立一個遵循 [Azure 儲存體文件][]。  您也可以從不同區域中的儲存體帳戶載入資料，但在此情況下，效能將不是最佳的。  

> [AZURE.NOTE] 本文件假設您已安裝 AZCopy 命令列公用程式，並能夠使用 Powershell 執行它。  如果情況並非如此，則請遵循 [AZCopy 安裝指示][]。  

現在，已提供一組使用 BCP 建立的檔案，因此 AzCopy 只需從 Azure powershell 或藉由執行 powershell 指令碼來執行。  在更高的層級中，執行 AZCopy 所需的提示將會採用下列格式：

```
AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256
```

除了基本作法外，我們也建議您採用下列最佳作法，使用 AZCopy 載入：


+ **並行連線**︰ 除了增加同時執行的 AZCopy 作業數目，AZCopy 作業本身可進一步平行處理藉由設定 /NC 參數，這樣會開啟到目的地的同時連線數。  儘管這個參數最高可以設定為 512，但是我們發現最佳的資料傳輸發生在 256，因此建議測試值的數目，以尋找何者最適合您的組態。

+ **快速路由**︰ 如上所述，此程序就能加速如果已啟用快速路由。  快速路由和步驟設定的概觀位於 [ExpressRoute 文件][]。 

+ **資料夾結構**︰ 若要更輕鬆地使用 PolyBase 進行傳輸，請確定每個資料表會對應到它自己的資料夾。  如此，稍後使用 PolyBase 載入時，將簡化步驟並將其減至最少。 換言之，如果資料表分割成多個檔案，或甚至是資料夾內的子目錄，也沒有任何影響。 
     

## 設定 PolyBase 

既然您的資料位於 Azure 儲存體 blob 中，我們即會使用 PolyBase 將它匯入 SQL 資料倉儲執行個體中。  以下步驟僅適用於組態，而且對於每個 SQL 資料倉儲執行個體、使用者或儲存體帳戶，其中許多步驟只需完成一次即可。  說明這些步驟必須也已在更詳細地我們 [使用 PolyBase 載入][] 文件。  

1. **建立資料庫主要金鑰。**  每個資料庫只需完成這項作業一次。 

2. **建立資料庫範圍認證。**  如果您要查看如何建立新的認證/使用者，只需建立這項作業，否則可以使用先前建立的認證。 

3. **建立外部檔案格式。**  外部檔案格式也可以重複使用。如果您要上傳新類型的檔案，只需建立一個即可。 

4. **建立外部資料來源。**  當指出儲存體帳戶時，如果從同一個容器載入，則可以使用外部資料來源。 對於您的 'LOCATION' 參數，請使用下列格式的位置：'wasbs://mycontainer@ test.blob.core.windows.net/path'。

```
-- Creating master key
CREATE MASTER KEY;

-- Creating a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL <Credential Name> 
WITH 
    IDENTITY = '<User Name>'
,   Secret = '<Azure Storage Key>'
;

-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(
    FORMAT_TYPE = DELIMITEDTEXT 
,   FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|' 
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
);

--Creating an external data source
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH 
(
    TYPE = HADOOP 
,   LOCATION ='wasbs://<Container>@<Blob Path>'
,   CREDENTIAL = <Credential Name>
)
;
```

既然已適當地設定您的儲存體帳戶，您就可以繼續將您的資料載入 SQL 資料倉儲中。  

## 使用 PolyBase 載入資料 
設定 PolyBase 之後，您可以將資料直接載入您的 SQL 資料倉儲中，方法是只需建立一個外部資料表，指向儲存體中的資料，然後將該資料對應到 SQL 資料倉儲內的新資料表。  您可以使用下列兩個簡單命令來完成此作業。 

1. 使用 ' CREATE EXTERNAL TABLE' 命令來定義您的資料結構。  若要確保您快速且有效地擷取資料的狀態，我們建議在 SSMS 中撰寫 SQL Server 資料表的指令碼，然後以手動方式調整，說明外部資料表差異。 在 Azure 中建立外部資料表之後，它將繼續指向相同的位置，即使更新資料或加入其他資料也一樣。  

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE <External Table Name> 
(
    <Column name>, <Column type>, <NULL/NOT NULL>
)
WITH 
(   LOCATION='<Folder Path>'
,   DATA_SOURCE = <Data Source>
,   FILE_FORMAT = <File Format>      
);
```

2. 使用 'CREATE TABLE...AS SELECT' 陳述式載入資料。 

```
CREATE TABLE <Table Name> 
WITH 
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = <HASH(<Column Name>)>/<ROUND_ROBIN>
)
AS 
SELECT  * 
FROM    <External Table Name>
;
```

請注意，您也可以使用更詳細的 SELECT 陳述式，從資料表載入資料列的子區段。  不過，因為 PolyBase 目前不會推送額外的運算至儲存體帳戶，所以如果您使用 SELECT 陳述式載入子區段，這將不會快於載入整個資料集。 

除了 `CREATE TABLE...AS SELECT` 陳述式外，您也可以使用 'INSERT...INTO' 陳述式，將資料從外部資料表載入到預先存在的資料表。

##  建立新載入資料的統計資料 

Azure 資料倉儲尚未支援自動建立或自動更新統計資料。  為了獲得查詢的最佳效能，在首次載入資料，或是資料中發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。  如需統計資料的詳細說明，請參閱 [統計資料][] 開發一組主題中的主題。  以下是快速範例，說明如何在此範例中建立載入資料表的統計資料。


```
create statistics [<name>] on [<Table Name>] ([<Column Name>]);
create statistics [<another name>] on [<Table Name>] ([<Another Column Name>]);
```

## 後續步驟
如需更多開發秘訣，請參閱 [開發概觀][]。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrate schema]: sql-data-warehouse-migrate-schema.md
[Migrate code]: sql-data-warehouse-migrate-code.md
[Statistics]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

<!--Other Web references-->
[AZCopy Installation Instructions]:https://azure.microsoft.com/en-us/documentation/articles/storage-use-azcopy/
[Microsoft Command Line Utilities for SQL Server]:http://www.microsoft.com/en-us/download/details.aspx?id=36433
[Import/Export]: https://azure.microsoft.com/en-us/documentation/articles/storage-import-export-service/
[Azure Storage Documentation]:https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[ExpressRoute documentation]:http://azure.microsoft.com/en-us/documentation/services/expressroute/


