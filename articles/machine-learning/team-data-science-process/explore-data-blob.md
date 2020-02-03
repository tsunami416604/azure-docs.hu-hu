---
title: Az adatok Azure blob storage-pandas – csoportos adatelemzési folyamat megismerése
description: Annak megismerése a pandas Python-csomag használatával az Azure blob-tárolóban tárolt adatokat.
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
ms.openlocfilehash: 29011760a94a05020150ceddeba4303b87c2f610
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722186"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Az adatok Azure blob storage-pandas megismerése

Ez a cikk bemutatja, hogyan vizsgálhatja meg az Azure Blob-tárolóban tárolt, a [pandák](https://pandas.pydata.org/) Python-csomaggal tárolt adatelemzést.

Ez a feladat a [csoportos adatelemzési folyamat](overview.md)egyik lépése.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című témakört.
* Tárolja az adatokat egy Azure blob storage-fiókot. Ha útmutatásra van szüksége, olvassa el az [adatok áthelyezése az Azure Storage-ba és az-ból](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Az adatok betöltése a pandas DataFrame-be
Vizsgálata, és a egy adatkészlet módosítására, akkor kell először le kell a blob-forrás egy helyi fájlt, amely majd tölthető be a pandas DataFrame. Ez az eljárás követéséhez lépései a következők:

1. Töltse le az Azure Blob adatait az alábbi Python-mintakód használatával Blob service segítségével. Cserélje le a konkrét értékek a változót a következő kódot:

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
# LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

Most már készen áll az adatok, és hozzon létre ehhez az adatkészlethez funkcióinak.

## <a name="blob-dataexploration"></a>Példák az adatfeltárásra a pandák használatával
Íme néhány példa többféle módon lehet adatokat pandas használatával:

1. **Sorok és oszlopok számának** vizsgálata

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

1. **Vizsgálja** meg a következő adatkészlet első vagy utolsó néhány **sorát** :

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

1. Az egyes oszlopok **adattípusának** ellenőrzését az alábbi mintakód használatával importálja.

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
```

1. A következőképpen tekintse meg az adathalmaz oszlopainak **alapvető statisztikáit**

```python
dataframe_blobdata.describe()
```

1. A következő tekintse meg a minden oszlop értékét a bejegyzések száma

```python
dataframe_blobdata['<column_name>'].value_counts()
```

1. A hiányzó értékeket és az egyes oszlopokban lévő bejegyzések tényleges számát az alábbi mintakód alapján **számítja** ki.

```python
miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
print miss_num
```

1. Ha **hiányoznak** az adatok egy adott oszlopához tartozó értékek, a következőképpen húzhatja őket:

```python
dataframe_blobdata_noNA = dataframe_blobdata.dropna()
dataframe_blobdata_noNA.shape
```

Cserélje le a hiányzó értékek másik módja, a függvény a mód:

```python
dataframe_blobdata_mode = dataframe_blobdata.fillna(
    {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
```

1. **Hisztogram** -ábra létrehozása változó számú raktárhely használatával a változó eloszlásának ábrázolásához

```python
dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
```

1. Megtekintheti a változók közötti **korrelációkat** egy scatterplot vagy a beépített korrelációs függvény használatával.

```python
# relationship between column_a and column_b using scatter plot
plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

# correlation between column_a and column_b
dataframe_blobdata[['<column_a>', '<column_b>']].corr()
```
