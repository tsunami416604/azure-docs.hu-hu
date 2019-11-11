---
title: Blobok listázása a .NET-Azure Storage használatával
description: Megtudhatja, hogyan listázhat blobokat egy tárolóban az Azure Storage-fiókban a .NET ügyféloldali kódtár használatával. A példák a Blobok listázására, illetve a Blobok hierarchikus listázására szolgálnak, mintha könyvtárakba vagy mappákba vannak rendezve.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/08/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4b6dc9d80cfe96e501e575d265b9fa383b1c4d2c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902007"
---
# <a name="list-blobs-with-net"></a>Blobok listázása .NET-tel

Ha a kódból listázza a blobokat, megadhatja az eredmények Azure Storage-ból való visszaadásának módját. Megadhatja az egyes eredményekben visszaadott eredmények számát, majd beolvashatja a következő készleteket. Megadhat egy előtagot olyan Blobok visszaadásához, amelyek neve megegyezik a karakterrel vagy karakterlánccal. Emellett a blobokat egy egyszerű felsorolási struktúrában vagy hierarchikusan is listázhatja. A hierarchikus lista a blobokat úgy adja vissza, mintha mappákba vannak rendezve. 

Ez a cikk bemutatja, hogyan listázhat blobokat az [Azure Storage .net-hez készült ügyféloldali kódtára](/dotnet/api/overview/azure/storage/client)használatával.  

## <a name="understand-blob-listing-options"></a>A Blobok listázási beállításainak ismertetése

A Storage-fiókban lévő Blobok listázásához hívja a következő módszerek egyikét:

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

A tárolóban lévő Blobok listázásához hívja a következő módszerek egyikét:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Ezeknek a módszereknek a túlterhelései további beállításokat biztosítanak a Blobok a listázási művelet által történő visszaadásához. Ezeket a beállításokat a következő szakaszokban ismertetjük.

### <a name="manage-how-many-results-are-returned"></a>A visszaadott eredmények számának kezelése

Alapértelmezés szerint a listázási művelet egyszerre legfeljebb 5000 eredményt ad vissza. Ha kisebb eredményeket szeretne visszaadni, adjon meg nullától eltérő értéket a `maxresults` paraméternek az egyik **ListBlobs** metódus hívásakor.

Ha a listázási művelet több mint 5000 blobot ad vissza, vagy ha olyan értéket adott meg, `maxresults` úgy, hogy a listázási művelet a Storage-fiókban lévő tárolók egy részhalmazát adja vissza, akkor az Azure Storage egy *folytatási tokent* ad vissza a Blobok listájával. A folytatási token egy átlátszatlan érték, amelyet az Azure Storage következő eredményeinek lekérésére használhat.

A kódban ellenőrizze a folytatási token értékét annak meghatározásához, hogy null értékű-e. Ha a folytatási jogkivonat null értékű, akkor az eredmények halmaza befejeződött. Ha a folytatási jogkivonat nem null értékű, akkor ismét hívja meg a listázási műveletet, és a folytatási tokenben adja meg a következő eredmények beolvasását, amíg a folytatási jogkivonat null nem lesz.

### <a name="filter-results-with-a-prefix"></a>Eredmények szűrése előtaggal

A tárolók listájának szűréséhez a `prefix` paraméterhez meg kell adni egy karakterláncot. Az előtag-karakterlánc tartalmazhat egy vagy több karaktert. Az Azure Storage ezt követően csak azokat a blobokat adja vissza, amelyeknek a neve az adott előtaggal kezdődik.

### <a name="return-metadata"></a>Metaadatok visszaküldése

Ha a blob-metaadatokat az eredményekkel szeretné visszaadni, akkor a [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) enumerálás **metaadat** értékét kell megadnia. Az Azure Storage tartalmazza az összes visszaadott blob metaadatait, így nem kell meghívnia a kontextus egyik **FetchAttributes** metódusát a blob metaadatainak lekéréséhez.

### <a name="flat-listing-versus-hierarchical-listing"></a>A lapos Listázás és a hierarchikus Listázás

A blobokat az Azure Storage-ban egy egyszerű paradigma, nem pedig hierarchikus paradigma (például klasszikus fájlrendszer) szerint rendezi a rendszer. A blobokat azonban a *virtuális könyvtárakba* rendezheti a mappák struktúrájának kiépítéséhez. A virtuális könyvtár a blob nevének részét képezi, amelyet a határoló karakter jelez.

Ha a blobokat virtuális könyvtárakba szeretné szervezni, használjon egy elválasztó karaktert a blob nevében. Az alapértelmezett elválasztó karakter egy perjel (/), de bármilyen karaktert megadhat elválasztóként.

Ha elválasztó karakterrel nevezi el a blobokat, a Blobok hierarchikus listázása is megadható. Hierarchikus listázási művelet esetén az Azure Storage a szülőobjektum alá tartozó összes virtuális könyvtárat és blobot visszaadja. A listázási művelet rekurzív módon hívható át a hierarchiába, hasonlóan ahhoz, ahogy a klasszikus fájlrendszer programozott módon bejárta.

## <a name="use-a-flat-listing"></a>Egyszerű lista használata

Alapértelmezés szerint a listázási művelet egy egyszerű listaelemben lévő blobokat ad vissza. Egy egyszerű felsorolásban a blobokat nem a virtuális könyvtár rendezi.

Az alábbi példa felsorolja a megadott tárolóban lévő blobokat egy egyszerű lista használatával, egy választható szegmens méretének megadása mellett, és a blob nevét a konzol ablakába írja.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

A minta kimenete a következőhöz hasonló:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Hierarchikus lista használata

Ha hierarchikusan hívja meg a listázási műveletet, az Azure Storage a hierarchia első szintjén adja vissza a virtuális könyvtárakat és blobokat. Az egyes virtuális könyvtárak [előtag](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) tulajdonsága úgy van beállítva, hogy egy rekurzív hívásban továbbítsa az előtagot a következő könyvtár lekéréséhez.

A Blobok hierarchikus listázásához állítsa a listázási metódus `useFlatBlobListing` paraméterét **hamis**értékre.

Az alábbi példa felsorolja a megadott tárolóban lévő blobokat egy egyszerű lista használatával, egy választható szegmens méretének megadása mellett, és a blob nevét a konzol ablakba írja.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and 
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, null, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

A minta kimenete a következőhöz hasonló:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> A blob-Pillanatképek nem szerepelhetnek hierarchikus listázási műveletekben.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Következő lépések

- [Blobok listázása](/rest/api/storageservices/list-blobs)
- [BLOB-erőforrások enumerálása](/rest/api/storageservices/enumerating-blob-resources)
