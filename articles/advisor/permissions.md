---
title: Az Azure Advisor engedélyek
description: Advisor engedélyeket, és hogyan lehet, hogy blokkolja előfizetések konfigurálása vagy elhalaszthatja vagy javaslatok elvetése képességét.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59052792"
---
# <a name="permissions-in-azure-advisor"></a>Az Azure Advisor engedélyek

Az Azure Advisor ajánlásokkal használatának és az Azure-erőforrások és-előfizetések konfigurációja alapján. Az Advisor használja a [beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) által biztosított [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) vonatkozó javaslatok és az Advisor szolgáltatást a hozzáférés kezelésére. 

## <a name="roles-and-their-access"></a>Szerepkörök és a hozzáférésüket

Az alábbi táblázat a szerepkörök és a hozzáférést, az Advisor belül határozza meg:

| **Szerepkör** | **Javaslatok megtekintése** | **Szabályok szerkesztése** | **Előfizetés-konfiguráció szerkesztése** | **Szerkessze a fürterőforrás-csoport konfigurálása**| **Zárja be és ajánlások elhalasztása**|
|---|:---:|:---:|:---:|:---:|:---:|
|Előfizetés tulajdonosa|**X**|**X**|**X**|**X**|**X**|
|Előfizetés Közreműködője|**X**|**X**|**X**|**X**|**X**|
|Előfizetés: olvasó|**X**|--|--|--|--|
|Erőforráscsoport tulajdonosa|**X**|--|--|**X**|**X**|
|Erőforráscsoport közreműködő|**X**|--|--|**X**|**X**|
|Erőforráscsoport-olvasó|**X**|--|--|--|--|
|Erőforrás tulajdonosa|**X**|--|--|--|**X**|
|Erőforrás-közreműködő|**X**|--|--|--|**X**|
|Erőforrás-olvasó|**X**|--|--|--|--|

> [!NOTE]
> Ajánlás megtekintése a hozzáférést az adott nyelvtől függ a hozzáférést a javaslat érintett erőforráshoz.

## <a name="permissions-and-unavailable-actions"></a>Engedélyek és a műveletek nem érhető el

Megfelelő engedélyek hiánya képes blokkolni a hajthatnak végre műveleteket az Advisor. Az alábbiakban néhány gyakori problémát.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nem sikerült beállítani a előfizetések vagy erőforráscsoportok

Előfizetések vagy erőforráscsoportok konfigurálása az Advisor kísérli meg, láthatja, hogy a bevonhat vagy kizárhat le van tiltva. Ez az állapot azt jelzi, hogy nincs megfelelő engedélye az adott erőforráscsoportba vagy előfizetésbe szintet. A probléma megoldásához, megtudhatja, hogyan [felhasználói hozzáférésének biztosítása](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nem lehet elhalaszthatja vagy javaslat bezárása

Elhalaszthatja vagy bezárása egy javaslatot tett kísérlet során hibaüzenetet kap, ha lehetséges, hogy nem rendelkezik megfelelő engedélyekkel. Győződjön meg arról, hogy rendelkezik-e legalább közreműködői hozzáféréssel az ajánlás elhalasztása vagy elvetése, akkor az érintett erőforrás. A probléma megoldásához, megtudhatja, hogyan [felhasználói hozzáférésének biztosítása](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>További lépések

Ez a cikk adott felvázoljuk, hogyan Advisor használja a felhasználói hozzáférést RBAC és kapcsolatos gyakori problémák megoldását. Az Advisor kapcsolatos további információkért lásd:

- [Mi az Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Az Azure Advisor használatának első lépései](https://docs.microsoft.com/azure/advisor/advisor-get-started)
