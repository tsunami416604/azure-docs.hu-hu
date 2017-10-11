---
title: "Azure Storage-minták .NET használatával |} Microsoft Docs"
description: "Megtekintése, töltse le és futtassa a mintakódot és az alkalmazások az Azure Storage. Felderíteni a bevezetés minták BLOB, üzenetsorok, táblák és fájlok, a .NET-storage ügyfélkódtáraival használatával."
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: 74777ed14ebb41ad31657f814e86724ff1e5e62e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-storage-samples-using-net"></a>Azure Storage-minták .NET használatával

## <a name="net-sample-index"></a>.NET minta index

A következő táblázat a minták tárház és minden egyes minta az ismertetett forgatókönyvek áttekintése. Kattintson a hivatkozásra kattintva a megfelelő mintakód a Githubon.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Végpont</th><th style="font-size:110%">Forgatókönyv</th><th style="font-size:110%">Mintakód</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>A BLOB</b></td>
<td>Hozzáfűző Blob</td> 
<td><a href="https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">CloudBlobContainer.GetAppendBlobReference módszer – példa</a></td> 
</tr> 
<tr> 
<td>Blokkblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage fotótár webalkalmazás</a></td>
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">A BLOB titkosítási minták</a></td>
</tr> 
<tr> 
<td>A Blob másolása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">A BLOB első lépések</a></td>
</tr> 
<tr> 
<td>Tároló létrehozása</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage fotótár webalkalmazás</a></td>
</tr> 
<tr> 
<td>A Blob törlése</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage fotótár webalkalmazás</a></td>
</tr> 
<tr> 
<td>Törli a tárolót</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">A BLOB első lépések</a></td>
</tr> 
<tr> 
<td>Metaadatok/tulajdonságok/Stats BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">A BLOB első lépések</a></td>
</tr> 
<tr> 
<td>A tároló hozzáférés-vezérlési lista/metaadatok/tulajdonságainak</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage fotótár webalkalmazás</a></td>
</tr> 
<tr> 
<td>Get tartományokat</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">A BLOB első lépések</a></td>
</tr> 
<tr> 
<td>Bérbe Blobtárolóban /</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">A BLOB első lépések</a></td>
</tr> 
<tr> 
<td>Lista Blobtárolóban</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">A BLOB első lépések</a></td>
</tr> 
<tr> 
<td>Az oldalakra vonatkozó Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">A BLOB első lépések</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">A BLOB első lépések</a></td>
</tr>   
<tr> 
<td>Szolgáltatás tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">A BLOB első lépések</a></td>
</tr>           
<tr> 
<td>Pillanatkép Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Biztonsági mentési Azure virtuális gépek lemezeit növekményes pillanatképek</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fájl</b></td>
<td>Megosztás-könyvtárak-fájlok létrehozása</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Az Azure Storage .NET fájl tárolási minta</a></td> 
</tr>
<tr> 
<td>Megosztások/könyvtárak/fájlok törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Bevezetés az Azure File szolgáltatás a .NET használatába</a></td> 
</tr> 
<tr> 
<td>Directory tulajdonságok/metaadatok</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET fájl tárolási minta</a></td> 
</tr> 
<tr> 
<td>Fájlok letöltése</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Az Azure Storage .NET fájl tárolási minta</a></td> 
</tr> 
<tr> 
<td>A fájl tulajdonságok/metaadatok/metrikák</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET fájl tárolási minta</a></td> 
</tr> 
<tr> 
<td>Fájlok szolgáltatás tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET fájl tárolási minta</a></td> 
</tr> 
<tr> 
<td>Fájlok és könyvtárak listája</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Az Azure Storage .NET fájl tárolási minta</a></td> 
</tr>
<tr> 
<td>Lista megosztások</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET fájl tárolási minta</a></td> 
</tr>
<tr> 
<td>Tulajdonságok/metaadatok/Stats megosztás</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET fájl tárolási minta</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Várólista</b></td>
<td>Üzenet hozzáadása</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés az Azure Queue szolgáltatás a .NET</a></td> 
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Az Azure Storage .NET várólista ügyféloldali titkosítás</a></td> 
</tr> 
<tr> 
<td>Várólista létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés az Azure Queue szolgáltatás a .NET</a></td> 
</tr> 
<tr> 
<td>Az üzenet-várólista törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés az Azure Queue szolgáltatás a .NET</a></td> 
</tr> 
<tr> 
<td>Üzenet megtekintése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés az Azure Queue szolgáltatás a .NET</a></td> 
</tr> 
<tr> 
<td>Hozzáférés-vezérlési lista/metaadatok/Stats várólista</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Ismerkedés az Azure Queue szolgáltatás a .NET</a></td> 
</tr> 
<tr> 
<td>Várólista szolgáltatás tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Ismerkedés az Azure Queue szolgáltatás a .NET</a></td> 
</tr> 
<tr> 
<td>Üzenet frissítése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés az Azure Queue szolgáltatás a .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tábla</b></td>
<td>Tábla létrehozása</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage - mintaalkalmazás használatával párhuzamossági kezelése</a></td> 
</tr> 
<tr> 
<td>Entitás vagy a tábla törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Entitás beszúrása és egyesítési/cseréje</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage - mintaalkalmazás használatával párhuzamossági kezelése</a></td> 
</tr> 
<tr> 
<td>Lekérdezés entitások</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Lekérdezés táblák</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Táblázat ACL/tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Entitás frissítése</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage - mintaalkalmazás használatával párhuzamossági kezelése</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Az Azure mintakódok könyvtár

A teljes minta könyvtár megtekintéséhez keresse fel a [Azure mintakódok](https://azure.microsoft.com/resources/samples/?service=storage) könyvtárban, és azt az Azure Storage töltse le és futtassa helyileg minták tartalmazza. A kód a minta könyvtár .zip formátumban példakódot tartalmaz. Azt is megteheti keresse meg és a GitHub-tárházban, minden egyes minta klónozását.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Első lépéseket ismertető útmutatók

Tekintse meg az alábbi útmutatókban, ha a telepítését, és ismerkedjen meg az Azure Storage Ügyfélkódtáraival útmutatást.

* [Ismerkedés az Azure Blob szolgáltatás a .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Ismerkedés az Azure Queue szolgáltatás a .NET](../storage-dotnet-how-to-use-queues.md)
* [Ismerkedés az Azure Table szolgáltatás a .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Bevezetés az Azure File szolgáltatás a .NET használatába](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Következő lépések

Egyéb nyelvek minták olvashat:

* Java: [Java használatával az Azure Storage-minták](storage-samples-java.md)
* Minden más nyelv: [Azure Storage-minták](../storage-samples.md)
