---
title: Ügyfél által biztosított kulcs megadása a Blob Storage-nak a .NET - Azure Storage szolgáltatással történő kérésére
description: Megtudhatja, hogyan adhat meg egy ügyfél által megadott kulcsot a Blob storage-nak a .NET használatával történő kérésre.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807000"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Ügyfél által biztosított kulcs megadása a Blob storage-nak a .NET-tel kapcsolatos kérésre

Az Azure Blob storage-ra vonatkozó kéréseket küldő ügyfelek egy titkosítási kulcsot adhatnak meg egy adott kéréshez. A titkosítási kulcs a kérelem részletes vezérlést biztosít a Blob storage-műveletek titkosítási beállításait. Az ügyfél által biztosított kulcsok (előzetes verzió) tárolhatók az Azure Key Vaultban vagy egy másik kulcstárolóban.

Ez a cikk bemutatja, hogyan adható meg egy ügyfél által megadott kulcs a .NET kéréssel.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Ha többet szeretne megtudni arról, hogyan hitelesítheti magát az Azure Identity ügyfélkódtáraz Azure Storage-ból, tekintse meg a **Hitelesítés az Azure Identity könyvtár az Azure** Active Directory és az Azure Resources felügyelt [identitások engedélyezése című](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)szakaszban.

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Példa: Blob feltöltéséhez használjon ügyfél által biztosított kulcsot

A következő példa létrehoz egy ügyfél által biztosított kulcsot, és ezt a kulcsot használja egy blob feltöltéséhez. A kód feltölt egy blokkot, majd véglegesíti a blokklistát a blob azure storage-ba írásához.

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

- [Az Azure Storage titkosítása az inaktív adatokhoz](../common/storage-service-encryption.md)
- [Blobok és várólisták elérésének engedélyezése az Azure Active Directoryval és az Azure Resources felügyelt identitásaival](../common/storage-auth-aad-msi.md)
