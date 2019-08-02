---
title: Azure Storage-minták .NET használatával | Microsoft Docs
description: Mintakód és alkalmazások megtekintése, letöltése és futtatása az Azure Storage-ban. A .NET Storage ügyféloldali kódtárak használatával megismerheti a Blobok, várólisták, táblák és fájlok első lépéseket bemutató mintáit.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: cec6b0498adf55428fade7ae00db6550496a20bd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721854"
---
# <a name="azure-storage-samples-using-net"></a>Azure Storage-minták .NET használatával

## <a name="net-sample-index"></a>.NET-minta indexe

A következő táblázat áttekintést nyújt a minták tárházáról, valamint az egyes mintákban szereplő forgatókönyvekről. A hivatkozásokra kattintva megtekintheti a megfelelő mintakód-kódot a GitHubon.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Végpont</th><th style="font-size:110%">Forgatókönyv</th><th style="font-size:110%">Mintakód</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Blob hozzáfűzése</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144">Első lépések Blobokkal</a></td> 
</tr> 
<tr> 
<td>Blokkblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery-webalkalmazás</a></td>
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">BLOB titkosítási minták</a></td>
</tr> 
<tr> 
<td>Blob másolása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Első lépések Blobokkal</a></td>
</tr> 
<tr> 
<td>Tároló létrehozása</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery-webalkalmazás</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery-webalkalmazás</a></td>
</tr> 
<tr> 
<td>Tároló törlése</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Első lépések Blobokkal</a></td>
</tr> 
<tr> 
<td>BLOB metaadatok/tulajdonságok/statisztika</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Első lépések Blobokkal</a></td>
</tr> 
<tr> 
<td>Container ACL/Metadata/Properties</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery-webalkalmazás</a></td>
</tr> 
<tr> 
<td>Oldalak tartományának beolvasása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Első lépések Blobokkal</a></td>
</tr> 
<tr> 
<td>Bérleti blob/tároló</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Első lépések Blobokkal</a></td>
</tr> 
<tr> 
<td>BLOB/tároló listázása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Első lépések Blobokkal</a></td>
</tr> 
<tr> 
<td>Lapblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Első lépések Blobokkal</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Első lépések Blobokkal</a></td>
</tr>   
<tr> 
<td>Szolgáltatás tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Első lépések Blobokkal</a></td>
</tr>           
<tr> 
<td>Snapshot Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Azure-beli virtuálisgép-lemezek biztonsági mentése növekményes pillanatképekkel</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fájl</b></td>
<td>Megosztások/címtárak/fájlok létrehozása</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage minta</a></td> 
</tr>
<tr> 
<td>Megosztások/címtárak/fájlok törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Első lépések az Azure file Service-ben a .NET-ben</a></td> 
</tr> 
<tr> 
<td>Címtár tulajdonságai/metaadatok</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage minta</a></td> 
</tr> 
<tr> 
<td>Fájlok letöltése</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage minta</a></td> 
</tr> 
<tr> 
<td>Fájltulajdonságok/metaadatok/mérőszámok</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage minta</a></td> 
</tr> 
<tr> 
<td>A file Service tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage minta</a></td> 
</tr> 
<tr> 
<td>Könyvtárak és fájlok listázása</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage minta</a></td> 
</tr>
<tr> 
<td>Megosztások listázása</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage minta</a></td> 
</tr>
<tr> 
<td>Megosztás tulajdonságai/metaadatok/statisztika</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage minta</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Várólista</b></td>
<td>Üzenet hozzáadása</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben</a></td> 
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage .NET-üzenetsor ügyféloldali titkosítása</a></td> 
</tr> 
<tr> 
<td>Várólisták létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben</a></td> 
</tr> 
<tr> 
<td>Üzenet/várólista törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben</a></td> 
</tr> 
<tr> 
<td>Betekintés üzenet</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben</a></td> 
</tr> 
<tr> 
<td>Várólista ACL/metaadatok/statisztika</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben</a></td> 
</tr> 
<tr> 
<td>Üzenetsor-szolgáltatás tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben</a></td> 
</tr> 
<tr> 
<td>Üzenet frissítése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Első lépések az Azure üzenetsor-szolgáltatással a .NET-ben</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tábla</b></td>
<td>Tábla létrehozása</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Egyidejűség kezelése az Azure Storage használatával – minta alkalmazás</a></td> 
</tr> 
<tr> 
<td>Entitás/tábla törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Entitás beszúrása/egyesítése/cseréje</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Egyidejűség kezelése az Azure Storage használatával – minta alkalmazás</a></td> 
</tr> 
<tr> 
<td>Lekérdezési entitások</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Lekérdezési táblázatok</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Táblázat ACL/Properties</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Entitás frissítése</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Egyidejűség kezelése az Azure Storage használatával – minta alkalmazás</a></td> 
</tr> 
</tbody> 
</table>
<br/>

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
* Minden más nyelv: [Azure Storage-minták](../storage-samples.md)
