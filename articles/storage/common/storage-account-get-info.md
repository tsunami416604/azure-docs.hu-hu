---
title: Tárfiók típusának és termékváltozatának beírása a .NET segítségével
titleSuffix: Azure Storage
description: Megtudhatja, hogyan szerezheti be az Azure Storage-fiók típusát és a termékváltozat nevét a .NET ügyfélkódtár használatával.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137058"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Tárfiók típusának és termékváltozatának beírása a .NET segítségével

Ez a cikk bemutatja, hogyan szerezheti be az Azure Storage-fiók típusát és a blob termékváltozatának nevét a [.NET Azure Storage-ügyfélkódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet)használatával.

A fiókadatok a 2018-03-28-as verziótól kezdődően érhetők el a szolgáltatásverziókban.

## <a name="about-account-type-and-sku-name"></a>A fióktípus és a Termékváltozat neve

**Számlatípus**: Az `BlobStorage`érvényes `BlockBlobStorage` `FileStorage`számlatípusok közé tartozik a , , , , `Storage`, és `StorageV2`. [Az Azure storage-fiók áttekintése](storage-account-overview.md) további információkat tartalmaz, beleértve a különböző tárfiókok leírását.

**Termékváltozat neve**: Az `Premium_LRS`érvényes `Premium_ZRS` `Standard_GRS`termékváltozatnevek közé tartozik , , `Standard_GZRS` `Standard_LRS`, , `Standard_RAGRS`, , `Standard_RAGZRS`és `Standard_ZRS`. A termékváltozatnevek ben a kis- és nagybetűket nem lehet figyelembe, és karakterláncmezők a [SkuName osztályban.](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)

## <a name="retrieve-account-information"></a>Fiókadatok lekérése

A tárfiók típusának és a blobhoz társított Termékváltozat nevének lekérnie, hívja meg a [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) vagy a [GetAccountPropertiesAsync metódust.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet)

A következő kódpélda beolvassa és megjeleníti az írásvédett fiók tulajdonságait.

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

## <a name="next-steps"></a>További lépések

Ismerje meg a tárfiókon az Azure [Portalon](https://portal.azure.com) és az Azure REST API-n keresztül végrehajtható egyéb műveleteket.

- [Fiókinformáció-művelet beszerezése (REST)](/rest/api/storageservices/get-account-information)
