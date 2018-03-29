---
title: Hozzon létre az Azure blob storage adatok Panda szolgáltatásai |} Microsoft Docs
description: Tudnivalók a Panda Python-csomag az Azure blob-tárolóban tárolt adatok funkciói.
services: machine-learning,storage
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev
ms.openlocfilehash: a5a4b92f04fcb4683f792263f3477ecea4d7239e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Funkciók létrehozása az Azure Blob Storage-adatokból a Pandas használatával
Ez a dokumentum bemutatja hogyan hozhat létre az Azure blob-tároló segítségével tárolt adatok funkciói a [Pandas](http://pandas.pydata.org/) Python-csomag. Az adatok betöltése Panda adatok keretbe tagolás után azt illusztrálja, hogyan kategorikus szolgáltatás Python-parancsfájl használata a kijelző értékekkel, és a szolgáltatások dobozolás létrehozásához.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Ez **menü** szolgáltatások adatok létrehozása a különböző környezetek leíró témakörök hivatkozásait. Ez a feladat Ez a lépés a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy az Azure blob storage-fiók létrehozása, és az adatok ott tárolt. Ha a fiókot utasításokat van szüksége, tekintse meg [egy Azure Storage-fiók létrehozása](../../storage/common/storage-create-storage-account.md#create-a-storage-account)

## <a name="load-the-data-into-a-pandas-data-frame"></a>Az adatok betöltése az adatok Pandas keret
Fedezze fel és kezelheti a DataSet adatkészlet le azt a blob-forrás egy helyi fájlba. Töltse be a Pandas adatok keret majd. Az eljárás végrehajtásához kövesse a lépések a következők:

1. Az adatokat az Azure blob-kódot a következő minta Python blob szolgáltatással letöltése. Cserélje le a változó a következő kódot a saját értékekkel:
   
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

## <a name="blob-featuregen"></a>Szolgáltatás létrehozása
A következő két szakaszok bemutatják, hogyan kategorikus mutató értékekkel dobozolás szolgáltatásokkal és Python-parancsfájl használata létrehozásához.

### <a name="blob-countfeature"></a>Kijelző érték-alapú szolgáltatás létrehozása
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
Binned szolgáltatások generálásához, folytassa a következőképpen:

1. Az oszlopok egy numerikus oszlopot bin egy sorozat hozzáadása
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. A dobozolás logikai változók sorozatát átalakítása
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Végezetül csatlakoztatása a dummy változók vissza az eredeti adatok keret
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Az adatok felhasználását, az Azure Machine Learning Azure blob vissza való írásakor
Az Azure Machine Learning, hogy megismerte rendelkezik, az adatok felhasználásához mintavételezett vagy featurized, az adatok feltöltése egy Azure-blobba. További funkciók is létrehozható az Azure Machine Learning Studio is. A következő lépések bemutatják, hogyan feltölteni az adatokat:

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
3. Most az adatokat a blobból az Azure Machine Learning segítségével elolvashatja [és adatokat importálhat](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modul az alábbi képernyőfelvételen látható módon:

![olvasó blob](./media/data-blob/reader_blob.png)

