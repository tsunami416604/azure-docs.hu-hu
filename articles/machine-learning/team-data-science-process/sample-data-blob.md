---
title: Mintaadatok az Azure blob storage |} Microsoft Docs
description: Az Azure Blob Storage mintaadatokat
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 48cc74a8fdef0103f79033ac2484f658303f405c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="heading"></a>A mintaadatok az Azure blob-tároló
Ez a dokumentum programozott módon letöltheti, és ezután mintavételi pythonban írt eljárások használatával az Azure blob storage szolgáltatásban tárolt mintavételi adatokat tartalmazza.

A következő **menü** az adatokat a különböző tárolási környezetekben módját leíró témakörök hivatkozásait. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Miért érdemes az az adatokat?**
Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, akkor általában down kétmintás az adatokat, hogy az kisebb, de reprezentatív és könnyebben kezelhető méretű jó ötlet. Ez lehetővé teszi az adatok ismertetése, feltárása és a szolgáltatás mérnöki csapathoz. A szerepkör a Cortana Analytics folyamat ahhoz, hogy az adatok feldolgozása funkciók és a gépi tanulási modellek gyors prototípusának.

Ez a mintavételi feladat Ez a lépés a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Töltse le és down kétmintás adatok
1. Töltse le az adatokat az Azure blob storage a blob szolgáltatás használatát a következő példakód Python: 
   
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

2. Olvassa el az adatok keretbe Pandas adatok-a fent letöltött fájlból.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Lefelé-mintákkal az adatok a `numpy`tartozó `random.choice` az alábbiak szerint:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Most dolgozhat a fenti adatokat keret további feltárása és a szolgáltatás-létrehozás a 1 százalék mintával.

## <a name="heading"></a>Adatok feltöltése és olvassa el, az Azure gépi tanulás
Használhatja az alábbi példakód lefelé-minta az adatokat, és közvetlenül az Azure Machine Learning használhatók:

1. Az adatok keret írni egy helyi fájlba
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. A helyi fájl feltöltése az Azure blob használatával az alábbi példakód:
   
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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Az adatokat olvasni az Azure-blobot Azure Machine Learning segítségével [és adatokat importálhat](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) az alábbi ábrán látható módon:

![olvasó blob](./media/sample-data-blob/reader_blob.png)

