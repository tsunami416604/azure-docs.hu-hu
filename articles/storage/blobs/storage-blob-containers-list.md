---
title: Blobtárolók listázása .NET -- Azure Storage
description: Megtudhatja, hogyan listázhatja a blobtárolókat az Azure Storage-fiókban a .NET ügyfélkódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135904"
---
# <a name="list-blob-containers-with-net"></a>Blobtárolók listázása .NET-tel

Amikor a tárolókat egy Azure Storage-fiókban sorolja fel a kódból, számos lehetőséget adhat meg az Eredmények Azure Storage-ból való visszaadásának kezeléséhez. Ez a cikk bemutatja, hogyan listázhatok a tárolókat a [.NET Azure Storage ügyfélkódtárhasználatával.](/dotnet/api/overview/azure/storage?view=azure-dotnet)  

## <a name="understand-container-listing-options"></a>A tárolók listázási beállításainak ismertetése

A tárolók listájának listázásához hívja meg az alábbi módszerek egyikét:

- [ListContainersszegénes](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Ezek a módszerek túlterhelése további lehetőségeket biztosít a tárolók lista-művelet általi visszaküldésének kezeléséhez. Ezeket a beállításokat a következő szakaszok ismertetik.

### <a name="manage-how-many-results-are-returned"></a>A visszaadott eredmények nagy számára

Alapértelmezés szerint a listaelem-művelet egyszerre legfeljebb 5000 eredményt ad vissza. Kisebb eredményhalmaz visszaadásához adjon meg nem nulla `maxresults` értéket a paraméterhez a **ListContainerSegmented** metódusok egyikének hívásakor.

Ha a tárfiók több mint 5000 tárolót tartalmaz, `maxresults` vagy ha olyan értéket adott meg, amely a listabavételi művelet a tárolók egy részét adja vissza a tárfiókban, majd az Azure Storage egy *folytatási jogkivonatot* ad vissza a tárolók listájával. A folytatási jogkivonat egy átlátszatlan érték, amely segítségével lekérheti a következő eredmények et az Azure Storage-ból.

A kódban ellenőrizze a folytatási jogkivonat értékét, és állapítsa meg, hogy null értékű-e. Ha a folytatási jogkivonat null értékű, akkor az eredmények készlete befejeződik. Ha a folytatási token nem null, akkor hívja meg újra **a ListContainersSegmented** vagy **a ListContainersSegmentedAsync metódust,** és adja át a folytatási jogkivonatot a következő eredményhalmaz beolvasásához, amíg a folytatási token null értékű nem lesz.

### <a name="filter-results-with-a-prefix"></a>Eredmények szűrése előtaggal

A tárolók listájának szűréséhez adjon `prefix` meg egy karakterláncot a paraméterhez. Az előtag karakterlánca egy vagy több karaktert is tartalmazhat. Az Azure Storage majd csak azokat a tárolókat adja vissza, amelyek nek a neve ezzel az előtaggal kezdődik.

### <a name="return-metadata"></a>Visszaadott metaadatok

A tároló metaadatainak az eredményekkel együtt történő visszaadására adja meg a [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enumerálás **metaadatértékét.** Az Azure Storage metaadatokat tartalmaz minden egyes visszaadott tárolóval, így nem kell meghívnia a **FetchAttributes metódusok** egyikét is a tároló metaadatainak lekéréséhez.

## <a name="example-list-containers"></a>Példa: Tárolók listázása

A következő példa aszinkron módon felsorolja a tárolók egy tárfiókban, hogy kezdődik egy megadott előtag. A példa felsorolja a tárolók lépésekben 5 eredmények egy időben, és használja a folytatási jogkivonatot, hogy a következő szegmensben az eredmények. A példa is ad tároló metaadatait az eredményekkel.

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

[Blob-erőforrások](/rest/api/storageservices/list-containers2)
[számbavétele tárolók listázása](/rest/api/storageservices/enumerating-blob-resources)
