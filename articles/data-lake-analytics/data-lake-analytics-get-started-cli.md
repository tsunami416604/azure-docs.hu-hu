<properties 
   pageTitle="運用 Azure 命令列介面開始使用 Azure 資料湖分析 | Microsoft Azure" 
   description="了解如何透過 Azure 命令列介面建立資料湖存放區帳戶、使用 U-SQL 建立資料湖分析工作，以及提交工作。 " 
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
   ms.date="11/02/2015"
   ms.author="jgao"/>

# 教學課程：運用 Azure 命令列介面 (CLI) 開始使用 Azure 資料湖分析

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


了解如何使用 Azure CLI 來建立 Azure 資料湖分析帳戶、 定義資料湖分析
中的工作 [U SQL](data-lake-analytics-u-sql-get-started.md), ，將作業提交至資料湖分析帳戶。 如需 
資料湖分析的相關資訊，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

在本教學課程中，您將開發工作讀取定位點分隔值 (TSV) 檔案，並將它轉換成逗號 
分隔的值 (CSV) 檔案。 若要使用其他支援的工具進行同一個教學課程，請按一下此區段最上方的索引標籤。

**基本資料湖分析程序：**

![Azure 資料湖分析程序流程圖](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. 建立資料湖分析帳戶。
2. 準備來源資料。 資料湖分析工作可從 Azure 資料湖存放區帳戶或 Azure Blob 儲存體帳戶讀取資料。   
3. 開發 U SQL 指令碼。
4. 將工作 (U-SQL 指令碼) 提交至資料湖分析帳戶。 作業會讀取來源資料，處理資料的指示 
U SQL 指令碼，然後再儲存輸出資料湖存放區帳戶或 Blob 儲存體帳戶。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure CLI**。 請參閱 [安裝和設定 Azure CLI](xplat-cli.md)。
    - 下載並安裝 **發行前版本** [Azure CLI 工具](https://github.com/MicrosoftBigData/AzureDataLake/releases) 才能完成這個示範。
- **驗證**, ，使用下列命令 ︰

        azure login
    如需有關如何使用工作或學校帳戶進行驗證的詳細資訊，請參閱 [從 Azure CLI 連接至 Azure 訂用帳戶](xplat-cli-connect.md)。
- **切換至 Azure 資源管理員模式**, ，使用下列命令 ︰

        azure config mode arm
        
## 建立資料湖分析帳戶

您必須擁有資料湖分析帳戶，才能執行工作。 若要建立資料湖分析帳戶，您必須指定下列項目：

- **Azure 資源群組**: 資料湖分析帳戶必須建立 Azure 資源群組內。 [Azure 資源管理員](resource-group-overview.md) 可讓您能夠使用您的應用程式群組中的資源。 您可以透過單一、協調的作業來部署、更新或刪除應用程式的所有資源。  

    若要列舉您訂用帳戶中的資源群組：
    
        azure group list 
    
    若要建立新的資源群組：

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **資料湖分析帳戶名稱**
- **位置**︰ 支援資料湖分析的 Azure 資料中心的其中一個。
- **預設資料湖帳戶**︰ 每個資料湖分析帳戶具有預設資料湖帳戶。

    若要列出現有的資料湖帳戶：
    
        azure datalake store account list

    若要建立新的資料湖帳戶：

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] 資料湖帳戶名稱只能包含小寫字母和數字。



**建立資料湖分析帳戶**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![資料湖分析顯示帳戶](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] 資料湖分析帳戶名稱只能包含小寫字母和數字。


## 將資料上傳至資料湖存放區

在本教學課程中，您將會處理一些搜尋記錄檔。  搜尋記錄檔可以儲存在資料湖存放區或 Azure Blob 儲存體中。 

Azure 入口網站會提供使用者介面，可將範例資料檔案複製到預設的資料湖帳戶，其中包括搜尋記錄檔案。 請參閱 [準備來源資料](data-lake-analytics-get-started-portal.md#prepare-source-data) 將資料上傳到預設資料湖存放區帳戶。

若要使用 CLI 上傳檔案，請使用下列命令：

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

資料湖分析也可存取 Azure Blob 儲存體。  資料上傳至 Azure Blob 儲存體，請參閱 [使用 Azure CLI 與 Azure 儲存體](storage-azure-cli.md)。

## 提交資料湖分析工作

資料湖分析工作是以 U-SQL 語言撰寫。 若要深入了解 U SQL，請參閱 [開始 U SQL 語言使用](data-lake-analytics-u-sql-get-started.md) 和 [U SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**建立資料湖分析工作指令碼**

- 使用下列 U-SQL 指令碼建立文字檔，並將該檔案儲存到您的工作站：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    這個 U SQL 指令碼會讀取來源資料檔使用 **Extractors.Tsv()**, ，然後建立 csv 檔案使用 **Outputters.Csv()**。 
    
    除非您將來源檔案複製到其他位置，否則請勿修改這兩個路徑。  資料湖分析將建立輸出資料夾 (若尚未建立)。
    
    使用儲存在預設資料湖帳戶中檔案的相對路徑，是比較容易的方法。 您也可以使用絕對路徑。  例如 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    您必須使用絕對路徑存取連結儲存體帳戶中的檔案。  儲存在連結 Azure 儲存體帳戶中之檔案的語法是：
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] 目前不支援具有公用 blob 或容器的公用存取權限的 azure Blob 容器。      

    
**提交工作**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
下列命令可用來列出工作、取得工作詳細資料和取消工作：

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

工作完成之後，您可以使用下列 Cmdlet 列出該檔案，並下載檔案：
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## 另請參閱

- 若要使用其他工具檢視同一個教學課程，請按一下頁面最上方的索引標籤選取器。
- 若要查看更 complexed 的查詢，請參閱 [使用 Azure 資料湖分析分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
- 若要開始開發 U SQL 應用程式，請參閱 [使用資料湖 Tools for Visual Studio 開發 U SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要了解 U SQL，請參閱 [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
- 管理工作，請參閱 [管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)。
- 若要取得資料湖分析的概觀，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。



