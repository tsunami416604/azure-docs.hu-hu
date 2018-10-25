---
title: Java használatával Azure Storage-minták |} A Microsoft Docs
description: Megtekintheti, letöltheti és mintakód és az Azure Storage-alkalmazások futtatását. Fedezze fel az első lépéseket ismertető minták a blobok, üzenetsorok, táblák és fájlok, a Java-tároló ügyfélkódtárai használatával.
services: storage
author: seguler
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 83080d1e73ddb06de9b454f106517ef230c4b25a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984264"
---
# <a name="azure-storage-samples-using-java"></a>Java használatával Azure Storage-minták

## <a name="java-sample-index"></a>Java-mintakód-index

Az alábbi táblázat a mintaadattárban és az egyes minták az ismertetett forgatókönyvek áttekintése. Kliknutím nA odkazy a megfelelő mintakód megtekintése a Githubon.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Végpont</th><th style="font-size:110%">Forgatókönyv</th><th style="font-size:110%">Mintakód</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Hozzáfűző Blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td> 
</tr> 
<tr> 
<td>Blokkblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Ismerkedés az Azure-ügyfél kiszolgálóoldali titkosítás Java nyelven</a></td>
</tr> 
<tr> 
<td>Blob másolása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>Tároló létrehozása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>Tároló törlése</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>A BLOB metaadatainak/tulajdonságok/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>Tároló ACL/metaadatok/tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>Laptartomány-beolvasási</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/CloudPageBlobTests.java">Lapblob tesztek minta</a></td>
</tr> 
<tr> 
<td>Blobtároló bérleti jogának megszerzése /</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>Lista Blob-tároló</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td>Lapblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/SasTests.java">SAS-tesztek minta</a></td>
</tr>   
<tr> 
<td>Szolgáltatás tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr>           
<tr> 
<td>Snapshot Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Ismerkedés az Azure Blob Service-szel Javával</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Fájl</b></td>
<td>Megosztások/könyvtárak és fájlok létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr>
<tr> 
<td>Megosztások/könyvtárak vagy fájlok törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr> 
<tr> 
<td>Tulajdonságok és metaadatok</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr> 
<tr> 
<td>Fájlok letöltése</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr> 
<tr> 
<td>A fájl tulajdonságok és metaadatok/metrikák</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr> 
<tr> 
<td>File Service Properties</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr> 
<tr> 
<td>Fájlok és könyvtárak listája</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr>
<tr> 
<td>Lista megosztások</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr>
<tr> 
<td>Tulajdonságok és metaadatok/Stats megosztása</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Ismerkedés az Azure File Service-szel Javával</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>várólista</b></td>
<td>Üzenet hozzáadása</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/queue/gettingstarted/QueueBasics.java">Storage Java ügyfél erőforrástár-minták</a></td> 
</tr> 
<tr> 
<td>Ügyféloldali titkosítás</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/encryption/queue/gettingstarted/QueueGettingStarted.java">Storage Java ügyfél erőforrástár-minták</a></td> 
</tr> 
<tr> 
<td>Várólista létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Ismerkedés az Azure Queue szolgáltatás Java-környezetben</a></td> 
</tr> 
<tr> 
<td>Üzenet-várólista törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Ismerkedés az Azure Queue szolgáltatás Java-környezetben</a></td> 
</tr> 
<tr> 
<td>Belepillantás üzenetbe</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Ismerkedés az Azure Queue szolgáltatás Java-környezetben</a></td> 
</tr> 
<tr> 
<td>ACL/metaadatok/Stats várólista</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Ismerkedés az Azure Queue szolgáltatás Java-környezetben</a></td> 
</tr> 
<tr> 
<td>A Queue szolgáltatás tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Ismerkedés az Azure Queue szolgáltatás Java-környezetben</a></td> 
</tr> 
<tr> 
<td>Üzenet frissítése</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Ismerkedés az Azure Queue szolgáltatás Java-környezetben</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tábla</b></td>
<td>Tábla létrehozása</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td> 
</tr> 
<tr> 
<td>Entitás/tábla törlése</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td> 
</tr> 
<tr> 
<td>Entitás beszúrása és egyesítés/cseréje</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td> 
</tr> 
<tr> 
<td>Entitáslekérdezés</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td> 
</tr> 
<tr> 
<td>Lekérdezés táblák</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td> 
</tr> 
<tr> 
<td>Táblázat ACL/tulajdonságai</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Bevezetés az Azure Table Service használatába Javában</a></td> 
</tr> 
<tr> 
<td>Entitások frissítése</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Az Azure Kódminták részletes ismertetése

A teljes minta könyvtár megtekintéséhez nyissa meg a [Azure-Kódminták](https://azure.microsoft.com/resources/samples/?service=storage) könyvtár, amely tartalmazza a mintákat, letöltheti és helyileg történő futtatása az Azure Storage. A kód a minta kódtár .zip formátumban mintakódot biztosít. Azt is megteheti keresse meg, és klónozza a GitHub-adattárát minden mintához.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Útmutatók az első lépésekhez

Tekintse meg a következő útmutatókat, ha arra kíváncsi, hogyan kell telepíteni, és az Azure Storage Ügyfélkódtáraival – első lépések útmutató.

* [Ismerkedés az Azure Blob Service-szel Javával](../blobs/storage-quickstart-blobs-java.md)
* [Ismerkedés az Azure Queue szolgáltatás Java-környezetben](../queues/storage-java-how-to-use-queue-storage.md)
* [Bevezetés az Azure Table Service használatába Javában](../../cosmos-db/table-storage-how-to-use-java.md)
* [Ismerkedés az Azure File Service-szel Javával](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>További lépések

Más nyelven minták tájékoztatást:

* .NET: [.NET használatával az azure Storage-minták](storage-samples-dotnet.md)
* Minden más nyelven: [Azure Storage-minták](storage-samples.md)
