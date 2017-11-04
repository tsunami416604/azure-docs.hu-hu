---
title: "Azure Storage-minták Java használatával |} Microsoft Docs"
description: "Megtekintése, töltse le és futtassa a mintakódot és az alkalmazások az Azure Storage. Fedezze fel bevezetés minták BLOB, üzenetsorok, táblák és fájlok, a Java storage ügyfélkódtáraival használatával."
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: fd27e1ac9a773e7b0f5245aa74acdb0521cd098c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-samples-using-java"></a>Azure Storage-minták javás környezetekben

## <a name="java-sample-index"></a>Java-sample index

A következő táblázat a minták tárház és minden egyes minta az ismertetett forgatókönyvek áttekintése. Kattintson a hivatkozásra kattintva a megfelelő mintakód a Githubon.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Végpont</th><th style="font-size:110%">Forgatókönyv</th><th style="font-size:110%">Mintakód</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>A BLOB</b></td>
<td>Hozzáfűző Blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Blokkblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Ismerkedés az Azure ügyféloldali titkosítása Java nyelven</a></td>
</tr> 
<tr> 
<td>A Blob másolása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>Tároló létrehozása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>A Blob törlése</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>Törli a tárolót</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>Metaadatok/tulajdonságok/Stats BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>A tároló hozzáférés-vezérlési lista/metaadatok/tulajdonságainak</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>Get tartományokat</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/CloudPageBlobTests.java">Az oldalakra vonatkozó Blob tesztek minta</a></td>
</tr> 
<tr> 
<td>Bérbe Blobtárolóban /</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>Lista Blobtárolóban</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td>Az oldalakra vonatkozó Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/SasTests.java">SAS-vizsgálatok minta</a></td>
</tr>   
<tr> 
<td>Szolgáltatás tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr>           
<tr> 
<td>Pillanatkép Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob szolgáltatás Java nyelven</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fájl</b></td>
<td>Megosztás-könyvtárak-fájlok létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr>
<tr> 
<td>Megosztások/könyvtárak/fájlok törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr> 
<tr> 
<td>Directory tulajdonságok/metaadatok</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr> 
<tr> 
<td>Fájlok letöltése</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr> 
<tr> 
<td>A fájl tulajdonságok/metaadatok/metrikák</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr> 
<tr> 
<td>Fájlok szolgáltatás tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr> 
<tr> 
<td>Fájlok és könyvtárak listája</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr>
<tr> 
<td>Lista megosztások</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr>
<tr> 
<td>Tulajdonságok/metaadatok/Stats megosztás</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Ismerkedés az Azure File szolgáltatással Java nyelven</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Várólista</b></td>
<td>Üzenet hozzáadása</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/queue/gettingstarted/QueueBasics.java">Tárolási Java ügyfél függvénytár – minták</a></td> 
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/encryption/queue/gettingstarted/QueueGettingStarted.java">Tárolási Java ügyfél függvénytár – minták</a></td> 
</tr> 
<tr> 
<td>Várólista létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Ismerkedés az Azure Queue szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Az üzenet-várólista törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Ismerkedés az Azure Queue szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Üzenet megtekintése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Ismerkedés az Azure Queue szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Hozzáférés-vezérlési lista/metaadatok/Stats várólista</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Ismerkedés az Azure Queue szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Várólista szolgáltatás tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Ismerkedés az Azure Queue szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Üzenet frissítése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Ismerkedés az Azure Queue szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tábla</b></td>
<td>Tábla létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Ismerkedés az Azure Table szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Entitás vagy a tábla törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Ismerkedés az Azure Table szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Entitás beszúrása és egyesítési/cseréje</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Tárolási Java ügyfél függvénytár – minták</a></td> 
</tr> 
<tr> 
<td>Lekérdezés entitások</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Ismerkedés az Azure Table szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Lekérdezés táblák</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Ismerkedés az Azure Table szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Táblázat ACL/tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableAdvanced.java">Ismerkedés az Azure Table szolgáltatás Java nyelven</a></td> 
</tr> 
<tr> 
<td>Entitás frissítése</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Tárolási Java ügyfél függvénytár – minták</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Az Azure mintakódok könyvtár

A teljes minta könyvtár megtekintéséhez keresse fel a [Azure mintakódok](https://azure.microsoft.com/resources/samples/?service=storage) könyvtárban, és azt az Azure Storage töltse le és futtassa helyileg minták tartalmazza. A kód a minta könyvtár .zip formátumban példakódot tartalmaz. Azt is megteheti keresse meg és a GitHub-tárházban, minden egyes minta klónozását.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Első lépéseket ismertető útmutatók

Tekintse meg az alábbi útmutatókban, ha a telepítését, és ismerkedjen meg az Azure Storage Ügyfélkódtáraival útmutatást.

* [Ismerkedés az Azure Blob szolgáltatás Java nyelven](../blobs/storage-java-how-to-use-blob-storage.md)
* [Ismerkedés az Azure Queue szolgáltatás Java nyelven](../storage-java-how-to-use-queue-storage.md)
* [Ismerkedés az Azure Table szolgáltatás Java nyelven](../../cosmos-db/table-storage-how-to-use-java.md)
* [Ismerkedés az Azure File szolgáltatással Java nyelven](../storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Következő lépések

Egyéb nyelvek minták olvashat:

* .NET: [.NET használatával az azure Storage-minták](../storage-samples-dotnet.md)
* Minden más nyelv: [Azure Storage-minták](../storage-samples.md)
