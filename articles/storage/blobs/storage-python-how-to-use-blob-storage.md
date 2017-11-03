---
title: "Hogyan használható az Azure Blob storage (object storage) a Python |} Microsoft Docs"
description: "Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló))."
services: storage
documentationcenter: python
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.openlocfilehash: ae5ad68929a6779ed4944de82a609321a5c4b5ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-blob-storage-from-python"></a>Azure Blob storage-ának Python használata
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Az Azure Blob Storage egy olyan szolgáltatás, amely a strukturálatlan adatokat objektumként/blobként tárolja a felhőben. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

Ez a cikk bemutatja, hogyan hajthat végre a Blob storage használatával gyakori forgatókönyvek. A mintákat a Python és -felhasználási nyelven íródtak a [Microsoft Azure Storage SDK for Python]. Az ismertetett forgatókönyvek közé tartozik a feltöltése, listázása, letöltése és blobok törlése.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Töltse le és telepítse az Azure Storage SDK Python-hez

Python-hez készült Azure Storage szolgáltatás SDK Python 2.7, 3.3-as, 3.4, 3.5-ös vagy 3.6 igényel, és 4 csomagokban származnak: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` és `azure-storage-queue`. Ebben az oktatóanyagban fogjuk használni `azure-storage-blob` csomag.
 
### <a name="install-via-pypi"></a>PyPi keresztül

A Python-Csomagindexet (PyPI) keresztül telepítéséhez írja be:

```bash
pip install azure-storage-blob
```


> [!NOTE]
> Ha az Azure Storage SDK-ból a Python 0.36 vagy korábbi verzióját frissíti, akkor először eltávolításához használja `pip uninstall azure-storage` , már nem szeptemberben a Storage szolgáltatás SDK Python csomagban.
> 
> 

Alternatív módszerek, látogasson el a [Azure Storage szolgáltatás SDK a Githubon Python](https://github.com/Azure/azure-storage-python/).

## <a name="create-a-container"></a>Tároló létrehozása
Alapján a blob szeretne használni, hozzon létre egy **BlockBlobService**, **AppendBlobService**, vagy **PageBlobService** objektum. A következő kódban a **BlockBlobService** objektum. Adja hozzá a következő tetejénél található minden olyan Python fájlt, amelyben programon keresztüli eléréséhez az Azure Blob Blokktárolást kívánja.

```python
from azure.storage.blob import BlockBlobService
```

Az alábbi kód létrehoz egy **BlockBlobService** objektumba a tárfiók nevét és a fiók kulcsot.  "Myaccount" és "SajátKulcs" cserélje le a fióknevet és kulcsot.

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Az alábbi példakód használhat egy **BlockBlobService** objektumot létrehozni a tárolót, ha még nem létezik.

```python
block_blob_service.create_container('mycontainer')
```

Alapértelmezés szerint az új tároló sajátja, ezért meg kell adnia a tárelérési kulcsát (úgy, ahogy korábban) a blobok letöltését az ebben a tárolóban. Ha azt szeretné, hogy a tárolóban található blobok mindenki számára elérhetővé, hozza létre a tárolót, és adja át a nyilvános hozzáférési szint, az alábbi kód használatával.

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

Azt is megteheti módosíthatja a tároló a létrehozást követően az alábbi kód használatával.

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

Ez a módosítás után bárki láthatja a nyilvános tárolókban lévő blobokat, de csak módosíthatja vagy törölheti őket.

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
A blokkblob létrehozásához, és töltse fel az adatokat, használja a **létrehozása\_blob\_a\_elérési**, **létrehozása\_blob\_a\_adatfolyam**, **létrehozása\_blob\_a\_bájt** vagy **létrehozása\_blob\_a\_szöveg** módszerek. Hajtsa végre a szükséges adattömbösítő, ha az adatok mérete meghaladja a 64 MB magas szintű módszerek.

**Hozzon létre\_blob\_a\_elérési** feltölt egy fájlt a megadott elérési és **létrehozása\_blob\_a\_adatfolyam** feltölt egy már megnyitott fájl vagy adatfolyam tartalmát. **Hozzon létre\_blob\_a\_bájt** bájttömb, feltölti és **létrehozása\_blob\_a\_szöveg** feltölti az adott szöveges értéket a megadott kódolás (alapértelmezett értéke UTF-8) használatával.

Az alábbi példa feltölti a tartalmát a **sunset.png** fájlt a **myblockblob** blob.

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához, használja a **lista\_blobok** metódust. Ez a módszer egy generátor adja vissza. A következő kimenetek kódot a **neve** minden egyes BLOB tároló a konzolhoz.

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>Blobok letöltése
Blob adatok letöltéséhez használjon **beolvasása\_blob\_való\_elérési**, **beolvasása\_blob\_való\_adatfolyam**, **beolvasása\_blob\_való\_bájt**, vagy **beolvasása\_blob\_való\_szöveg**. Hajtsa végre a szükséges adattömbösítő, ha az adatok mérete meghaladja a 64 MB magas szintű módszerek.

A következő példa bemutatja, hogy használatával **beolvasása\_blob\_való\_elérési** tartalmának letöltése a **myblockblob** blob-, és tárolja el azt a **out-sunset.png** fájlt.

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>Blob törlése
Végezetül blob törléséhez hívja meg a **delete_blob**.

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>Írás hozzáfűző blobba
A hozzáfűző blob hozzáfűzési feladatokra, például naplózásra van optimalizálva. A blokkblobhoz hasonlóan a hozzáfűző blobot is blokkok alkotják, de ha egy hozzáfűző blobhoz új blokkot ad hozzá, az mindig a blob végéhez lesz hozzáfűzve. Hozzáfűző blob meglévő blokkja nem frissíthető és nem törölhető. A hozzáfűző blob blokkazonosítói nincsenek közzétéve, mert azok egy blokkblobhoz tartoznak.

Egy hozzáfűző blob minden blokkja különböző méretű lehet – 4 MB maximális értékig –, egy hozzáfűző blob pedig legfeljebb 50 000 blokkot tartalmazhat. A hozzáfűző blob maximális mérete ezért valamivel több mint 195 GB (4 MB X 50 000 blokk).

Az alábbi példa új hozzáfűző blob létrehozását és adatok hozzáfűzését mutatja be, egy egyszerű naplózási műveletet szimulálva.

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Blob Storage alapjait, az alábbi hivatkozásokat követve tudhat meg többet.

* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](http://msdn.microsoft.com/library/azure/dd179355)
* [Az Azure Storage csapat blogja]
* [Microsoft Azure Storage SDK for Python]

[Az Azure Storage csapat blogja]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
