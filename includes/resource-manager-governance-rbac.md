---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 24863e239620f84a57c631b3ecf5b08997de31c5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
Azt szeretné, győződjön meg róla a szervezethez tartozó felhasználók a megfelelő szintű ezekhez az erőforrásokhoz való hozzáférés. Nem kívánja korlátlan hozzáférést a felhasználók számára, de azt is meg kell győződnie arról tehetik munkájukat. Szerepköralapú hozzáférés-vezérlést (RBAC) lehetővé teszi kezelését a jogosult felhasználók hatókörre konkrét műveletek elvégzéséhez. Egy szerepkör engedélyezett műveletek egy csoportját határozza meg. A szerepkör hozzárendelése egy hatókört, és adja meg, hogy mely felhasználók a hatókör szerepkörhöz tartozik.

A hozzáférés-vezérlési stratégia tervezésekor felhasználóknak adja meg a minimális jogosultság a munkájukat. A következő kép bemutatja az RBAC hozzárendelése javasolt mintázatát.

![Hatókör](./media/resource-manager-governance-rbac/role-examples.png)

Minden erőforrás - tulajdonos, közreműködő, és ahhoz való olvasóra vonatkozó három szerepkör is létezik. Azokat a fiókokat a tulajdonosi szerepkört rendelt szigorú ellenőrzés legyen, és ritkán használt. Felhasználókat csak megoldások állapotát megfigyelheti az olvasó szerepkört meg kell adni.

A legtöbb felhasználó megkapja [erőforrás-specifikus szerepköröket](../articles/active-directory/role-based-access-built-in-roles.md) vagy [egyéni szerepkörök](../articles/active-directory/role-based-access-control-custom-roles.md) az előfizetés vagy az erőforrás csoportok szintjén. Ezek a szerepkörök szorosan megadása az engedélyezett műveleteket. Rendelhet a felhasználók ezeket a szerepköröket, a felhasználók számára szükséges hozzáférést lehetővé tevő túl sok vezérlő nélkül engedélyez. Hozzárendelhet egy fiókot egynél több szerepkörhöz, és, hogy a felhasználó élvezheti a szerepkörök kombinált engedélyeit. Az erőforrás-szintű hozzáférés biztosítása gyakran túl szigorú a felhasználók számára, de előfordulhat, hogy egy adott feladat készült automatizált folyamat működnek.

### <a name="who-can-assign-roles"></a>Szerepkörök megadásukra

Hozzon létre, és távolítsa el a szerepkör-hozzárendelések, felhasználónak rendelkeznie kell `Microsoft.Authorization/roleAssignments/*` hozzáférést. A hozzáférés a tulajdonos vagy a felhasználói hozzáférés adminisztrátora szerepkörök keresztül.