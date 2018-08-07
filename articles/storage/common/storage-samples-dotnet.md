---
title: .NET-tel az Azure Storage-minták |} A Microsoft Docs
description: Megtekintheti, letöltheti és mintakód és az Azure Storage-alkalmazások futtatását. Fedezze fel az első lépéseket ismertető minták a blobok, üzenetsorok, táblák és fájlok, a .NET-tároló ügyfélkódtárai használatával.
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 663df77a7fb574f05cfaa9378dff53ca5db21c49
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526109"
---
# <a name="azure-storage-samples-using-net"></a>.NET-tel az Azure Storage-minták

## <a name="net-sample-index"></a>.NET mintakód-index

Az alábbi táblázat a mintaadattárban és az egyes minták az ismertetett forgatókönyvek áttekintése. Kliknutím nA odkazy a megfelelő mintakód megtekintése a Githubon.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Végpont</th><th style="font-size:110%">Forgatókönyv</th><th style="font-size:110%">Mintakód</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Hozzáfűző Blob</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">Példa CloudBlobContainer.GetAppendBlobReference metódus</a></td> 
</tr> 
<tr> 
<td>Blokkblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Az Azure Blob Storage fotótár webalkalmazás</a></td>
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">A BLOB titkosítási minták</a></td>
</tr> 
<tr> 
<td>Blob másolása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Blobok – első lépések</a></td>
</tr> 
<tr> 
<td>Tároló létrehozása</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Az Azure Blob Storage fotótár webalkalmazás</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Az Azure Blob Storage fotótár webalkalmazás</a></td>
</tr> 
<tr> 
<td>Tároló törlése</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Blobok – első lépések</a></td>
</tr> 
<tr> 
<td>A BLOB metaadatainak/tulajdonságok/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Blobok – első lépések</a></td>
</tr> 
<tr> 
<td>Tároló ACL/metaadatok/tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Az Azure Blob Storage fotótár webalkalmazás</a></td>
</tr> 
<tr> 
<td>Laptartomány-beolvasási</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Blobok – első lépések</a></td>
</tr> 
<tr> 
<td>Blobtároló bérleti jogának megszerzése /</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Blobok – első lépések</a></td>
</tr> 
<tr> 
<td>Lista Blob-tároló</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Blobok – első lépések</a></td>
</tr> 
<tr> 
<td>Lapblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Blobok – első lépések</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Blobok – első lépések</a></td>
</tr>   
<tr> 
<td>Szolgáltatás tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Blobok – első lépések</a></td>
</tr>           
<tr> 
<td>Snapshot Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Biztonsági mentési Azure-beli virtuálisgép-lemezek növekményes pillanatképekkel</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fájl</b></td>
<td>Megosztások/könyvtárak és fájlok létrehozása</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Az Azure Storage .NET File Storage – minta</a></td> 
</tr>
<tr> 
<td>Megosztások/könyvtárak vagy fájlok törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Ismerkedés az Azure File Storage-szolgáltatás a .NET-ben</a></td> 
</tr> 
<tr> 
<td>Tulajdonságok és metaadatok</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET File Storage – minta</a></td> 
</tr> 
<tr> 
<td>Fájlok letöltése</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Az Azure Storage .NET File Storage – minta</a></td> 
</tr> 
<tr> 
<td>A fájl tulajdonságok és metaadatok/metrikák</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET File Storage – minta</a></td> 
</tr> 
<tr> 
<td>File Service Properties</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET File Storage – minta</a></td> 
</tr> 
<tr> 
<td>Fájlok és könyvtárak listája</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Az Azure Storage .NET File Storage – minta</a></td> 
</tr>
<tr> 
<td>Lista megosztások</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET File Storage – minta</a></td> 
</tr>
<tr> 
<td>Tulajdonságok és metaadatok/Stats megosztása</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Az Azure Storage .NET File Storage – minta</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>várólista</b></td>
<td>Üzenet hozzáadása</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés a .NET-keretrendszerben Azure Queue szolgáltatás</a></td> 
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Az Azure Storage .NET várólista ügyféloldali titkosítás</a></td> 
</tr> 
<tr> 
<td>Várólista létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés a .NET-keretrendszerben Azure Queue szolgáltatás</a></td> 
</tr> 
<tr> 
<td>Üzenet-várólista törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés a .NET-keretrendszerben Azure Queue szolgáltatás</a></td> 
</tr> 
<tr> 
<td>Belepillantás üzenetbe</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés a .NET-keretrendszerben Azure Queue szolgáltatás</a></td> 
</tr> 
<tr> 
<td>ACL/metaadatok/Stats várólista</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Ismerkedés a .NET-keretrendszerben Azure Queue szolgáltatás</a></td> 
</tr> 
<tr> 
<td>A Queue szolgáltatás tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Ismerkedés a .NET-keretrendszerben Azure Queue szolgáltatás</a></td> 
</tr> 
<tr> 
<td>Üzenet frissítése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Ismerkedés a .NET-keretrendszerben Azure Queue szolgáltatás</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tábla</b></td>
<td>Tábla létrehozása</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Az Azure Storage - mintaalkalmazás használata az egyidejűség kezelése</a></td> 
</tr> 
<tr> 
<td>Entitás/tábla törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel</a></td> 
</tr> 
<tr> 
<td>Entitás beszúrása és egyesítés/cseréje</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Az Azure Storage - mintaalkalmazás használata az egyidejűség kezelése</a></td> 
</tr> 
<tr> 
<td>Entitáslekérdezés</td> 
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
<td>Entitások frissítése</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Az Azure Storage - mintaalkalmazás használata az egyidejűség kezelése</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Az Azure Kódminták részletes ismertetése

A teljes minta könyvtár megtekintéséhez nyissa meg a [Azure-Kódminták](https://azure.microsoft.com/resources/samples/?service=storage) könyvtár, amely tartalmazza a mintákat, letöltheti és helyileg történő futtatása az Azure Storage. A kód a minta kódtár .zip formátumban mintakódot biztosít. Azt is megteheti keresse meg, és klónozza a GitHub-adattárát minden mintához.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Útmutatók az első lépésekhez

Tekintse meg a következő útmutatókat, ha arra kíváncsi, hogyan kell telepíteni, és az Azure Storage Ügyfélkódtáraival – első lépések útmutató.

* [Ismerkedés az Azure Blob Service, a .NET-ben](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Ismerkedés a .NET-keretrendszerben Azure Queue szolgáltatás](../storage-dotnet-how-to-use-queues.md)
* [Ismerkedés az Azure Table Storage-szolgáltatás a .NET-ben](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Ismerkedés az Azure File Storage-szolgáltatás a .NET-ben](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>További lépések

Más nyelven minták tájékoztatást:

* Java: [javát használó Azure Storage-minták](storage-samples-java.md)
* Minden más nyelven: [Azure Storage-minták](../storage-samples.md)
