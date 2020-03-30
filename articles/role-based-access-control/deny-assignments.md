---
title: Az Azure-erőforrások megtagadási hozzárendeléseinek ismertetése
description: Ismerje meg az Azure-erőforrások szerepköralapú hozzáférés-vezérlésében (RBAC) való letagadni a hozzárendeléseket.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372491"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Az Azure-erőforrások megtagadási hozzárendeléseinek ismertetése

A szerepkör-hozzárendeléshez hasonlóan a *megtagadási hozzárendelés* egy felhasználói, csoport- vagy szolgáltatásnévi műveleteket csatol egy adott hatókörben a hozzáférés megtagadása céljából. A hozzárendelések letiltása letiltja a felhasználókat bizonyos Azure-erőforrás-műveletek végrehajtásában, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít számukra.

Ez a cikk a megtagadási hozzárendelések definiálásának módját ismerteti.

## <a name="how-deny-assignments-are-created"></a>A megtagadási hozzárendelések létrehozásának

A megtagadási hozzárendeléseket az Azure hozta létre és kezeli az erőforrások védelme érdekében. Az Azure Blueprints és az Azure által felügyelt alkalmazások megtagadási hozzárendeléseket használnak a rendszer által felügyelt erőforrások védelmére. Az Azure Blueprints és az Azure felügyelt alkalmazások az egyetlen módja annak, hogy megtagadják a hozzárendelések hozhatók létre. Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. Ha többet szeretne tudni arról, hogy a Blueprints hogyan használja a megtagadási hozzárendeléseket az erőforrások zárolásához, [olvassa el az Erőforrások zárolásának ismertetése az Azure Blueprints alkalmazásban című témakört.](../governance/blueprints/concepts/resource-locking.md)

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket.

## <a name="compare-role-assignments-and-deny-assignments"></a>Szerepkör-hozzárendelések összehasonlítása és megtagadási hozzárendelések

A megtagadási hozzárendelések hasonló mintát követnek, mint a szerepkör-hozzárendelések, de vannak különbségek is.

| Képesség | Szerepkör-kijelölés | Hozzárendelés megtagadása |
| --- | --- | --- |
| Hozzáférés biztosítása | :heavy_check_mark: |  |
| Hozzáférés megtagadása |  | :heavy_check_mark: |
| Közvetlenül létrehozható | :heavy_check_mark: |  |
| Alkalmazás hatókörre | :heavy_check_mark: | :heavy_check_mark: |
| Rendszerbiztonsági tagok kizárása |  | :heavy_check_mark: |
| Gyermekhatókörök öröklésének megakadályozása |  | :heavy_check_mark: |
| Alkalmazás [klasszikus előfizetés-rendszergazdai](rbac-and-directory-admin-roles.md) hozzárendelésekre |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Hozzárendelés tulajdonságainak megtagadása

 A megtagadási hozzárendelés a következő tulajdonságokkal rendelkezik:

> [!div class="mx-tableFixed"]
> | Tulajdonság | Kötelező | Típus | Leírás |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Igen | Sztring | A megtagadási hozzárendelés megjelenítendő neve. A neveknek egyedinek kell lenniük egy adott hatókörhöz. |
> | `Description` | Nem | Sztring | A megtagadási hozzárendelés leírása. |
> | `Permissions.Actions` | Legalább egy művelet vagy egy DataActions művelet | Karakterlánc[] | Karakterláncok tömbje, amely meghatározza azokat a felügyeleti műveleteket, amelyekhez a megtagadási hozzárendelés blokkolja a hozzáférést. |
> | `Permissions.NotActions` | Nem | Karakterlánc[] | Karakterláncok tömbje, amely meghatározza a megtagadási hozzárendelésből kizárandó felügyeleti műveleteket. |
> | `Permissions.DataActions` | Legalább egy művelet vagy egy DataActions művelet | Karakterlánc[] | Karakterláncok tömbje, amely meghatározza azokat az adatműveleteket, amelyekhez a megtagadási hozzárendelés blokkolja a hozzáférést. |
> | `Permissions.NotDataActions` | Nem | Karakterlánc[] | Karakterláncok tömbje, amely meghatározza a megtagadási hozzárendelésből kizárandó adatműveleteket. |
> | `Scope` | Nem | Sztring | Olyan karakterlánc, amely megadja azt a hatókört, amelyre a megtagadási hozzárendelés vonatkozik. |
> | `DoNotApplyToChildScopes` | Nem | Logikai | Itt adható meg, hogy a megtagadási hozzárendelés vonatkozik-e a gyermekhatókörökre. Az alapértelmezett érték hamis. |
> | `Principals[i].Id` | Igen | Karakterlánc[] | Az Azure AD egyszerű objektumazonosítók (felhasználó, csoport, egyszerű szolgáltatás vagy felügyelt identitás) tömbje, amelyre a megtagadási hozzárendelés vonatkozik. Üres GUID `00000000-0000-0000-0000-000000000000` azonosítóra állítva az összes rendszerbiztonsági tag ot jelöli. |
> | `Principals[i].Type` | Nem | Karakterlánc[] | Egyszerű rendszer-biztonsági tagok által képviselt objektumtípusok tömbje..A `SystemDefined` rendszertartalmazza az összes rendszerbiztonsági tag ot. |
> | `ExcludePrincipals[i].Id` | Nem | Karakterlánc[] | Az Azure AD egyszerű objektumazonosítók (felhasználó, csoport, egyszerű szolgáltatás vagy felügyelt identitás) tömbje, amelyre a megtagadási hozzárendelés nem vonatkozik. |
> | `ExcludePrincipals[i].Type` | Nem | Karakterlánc[] | A ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Nem | Logikai | Itt adható meg, hogy ezt a megtagadási hozzárendelést az Azure hozta-e létre, és nem szerkeszthető vagy törölhető. Jelenleg minden megtagadási hozzárendelés rendszervédett. |

## <a name="the-all-principals-principal"></a>Az Összes megbízó kúra

A megtagadási hozzárendelések támogatása érdekében egy rendszer által definiált, *Minden egyszerű* felhasználó nevű főtag lett bevezetve. Ez a fő névvel jelöli az összes felhasználó, csoport, szolgáltatásnévés felügyelt identitások egy Azure AD-címtárban. Ha a fő azonosító nulla `00000000-0000-0000-0000-000000000000` GUID `SystemDefined`azonosító, és a fő típus, akkor a fő az összes rendszerbiztonsági tag. Az Azure PowerShell-kimenetben az Összes rendszerfőelem a következőképpen néz ki:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Az összes rendszerbiztonsági `ExcludePrincipals` tag kombinálható az összes felhasználó megtagadásával, kivéve néhány felhasználót. Minden megbízók a következő korlátozások:

- Csak a-ban `Principals` használható, és `ExcludePrincipals`nem használható a ban.
- `Principals[i].Type`a beállítását `SystemDefined`a beállításra kell állítani.

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Új erőforrások védelme az Azure Blueprints erőforrászárolásokkal](../governance/blueprints/tutorials/protect-new-resources.md)
* [Az Azure-erőforrások letagadni idulásának listája az Azure Portal használatával](deny-assignments-portal.md)
