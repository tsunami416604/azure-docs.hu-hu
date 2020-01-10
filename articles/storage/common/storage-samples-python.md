---
title: Azure Storage-minták a Python használatával | Microsoft Docs
description: Mintakód és alkalmazások megtekintése, letöltése és futtatása az Azure Storage-ban. A Python Storage ügyféloldali kódtárak használatával megismerheti a Blobok, várólisták, táblák és fájlok első lépéseket bemutató mintáit.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 7f694ee51989023a3e7a72f40700edcbb6a97bae
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747987"
---
# <a name="azure-storage-samples-using-python"></a>Azure Storage-minták a Python használatával

A következő táblázatok áttekintést nyújtanak a minták tárházáról és az egyes mintákban szereplő forgatókönyvekről. A hivatkozásokra kattintva megtekintheti a megfelelő mintakód-kódot a GitHubon.

> [!NOTE]
> Ezek a minták az Azure Storage Python v 2.1 függvénytárat használják. A V12-es kód esetében lásd: [minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples) a GitHub-tárházban.

## <a name="blob-samples-v21"></a>BLOB-minták (v 2.1)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| BLOB hozzáfűzése | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| Blokkblob | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| Ügyféloldali titkosítás | [A Storage-fiók kulcsainak kezelése az Azure Key Value-ben Python használatával](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| BLOB másolása | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| Tároló létrehozása | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| BLOB törlése | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| Tároló törlése | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| BLOB metaadatok/tulajdonságok/statisztika | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| Tároló ACL/metaadatok/tulajdonságok | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| Oldalak tartományának beolvasása | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| Bérleti blob/tároló | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| BLOB/tároló listázása | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| Oldal Blobja | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Közös hozzáférésű aláírás a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| Szolgáltatás tulajdonságai | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Pillanatkép-blob | [Első lépések az Azure Blob Service-szel a Pythonban](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples-v21"></a>Fájl minták (v 2.1)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Megosztások/címtárak/fájlok létrehozása | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| Megosztások/címtárak/fájlok törlése | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| Címtár tulajdonságai/metaadatok | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| Fájlok letöltése | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| Fájltulajdonságok/metaadatok/mérőszámok | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| A file Service tulajdonságai | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| Könyvtárak és fájlok listázása | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| Megosztások listázása | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| Megosztás tulajdonságai/metaadatok/statisztika | [Első lépések az Azure file Service-szel a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples-v21"></a>Üzenetsor-minták (v 2.1)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Üzenet hozzáadása | [Első lépések az Azure üzenetsor-szolgáltatással a Pythonban](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| Ügyféloldali titkosítás | [A Storage-fiók kulcsainak kezelése Azure Key Vault a Python használatával](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Várólisták létrehozása | [Első lépések az Azure üzenetsor-szolgáltatással a Pythonban](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| Üzenet/várólista törlése | [Első lépések az Azure üzenetsor-szolgáltatással a Pythonban](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| Betekintés üzenet | [Első lépések az Azure üzenetsor-szolgáltatással a Pythonban](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| Várólista ACL/metaadatok/statisztika | [Első lépések az Azure üzenetsor-szolgáltatással a Pythonban](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| Üzenetsor-szolgáltatás tulajdonságai | [Első lépések az Azure üzenetsor-szolgáltatással a Pythonban](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| Üzenet frissítése | [Első lépések az Azure üzenetsor-szolgáltatással a Pythonban](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples-v21"></a>Táblázatos minták (v 2.1)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Create Table | [Első lépések az Azure Table Service-szel a Pythonban](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| Entitás/tábla törlése | [Első lépések az Azure Table Service-szel a Pythonban](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| Entitás beszúrása/egyesítése/cseréje | [Első lépések az Azure Table Service-szel a Pythonban](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| Lekérdezési entitások | [Első lépések az Azure Table Service-szel a Pythonban](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| Lekérdezési táblázatok | [Első lépések az Azure Table Service-szel a Pythonban](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| Táblázat ACL/Properties | [Első lépések az Azure Table Service-szel a Pythonban](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| Entitás frissítése | [Első lépések az Azure Table Service-szel a Pythonban](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Azure Code Samples-könyvtár

A teljes minta függvénytár megtekintéséhez lépjen az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=storage) Library webhelyre, amely az Azure Storage-hoz letölthető és helyileg futtatható mintákat tartalmaz. A kód minta függvénytár. zip formátumban biztosít mintakód-kódot. Azt is megteheti, hogy megkeresi és megnyithatja a GitHub-tárházat az egyes mintákhoz.

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>Első lépések útmutatók

A következő útmutatókból megtudhatja, hogyan telepítheti az Azure Storage ügyféloldali kódtárait, és hogyan kezdheti meg az első lépéseket.

* [Első lépések az Azure Blob Service-szel a Pythonban](../blobs/storage-quickstart-blobs-python.md)
* [Első lépések az Azure üzenetsor-szolgáltatással a Pythonban](../queues/storage-python-how-to-use-queue-storage.md)
* [Első lépések az Azure Table Service-szel a Pythonban](../../cosmos-db/table-storage-how-to-use-python.md)
* [Első lépések az Azure file Service-szel a Pythonban](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Következő lépések

További információ a mintákról más nyelveken:

* .NET: [Azure Storage-minták .NET használatával](storage-samples-dotnet.md)
* Java: [Azure Storage-minták Java használatával](storage-samples-java.md)
* JavaScript/Node. js: [Azure Storage-minták JavaScript használatával](storage-samples-javascript.md)
* Minden más nyelv: [Azure Storage-minták](storage-samples.md)
