---
title: Azure szerepköralapú hozzáférés-vezérlés Media Services-fiókokhoz – Azure | Microsoft Docs
description: Ez a cikk az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) ismerteti Azure Media Services fiókokhoz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 8fba3db14c2a950dd230a4721841b4baa9f64636
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426791"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) Media Services fiókokhoz

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services jelenleg nem határoz meg a szolgáltatásra vonatkozó egyéni szerepköröket. Ha teljes hozzáférést szeretne kapni a Media Services-fiókhoz, az ügyfelek használhatják a **tulajdonos** vagy a **közreműködő**beépített szerepköreit. A szerepkörök közül a fő különbség a következő: a **tulajdonos** vezérelheti, hogy ki férhet hozzá egy erőforráshoz, és a **közreműködő** nem. A beépített **olvasó** szerepkör is használható, de a felhasználó vagy az alkalmazás csak olvasási hozzáféréssel rendelkezik a Media Services API-khoz. 

## <a name="design-principles"></a>Tervezési alapelvek

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. a V3 API-k nem adnak vissza titkokat vagy hitelesítő adatokat a **Get** vagy a **List** műveletekhez. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. A felhasználónak külön műveleti módszert kell meghívnia a titkok és a hitelesítő adatok beszerzéséhez. Az **olvasó** szerepkör nem hívhat meg olyan műveleteket, mint például az Asset. ListContainerSas, a StreamingLocator. ListContentKeys, a ContentKeyPolicies. GetPolicyPropertiesWithSecrets. A különálló műveletek lehetővé teszik, hogy szükség esetén részletesebb Azure RBAC biztonsági engedélyeket állítson be egy egyéni szerepkörben.

Az Media Services által támogatott műveletek listázásához tegye a következőket:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

A [beépített szerepkör-definíciók](../../role-based-access-control/built-in-roles.md) című cikkből megtudhatja, hogy pontosan mit biztosít a szerepkör. 

További információt a következő cikkekben talál:

- [Klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure AD-szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása a REST API használatával](../../role-based-access-control/role-assignments-rest.md)
- [Erőforrás-szolgáltatói műveletek Media Services](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>További lépések

- [Fejlesztés Media Services V3 API-kkal](media-services-apis-overview.md)
- [Tartalom-kulcs házirend beszerzése Media Services .NET használatával](get-content-key-policy-dotnet-howto.md)
