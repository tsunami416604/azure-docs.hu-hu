---
title: Azure Storage-minták .NET használatával | Microsoft Docs
description: Mintakód és alkalmazások megtekintése, letöltése és futtatása az Azure Storage-ban. A .NET Storage ügyféloldali kódtárak használatával megismerheti a Blobok, várólisták, táblák és fájlok első lépéseket bemutató mintáit.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 57249bd2fa4d3c8aefe19a85ec9a2b6b584b00d2
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743953"
---
# <a name="azure-storage-samples-using-net"></a>Azure Storage-minták .NET használatával

A következő táblázat áttekintést nyújt a minták tárházáról, valamint az egyes mintákban szereplő forgatókönyvekről. A hivatkozásokra kattintva megtekintheti a megfelelő mintakód-kódot a GitHubon.

## <a name="blob-samples"></a>BLOB-minták

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Blob hozzáfűzése | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Blokkblob | [Azure Blob Storage Photo Gallery-webalkalmazás](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Ügyféloldali titkosítás | [BLOB titkosítási minták](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Blob másolása | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Tároló létrehozása | [Azure Blob Storage Photo Gallery-webalkalmazás](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Delete Blob | [Azure Blob Storage Photo Gallery-webalkalmazás](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Tároló törlése | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB metaadatok/tulajdonságok/statisztika | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Container ACL/Metadata/Properties | [Azure Blob Storage Photo Gallery-webalkalmazás](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Oldalak tartományának beolvasása | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Bérleti blob/tároló | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB/tároló listázása | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Lapblob | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Szolgáltatás tulajdonságai | [Első lépések Blobokkal](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Snapshot Blob | [Azure-beli virtuálisgép-lemezek biztonsági mentése növekményes pillanatképekkel](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples"></a>Fájlok mintái

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Megosztások/címtárak/fájlok létrehozása | [Azure Storage .NET File Storage minta](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Megosztások/címtárak/fájlok törlése | [Első lépések az Azure file Service-ben a .NET-ben](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Címtár tulajdonságai/metaadatok | [Azure Storage .NET File Storage minta](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Fájlok letöltése | [Azure Storage .NET File Storage minta](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Fájltulajdonságok/metaadatok/mérőszámok | [Azure Storage .NET File Storage minta](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| A file Service tulajdonságai | [Azure Storage .NET File Storage minta](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Könyvtárak és fájlok listázása | [Azure Storage .NET File Storage minta](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Megosztások listázása | [Azure Storage .NET File Storage minta](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Megosztás tulajdonságai/metaadatok/statisztika | [Azure Storage .NET File Storage minta](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples"></a>Üzenetsor-minták

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Üzenet hozzáadása | [Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Ügyféloldali titkosítás | [Azure Storage .NET-üzenetsor ügyféloldali titkosítása](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Várólisták létrehozása | [Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Üzenet/várólista törlése | [Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Betekintés üzenet | [Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Várólista ACL/metaadatok/statisztika | [Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Üzenetsor-szolgáltatás tulajdonságai | [Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Üzenet frissítése | [Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples"></a>Táblázatos minták

| **Forgatókönyv** | **Mintakód** |
|--------------|-----------------|
| Tábla létrehozása | [Egyidejűség kezelése az Azure Storage használatával – minta alkalmazás](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Entitás/tábla törlése | [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Entitás beszúrása/egyesítése/cseréje | [Egyidejűség kezelése az Azure Storage használatával – minta alkalmazás](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Lekérdezési entitások | [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Lekérdezési táblázatok | [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Táblázat ACL/Properties | [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| Entitás frissítése | [Egyidejűség kezelése az Azure Storage használatával – minta alkalmazás](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Azure Code Samples-könyvtár

A teljes minta függvénytár megtekintéséhez lépjen az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=storage) Library webhelyre, amely az Azure Storage-hoz letölthető és helyileg futtatható mintákat tartalmaz. A kód minta függvénytár. zip formátumban biztosít mintakód-kódot. Azt is megteheti, hogy megkeresi és megnyithatja a GitHub-tárházat az egyes mintákhoz.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Első lépések útmutatók

A következő útmutatókból megtudhatja, hogyan telepítheti az Azure Storage ügyféloldali kódtárait, és hogyan kezdheti meg az első lépéseket.

* [Első lépések az Azure Blob Service-szel a .NET-ben](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben](../storage-dotnet-how-to-use-queues.md)
* [Első lépések az Azure Table Service-ben a .NET-ben](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Első lépések az Azure file Service-ben a .NET-ben](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>További lépések

További információ a mintákról más nyelveken:

* Java: [Javát használó Azure Storage-minták](storage-samples-java.md)
* Python: [Azure Storage-minták a Python használatával](storage-samples-python.md)
* Minden más nyelv: [Azure Storage-minták](../storage-samples.md)
