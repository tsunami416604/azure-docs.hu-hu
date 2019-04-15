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
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f72e98d8874a5a5dc94deb882affdf66388b13c9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548525"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) a Media Services-fiókok

Jelenleg az Azure Media Services nincs definiálva a bármely egyéni szerepkörök különleges a szolgáltatáshoz. Az ügyfelek használhatják a beépített szerepkörök adott biztonsági **tulajdonosa** vagy **közreműködői** való teljes hozzáférés a Media Services-fiókba. Ezek a szerepkörök közötti fő különbség: a **tulajdonosa** szabályozhatja, ki férhet hozzá egy erőforrást, és a **közreműködői** nem. A beépített olvasó csak olvasási hozzáféréssel rendelkezik a Media Services-fiók. 

## <a name="design-principles"></a>Tervezési alapelvek

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. V3 API-k nem adnak vissza titkos kulcsok és hitelesítő adatok a **első** vagy **lista** műveleteket. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. A felhasználónak kell beolvasni a titkos kulcsok és hitelesítő adatok külön műveletet metódusának meghívása. A **olvasó** szerepkör nelze volat operations, műveletek, mint a Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets, nelze volat. Önálló műveletek kellene lehetővé teszi a részletesebb RBAC biztonsági engedélyeinek beállítása egy egyéni szerepkör, ha szükséges.

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
