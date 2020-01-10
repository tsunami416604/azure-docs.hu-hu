---
title: Azure Storage-minták JavaScript használatával | Microsoft Docs
description: Mintakód és alkalmazások megtekintése, letöltése és futtatása az Azure Storage-ban. A JavaScript/Node. js Storage ügyféloldali kódtárak használatával megismerheti a Blobok, várólisták, táblák és fájlok első lépéseket bemutató mintáit.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 44fe68b8b04a1192c928e04c7d2a9d147f400130
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748292"
---
# <a name="azure-storage-samples-using-javascript"></a>Azure Storage-minták JavaScript használatával

A következő táblázatok áttekintést nyújtanak a minták tárházáról és az egyes mintákban szereplő forgatókönyvekről. A hivatkozásokra kattintva megtekintheti a megfelelő mintakód-kódot a GitHubon.

> [!NOTE]
> Ezek a minták az Azure Storage JavaScript v10 könyvtárat használják. A V12-es kód esetében lásd: [minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples) a GitHub-tárházban.

## <a name="blob-samples-v10"></a>BLOB-minták (v10)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Blokkblob | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| Ügyféloldali titkosítás | [A Storage-fiók kulcsainak kezelése az Azure Key értékben a JavaScript használatával](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| BLOB másolása | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| Tároló létrehozása | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| BLOB törlése | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| Tároló törlése | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| BLOB metaadatai | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| BLOB tulajdonságai | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| Tároló ACL | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| Tároló metaadatai | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| Tároló tulajdonságai | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| Oldalak tartományának beolvasása | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| Blobbérlet | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| Bérlet tárolója | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| BLOB/tároló listázása | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| Oldal Blobja | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [Közös hozzáférésű aláírás a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| Szolgáltatás tulajdonságai | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| CORS szabályok beállítása | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| Pillanatkép-blob | [Első lépések az Azure Blob Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples-v10"></a>File Samples (v10)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Megosztások/címtárak/fájlok létrehozása | [Első lépések az Azure file Service szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| Megosztások/címtárak/fájlok törlése | [Első lépések az Azure file Service szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| Fájlok letöltése | [Első lépések az Azure file Service szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| Könyvtárak és fájlok listázása | [Első lépések az Azure file Service szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| Megosztások listázása | [Első lépések az Azure file Service szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples-v10"></a>Üzenetsor-minták (v10)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Üzenet hozzáadása | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| Ügyféloldali titkosítás | [A Storage-fiók kulcsainak kezelése Azure Key Vault a JavaScript használatával](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Várólisták létrehozása | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| Üzenet törlése | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| Üzenetsor törlése | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| Várólisták listázása | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| Betekintés üzenet | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| Üzenetsor-ACL | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| Üzenetsor-CORS szabályok | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| Üzenetsor metaadatai | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| Üzenetsor-szolgáltatás tulajdonságai | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| Üzenetsor-statisztika | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| Üzenet frissítése | [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples-v10"></a>Táblázatos minták (v10)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Batch-entitások | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| Create Table | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| Entitás/tábla törlése | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| Entitás beszúrása/egyesítése/cseréje | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| Táblák listázása | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| Lekérdezési entitások | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| Lekérdezési táblázatok | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| Tartomány lekérdezése | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [Közös hozzáférésű aláírás a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| Táblázat ACL | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| Tábla CORS szabályai | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| Tábla tulajdonságai | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| Tábla statisztikái | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| Entitás frissítése | [Első lépések az Azure Table Service-szel a JavaScriptben](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Azure Code Samples-könyvtár

A teljes minta függvénytár megtekintéséhez lépjen az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=storage) Library webhelyre, amely az Azure Storage-hoz letölthető és helyileg futtatható mintákat tartalmaz. A kód minta függvénytár. zip formátumban biztosít mintakód-kódot. Azt is megteheti, hogy megkeresi és megnyithatja a GitHub-tárházat az egyes mintákhoz.

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>Első lépések útmutatók

A következő útmutatókból megtudhatja, hogyan telepítheti az Azure Storage ügyféloldali kódtárait, és hogyan kezdheti meg az első lépéseket.

* [Első lépések az Azure Blob Service-szel a JavaScriptben](../blobs/storage-quickstart-blobs-nodejs.md)
* [Első lépések az Azure üzenetsor-kezelési szolgáltatással a JavaScriptben](../queues/storage-nodejs-how-to-use-queues.md)
* [Első lépések az Azure Table Service-szel a JavaScriptben](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>Következő lépések

További információ a mintákról más nyelveken:

* .NET: [Azure Storage-minták .NET használatával](storage-samples-dotnet.md)
* Java: [Azure Storage-minták Java használatával](storage-samples-java.md)
* Python: [Azure Storage-minták a Python használatával](storage-samples-python.md)
* Minden más nyelv: [Azure Storage-minták](storage-samples.md)
