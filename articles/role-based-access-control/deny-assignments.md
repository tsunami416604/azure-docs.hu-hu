---
title: Megismerheti az Azure RBAC-hozzárendelések megtagadása |} A Microsoft Docs
description: További információk az elutasítás-hozzárendeléseit a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: fa1a979c01999bd79c45d24e4c7771edaf346dd8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632415"
---
# <a name="understand-deny-assignments"></a>A megtagadási hozzárendelések ismertetése

Szerepkör-hozzárendelés, hasonlóan egy *hozzárendelés megtagadása* rendeli egy sor megtagadási műveletek egy felhasználó, csoport vagy szolgáltatásnév céljából megtagadja a hozzáférést egy adott hatókörben. Megtagadási hozzárendelések letiltása a felhasználók számára az adott műveletek végrehajtására, még akkor is, ha a szerepkör-hozzárendelés hozzáférést biztosít számukra. Néhány erőforrás-szolgáltatók az Azure-ban most már tartalmazza a hozzárendelések tiltása. Jelenleg a megtagadás-hozzárendelések **csak olvashatók**, és csak az Azure állíthatja be azokat.

Bizonyos értelemben a hozzárendelések eltérnek szerepkör-hozzárendelések tiltása. Megtagadási hozzárendelések rendszerbiztonsági tagok kizárása, és megakadályozza az öröklési gyermek hatókörhöz. Megtagadási hozzárendelések is alkalmazni kell [hagyományos előfizetés-adminisztrátor](rbac-and-directory-admin-roles.md) hozzárendelések.

Ez a cikk azt ismerteti, hogyan megtagadása a hozzárendelések vannak definiálva.

## <a name="deny-assignment-properties"></a>Hozzárendelés tulajdonságait megtagadása

 Egy megtagadási-hozzárendelést a következő tulajdonságokkal rendelkezik:

> [!div class="mx-tableFixed"]
> | Tulajdonság | Szükséges | Típus | Leírás |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Igen | Karakterlánc | A megtagadási hozzárendelés megjelenített neve. Egy adott hatókör egyedi névvel kell rendelkeznie. |
> | `Description` | Nem | Karakterlánc | A megtagadási hozzárendelés leírása. |
> | `Permissions.Actions` | Legalább egy műveletet, vagy egy DataActions | String] | Adja meg a felügyeleti műveleteket, amelyhez a Megtagadás hozzárendelés letiltja a hozzáférést, amelyek tömbje. |
> | `Permissions.NotActions` | Nem | String] | Adja meg a Megtagadás hozzárendelés kizárása a felügyeleti műveleteket, amelyek tömbje. |
> | `Permissions.DataActions` | Legalább egy műveletet, vagy egy DataActions | String] | Adja meg, mely adatműveletekre, amelyhez a Megtagadás hozzárendelés letiltja a hozzáférést, amelyek tömbje. |
> | `Permissions.NotDataActions` | Nem | String] | Adja meg, mely adatműveletekre kell kizárása a Megtagadás hozzárendelésekor karakterláncok tömbje. |
> | `Scope` | Nem | Karakterlánc | Egy karakterlánc, amely meghatározza azt a hatókört, amely a Megtagadás hozzárendelés vonatkozik. |
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

* [Listában elutasítása hozzárendelések rbac-RÓL és a REST API használatával](deny-assignments-rest.md)
* [Szerepkör-definíciók ismertetése](role-definitions.md)
