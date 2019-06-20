---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f77a5d482c3f8632a3d86bd8e027fbb4418168c3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205328"
---
Győződjön meg arról, hogy az intézmény felhasználói megfelelő hozzáférési szinttel rendelkeznek ezekhez az erőforrásokhoz. Nem ajánlott korlátlan hozzáférést nyújtania a felhasználóknak, de fontos biztosítania, hogy el tudják végezni a munkájukat. Szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi egy hatókörben bizonyos műveletek elvégzéséhez jogosult felhasználók kezelését. Egy szerepkör engedélyezett műveletek egy csoportját határozza meg. Rendelje hozzá a szerepkört egy hatókört, és adja meg, hogy mely felhasználók tartoznak a hatókör szerepkörhöz.

A hozzáférés-vezérlési stratégia megtervezésekor csak a munkájuk elvégzéséhez minimálisan szükséges jogosultságokat biztosítsa a felhasználók számára. Az alábbi képen az RBAC használatának egy javasolt mintája látható.

![Scope](./media/resource-manager-governance-rbac/role-examples.png)

Nincsenek három szerepkört, amely a alkalmazni az összes erőforrás - tulajdonos, közreműködő és olvasó. A tulajdonosi szerepkörhöz rendelt összes fiókot kell szigorúan ellenőrzött és a ritkán használt. Az Olvasó szerepkör felhasználóknak csak a megoldások állapotának megfigyelését kell engedélyezni.

A legtöbb felhasználók kapjanak [erőforrás-specifikus szerepkörökhöz](../articles/role-based-access-control/built-in-roles.md) vagy [egyéni szerepkörök](../articles/role-based-access-control/custom-roles.md) az előfizetés vagy a resource csoport szintjén. Ezek a szerepkörök szorosan meghatározzák az engedélyezett műveleteket. Ezeket a szerepköröket rendelhet a felhasználók, hozzáférést biztosít a szükséges felhasználók túl sok a vezérlő lehetővé tevő nélkül. Egynél több szerepkört hozzárendelheti egy fiókot, és, hogy a felhasználó a kombinált engedélyekkel szerepkörök beolvasása. Az erőforráscsoport szintjén hozzáférést gyakran túl korlátozó a felhasználók számára, de egy adott feladat készült automatikus folyamat is dolgozhat.

### <a name="who-can-assign-roles"></a>Akik rendelhet szerepköröket

A szerepkör-hozzárendelések létrehozásához és eltávolításához a felhasználóknak `Microsoft.Authorization/roleAssignments/*` hozzáféréssel kell rendelkezniük. Ez a hozzáférés a tulajdonosi vagy a felhasználói hozzáférés rendszergazdájának szerepkörével adható meg.