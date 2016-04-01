<properties 
    pageTitle="Azure 上的 SQL Server 取樣資料 | Microsoft Azure" 
    description="在 Azure 上 SQL Server 中進行資料取樣" 
    services="machine-learning" 
    documentationCenter="" 
    authors="fashah" 
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2015" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Azure 上的 SQL Server 取樣資料

本文件顯示如何使用 SQL 和 Python 程式設計語言，對儲存在 Azure 上之 SQL Server 中的資料進行取樣。 也示範如何透過將取樣的資料儲存到檔案，讓取樣資料移動到 Azure Machine Learning、將取樣的資料上傳至 Azure blob，然後將其讀入 Azure ML。

Python 取樣使用 [pyodbc](https://code.google.com/p/pyodbc/) ODBC 程式庫來連接到 Azure 上 SQL Server 和[Pandas](http://pandas.pydata.org/) 要取樣的程式庫。

>[AZURE.NOTE] 本文件中的範例 SQL 程式碼假設資料位於 Azure 上的 SQL Server。 如果沒有，請參閱 [在 Azure 上將資料移至 SQL Server](machine-learning-data-science-move-sql-server-virtual-machine.md) 主題，如需如何將資料移至 SQL Server，在 Azure 上的指示。

##<a name="SQL"></a>使用 SQL

本節將說明使用 SQL，對資料庫中的資料執行簡單隨機取樣的數個方法。 請根據資料大小及其分佈來選擇方法。

以下兩個項目示範如何在 SQL Server 中使用 newid 進行取樣。 您選擇的方法取決於想要的隨機取樣程度 (程式碼範例底下的 pk_id 已假設為自動產生的主索引鍵)。

1. 較不嚴格的隨機取樣

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. 更隨機範例 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample 可用來進行取樣及示範，如下所示。 如果資料大小很大 (假設不同頁面上的資料不會相互關聯)，而且要讓查詢在合理時間內完成，這可能是較好的方法。

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] 您可以瀏覽並從這個取樣的資料產生功能，將它儲存在新的資料表


###<a name="sql-aml"></a>連接到 Azure Machine Learning

直接，您可以使用 Azure ML 讀取程式模組中上述的範例查詢與向下取樣的資料即時帶入 Azure ML 實驗。 使用讀取程式模組讀取取樣資料的螢幕擷取畫面如下所示：
   
![讀取器 SQL][1]

##<a name="python"></a>使用 Python 程式設計語言 

本節示範如何使用 [pyodbc 程式庫](https://code.google.com/p/pyodbc/) 建立 ODBC 連接到 Python 中的 SQL server 資料庫。 資料庫連接字串如下：(使用您的設定來取代伺服器名稱、資料庫名稱、使用者名稱和密碼)：

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

 [Pandas](http://pandas.pydata.org/) Python 中的程式庫提供一組豐富的資料結構和資料分析工具，對 Python 程式設計進行資料操作。 下列程式碼會從 Azure SQL 資料庫中的資料表，將 0.1% 的資料樣本讀取至 Pandas 資料中：

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

您現在可以在 Pandas 資料框架中使用取樣資料。 

###<a name="python-aml"></a>連接到 Azure Machine Learning

您可以使用下列程式碼範例，將向下取樣的資料儲存至檔案，並將它上傳至 Azure Blob。 Blob 中的資料可以直接讀取至 Azure ML 實驗使用 *讀取器模組*。 步驟如下： 

1. 將 Pandas 資料框架寫入本機檔案

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 將本機檔案上傳至 Azure Blob

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. 從使用 Azure ML 的 Azure blob 讀取資料 *讀取器模組* 如下列螢幕擷取畫面所示 ︰
 
![讀取器 Blob][2]

## Cortana 分析程序實務範例

Cortana 分析程序的端對端逐步解說範例，使用公用資料集，請參閱 [作用中的 Cortana 分析程序 ︰ 使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md)。

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 

