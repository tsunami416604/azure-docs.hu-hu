---
title: Blobok listázása a .NET használatával – Azure Storage
description: Megtudhatja, hogyan listázhatja a blobokat egy tárolóban az Azure Storage-fiókban a .NET ügyfélkódtár használatával. A kódpéldák bemutatják, hogyan listázhatja a blobokat egy egyszerű listában, illetve hogyan listázhatja a blobokat hierarchikusan, mintha könyvtárakba vagy mappákba lennének rendezve.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137920"
---
# <a name="list-blobs-with-net"></a>Blobok listázása .NET-tel

Amikor a blobok a kódból, megadhatja, hogy számos lehetőséget az Eredmények visszaaz Azure Storage-ból. Megadhatja az egyes eredményhalmazokban visszaadandó eredmények számát, majd lekérheti a következő halmazokat. Megadhat egy előtagot olyan blobok visszaadására, amelyek neve ezzel a karakterrel vagy karakterlánccal kezdődik. És a blobokat egy sima listastruktúrában vagy hierarchikusan is listázhatja. A hierarchikus listablobokat úgy adja vissza, mintha mappákba lennének rendezve. 

Ez a cikk bemutatja, hogyan listázhatoa a blobokat a [.NET Azure Storage ügyfélkódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet)használatával.  

## <a name="understand-blob-listing-options"></a>A bloblista-beállítások ismertetése

A blobok listában egy tárfiókban, hívja meg az alábbi módszerek egyikét:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSzegmentált](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

A blobok listában egy tárolóban, hívja meg az alábbi módszerek egyikét:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSzegmentált](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Ezek a módszerek túlterhelése további lehetőségeket biztosít a blobok listaművelet általi visszaadásának kezeléséhez. Ezeket a beállításokat a következő szakaszok ismertetik.

### <a name="manage-how-many-results-are-returned"></a>A visszaadott eredmények nagy számára

Alapértelmezés szerint a listaelem-művelet egyszerre legfeljebb 5000 eredményt ad vissza. Kisebb eredményhalmaz támpontként adjon meg egy `maxresults` nem nulla értéket a paraméterhez a **ListBlobs metódusok** egyikének hívásakor.

Ha egy listázási művelet több mint 5000 blobot ad vissza, vagy ha `maxresults` olyan értéket adott meg, amelyhez a listabavételi művelet a tárolók egy részét adja vissza a tárfiókban, majd az Azure Storage egy *folytatási jogkivonatot* ad vissza a blobok listájával. A folytatási jogkivonat egy átlátszatlan érték, amely segítségével lekérheti a következő eredmények et az Azure Storage-ból.

A kódban ellenőrizze a folytatási jogkivonat értékét, és állapítsa meg, hogy null értékű-e. Ha a folytatási jogkivonat null értékű, akkor az eredmények készlete befejeződik. Ha a folytatási jogkivonat nem null, akkor hívja meg újra a listaelem-műveletet, és adja át a folytatási jogkivonatot a következő eredményhalmaz lekéréséhez, amíg a folytatási jogkivonat null értékű nem lesz.

### <a name="filter-results-with-a-prefix"></a>Eredmények szűrése előtaggal

A tárolók listájának szűréséhez adjon `prefix` meg egy karakterláncot a paraméterhez. Az előtag karakterlánca egy vagy több karaktert is tartalmazhat. Az Azure Storage majd csak azokat a blobokat adja vissza, amelyek nek a neve ezzel az előtaggal kezdődik.

### <a name="return-metadata"></a>Visszaadott metaadatok

Blob metaadatok visszaadása az eredményekkel, adja meg a [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) enumerálás **metaadatértékét.** Az Azure Storage metaadatokat tartalmaz minden visszaadott blob, így ebben a környezetben nem kell meghívnia a **FetchAttributes metódusok** egyikét a blob metaadatok lekéréséhez.

### <a name="flat-listing-versus-hierarchical-listing"></a>Lakáslista kontra hierarchikus lista

Az Azure Storage-ban lévő blobok egy hierarchikus paradigma (például egy klasszikus fájlrendszer) helyett egy átalányparadigma szerint vannak rendezve. A blobokat azonban *virtuális könyvtárakba* rendezheti, hogy egy mappastruktúrát utánozzon. A virtuális könyvtár a blob nevének részét képezi, és a határoló karakter jelzi.

Blobok virtuális könyvtárakba rendezéséhez használjon határoló karaktert a blob nevében. Az alapértelmezett határoló karakter egy perjel (/), de bármely karaktert megadhat határolójelként.

Ha a blobok nevét egy határolójel használatával, majd választhat, hogy a blobok hierarchikusan listázható. Hierarchikus listázási művelet esetén az Azure Storage a szülőobjektum alatt lévő virtuális könyvtárakat és blobokat adja vissza. A listaelemezési műveletet rekurzív módon hívhatja meg a hierarchián való áthaladáshoz, hasonlóan ahhoz, ahogyan a klasszikus fájlrendszeren programozott módon haladna át.

## <a name="use-a-flat-listing"></a>Lapos listahasználata

Alapértelmezés szerint a listaelem-művelet blobokat ad vissza egy egypados listában. Egy lapos listalista blobok nem virtuális könyvtár szerint vannak rendezve.

A következő példa felsorolja a blobok a megadott tárolóban egy lapos lista, egy választható szegmens mérete megadva, és írja a blob nevét egy konzolablakba.

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

A minta kimenete hasonló a következőkhöz:

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

Amikor hierarchikusan hív meg egy adatlapi műveletet, az Azure Storage a hierarchia első szintjén adja vissza a virtuális könyvtárakat és blobokat. Az egyes virtuális könyvtárak [előtag](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) tulajdonsága úgy van beállítva, hogy az előtagot rekurzív hívásban adja át a következő könyvtár beolvasásához.

A blobok hierarchikus listázásához állítsa a `useFlatBlobListing` listaelemmetódus paraméterét **false**értékűre.

A következő példa felsorolja a blobok a megadott tárolóban egy lapos lista, egy választható szegmens mérete megadva, és írja a blob nevét a konzol ablakba.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
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

A minta kimenete hasonló a következőkhöz:

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
> A blob-pillanatképek nem szerepelhetnek hierarchikus listázási műveletben.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>További lépések

- [Blobok listája](/rest/api/storageservices/list-blobs)
- [Blob-erőforrások számbavétele](/rest/api/storageservices/enumerating-blob-resources)
