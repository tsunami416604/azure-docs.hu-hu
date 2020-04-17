---
title: Adatok feltárása az Azure blob storage-ban pandákkal – Csapatadat-elemzési folyamat
description: Az Azure blobtárolóban tárolt adatok feltárása a Pandas Python-csomag használatával.
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
ms.openlocfilehash: 0b602660142f70fb1442977b22fce2bc1bb275cd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481927"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Adatok feltárása az Azure blob storage-ban pandákkal

Ez a cikk ismerteti, hogyan tárja fel az Azure blob tárolóban tárolt adatok [pandas](https://pandas.pydata.org/) Python-csomag használatával.

Ez a feladat egy lépés a [csapat adatelemzési folyamatában.](overview.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy a következőket feltételezi:

* Létrehozott egy Azure-tárfiókot. Ha utasításokra van szüksége, olvassa [el az Azure Storage-fiók létrehozása című témakört.](../../storage/common/storage-account-create.md)
* Az adatokat egy Azure blob storage-fiókban tárolta. Ha utasításokra van szüksége, olvassa el [az Adatok áthelyezése az Azure Storage-ba és onnan való áthelyezése](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Töltse be az adatokat egy pandadataframe-be
Egy adatkészlet feltárásához és kezeléséhez először le kell tölteni a blob forrásból egy helyi fájlba, amely ezután betölthető egy panda DataFrame.To explore and manipulated and a dataset it must first be downloaded from the blob source to a local file, which can then be loaded in a pandas DataFrame. Az eljáráshoz az alábbi lépéseket kell végrehajtania:

1. Töltse le az adatokat az Azure blobból a következő Python-kódmintával a Blob szolgáltatás használatával. Cserélje le a változót a következő kódra az adott értékekre:

    ```python
    from azure.storage.blob import BlockBlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

1. Olvassa be az adatokat egy pandas DataFrame a letöltött fájlt.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Most már készen áll az adatok feltárására és az adatkészlet funkcióinak létrehozására.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Példák a pandák használatával végzett adatfeltárásra
Íme néhány példa arra, hogyan fedezheti fel az adatokat a pandák használatával:

1. Sorok **és oszlopok számának vizsgálata**

    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```

1. **Vizsgálja meg** a következő adatkészlet első vagy utolsó **néhány sorát:**

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Ellenőrizze, hogy az egyes oszlopok at importált **adattípus** a következő mintakód használatával importálta-e

    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```

1. Ellenőrizze az **alapstatisztikákat** az adatkészlet oszlopaihoz az alábbiak szerint

    ```python
    dataframe_blobdata.describe()
    ```

1. Nézd meg az egyes oszlopértékek bejegyzéseinek számát az alábbiak szerint:

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **A hiányzó értékek megszámlálása** az egyes oszlopokban szereplő bejegyzések tényleges számával szemben a következő mintakód használatával

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```

1. Ha az adatok egy adott oszlopához **hiányoznak értékek,** azokat a következőképpen dobhatja ki:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    A hiányzó értékek cseréjének másik módja a módfunkció:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. **Hisztogram-ábrázolás** létrehozása változószámú tárolóval a változó eloszlásának ábrázolására

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. A **változók közötti korrelációk megtekintése** scatterplot vagy a beépített korrelációs függvény használatával

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
