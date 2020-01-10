---
title: Azure Storage-minták Java használatával | Microsoft Docs
description: Mintakód és alkalmazások megtekintése, letöltése és futtatása az Azure Storage-ban. A Java Storage ügyféloldali kódtárainak használatával megismerheti a Blobok, várólisták, táblák és fájlok első lépéseket bemutató mintáit.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: dfdde1ae981dcd2d539dec3667e44e90cef4d1c8
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748041"
---
# <a name="azure-storage-samples-using-java"></a>A Java-t használó Azure Storage-minták

A következő táblázat áttekintést nyújt a minták tárházáról, valamint az egyes mintákban szereplő forgatókönyvekről. A hivatkozásokra kattintva megtekintheti a megfelelő mintakód-kódot a GitHubon.

> [!NOTE]
> Ezek a minták az Azure Storage Java v11 könyvtárat használják. A V12-es kód esetében lásd: [minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob) a GitHub-tárházban.

## <a name="blob-samples-v11"></a>BLOB-minták (v11)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| BLOB hozzáfűzése | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blokkblob | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Ügyféloldali titkosítás | [Első lépések Azure ügyféloldali titkosítással javában](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| BLOB másolása | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Tároló létrehozása | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB törlése | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Tároló törlése | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB metaadatok/tulajdonságok/statisztika | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Tároló ACL/metaadatok/tulajdonságok | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Oldalak tartományának beolvasása | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| Bérleti blob/tároló | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB/tároló listázása | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Oldal Blobja | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [SAS-tesztek mintája](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| Szolgáltatás tulajdonságai | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Pillanatkép-blob | [Első lépések az Azure Blob Service-ben Java-ban](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples-v11"></a>File Samples (v11)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Megosztások/címtárak/fájlok létrehozása | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Megosztások/címtárak/fájlok törlése | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Címtár tulajdonságai/metaadatok | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Fájlok letöltése | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Fájltulajdonságok/metaadatok/mérőszámok | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| A file Service tulajdonságai | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Könyvtárak és fájlok listázása | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Megosztások listázása | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Megosztás tulajdonságai/metaadatok/statisztika | [Első lépések az Azure file Service-ben Java-ban](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples-v11"></a>Üzenetsor-minták (v11)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Üzenet hozzáadása | [Első lépések az Azure üzenetsor-szolgáltatással javában](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| Ügyféloldali titkosítás | [Első lépések Azure ügyféloldali titkosítással javában](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| Várólisták létrehozása | [Első lépések az Azure üzenetsor-szolgáltatással javában](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Üzenet/várólista törlése | [Első lépések az Azure üzenetsor-szolgáltatással javában](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Betekintés üzenet | [Első lépések az Azure üzenetsor-szolgáltatással javában](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Várólista ACL/metaadatok/statisztika | [Első lépések az Azure üzenetsor-szolgáltatással javában](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Üzenetsor-szolgáltatás tulajdonságai | [Első lépések az Azure üzenetsor-szolgáltatással javában](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Üzenet frissítése | [Első lépések az Azure üzenetsor-szolgáltatással javában](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples-v11"></a>Table Samples (v11)

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Create Table | [Bevezetés az Azure Table Service használatába Javában](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Entitás/tábla törlése | [Bevezetés az Azure Table Service használatába Javában](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Entitás beszúrása/egyesítése/cseréje | [Bevezetés az Azure Table Service használatába Javában](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Lekérdezési entitások | [Bevezetés az Azure Table Service használatába Javában](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Lekérdezési táblázatok | [Bevezetés az Azure Table Service használatába Javában](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Táblázat ACL/Properties | [Bevezetés az Azure Table Service használatába Javában](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) |
| Entitás frissítése | [Bevezetés az Azure Table Service használatába Javában](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
## <a name="azure-code-samples-library"></a>Azure Code Samples-könyvtár

A teljes minta függvénytár megtekintéséhez lépjen az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=storage) Library webhelyre, amely az Azure Storage-hoz letölthető és helyileg futtatható mintákat tartalmaz. A kód minta függvénytár. zip formátumban biztosít mintakód-kódot. Azt is megteheti, hogy megkeresi és megnyithatja a GitHub-tárházat az egyes mintákhoz.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Első lépések útmutatók

A következő útmutatókból megtudhatja, hogyan telepítheti az Azure Storage ügyféloldali kódtárait, és hogyan kezdheti meg az első lépéseket.

* [Első lépések az Azure Blob Service-ben Java-ban](../blobs/storage-quickstart-blobs-java.md)
* [Első lépések az Azure üzenetsor-szolgáltatással javában](../queues/storage-java-how-to-use-queue-storage.md)
* [Bevezetés az Azure Table Service használatába Javában](../../cosmos-db/table-storage-how-to-use-java.md)
* [Első lépések az Azure file Service-ben Java-ban](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Következő lépések

További információ a mintákról más nyelveken:

* .NET: [Azure Storage-minták .NET használatával](storage-samples-dotnet.md)
* JavaScript/Node. js: [Azure Storage-minták JavaScript használatával](storage-samples-javascript.md)
* Python: [Azure Storage-minták a Python használatával](storage-samples-python.md)
* Minden más nyelv: [Azure Storage-minták](storage-samples.md)
