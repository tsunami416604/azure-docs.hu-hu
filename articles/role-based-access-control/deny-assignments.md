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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980168"
---
# <a name="understand-deny-assignments"></a>Megismerheti hozzárendelés elutasítása

Szerepkör-hozzárendelés, hasonlóan egy *hozzárendelés megtagadása* egy sor kötések megtagadási műveletek egy felhasználó, csoport vagy szolgáltatásnév céljából megtagadja a hozzáférést egy adott hatókörben. Egy megtagadási hozzárendelés is rendszerbiztonsági tagok kizárása, és megakadályozza az öröklési gyermek hatókörhöz, amely eltér attól a szerepkör-hozzárendelések. Jelenleg a hozzárendelés elutasítása **csak olvasható** és lze nastavit pouze az Azure-ban. Ez a cikk azt ismerteti, hogyan megtagadása a hozzárendelések vannak definiálva.

## <a name="deny-assignment-properties"></a>Hozzárendelés tulajdonságait megtagadása

 Egy megtagadási-hozzárendelést a következő tulajdonságokkal rendelkezik:

> [!div class="mx-tableFixed"]
> | Tulajdonság | Szükséges | Típus | Leírás |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Igen | Sztring | A megtagadási hozzárendelés megjelenített neve. Egy adott hatókör egyedi névvel kell rendelkeznie. |
> | `Description` | Nem | Sztring | A megtagadási hozzárendelés leírása. |
> | `Permissions.Actions` | Legalább egy műveletet, vagy egy DataActions | String] | Adja meg a felügyeleti műveleteket, amelyhez a Megtagadás hozzárendelés letiltja a hozzáférést, amelyek tömbje. |
> | `Permissions.NotActions` | Nem | String] | Adja meg a Megtagadás hozzárendelés kizárása a felügyeleti műveleteket, amelyek tömbje. |
> | `Permissions.DataActions` | Legalább egy műveletet, vagy egy DataActions | String] | Adja meg, mely adatműveletekre, amelyhez a Megtagadás hozzárendelés letiltja a hozzáférést, amelyek tömbje. |
> | `Permissions.NotDataActions` | Nem | String] | Adja meg, mely adatműveletekre kell kizárása a Megtagadás hozzárendelésekor karakterláncok tömbje. |
> | `Scope` | Nem | Sztring | Egy karakterlánc, amely meghatározza azt a hatókört, amely a Megtagadás hozzárendelés vonatkozik. |
> | `DoNotApplyToChildScopes` | Nem | Logikai | Itt adhatja meg, hogy érvényes-e a Megtagadás hozzárendelés gyermek hatókörhöz. Alapértelmezett értéke FALSE (hamis). |
> | `Principals[i].Id` | Igen | String] | Az Azure AD egyszerű objektumazonosítók (felhasználó, csoport vagy szolgáltatásnév), amelyekre vonatkozik a Megtagadás hozzárendelés tömbje. Állítsa egy üres GUID azonosító `00000000-0000-0000-0000-000000000000` , amelyek mindenki számára. |
> | `Principals[i].Type` | Nem | String] | Rendszerbiztonsági tagok [i] .id által képviselt objektumtípusok tömbje. Állítsa be `Everyone` , amelyek mindenki számára. |
> | `ExcludePrincipals[i].Id` | Nem | String] | Az Azure AD egyszerű objektumazonosítók (felhasználó, csoport vagy szolgáltatásnév), amelyre nem vonatkozik a Megtagadás hozzárendelés tömbje. |
> | `ExcludePrincipals[i].Type` | Nem | String] | Objektumtípusok ExcludePrincipals [i] .id által képviselt tömbje. |
> | `IsSystemProtected` | Nem | Logikai | Itt adhatja meg, hogy ez megtagadása hozzárendelés hozta létre az Azure és a nem szerkeszthető vagy törölhető. Jelenleg az összes elutasítása hozzárendelések a védett rendszer. |

## <a name="everyone-principal"></a>Mindenki rendszerbiztonsági tag

A támogatási megtagadása hozzárendelések, a Mindenki egyszerű fejlődéséből. A Mindenki egyszerű jelöli az összes olyan felhasználók, csoportok és az egyszerű szolgáltatások Azure AD-címtárral. Ha az egyszerű szolgáltatásnév azonosítója nem nulla GUID `00000000-0000-0000-0000-000000000000` és a résztvevő típusa `Everyone`, az egyszerű mindenki jelöli. A Mindenki egyszerű kombinálva `ExcludePrincipals` mindenki kivételével az egyes felhasználók. A következő korlátozások vonatkoznak a Mindenki egyszerű:

- Csak a felhasznált `Principals` és nem használható `ExcludePrincipals`.
- `Principals[i].Type` meg kell `Everyone`.

## <a name="next-steps"></a>További lépések

* [Listában elutasítása hozzárendelések rbac-RÓL és a REST API használatával](deny-assignments-rest.md)
* [Szerepkör-definíciók ismertetése](role-definitions.md)
