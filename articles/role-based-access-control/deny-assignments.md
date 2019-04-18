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
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 497571a65510f806d7d7994c9dc37f9a00b65a5f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006720"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Megismerheti az Azure-erőforrások hozzárendelések megtagadása

Szerepkör-hozzárendelés, hasonlóan egy *hozzárendelés megtagadása* rendeli egy sor megtagadási műveletek egy felhasználó, csoport vagy szolgáltatásnév céljából megtagadja a hozzáférést egy adott hatókörben. Megtagadási hozzárendelések letiltása a felhasználók számára az adott Azure-erőforrás műveleteket végrehajtani, akkor is, ha a szerepkör-hozzárendelés hozzáférést biztosít számukra. Néhány erőforrás-szolgáltatók az Azure-ban most már tartalmazza a hozzárendelések tiltása.

Bizonyos értelemben a hozzárendelések eltérnek szerepkör-hozzárendelések tiltása. Megtagadási hozzárendelések rendszerbiztonsági tagok kizárása, és megakadályozza az öröklési gyermek hatókörhöz. Megtagadási hozzárendelések is alkalmazni kell [hagyományos előfizetés-adminisztrátor](rbac-and-directory-admin-roles.md) hozzárendelések.

Ez a cikk azt ismerteti, hogyan megtagadása a hozzárendelések vannak definiálva.

> [!NOTE]
> Jelenleg az egyetlen módszer, hozzáadhatja a saját megtagadási hozzárendelések van Azure-tervek használatával. További információkért lásd: [Azure tervezetek erőforrászárat az új erőforrások védelmét](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="deny-assignment-properties"></a>Hozzárendelés tulajdonságait megtagadása

 Egy megtagadási-hozzárendelést a következő tulajdonságokkal rendelkezik:

> [!div class="mx-tableFixed"]
> | Tulajdonság | Szükséges | Típus | Leírás |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Igen | String | A megtagadási hozzárendelés megjelenített neve. Egy adott hatókör egyedi névvel kell rendelkeznie. |
> | `Description` | Nem | String | A megtagadási hozzárendelés leírása. |
> | `Permissions.Actions` | Legalább egy műveletet, vagy egy DataActions | String] | Adja meg a felügyeleti műveleteket, amelyhez a Megtagadás hozzárendelés letiltja a hozzáférést, amelyek tömbje. |
> | `Permissions.NotActions` | Nem | String] | Adja meg a Megtagadás hozzárendelés kizárása a felügyeleti műveleteket, amelyek tömbje. |
> | `Permissions.DataActions` | Legalább egy műveletet, vagy egy DataActions | String] | Adja meg, mely adatműveletekre, amelyhez a Megtagadás hozzárendelés letiltja a hozzáférést, amelyek tömbje. |
> | `Permissions.NotDataActions` | Nem | String] | Adja meg, mely adatműveletekre kell kizárása a Megtagadás hozzárendelésekor karakterláncok tömbje. |
> | `Scope` | Nem | String | Egy karakterlánc, amely meghatározza azt a hatókört, amely a Megtagadás hozzárendelés vonatkozik. |
> | `DoNotApplyToChildScopes` | Nem | Logikai | Itt adhatja meg, hogy érvényes-e a Megtagadás hozzárendelés gyermek hatókörhöz. Alapértelmezett értéke FALSE (hamis). |
> | `Principals[i].Id` | Igen | String] | Az Azure AD egyszerű objektumazonosítók (felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás), amelyekre vonatkozik a Megtagadás hozzárendelés tömbje. Állítsa egy üres GUID azonosító `00000000-0000-0000-0000-000000000000` képviselő összes rendszerbiztonsági tag. |
> | `Principals[i].Type` | Nem | String] | Rendszerbiztonsági tagok [i] .id által képviselt objektumtípusok tömbje. Állítsa be `SystemDefined` képviselő összes rendszerbiztonsági tag. |
> | `ExcludePrincipals[i].Id` | Nem | String] | Az Azure AD egyszerű objektumazonosítók (felhasználó, csoport, szolgáltatásnevet vagy felügyelt identitás), amelyre nem vonatkozik a Megtagadás hozzárendelés tömbje. |
> | `ExcludePrincipals[i].Type` | Nem | String] | Objektumtípusok ExcludePrincipals [i] .id által képviselt tömbje. |
> | `IsSystemProtected` | Nem | Logikai | Itt adhatja meg, hogy ez megtagadása hozzárendelés hozta létre az Azure és a nem szerkeszthető vagy törölhető. Jelenleg az összes elutasítása hozzárendelések a védett rendszer. |

## <a name="system-defined-principal"></a>A rendszer által definiált egyszerű

A támogatási hozzárendelések megtagadása a **rendszer általi egyszerű** fejlődéséből. Ez az egyszerű felhasználók, csoportok, a szolgáltatásnevek és az Azure AD-címtár felügyelt identitások jelöli. Ha az egyszerű szolgáltatásnév azonosítója nem nulla GUID `00000000-0000-0000-0000-000000000000` és egyszerű típus `SystemDefined`, az egyszerű jelöli az összes rendszerbiztonsági tag. `SystemDefined` kombinálva `ExcludePrincipals` megtagadni az egyes felhasználók kivételével az összes rendszerbiztonsági tagok. `SystemDefined` a következő korlátozások vonatkoznak:

- Csak a felhasznált `Principals` és nem használható `ExcludePrincipals`.
- `Principals[i].Type` meg kell `SystemDefined`.

## <a name="next-steps"></a>További lépések

* [Megtekintése az Azure portal segítségével Azure-erőforrások hozzárendelések megtagadása](deny-assignments-portal.md)
* [Megismerheti az Azure-erőforrások szerepkör-definíciók](role-definitions.md)
