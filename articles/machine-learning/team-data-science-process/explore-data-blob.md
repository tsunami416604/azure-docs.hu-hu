---
title: Az Azure blob storage Pandas az adatokba |} Microsoft Docs
description: "Hogyan Pandas használata az Azure blob-tárolóban tárolt adatokba."
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: f86b2bb2c12edc5274777f049906c59dca22ac87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Az Azure Blob Storage-ban található adatok megismerése a Pandas használatával
Ez a dokumentum bemutatja, hogyan adhat az Azure blob-tároló segítségével tárolt adatokba [Pandas](http://pandas.pydata.org/) Python-csomag.

A következő **menü** eszközök segítségével áttekintheti az különböző tárolási környezetekben adatokat leíró témakörökre mutató hivatkozásokat tartalmaz. Ez a feladat Ez a lépés a [adatok tudományos folyamat]().

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Egy Azure storage-fiók létrehozása. Ha módosítania kell az utasításokat, lásd: [egy Azure Storage-fiók létrehozása](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Az adatok tárolódnak az Azure blob storage-fiók. Ha módosítania kell az utasításokat, lásd: [az adatok Azure Storage-ból mozgatása](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Az adatok betöltése az egy Pandas DataFrame
Megismerheti, és kezelheti a DataSet adatkészlet, azt kell először letölteni a blob-forrás egy helyi fájlba, amely egy Pandas DataFrame majd tölthetők. Az eljárás végrehajtásához kövesse a lépések a következők:

1. Az adatok az Azure blob-blob szolgáltatás használatakor a következő Python kódminta a letöltés. Cserélje le a változó a következő kódot a saját értékekkel: 
   
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

## <a name="blob-dataexploration"></a>Példák az adatok feltárása Pandas használatával
Íme néhány példa a Pandas használatával adatokba módjai:

1. Vizsgálja meg a **sorok és oszlopok száma** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Vizsgálja meg** az első vagy utolsó néhány **sorok** a következő adatkészletben:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Ellenőrizze a **adattípus** minden oszlop a következő példakód használatával lett importálva
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Ellenőrizze a **alapvető statisztikák** az adatok az alábbiak szerint állíthatja az oszlopokhoz
   
        dataframe_blobdata.describe()
5. Tekintse meg az összes oszlop értékhez bejegyzések száma az alábbiak szerint
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Hiányzó értékek száma** és az egyes oszlopok használatával az alábbi példakód bejegyzések tényleges száma
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Ha rendelkezik **hiányzó értékek** egy adott oszlop az adatokat, akkor dobhatja el őket az alábbiak szerint:
   
     dataframe_blobdata_noNA dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape =
   
   Egy másik cserélje le a hiányzó értékeket módja a mód függvénnyel:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< oszlopnév >: .mode()[0]}) dataframe_blobdata ["< oszlopnév >"]        
8. Hozzon létre egy **hisztogram** megrajzolásához rekeszek változó száma ábrázolásához egy változó a terjesztési használatával    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Nézze meg **korrelációk** közötti változók egy scatterplot vagy a beépített korrelációs függvény használatával
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

