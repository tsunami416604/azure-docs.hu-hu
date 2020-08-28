---
title: A Storage-fiók típusának és az SKU nevének beolvasása a .NET-tel
titleSuffix: Azure Storage
description: Ismerje meg, hogyan kérheti le az Azure Storage-fiók típusát és az SKU nevét a .NET ügyféloldali kódtár használatával.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 8fa1e258b07ab98040cbbc5217be789e0bb1b783
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020133"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>A Storage-fiók típusának és az SKU nevének beolvasása a .NET-tel

Ez a cikk bemutatja, hogyan kérhető le az Azure Storage-fiók típusa és SKU-neve egy blobhoz az [Azure Storage .net](/dotnet/api/overview/azure/storage?view=azure-dotnet)-hez készült ügyféloldali kódtár használatával.

A fiókadatok a 2018-03-28-es verziótól kezdődően elérhetők a szolgáltatás verzióiban.

## <a name="about-account-type-and-sku-name"></a>A fiók típusa és az SKU neve

**Fióktípus**: az érvényes fióktípus például a,,, `BlobStorage` `BlockBlobStorage` `FileStorage` `Storage` és `StorageV2` . Az [Azure Storage-fiók áttekintése](storage-account-overview.md) több információt tartalmaz, többek között a különböző Storage-fiókok leírását.

**SKU neve**: érvényes SKU-nevek a következők:,,,,,, `Premium_LRS` `Premium_ZRS` `Standard_GRS` `Standard_GZRS` `Standard_LRS` `Standard_RAGRS` `Standard_RAGZRS` és `Standard_ZRS` . Az SKU-nevek megkülönböztetik a kis-és nagybetűket, és a [SkuName osztály](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)sztring mezői.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan végezhet el más műveleteket a Storage-fiókokon a [Azure Portal](https://portal.azure.com) és az Azure REST API használatával.

- [Fiókadatok beolvasása művelet (REST)](/rest/api/storageservices/get-account-information)
