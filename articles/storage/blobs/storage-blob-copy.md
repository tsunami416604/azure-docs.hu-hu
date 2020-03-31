---
title: Blob másolása a .NET használatával – Azure Storage
description: Megtudhatja, hogyan másolhat egy blobot az Azure Storage-fiókban a .NET ügyfélkódtár használatával.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135887"
---
# <a name="copy-a-blob-with-net"></a>Blob másolása a .NET segítségével

Ez a cikk bemutatja, hogyan másolhat egy blobot egy Azure Storage-fiókkal. Azt is bemutatja, hogyan szakítható meg egy aszinkron másolási művelet. A példakód az [Azure Storage ügyfélkódtárját használja a .NET rendszerhez.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-copying-blobs"></a>Blobok másolása –

Ha egy blobot ugyanabban a tárfiókban másol, az szinkron művelet. A fiókok közötti másolás aszinkron művelet. A [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) és [a StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) metódusok egy másolat-azonosítóértéket adnak vissza, amely az állapot ellenőrzéséhez vagy a másolási művelet megszakításához használatos.

A másolási művelet forrásblobja lehet egy blokkblob, egy hozzáfűző blob, egy lapblob vagy egy pillanatkép. Ha a célblob már létezik, akkor ugyanolyan blobtípussal kell rendelkeznie, mint a forrásblobnak. Minden meglévő célblob felüllesz írva. 

A célblob nem módosítható, amíg egy másolási művelet folyamatban van. A célblobcsak egy függőben lévő másolási blob művelet. Más szóval egy blob nem lehet a cél több függőben lévő másolási műveletek.

A teljes forrásblob vagy fájl mindig másolásra kerül. Bájt- vagy blokktartomány másolása nem támogatott.

Blob másolásakor a rendszer tulajdonságait másolja a cél blob azonos értékekkel.

Az összes blobtípus esetében ellenőrizheti a [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) tulajdonságot a célblobon a másolási művelet állapotának lekéri. A végső blob véglegesítése, amikor a másolat befejeződik.

A másolási művelet a következő formák bármelyikét használhatja:

  - Másolhatja a forrásblobot egy másik nevű célblobba. A célblob lehet egy meglévő blob azonos blob típusú (blokk, hozzáfűzés vagy lap), vagy lehet egy új blob által létrehozott másolási művelet.
  - A forrásblob ot egy azonos nevű célblobba másolhatja, így hatékonyan lecserélheti a célblobot. Egy ilyen másolási művelet eltávolítja a nem véglegesített blokkokat, és felülírja a célblob metaadatait.
  - Az Azure File szolgáltatásban egy forrásfájlt másolhat egy célblobba. A célblob lehet egy meglévő blokkblob, vagy lehet egy új blokkblob, amelyet a másolási művelet hozott létre. A fájlokból lapblobokba vagy hozzáfűző blobokba másolása nem támogatott.
  - A pillanatképet átmásolhatja az alap blob. Egy pillanatkép az alap blob pozíciójában, visszaállíthatja egy blob egy korábbi verzióját.
  - A pillanatképet más néven más célblobba másolhatja. Az eredményül kapott célblob egy írható blob, és nem egy pillanatkép.

## <a name="copy-a-blob"></a>Blob másolása

Blob másolásához hívja meg az alábbi módszerek egyikét:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

A következő kódpélda egy korábban létrehozott blobra mutató hivatkozást kap, és ugyanabban a tárolóban egy új blobba másolja azt:

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

## <a name="abort-a-blob-copy-operation"></a>Blob másolási művelet megszakítása

Egy másolási művelet megszakítása azt eredményezi, hogy a cél blobok, hozzáfűző blobok és lapblobok nulla hosszúságú célblobot eredményez. A célblob metaadatai azonban az új értékeket a forrásblobból másolja, vagy explicit módon a [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) vagy a [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) hívásban állítják be. Ha meg szeretné tartani az eredeti metaadatokat a másolat `StartCopy` előttről, készítsen pillanatképet a célblobról, mielőtt hívna vagy `StartCopyAsync`.

Folyamatban lévő blobmásolási művelet megszakításakor a célblob [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) beállítása [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Az [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) és [az AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) metódusok megszakítják a blob másolási műveletét, és nulla hosszúságú és teljes metaadatokkal rendelkező célblobot hagynak.

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

## <a name="next-steps"></a>További lépések

Az alábbi témakörök a blobok másolásával és a folyamatban lévő másolási műveletek az Azure REST API-k használatával történő megszakításával kapcsolatos információkat tartalmaznak.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Blob másolásának megszakítása](/rest/api/storageservices/abort-copy-blob)
