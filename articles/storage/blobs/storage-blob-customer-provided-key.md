---
title: Ügyfél által megadott kulcs meghatározása a blob Storage-hoz a .NET-Azure Storage szolgáltatással
description: Megtudhatja, hogyan határozhat meg egy ügyfél által megadott kulcsot a blob Storage-hoz a .NET használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/20/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3f4ab13c893ec7125ae720cae63b0c1e45f6f3de
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091283"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Ügyfél által megadott kulcs meghatározása a blob Storage-hoz a .NET-tel

Az Azure Blob Storage-ra irányuló kérelmeket használó ügyfelek számára lehetősége van egy titkosítási kulcs megadására egy egyedi kérelemben. A kérelemben szereplő titkosítási kulcs részletesen szabályozható a blob Storage-műveletek titkosítási beállításai között. A felhasználó által megadott kulcsok Azure Key Vault vagy egy másik kulcstárolóban tárolhatók.

Ez a cikk bemutatja, hogyan határozhat meg egy ügyfél által megadott kulcsot a .NET-kérelemben.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Ha többet szeretne megtudni arról, hogyan lehet hitelesíteni az Azure Identity ügyféloldali függvénytárát az Azure Storage-ból, tekintse meg a **hitelesítés az Azure Identity Library** használatával című szakaszt a [blobokhoz és várólistákhoz való hozzáférés engedélyezése a Azure Active Directory és felügyelt identitások Azure-erőforrásokhoz](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)című részben.

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Példa: felhasználó által megadott kulcs használata blob feltöltéséhez

Az alábbi példa egy ügyfél által megadott kulcsot hoz létre, és ezt a kulcsot használja a Blobok feltöltéséhez. A kód feltölt egy blokkot, majd véglegesíti a blokkok listáját, hogy a blobot az Azure Storage-ba írja.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>További lépések

- [Inaktív adatok Azure Storage-titkosítása](../common/storage-service-encryption.md)
- [Blobok és várólisták hozzáférésének engedélyezése Azure Active Directory és felügyelt identitásokkal az Azure-erőforrásokhoz](../common/storage-auth-aad-msi.md)
