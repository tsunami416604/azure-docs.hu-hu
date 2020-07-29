---
title: BLOB-tárolók listázása a .NET-Azure Storage használatával
description: Megtudhatja, hogyan listázhatja a blob-tárolókat az Azure Storage-fiókban a .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: da0c5bf6bc371bc512d9264afeab52b9908396fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463558"
---
# <a name="list-blob-containers-with-net"></a>BLOB-tárolók listázása a .NET-tel

Amikor egy Azure Storage-fiókban lévő tárolókat listáz a kódból, megadhat számos lehetőséget, amelyekkel kezelheti az Azure Storage-beli eredmények eredményét. Ez a cikk bemutatja, hogyan listázhat tárolókat a [.net-hez készült Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet)használatával.  

## <a name="understand-container-listing-options"></a>A tárolók listázási beállításainak ismertetése

A Storage-fiókban lévő tárolók listázásához hívja a következő módszerek egyikét:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Ezeknek a módszereknek a túlterhelései további lehetőségeket biztosítanak a tárolók a listázási művelettel történő visszaküldéséhez. Ezeket a beállításokat a következő szakaszokban ismertetjük.

### <a name="manage-how-many-results-are-returned"></a>A visszaadott eredmények számának kezelése

Alapértelmezés szerint a listázási művelet egyszerre legfeljebb 5000 eredményt ad vissza. Kisebb eredmények visszaadásához adjon meg nullától eltérő értéket a `maxresults` paraméterhez, amikor az egyik **ListContainerSegmented** metódust hívja meg.

Ha a Storage-fiók több mint 5000 tárolót tartalmaz, vagy ha olyan értéket adott meg, `maxresults` amelyet a listázási művelet a Storage-fiókban lévő tárolók egy részhalmazát adja vissza, akkor az Azure Storage egy *folytatási tokent* ad vissza a tárolók listájával. A folytatási token egy átlátszatlan érték, amelyet az Azure Storage következő eredményeinek lekérésére használhat.

A kódban ellenőrizze a folytatási token értékét annak meghatározásához, hogy null értékű-e. Ha a folytatási jogkivonat null értékű, akkor az eredmények halmaza befejeződött. Ha a folytatási jogkivonat nem null értékű, akkor hívja meg újra a **ListContainersSegmented** vagy a **ListContainersSegmentedAsync** , majd a folytatási tokenben adja meg a következő eredmények beolvasását, amíg a folytatási token null nem lesz.

### <a name="filter-results-with-a-prefix"></a>Eredmények szűrése előtaggal

A tárolók listájának szűréséhez a paraméterhez meg kell adni egy karakterláncot `prefix` . Az előtag-karakterlánc tartalmazhat egy vagy több karaktert. Az Azure Storage ezt követően csak azokat a tárolókat adja vissza, amelyeknek a neve az adott előtaggal kezdődik.

### <a name="return-metadata"></a>Metaadatok visszaküldése

Ha vissza szeretné állítani a tároló metaadatait az eredményekkel, akkor a [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enumerálás **metaadat** értékét kell megadnia. Az Azure Storage a visszaadott tárolók metaadatait tartalmazza, így nem kell meghívnia az egyik **FetchAttributes** metódust is a tároló metaadatainak beolvasásához.

## <a name="example-list-containers"></a>Példa: tárolók listázása

A következő példa aszinkron módon Listázza azokat a tárolókat, amelyek egy megadott előtaggal kezdődnek. A példa egyszerre 5 eredményből álló tárolókat listáz, és a folytatási tokent használja az eredmények következő szegmensének beszerzéséhez. A példa a tároló metaadatait is visszaadja az eredményekkel együtt.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Lásd még

[Tárolók](/rest/api/storageservices/list-containers2) 
 listázása [Blob-erőforrások enumerálása](/rest/api/storageservices/enumerating-blob-resources)
