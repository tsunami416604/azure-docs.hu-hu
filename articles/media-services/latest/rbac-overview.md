---
title: Szerepköralapú hozzáférés-vezérlés a Media Services-fiókok – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Media Services-fiókok esetében.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 82c438ea246ba18fa7dac2281d68a3ea7ba41db8
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617716"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) a Media Services-fiókok

Jelenleg az Azure Media Services nem határoz meg konkrét egyéni szerepköröket a szolgáltatáshoz. A Media Services-fiók teljes hozzáférést kap, az ügyfelek használhatják a beépített szerepkörök adott biztonsági **tulajdonosa** vagy **közreműködői**. Ezek a szerepkörök közötti fő különbség: a **tulajdonosa** szabályozhatja, ki férhet hozzá egy erőforrást, és a **közreműködői** nem. A beépített **olvasó** szerepkör is használható, de a felhasználó vagy az alkalmazás csak van olvasási hozzáférés a Media Services API-k. 

## <a name="design-principles"></a>Tervezési alapelvek

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. V3 API-k nem adnak vissza titkos kulcsok és hitelesítő adatok a **első** vagy **lista** műveleteket. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. A felhasználónak kell beolvasni a titkos kulcsok és hitelesítő adatok külön műveletet metódusának meghívása. A **olvasó** szerepkör műveletek, mint a Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets nelze volat. Önálló műveletek kellene lehetővé teszi a részletesebb RBAC biztonsági engedélyeinek beállítása egy egyéni szerepkör, ha szükséges.

A műveletek a Media Services által támogatott listázásához hajtsa végre:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

A [beépített szerepkör-definíciók](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) a cikk bemutatja, pontosan mi a szerepkörök. 

További információ a következő cikkekben talál:

- [A hagyományos előfizetés-rendszergazdai szerepkörök, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Mi az RBAC Azure-erőforrások?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Az RBAC használatával hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)

## <a name="next-steps"></a>További lépések

- [Fejlesztés a Media Services v3 API-k](media-services-apis-overview.md)
- [A Media Services .NET-tel content key szabályzat lekérése](get-content-key-policy-dotnet-howto.md)
