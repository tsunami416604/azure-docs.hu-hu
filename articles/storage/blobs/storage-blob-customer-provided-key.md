---
title: Ügyfél által megadott kulcs meghatározása a blob Storage-hoz a .NET-Azure Storage szolgáltatással
description: Megtudhatja, hogyan határozhat meg egy ügyfél által megadott kulcsot a blob Storage-hoz a .NET használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1c282b1b9a4693da2aa71831a503d443660b65c1
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666636"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Ügyfél által megadott kulcs meghatározása a blob Storage-hoz a .NET-tel

Az Azure Blob Storage-ra irányuló kérelmeket használó ügyfelek számára lehetősége van egy titkosítási kulcs megadására egy egyedi kérelemben. A kérelemben szereplő titkosítási kulcs részletesen szabályozható a blob Storage-műveletek titkosítási beállításai között. A felhasználó által megadott kulcsok (előzetes verzió) Azure Key Vault vagy egy másik kulcstárolóban is tárolhatók.

Ez a cikk bemutatja, hogyan határozhat meg egy ügyfél által megadott kulcsot a .NET-kérelemben.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

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

## <a name="next-steps"></a>Következő lépések

- [Azure Storage-titkosítás a REST-adatokhoz](../common/storage-service-encryption.md)
- [Blobok és várólisták hozzáférésének engedélyezése Azure Active Directory és felügyelt identitásokkal az Azure-erőforrásokhoz](../common/storage-auth-aad-msi.md)
