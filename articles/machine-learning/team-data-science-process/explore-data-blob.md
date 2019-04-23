---
title: Az adatok Azure blob storage-pandas – csoportos adatelemzési folyamat megismerése
description: Annak megismerése a pandas Python-csomag használatával az Azure blob-tárolóban tárolt adatokat.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3c399491f0a2048fe924e9ed9600dd5ce3899ca2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798553"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Az adatok Azure blob storage-pandas megismerése

Ez a cikk bemutatja, hogyan fedezheti fel az Azure blob-tároló használatával tárolt adatok [pandas](https://pandas.pydata.org/) Python-csomag.

Ez a feladat Ez a lépés a [csoportos adatelemzési folyamat](overview.md).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure storage-fiókot. Ha utasításokat van szüksége, tekintse meg [Azure Storage-fiók létrehozása](../../storage/common/storage-quickstart-create-account.md)
* Tárolja az adatokat egy Azure blob storage-fiókot. Ha utasításokat van szüksége, tekintse meg [adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Az adatok betöltése a pandas DataFrame-be
Vizsgálata, és a egy adatkészlet módosítására, akkor kell először le kell a blob-forrás egy helyi fájlt, amely majd tölthető be a pandas DataFrame. Ez az eljárás követéséhez lépései a következők:

1. Letöltés az adatok Azure blob-a blob szolgáltatással következő Python-kódmintát. Cserélje le a konkrét értékek a változót a következő kódot:

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

1. Az adatok olvashatók be egy pandas DataFrame a letöltött fájl.

```python
#LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

Most már készen áll az adatok, és hozzon létre ehhez az adatkészlethez funkcióinak.

## <a name="blob-dataexploration"></a>Példák a pandas használatával az adatok feltárása
Íme néhány példa többféle módon lehet adatokat pandas használatával:

1. Vizsgálja meg a **sorok és oszlopok száma**

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

1. **Vizsgálja meg** az első vagy utolsó néhány **sorok** a következő adatkészletben:

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

1. Ellenőrizze a **adattípus** minden oszlop lett importálva, a következő mintakód segítségével

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
```

1. Ellenőrizze a **alapszintű stats** az oszlopok a következő adatok

```python
dataframe_blobdata.describe()
```

1. A következő tekintse meg a minden oszlop értékét a bejegyzések száma

```python
dataframe_blobdata['<column_name>'].value_counts()
```

1. **Hiányzó értékek száma** és az egyes oszlopokban a következő mintakód segítségével bejegyzések tényleges száma

```python
miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
print miss_num
```

1. Ha rendelkezik **hiányzó** az adatok adott oszlopban, akkor is el kell dobni ezeket a következő:

```python
dataframe_blobdata_noNA = dataframe_blobdata.dropna()
dataframe_blobdata_noNA.shape
```

Cserélje le a hiányzó értékek másik módja, a függvény a mód:

```python
dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})
```

1. Hozzon létre egy **hisztogram** nyomtatandó dobozok száma változó segítségével jeleníti meg a változó terjesztési

```python
dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
```

1. Tekintse meg **összefüggéseket** változók a teszteredményekből, vagy pedig a beépített korrelációs függvény között

```python
#relationship between column_a and column_b using scatter plot
plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

#correlation between column_a and column_b
dataframe_blobdata[['<column_a>', '<column_b>']].corr()
```
