---
title: Tárolt hozzáférési szabályzat definiálása a .NET-Azure Storage használatával
description: Megtudhatja, hogyan határozhat meg egy tárolt hozzáférési szabályzatot a .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "68990740"
---
# <a name="define-a-stored-access-policy-with-net"></a>Tárolt hozzáférési szabályzat definiálása a .NET-tel

A tárolt hozzáférési szabályzatok a kiszolgálóoldali megosztott hozzáférési aláírások (SAS) további szintjét biztosítják. A tárolt hozzáférési szabályzatok meghatározása a közös hozzáférési aláírások csoportosítására szolgál, és további korlátozásokat biztosít a szabályzat által kötött közös hozzáférési aláírásokhoz. A tárolt hozzáférési szabályzatok segítségével megváltoztathatja az SAS kezdési idejét, lejárati idejét vagy engedélyeit, illetve visszavonhatja azt a kiállítása után.
  
 A következő tárolási erőforrások támogatják a tárolt hozzáférési szabályzatokat:  
  
- BLOB-tárolók  
- Fájlmegosztások  
- Üzenetsorok  
- Táblák  
  
> [!NOTE]
> Egy tároló tárolt hozzáférési szabályzata társítható egy közös hozzáférési aláírással, amely engedélyeket biztosít magának a tárolónak vagy a benne található bloboknak. Hasonlóképpen, egy fájlmegosztás tárolt hozzáférési szabályzata társítható egy közös hozzáférési aláírással, amely jogosultságokat biztosít a megosztáshoz vagy a benne található fájlokhoz.  
>
> A tárolt hozzáférési szabályzatok csak a Service SAS esetében támogatottak. A tárolt hozzáférési szabályzatok nem támogatottak a fiók SAS vagy a felhasználói delegálás SAS esetében.  

## <a name="create-a-stored-access-policy"></a>Tárolt hozzáférési szabályzat létrehozása

A következő kód egy tárolt hozzáférési szabályzatot hoz létre egy tárolón. A hozzáférési házirend segítségével megadhatja a tárolón vagy annak blobján lévő szolgáltatás SAS-korlátozásait.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Lásd még

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
- [Tárolt hozzáférési szabályzat meghatározása](/rest/api/storageservices/define-stored-access-policy)

