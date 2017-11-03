---
title: "A speciális elemzés Azure blob-adatok feldolgozása |} Microsoft Docs"
description: "Folyamat adataihoz az Azure Blob Storage tárolóban."
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d8a59078-91d3-4440-b85c-430363c3f4d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: 61792050cbda7d6c1ec3f7e88c3552c752ca74da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="heading"></a>A speciális elemzés Azure blob-adatok feldolgozása
Ez a dokumentum ismerteti az adatok felfedezése és az Azure Blob storage-ban tárolt adatok előállítása szolgáltatások. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Az adatok betöltése az adatok Pandas keret
Ahhoz, hogy vizsgálatát, és kezelheti a DataSet adatkészlet, azt kell letölteni a blob-forrás egy helyi fájlba, amely majd tölthetők be adatok Pandas keret. Az eljárás végrehajtásához kövesse a lépések a következők:

1. Az adatokat az Azure blob-kódot a következő minta Python blob szolgáltatással letöltése. Cserélje le az alábbi kódot a változót az adott értékek: 
   
        from azure.storage.blob import BlobService
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
2. Az adatok olvasása a keretbe Pandas adatok-a letöltött fájlból.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Most már készen áll az adatokba, és ez az adatkészlet funkcióinak generálásához.

## <a name="blob-dataexploration"></a>Az adatok feltárása
Íme néhány példa a Pandas használatával adatokba módjai:

1. Vizsgálja meg a sorok és oszlopok száma 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Vizsgálja meg az első vagy utolsó néhány sor a következő adatkészletben:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Ellenőrizze az egyes oszlopok használatával az alábbi példakód importált adattípus
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Az oszlopok a következő adatkészletben egyszerű statisztikák az alábbiak szerint ellenőrzése
   
        dataframe_blobdata.describe()
5. Tekintse meg az összes oszlop értékhez bejegyzések száma az alábbiak szerint
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Hiányzó értékek száma és az egyes oszlopok használatával az alábbi példakód bejegyzések tényleges száma
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Ha az adatok egy adott oszlopban a hiányzó értékeket, elvetné azokat az alábbiak szerint:
   
     dataframe_blobdata_noNA dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape =
   
   Egy másik cserélje le a hiányzó értékeket módja a mód függvénnyel:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< oszlopnév >: .mode()[0]}) dataframe_blobdata ["< oszlopnév >"]        
8. Hisztogram rajzot használatával rekeszek változó száma ábrázolásához egy változó terjesztési létrehozása    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Nézze meg változók egy scatterplot vagy a beépített korrelációs függvény használatával közötti összefüggések
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Szolgáltatás létrehozása
A Microsoft hozhat létre a funkciók Python az alábbiak szerint:

### <a name="blob-countfeature"></a>Kijelző értékének alapú szolgáltatás létrehozása
A kockák szolgáltatásai az alábbiak szerint hozhatók létre:

1. Vizsgálja meg az a kategorikus oszlop terjesztését:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Az oszlop értékeit jelző értékek generálásához
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Csatlakozás az eredeti adatok keret kijelző oszlop 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Távolítsa el az eredeti változó saját magát:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>A dobozolás szolgáltatás létrehozása
Binned szolgáltatások létrehozásának, a Folytatás az alábbiak szerint:

1. Az oszlopok egy numerikus oszlopot bin egy sorozat hozzáadása
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. A dobozolás logikai változók sorozatát átalakítása
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Végezetül csatlakoztatása a dummy változók vissza az eredeti adatok keret
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Írás a vissza az Azure blob, és az Azure Machine Learning felhasználása
Miután rendelkezik megismerte az adatokat, és létrehozni a szükséges szolgáltatások, az adatait feltöltheti (mintát vagy featurized) az Azure blob-, és az Azure Machine Learning az alábbi lépéseket követve szokásokra is: az Azure Machine Learning Studio, valamint további szolgáltatásokat lehet létrehozni. 

1. Az adatok keret helyi fájl írása
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Az adatok feltöltése az Azure-blobhoz az alábbiak szerint:
   
        from azure.storage.blob import BlobService
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
3. Most az adatokat a blobból az Azure Machine Learning segítségével elolvashatja [és adatokat importálhat] [ import-data] modul alábbi képernyőn látható módon:

![olvasó blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

