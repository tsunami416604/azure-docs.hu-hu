---
title: Azure Storage-minták Java használatával | Microsoft Docs
description: Mintakód és alkalmazások megtekintése, letöltése és futtatása az Azure Storage-ban. A Java Storage ügyféloldali kódtárainak használatával megismerheti a Blobok, várólisták, táblák és fájlok első lépéseket bemutató mintáit.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 4348400e66e4b975d1eeb899cab8bb5024260149
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721835"
---
# <a name="azure-storage-samples-using-java"></a>A Java-t használó Azure Storage-minták

## <a name="java-sample-index"></a>Java-minta index

A következő táblázat áttekintést nyújt a minták tárházáról, valamint az egyes mintákban szereplő forgatókönyvekről. A hivatkozásokra kattintva megtekintheti a megfelelő mintakód-kódot a GitHubon.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Végpont</th><th style="font-size:110%">Forgatókönyv</th><th style="font-size:110%">Mintakód</th></tr></thead><tbody>
<tr>
<td rowspan="16"><b>Blob</b></td>
<td>Blob hozzáfűzése</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Blokkblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Ügyféloldali titkosítás</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Első lépések Azure ügyféloldali titkosítással javában</a></td>
</tr>
<tr>
<td>Blob másolása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Tároló létrehozása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Tároló törlése</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>BLOB metaadatok/tulajdonságok/statisztika</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Container ACL/Metadata/Properties</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Oldalak tartományának beolvasása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Bérleti blob/tároló</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>BLOB/tároló listázása</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Lapblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">SAS-tesztek mintája</a></td>
</tr>   
<tr>
<td>Szolgáltatás tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Snapshot Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Első lépések az Azure Blob Service-ben Java-ban</a></td>
</tr>
<tr>
<td rowspan="9"><b>Fájl</b></td>
<td>Megosztások/címtárak/fájlok létrehozása</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Megosztások/címtárak/fájlok törlése</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Címtár tulajdonságai/metaadatok</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Fájlok letöltése</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Fájltulajdonságok/metaadatok/mérőszámok</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td>A file Service tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Könyvtárak és fájlok listázása</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Megosztások listázása</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td>Megosztás tulajdonságai/metaadatok/statisztika</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Első lépések az Azure file Service-ben Java-ban</a></td>
</tr>
<tr>
<td rowspan="8"><b>Várólista</b></td>
<td>Üzenet hozzáadása</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63">Első lépések az Azure üzenetsor-szolgáltatással javában</a></td>
</tr>
<tr>
<td>Ügyféloldali titkosítás</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java">Első lépések Azure ügyféloldali titkosítással javában</a></td>
</tr>
<tr>
<td>Várólisták létrehozása</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Első lépések az Azure üzenetsor-szolgáltatással javában</a></td>
</tr>
<tr>
<td>Üzenet/várólista törlése</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Első lépések az Azure üzenetsor-szolgáltatással javában</a></td>
</tr>
<tr>
<td>Betekintés üzenet</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Első lépések az Azure üzenetsor-szolgáltatással javában</a></td>
</tr>
<tr>
<td>Várólista ACL/metaadatok/statisztika</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Első lépések az Azure üzenetsor-szolgáltatással javában</a></td>
</tr>
<tr>
<td>Üzenetsor-szolgáltatás tulajdonságai</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Első lépések az Azure üzenetsor-szolgáltatással javában</a></td>
</tr>
<tr>
<td>Üzenet frissítése</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Első lépések az Azure üzenetsor-szolgáltatással javában</a></td>
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
<td>Entitás beszúrása/egyesítése/cseréje</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td>
</tr>
<tr>
<td>Lekérdezési entitások</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td>
</tr>
<tr>
<td>Lekérdezési táblázatok</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td>
</tr>
<tr>
<td>Táblázat ACL/Properties</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Bevezetés az Azure Table Service használatába Javában</a></td>
</tr>
<tr>
<td>Entitás frissítése</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Bevezetés az Azure Table Service használatába Javában</a></td>
</tr>
</tbody>
</table>
<br/>

## <a name="azure-code-samples-library"></a>Azure Code Samples-könyvtár

A teljes minta függvénytár megtekintéséhez lépjen az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=storage) Library webhelyre, amely az Azure Storage-hoz letölthető és helyileg futtatható mintákat tartalmaz. A kód minta függvénytár. zip formátumban biztosít mintakód-kódot. Azt is megteheti, hogy megkeresi és megnyithatja a GitHub-tárházat az egyes mintákhoz.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Első lépések útmutatók

A következő útmutatókból megtudhatja, hogyan telepítheti az Azure Storage ügyféloldali kódtárait, és hogyan kezdheti meg az első lépéseket.

* [Első lépések az Azure Blob Service-ben Java-ban](../blobs/storage-quickstart-blobs-java.md)
* [Első lépések az Azure üzenetsor-szolgáltatással javában](../queues/storage-java-how-to-use-queue-storage.md)
* [Bevezetés az Azure Table Service használatába Javában](../../cosmos-db/table-storage-how-to-use-java.md)
* [Első lépések az Azure file Service-ben Java-ban](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>További lépések

További információ a mintákról más nyelveken:

* .NET: [.NET-keretrendszert használó Azure Storage-minták](storage-samples-dotnet.md)
* Minden más nyelv: [Azure Storage-minták](storage-samples.md)
