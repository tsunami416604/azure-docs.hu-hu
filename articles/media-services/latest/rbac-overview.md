---
title: Szerepköralapú hozzáférés-vezérlés Media Services-fiókokhoz – Azure | Microsoft Docs
description: Ez a cikk a Azure Media Services fiókokhoz tartozó szerepköralapú hozzáférés-vezérlést (RBAC) ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "66236922"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) Media Services fiókokhoz

A Azure Media Services jelenleg nem határoz meg a szolgáltatásra vonatkozó egyéni szerepköröket. Ha teljes hozzáférést szeretne kapni a Media Services-fiókhoz, az ügyfelek használhatják a **tulajdonos** vagy a **közreműködő**beépített szerepköreit. A szerepkörök közül a fő különbség a következő: a **tulajdonos** vezérelheti, hogy ki férhet hozzá egy erőforráshoz, és a **közreműködő** nem. A beépített **olvasó** szerepkör is használható, de a felhasználó vagy az alkalmazás csak olvasási hozzáféréssel rendelkezik a Media Services API-khoz. 

## <a name="design-principles"></a>Tervezési alapelvek

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. a V3 API-k nem adnak vissza titkokat vagy hitelesítő adatokat a **Get** vagy a **List** műveletekhez. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. A felhasználónak külön műveleti módszert kell meghívnia a titkok és a hitelesítő adatok beszerzéséhez. Az **olvasó** szerepkör nem hívhat meg olyan műveleteket, mint például az Asset. ListContainerSas, a StreamingLocator. ListContentKeys, a ContentKeyPolicies. GetPolicyPropertiesWithSecrets. A különálló műveletek lehetővé teszik, hogy szükség esetén részletesebb RBAC biztonsági engedélyeket állítson be egy egyéni szerepkörben.

Az Media Services által támogatott műveletek listázásához tegye a következőket:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

A [beépített szerepkör-definíciók](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) című cikkből megtudhatja, hogy pontosan mit biztosít a szerepkör. 

További információt a következő cikkekben talál:

- [A hagyományos előfizetés-rendszergazdai szerepkörök, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Mi az Azure-erőforrások RBAC?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [A RBAC használata a hozzáférés kezeléséhez](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Erőforrás-szolgáltatói műveletek Media Services](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>További lépések

- [Fejlesztés Media Services V3 API-kkal](media-services-apis-overview.md)
- [Tartalom-kulcs házirend beszerzése Media Services .NET használatával](get-content-key-policy-dotnet-howto.md)
