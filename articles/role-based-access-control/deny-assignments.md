---
title: Megismerheti az Azure-erőforrások hozzárendelések elutasítása |} A Microsoft Docs
description: 'További tudnivalók: elutasítás-hozzárendeléseit a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz.'
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
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f15d6fd81337aa4a859539e86f37a516848c9370
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165984"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Megismerheti az Azure-erőforrások hozzárendelések megtagadása

Szerepkör-hozzárendelés, hasonlóan egy *hozzárendelés megtagadása* rendeli egy sor megtagadási műveletek egy felhasználó, csoport vagy szolgáltatásnév céljából megtagadja a hozzáférést egy adott hatókörben. Megtagadási hozzárendelések letiltása a felhasználók számára az adott Azure-erőforrás műveleteket végrehajtani, akkor is, ha a szerepkör-hozzárendelés hozzáférést biztosít számukra.

Ez a cikk azt ismerteti, hogyan megtagadása a hozzárendelések vannak definiálva.

## <a name="how-deny-assignments-are-created"></a>Hogyan megtagadása a hozzárendelések jönnek létre.

Megtagadási hozzárendelések létrehozása és felügyelete az Azure-erőforrások védelmét. Ha például az Azure-tervek és az Azure felügyelt alkalmazások használata megtagadása-hozzárendeléseket a rendszer által felügyelt erőforrások védelmét. További információkért lásd: [Azure tervezetek erőforrászárat az új erőforrások védelmét](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="compare-role-assignments-and-deny-assignments"></a>Hasonlítsa össze a szerepkör-hozzárendelések és a hozzárendelés elutasítása

Megtagadási hozzárendelések hasonló mintát követi, szerepkör-hozzárendeléseket, de néhány eltérés is rendelkezik.

| Képesség | Szerepkör-kijelölés | Hozzárendelés elutasítása |
| --- | --- | --- |
| Hozzáférés biztosítása | :heavy_check_mark: |  |
| Hozzáférés megtagadása |  | :heavy_check_mark: |
| Közvetlenül hozható létre | :heavy_check_mark: |  |
| A alkalmazni a hatókörben | :heavy_check_mark: | :heavy_check_mark: |
| Rendszerbiztonsági tagok kizárása |  | :heavy_check_mark: |
| Gyermek hatókörök öröklődés megakadályozása |  | :heavy_check_mark: |
| A alkalmazni [hagyományos előfizetés-adminisztrátor](rbac-and-directory-admin-roles.md) hozzárendelések |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Hozzárendelés tulajdonságait megtagadása

 Egy megtagadási-hozzárendelést a következő tulajdonságokkal rendelkezik:

> [!div class="mx-tableFixed"]
> | Tulajdonság | Kötelező | Típus | Leírás |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Igen | String | A megtagadási hozzárendelés megjelenített neve. Egy adott hatókör egyedi névvel kell rendelkeznie. |
> | `Description` | Nem | String | A megtagadási hozzárendelés leírása. |
> | `Permissions.Actions` | Legalább egy műveletet, vagy egy DataActions | String] | Adja meg a felügyeleti műveleteket, amelyhez a Megtagadás hozzárendelés letiltja a hozzáférést, amelyek tömbje. |
> | `Permissions.NotActions` | Nem | String] | Adja meg a Megtagadás hozzárendelés kizárása a felügyeleti műveleteket, amelyek tömbje. |
> | `Permissions.DataActions` | Legalább egy műveletet, vagy egy DataActions | String] | Adja meg, mely adatműveletekre, amelyhez a Megtagadás hozzárendelés letiltja a hozzáférést, amelyek tömbje. |
> | `Permissions.NotDataActions` | Nem | String] | Adja meg, mely adatműveletekre kell kizárása a Megtagadás hozzárendelésekor karakterláncok tömbje. |
> | `Scope` | Nem | String | Egy karakterlánc, amely meghatározza azt a hatókört, amely a Megtagadás hozzárendelés vonatkozik. |
> | `DoNotApplyToChildScopes` | Nem | Boolean | Itt adhatja meg, hogy érvényes-e a Megtagadás hozzárendelés gyermek hatókörhöz. Alapértelmezett értéke FALSE (hamis). |
> | `Principals[i].Id` | Igen | String] | Az Azure AD egyszerű objektumazonosítók (felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás), amelyekre vonatkozik a Megtagadás hozzárendelés tömbje. Állítsa egy üres GUID azonosító `00000000-0000-0000-0000-000000000000` képviselő összes rendszerbiztonsági tag. |
> | `Principals[i].Type` | Nem | String] | Rendszerbiztonsági tagok [i] .id által képviselt objektumtípusok tömbje. Állítsa be `SystemDefined` képviselő összes rendszerbiztonsági tag. |
> | `ExcludePrincipals[i].Id` | Nem | String] | Az Azure AD egyszerű objektumazonosítók (felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás), amelyre nem vonatkozik a Megtagadás hozzárendelés tömbje. |
> | `ExcludePrincipals[i].Type` | Nem | String] | Objektumtípusok ExcludePrincipals [i] .id által képviselt tömbje. |
> | `IsSystemProtected` | Nem | Boolean | Itt adhatja meg, hogy ez megtagadása hozzárendelés hozta létre az Azure és a nem szerkeszthető vagy törölhető. Jelenleg az összes elutasítása hozzárendelések a védett rendszer. |

## <a name="the-all-principals-principal"></a>Az összes rendszerbiztonsági tag egyszerű

A támogatási megtagadása hozzárendelések, a rendszer által meghatározott nevű egyszerű *összes rendszerbiztonsági tag* fejlődéséből. Ez az egyszerű felhasználók, csoportok, a szolgáltatásnevek és az Azure AD-címtár felügyelt identitások jelöli. Ha az egyszerű szolgáltatásnév azonosítója nem nulla GUID `00000000-0000-0000-0000-000000000000` és egyszerű típus `SystemDefined`, az egyszerű jelöli az összes rendszerbiztonsági tag. Az Azure PowerShell-lel kimeneti összes rendszerbiztonsági tag a következőhöz hasonlóan néz ki:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Minden rendszerbiztonsági tagok kombinálva `ExcludePrincipals` megtagadni az egyes felhasználók kivételével az összes rendszerbiztonsági tagok. Összes rendszerbiztonsági tag a következő korlátozások vonatkoznak:

- Csak a felhasznált `Principals` és nem használható `ExcludePrincipals`.
- `Principals[i].Type` meg kell `SystemDefined`.

## <a name="next-steps"></a>További lépések

* [Listában elutasítása hozzárendelések Azure-erőforrások az Azure portal használatával](deny-assignments-portal.md)
* [Megismerheti az Azure-erőforrások szerepkör-definíciók](role-definitions.md)
