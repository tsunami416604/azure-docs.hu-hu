---
title: Mintaadatok az Azure blob storage-ban – Csapatadat-elemzési folyamat
description: Az Azure blob storage-ban tárolt adatok mintavételezése programozott módon, majd mintavételezési eljárások segítségével python használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720282"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Adatmintavétel Azure blobtárolóban

Ez a cikk az Azure blob storage-ban tárolt mintavételi adatokat ismerteti, programozott módon letöltve, majd a Pythonban írt eljárások használatával mintavételezve.

**Miért érdemes mintát venni az adataiból?**
Ha az elemezni kívánt adatkészlet nagy, általában érdemes levenni az adatokat, hogy csökkentse azokat egy kisebb, de reprezentatívabb és kezelhetőbb méretre. A mintavételezés megkönnyíti az adatok megértését, feltárását és a szolgáltatástervezést. A Cortana Analytics-folyamatban betöltött szerepe az adatfeldolgozási funkciók és a gépi tanulási modellek gyors prototípus-készítése.

Ez a mintavételezési feladat egy lépés a [csapat adatelemzési folyamatában (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="download-and-down-sample-data"></a>Adatok letöltése és lebontása
1. Töltse le az adatokat az Azure Blob Storage-ból a Blob szolgáltatás használatával a következő Python-mintakódból: 
   
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

2. Olvassa be az adatokat egy Pandas adatkeretbe a fent letöltött fájlból.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Le-minta az `numpy`adatokat `random.choice` az 's az alábbiak szerint:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Most már dolgozhat a fenti adatkerettel az egy százalékos mintával a további feltáráshoz és a szolgáltatás generálásához.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Töltse fel az adatokat, és olvassa el az Azure Machine Learningbe
A következő mintakód segítségével csökkentheti az adatok mintavételezését, és közvetlenül használhatja őket az Azure Machine Learningben:

1. Az adatkeret írása helyi fájlba
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Töltse fel a helyi fájlt egy Azure-blobba a következő mintakód használatával:
   
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

3. Olvassa el az Azure blob adatait az Azure Machine Learning [importálási adatainak](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) használatával az alábbi képen látható módon:

![olvasó blob](./media/sample-data-blob/reader_blob.png)

