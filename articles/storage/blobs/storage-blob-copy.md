---
title: BLOB másolása a .NET-Azure Storage szolgáltatással
description: Megtudhatja, hogyan másolhat egy blobot az Azure Storage-fiókba a .NET ügyféloldali kódtár használatával.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: f4e6e2f2732d1c90e8fe669788d82692c8016fd6
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84463450"
---
# <a name="copy-a-blob-with-net"></a>BLOB másolása .NET-tel

Ez a cikk bemutatja, hogyan másolhat egy blobot egy Azure Storage-fiókkal. Azt is bemutatja, hogyan lehet megszakítani egy aszinkron másolási műveletet. A példában szereplő kód a [.net-hez készült Azure Storage ügyféloldali kódtárat](/dotnet/api/overview/azure/storage?view=azure-dotnet)használja.

## <a name="about-copying-blobs"></a>Tudnivalók a Blobok másolásáról

Amikor ugyanabba a Storage-fiókba másol egy blobot, ez egy szinkron művelet. A fiókok közötti másolás aszinkron művelet. A [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) és a [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) metódus az állapot vizsgálatára, illetve a másolási művelet megszakítására szolgáló másolati azonosító értéket ad vissza.

A másolási műveletekhez tartozó forrás blob lehet egy blokkos blob, egy hozzáfűző blob, egy oldal blobja vagy egy pillanatkép. Ha a cél blob már létezik, a forrás-blobtal megegyező típusú blobnak kell lennie. A rendszer felülírja a meglévő cél blobokat. 

A cél blob nem módosítható, amíg folyamatban van egy másolási művelet. A cél blob csak egy függőben lévő másolási blob művelettel rendelkezhet. Más szóval a blob nem lehet a több függőben lévő másolási művelet célja.

A teljes forrás blobot vagy fájlt mindig másolja a rendszer. A bájtok vagy blokkok egy tartományának másolása nem támogatott.

A Blobok másolásakor a rendszer a cél blobba másolja a rendszertulajdonságokat ugyanazokkal az értékekkel.

A másolási művelet állapotának lekéréséhez a cél blobban található [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) tulajdonságot az összes blob-típusnál tekintheti meg. A másolás befejezésekor a rendszer véglegesíti a végső blobot.

A másolási művelet a következő formák bármelyikét elvégezheti:

  - A forrás blobokat átmásolhatja egy másik névvel ellátott cél blobba. A cél blob lehet ugyanazon blob típusú (blokk, Hozzáfűzés vagy lap) meglévő blobja, vagy a másolási művelet által létrehozott új blob is lehet.
  - A forrás blobot a cél blobba másolhatja ugyanazzal a névvel, és így gyakorlatilag lecserélheti a cél blobot. A másolási művelet eltávolítja a nem véglegesített blokkokat, és felülírja a cél blob metaadatait.
  - A forrásfájl az Azure file Service-ben egy cél blobba másolható. A cél blob lehet egy meglévő blokk-blob, vagy lehet egy új, a másolási művelet által létrehozott blokk-blob. A fájlok másolása az oldal blobokra vagy a hozzáfűzési Blobok nem támogatottak.
  - A pillanatképek az alap blobon keresztül másolhatók. Egy pillanatképnek az alap blob pozícióba való előléptetésével a blob egy korábbi verzióját állíthatja vissza.
  - A pillanatképeket átmásolhatja egy másik néven megadott cél blobba. Az eredményül kapott cél blob egy írható blob, nem pillanatkép.

## <a name="copy-a-blob"></a>BLOB másolása

BLOB másolásához hívja a következő módszerek egyikét:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

A következő kódrészlet egy korábban létrehozott blobra mutató hivatkozást kap, és egy új blobba másolja azt ugyanabban a tárolóban:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>BLOB-másolási művelet megszakítása

A másolási művelet megszakításakor a rendszer nulla hosszúságú cél blobot eredményez a Blobok, a blobok és a Blobok hozzáfűzéséhez. A cél blob metaadatai azonban az új értékeket a forrás blobból másolják, vagy explicit módon beállították a [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) vagy a [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) -hívásban. Ha meg szeretné őrizni az eredeti metaadatokat a másolás előtt, készítsen pillanatképet a cél blobról a hívás vagy a előtt `StartCopy` `StartCopyAsync` .

Egy folyamatban lévő blob másolási művelet megszakításakor a cél blob [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) értéke [CopyStatus. megszakítva](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

A [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) és a [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) metódus megszakít egy folyamatban lévő blob másolási műveletet, és a cél blobot nulla hosszúságú és teljes metaadatokkal hagyja el.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Következő lépések

A következő témakörök a Blobok másolásával és a folyamatban lévő másolási műveletek megszakításával kapcsolatos információkat tartalmazzák az Azure REST API-k használatával.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [BLOB másolásának megszakítása](/rest/api/storageservices/abort-copy-blob)
