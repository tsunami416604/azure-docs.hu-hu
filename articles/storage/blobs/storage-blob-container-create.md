---
title: Blob-tároló létrehozása vagy törlése a .NET használatával – Azure Storage
description: Ismerje meg, hogyan hozhat létre vagy törölhet egy blobtárolót az Azure Storage-fiókban a .NET ügyfélkódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135938"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Tároló létrehozása vagy törlése az Azure Storage szolgáltatásban a .NET használatával

Az Azure Storage-ban lévő blobok tárolókba vannak rendezve. Blob feltöltése előtt létre kell hoznia egy tárolót. Ez a cikk bemutatja, hogyan hozhat létre és törölhet tárolókat az [Azure Storage-ügyfélkódtárban .](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="name-a-container"></a>Tároló elnevezése

A tároló nevének érvényes DNS-névnek kell lennie, mivel a tároló vagy blobjai címzéséhez használt egyedi URI részét képezi. A tároló elnevezésekénekkor kövesse az alábbi szabályokat:

- A tárolónevek 3 és 63 karakter közötti hosszúak lehetnek.
- A tárolóneveknek betűvel vagy számmal kell kezdődniük, és csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatják.
- A tárolónevekben két vagy több egymást követő kötőjel karakter nem engedélyezett.

A tároló URI-ja a következő formátumban van:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához hívja meg az alábbi módszerek egyikét:

- [Létrehozás](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

A **Create** és **a CreateAsync** metódusok kivételt jelentenek, ha már létezik ilyen nevű tároló.

A **CreateIfNotExists** és **a CreateIfNotExistsAsync** metódus logikai értéket ad vissza, amely jelzi, hogy a tároló t a rendszer létrehozta-e. Ha már létezik ilyen nevű tároló, akkor ezek a metódusok **hamis** értéket adnak vissza, jelezve, hogy nem jött létre új tároló.

A tárolók közvetlenül a tárfiók alatt jönnek létre. Nem lehet egyik konténert a másik alá ágyazni.

A következő példa aszinkron tárolót hoz létre:

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

## <a name="create-the-root-container"></a>A gyökértároló létrehozása

A gyökértároló alapértelmezett tárolóként szolgál a tárfiókhoz. Minden tárfióknak lehet egy gyökértárolója, amelynek *$root.*. Explicit módon létre kell hoznia vagy törölnie kell a gyökértárolót.

Hivatkozhat a gyökértárolóban tárolt blobra a gyökértároló nevének megfoglalása nélkül. A gyökértároló lehetővé teszi, hogy a tárfiók-hierarchia legfelső szintjén hivatkozzon egy blobra. Hivatkozhat például egy blobra, amely a gyökértárolóban található a következő módon:

`https://myaccount.blob.core.windows.net/default.html`

A következő példa szinkron módon hozza létre a gyökértárolót:

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

A .NET tárolók törléséhez használja az alábbi módszerek egyikét:

- [Szabályzat](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

A **Delete** és **a DeleteAsync** metódus kivételt okoz, ha a tároló nem létezik.

A **DeleteIfExists** és **a DeleteIfExistsAsync** metódus logikai értéket ad vissza, amely jelzi, hogy a tárolótörölve lett-e törölve. Ha a megadott tároló nem létezik, akkor ezek a módszerek **hamis** értéket adnak vissza, jelezve, hogy a tároló nem lett törölve.

Miután törölt egy tárolót, nem hozhat létre azonos nevű tárolót legalább 30 másodpercig, de valószínűleg tovább. A tároló törlése közben az azonos nevű tároló létrehozására tett kísérlet sikertelen lesz a 409-es HTTP-hibakóddal (Ütközés). A tárolón vagy a benne található blobokon lévő egyéb műveletek sikertelenek lesznek a 404-es HTTP-hibakóddal (Nem található), miközben a tároló törlés alatt van.

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

A következő példa bemutatja, hogyan lehet törölni az összes olyan tárolót, amely egy megadott előtaggal kezdődik. A példa megszakítja a bérletet, ha van egy meglévő bérlet a tárolón.

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
