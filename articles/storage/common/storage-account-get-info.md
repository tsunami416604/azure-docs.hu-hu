---
title: A Storage-fiók típusának és az SKU nevének beolvasása a .NET-Azure Storage szolgáltatással
description: Ismerje meg, hogyan kérheti le az Azure Storage-fiók típusát és az SKU nevét a .NET ügyféloldali kódtár használatával.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 21ecaae679ad4a5f21107ef53d3899cf03593c9d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829202"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>A Storage-fiók típusának és az SKU nevének beolvasása a .NET-tel

Ez a cikk bemutatja, hogyan kérhető le az Azure Storage-fiók típusa és SKU-neve egy blobhoz az [Azure Storage .net](/dotnet/api/overview/azure/storage/client)-hez készült ügyféloldali kódtár használatával.

A fiókadatok a 2018-03-28-es verziótól kezdődően elérhetők a szolgáltatás verzióiban.

## <a name="about-account-type-and-sku-name"></a>A fiók típusa és az SKU neve

**Fiók típusa**: Érvényes fióktípus például a `BlobStorage`következők `BlockBlobStorage` `FileStorage`:,,, `StorageV2`és. `Storage` Az [Azure Storage-fiók áttekintése](storage-account-overview.md) több információt tartalmaz, többek között a különböző Storage-fiókok leírását.

**SKU neve**: Az érvényes SKU- `Premium_LRS`nevek `Premium_ZRS` `Standard_GRS`a `Standard_GZRS` következők:`Standard_ZRS`,,,,,, és. `Standard_LRS` `Standard_RAGRS` `Standard_RAGZRS` Az SKU-nevek megkülönböztetik a kis-és nagybetűket, és a [SkuName osztály](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)sztring mezői.

## <a name="retrieve-account-information"></a>Fiókadatok beolvasása

A Storage-fiók típusának és a blobhoz társított SKU-nak a meghívásával hívja meg a [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) vagy a [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) metódust.

A következő kódrészlet lekéri és megjeleníti a csak olvasható fiók tulajdonságait.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
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

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan végezhet el más műveleteket a Storage-fiókokon a [Azure Portal](https://portal.azure.com) és az Azure REST API használatával.

- [Storage-fiók kezelése](storage-account-manage.md)
- [Storage-fiók frissítése](storage-account-upgrade.md)
- [Fiókadatok beolvasása művelet (REST)](/rest/api/storageservices/get-account-information)
