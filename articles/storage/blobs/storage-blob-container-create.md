---
title: BLOB-tároló létrehozása vagy törlése a .NET-Azure Storage használatával
description: Megtudhatja, hogyan hozhat létre vagy törölhet blob-tárolókat az Azure Storage-fiókban a .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79135938"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Tároló létrehozása vagy törlése az Azure Storage-ban .NET-tel

Az Azure Storage-beli Blobok tárolóba vannak rendezve. A Blobok feltöltéséhez először létre kell hoznia egy tárolót. Ez a cikk bemutatja, hogyan hozhat létre és törölhet tárolókat a [.net-hez készült Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet)használatával.

## <a name="name-a-container"></a>Tároló neve

A tároló nevének érvényes DNS-névnek kell lennie, mivel az a tároló vagy a Blobok kezeléséhez használt egyedi URI részét képezi. A tárolók elnevezése esetén kövesse az alábbi szabályokat:

- A tároló neve 3 – 63 karakter hosszúságú lehet.
- A tároló nevének betűvel vagy számmal kell kezdődnie, és csak kisbetűket, számokat és kötőjel (-) karaktert tartalmazhat.
- Két vagy több egymást követő kötőjel karakter nem engedélyezett a tárolók neveiben.

A tároló URI-ja ebben a formátumban van:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához hívja a következő módszerek egyikét:

- [Létrehozás](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [Createifnotexists metódust](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

A **create** és a **CreateAsync** metódus kivételt jelez, ha már létezik ilyen nevű tároló.

A **createifnotexists metódust** és a **CreateIfNotExistsAsync** metódus egy logikai értéket ad vissza, amely azt jelzi, hogy létrejött-e a tároló. Ha már létezik ilyen nevű tároló, akkor ezek a metódusok **Hamis értéket** adnak vissza, jelezve, hogy az új tároló nem lett létrehozva.

A tárolók közvetlenül a Storage-fiók alatt jönnek létre. Egy tárolót nem lehet beágyazni egy másik alá.

A következő példa aszinkron módon hoz létre egy tárolót:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>A gyökér tároló létrehozása

A legfelső szintű tároló a Storage-fiók alapértelmezett tárolója. Mindegyik Storage-fiók rendelkezhet egy legfelső szintű tárolóval, amelynek neve *$root.*. Explicit módon létre kell hoznia vagy törölnie kell a legfelső szintű tárolót.

A gyökér tárolóban tárolt blobokra hivatkozhat a gyökér-tároló nevének megadása nélkül is. A gyökérszintű tároló lehetővé teszi, hogy a Storage-fiók hierarchiájának legfelső szintjén lévő blobra hivatkozzon. Hivatkozhat például egy blobra, amely a következő módon található a gyökér tárolóban:

`https://myaccount.blob.core.windows.net/default.html`

A következő példa a gyökérszintű tárolót szinkron módon hozza létre:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Tároló törlése

A tárolók .NET-ben való törléséhez használja a következő módszerek egyikét:

- [Szabályzat](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [Deleteifexists paranccsal](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

A **delete** és a **DeleteAsync** metódus kivételt jelez, ha a tároló nem létezik.

A **deleteifexists paranccsal** és a **DeleteIfExistsAsync** metódus egy logikai értéket ad vissza, amely azt jelzi, hogy a tároló törölve lett-e. Ha a megadott tároló nem létezik, akkor ezek a metódusok **Hamis értéket** adnak vissza, jelezve, hogy a tároló nem lett törölve.

A tárolók törlése után nem hozhat létre azonos nevű tárolót legalább 30 másodpercig, és esetleg hosszabb időt is. A tároló törlését követően az azonos nevű tároló létrehozása sikertelen lesz a 409-es HTTP-hibakód miatt (ütközés). A tárolón vagy a benne található blobokon végrehajtott egyéb műveletek sikertelenek lesznek a 404-as HTTP-hibakód miatt (nem található) a tároló törlésekor.

A következő példa törli a megadott tárolót, és kezeli a kivételt, ha a tároló nem létezik:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Az alábbi példa bemutatja, hogyan törölheti az összes olyan tárolót, amely a megadott előtaggal kezdődik. A példa megszakítja a bérletet, ha van egy meglévő bérlet a tárolóban.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Lásd még

- [Tároló létrehozása művelet](/rest/api/storageservices/create-container)
- [Tároló törlése művelet](/rest/api/storageservices/delete-container)
