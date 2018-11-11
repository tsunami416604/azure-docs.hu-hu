---
title: Adatmintavétel az Azure blob storage-ban |} A Microsoft Docs
description: Adatmintavétel az Azure Blob Storage
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 9f987daf8b0f111f8d527b2f18dc5e3428df282a
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344926"
---
# <a name="heading"></a>Adatmintavétel az Azure blob storage-ban

Ez a cikk ismerteti a mintavételi, töltse le a programozott módon, és ezután mintavételezi a pythonban írt eljárások az Azure blob storage-ban tárolt adatok.

**Miért érdemes az az adatokat?**
Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, általában egy célszerű való az adatokat egy kisebb, de reprezentatív és könnyebben kezelhető méretű-re csökkenteni. Ez lehetővé teszi az adatok megértését, feltárási és funkciófejlesztési. Szerepét a Cortana Analytics-folyamatban, az adatfeldolgozás funkciók és a gépi tanulási modellek gyors prototípus-készítés engedélyezése.

Ez a mintavételi feladat Ez a lépés a [csoportos adatelemzési folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Töltse le és lefelé mintaadatok
1. Töltse le az adatokat a blob szolgáltatással az alábbi Python-mintakód az Azure blob storage-ból: 
   
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

2. Adatokat olvas be egy Pandas-adatkeretbe a fent letöltött fájlból.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Az adatokat az alsó-minta a `numpy`a `random.choice` módon:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Most már dolgozhat a fenti adatok keret további feltárása és a szolgáltatás-létrehozás a 1 százalékát mintával.

## <a name="heading"></a>Adatok feltöltése és olvassa el, az Azure Machine Learningbe
Az alábbi mintakód segítségével lefelé-minta az adatok, és közvetlenül az Azure Machine Learning használhatja:

1. Az adathalmaz egy helyi fájl írása
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. A helyi fájl feltöltése az Azure-blobba az alábbi mintakód segítségével:
   
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

3. Olvassa el az adatokat az Azure-blobból az Azure Machine Learning segítségével [adatok importálása](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) az alábbi képen látható módon:

![olvasó blob](./media/sample-data-blob/reader_blob.png)

