---
title: Szerepköralapú hozzáférés-vezérlés Media Services-fiókokhoz – Azure | Microsoft dokumentumok
description: Ez a cikk ismerteti az Azure Media Services-fiókok szerepköralapú hozzáférés-vezérlési (RBAC).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236922"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) A Media Services-fiókok

Jelenleg az Azure Media Services nem határoz meg a szolgáltatásra jellemző egyéni szerepköröket. A Media Services-fiók teljes körű eléréséhez az ügyfelek használhatják a **Tulajdonos** vagy közreműködő beépített **szerepköreit.** A fő különbség ezek a szerepkörök között: a **tulajdonos** szabályozhatja, hogy ki férhet hozzá egy erőforráshoz, és a **közreműködő** nem. A beépített **Reader** szerepkör is használható, de a felhasználó vagy az alkalmazás csak olvasási hozzáféréssel rendelkezik a Media Services API-khoz. 

## <a name="design-principles"></a>Tervezési alapelvek

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. a v3 API-k nem adnak vissza titkos kulcsokat vagy hitelesítő adatokat a **Lehívási** vagy **listaműveletekben.** A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. A felhasználónak meg kell hívnia egy külön műveletmódszert a titkos kulcsok vagy hitelesítő adatok beszerezéséhez. Az **Olvasó** szerepkör nem hívhat meg olyan műveleteket, mint a Asset.ListContainerSas, a StreamingLocator.ListContentKeys, a ContentKeyPolicies.GetPolicyPropertiesWithSecrets. A különálló műveletek lehetővé teszik, hogy szükség esetén részletesebb RBAC biztonsági engedélyeket állítson be egy egyéni szerepkörben.

A Media Services által támogatott műveletek listázásához tegye a következőket:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

A [beépített szerepkör-definíciók](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) cikk pontosan ismerteti, hogy mit a szerepkör nyújt. 

További információt az alábbi cikkekben talál:

- [A hagyományos előfizetés-rendszergazdai szerepkörök, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Mi az RBAC az Azure-erőforrásokhoz?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Hozzáférés kezelése az RBAC segítségével](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Media Services erőforrás-szolgáltatói műveletei](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>További lépések

- [Fejlesztés a Media Services v3 API-jával](media-services-apis-overview.md)
- [Tartalomkulcs-házirend beszereznie a Media Services .NET használatával](get-content-key-policy-dotnet-howto.md)
