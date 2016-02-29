<properties 
    pageTitle="使用 Pandas 瀏覽 Azure blob 儲存體中的資料 | Microsoft Azure" 
    description="如何使用 Pandas 瀏覽儲存在 Azure blob 容器的資料。" 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="bradsev" 
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2015" 
    ms.author="fashah;garye;bradsev" /> 

#使用 Pandas 瀏覽 Azure blob 儲存體中的資料

這 **功能表** 主題連結，說明如何使用工具來瀏覽來自不同的儲存體環境。 此工作是 Cortana 分析程序 (CAP) 中的一個步驟。

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## 簡介

本文件將說明如何瀏覽儲存在 Azure blob 容器使用的資料 [Pandas](http://pandas.pydata.org/) Python 封裝。

## 先決條件
本文假設您已經：

* 建立 Azure 儲存體帳戶。 如需指示，請參閱 [建立 Azure 儲存體帳戶](../hdinsight-get-started.md#storage)
* 將您的資料儲存在 Azure blob 儲存體帳戶。

## 將資料載入至 Pandas 資料框架
若要進行探索和操作資料集，必須從 Blob 來源將資料集下載至本機檔案，然後將其載入 Pandas 資料框架中。 以下是此程序的遵循步驟：

1. 使用 blob 服務，透過下列 Python 程式碼範例，從 Azure blob 下載資料。 使用您的特定值來取代下列程式碼中的變數： 

        from azure.storage import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. 從下載的檔案中，將資料讀取至 Pandas 資料框架。

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

現在您已經準備好探索資料並在此資料集上產生功能。

##<a name="blob-dataexploration"></a>使用 Pandas 資料瀏覽的範例

以下是數個可使用 Pandas 探索資料的範例方式：

1. 檢查 **數目的資料列和資料行** 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **檢查** 第一個或最後幾 **列** 中的資料集，如下所示:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. 檢查 **資料型別** 匯入每個資料行使用下列程式碼範例
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. 檢查 **基本統計資料** 中的資料集，如下所示的資料行
 
        dataframe_blobdata.describe()
    
5. 查看每個資料行值的項目數，如下所示

        dataframe_blobdata['<column_name>'].value_counts()

6. **計算遺漏值** 與實際使用下列程式碼範例每個資料行中的項目

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  如果您有 **遺漏值** 資料中的特定資料行，您可以卸除它們，如下所示:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    取代遺漏值的另一種方式是使用模式函式：
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. 建立 **長條圖** 繪製以繪製變數的分佈中使用變動數目的紙匣 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. 看看 **相互關聯** 之間使用散佈圖或使用內建的相互關聯函式的變數

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

