---
title: Tárolt hozzáférési szabályzat definiálása a .NET – Azure Storage szolgáltatással
description: Megtudhatja, hogy miként definiálhat tárolt hozzáférési házirendet a .NET ügyfélkódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990740"
---
# <a name="define-a-stored-access-policy-with-net"></a>Tárolt hozzáférési házirend definiálása a .NET segítségével

A tárolt hozzáférési házirend a kiszolgálóoldalon a szolgáltatásszintű megosztott hozzáférési aláírások (SAS) további szintű vezérlését biztosítja. A tárolt hozzáférési házirend meghatározása a megosztott hozzáférési aláírások csoportosítására és a házirend által kötött megosztott hozzáférésű aláírásokra vonatkozó további korlátozások biztosítását szolgálja. A tárolt hozzáférési szabályzat segítségével módosíthatja a SAS kezdési idejét, lejárati idejét vagy engedélyeit, vagy visszavonhatja azt a kiadása után.
  
 A következő tárolási erőforrások támogatják a tárolt hozzáférési házirendeket:  
  
- Blob-tárolók  
- Fájlmegosztások  
- Üzenetsorok  
- Táblák  
  
> [!NOTE]
> A tárolón tárolt hozzáférési szabályzat társítható egy megosztott hozzáférési aláírással, amely engedélyeket ad magának a tárolónak vagy a benne lévő bloboknak. Hasonlóképpen a fájlmegosztáson tárolt hozzáférési házirendek is társíthatók olyan megosztott hozzáférési aláírással, amely engedélyt ad magának a megosztásnak vagy a benne lévő fájloknak.  
>
> A tárolt hozzáférési szabályzatok csak egy szolgáltatás SAS-on támogatottak. A tárolt hozzáférési házirendek nem támogatottak a fiók SAS-vagy a felhasználói delegálássAS esetében.  

## <a name="create-a-stored-access-policy"></a>Tárolt hozzáférési szabályzat létrehozása

A következő kód egy tárolón tárolt hozzáférési szabályzatot hoz létre. A hozzáférési szabályzat segítségével megkorlátozásokat adhat meg egy szolgáltatás SAS a tárolón vagy annak blobok.

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

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
- [Tárolt hozzáférési szabályzat meghatározása](/rest/api/storageservices/define-stored-access-policy)

