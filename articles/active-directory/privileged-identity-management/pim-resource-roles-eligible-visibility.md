---
title: Jogosult hozzárendelések és a Privileged Identity Management Azure-erőforrás láthatóságát |} A Microsoft Docs
description: Ismerteti, hogyan lehet hozzárendelni a tagokat, jogosult az erőforrás-szerepkörökkel, a PIM használata esetén.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 336453c1ef6ef8d0295d00f31afc6a5e7e42e8b6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621548"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Jogosult hozzárendelések és a Privileged Identity Management láthatósága

Privileged Identity Management (PIM) az Azure-erőforrások szerepköreihez tartozó olyan szervezeteknek, amelyek kritikus fontosságú Azure-erőforrások fokozott biztonságot nyújt. Erőforrás-rendszergazdák használhatják a PIM jogosult az erőforrás-szerepkörökkel, tagok. További információ a különböző típusok és a hozzárendelés állapotai az alábbi szakaszok az Azure-erőforrások szerepköreihez tartozó. 

## <a name="assignment-types"></a>Hozzárendelés-típusok

A PIM használata Azure-erőforrások két különböző típusok biztosítja:

- Jogosult
- Aktív

Jogosult hozzárendelés szükséges a szerepkör tagjai egy műveletet a szerepkör használatához. Műveletek egy multi-factor authentication szolgáltatás-ellenőrzés sikeres, egy üzleti indoklás megadása vagy jóváhagyás kérése a kijelölt jóváhagyókat között lehet.

Aktív hozzárendelések nem igénylik a tagot a szerepkör használatához bármely művelet elvégzésére. A tagok hozzárendelve aktívként mindig a szerepkörhöz rendelt jogosultság.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

Erőforrás-rendszergazdák minden hozzárendelés-típus két lehetőség közül választhat, azokat a PIM szerepkör beállításainak konfigurálásakor. Ezek a beállítások a alapértelmezett maximális időtartamot, amikor egy tag szerepkör van rendelve, a PIM a válnak. 

A rendszergazda ezen hozzárendelés-típusok közül választhat:

- Állandó jogosult hozzárendelés engedélyezése
- Állandó aktív hozzárendelés engedélyezése

Vagy a rendszergazda ezen hozzárendelés-típusok közül választhat:

- Jogosult hozzárendelés után lejár.
- Az aktív hozzárendelések elévülnek a jelzett idő elteltével:

Ha egy erőforrás-rendszergazda úgy dönt, **állandó jogosult hozzárendelés engedélyezése** vagy **állandó aktív hozzárendelés engedélyezése**, tagok hozzárendelése az erőforrás végző összes rendszergazda állandó is hozzárendelhet. csoporttagságok.

Ha egy erőforrás-rendszergazda úgy dönt, **jogosult hozzárendelésekkel után lejár** vagy **aktív hozzárendelések után lejár**, az erőforrás-rendszergazda szabályozza a hozzárendelés életciklus úgy, hogy az összes hozzárendelések rendelkeznek a megadott kezdő és záró dátumát.

> [!NOTE] 
> Erőforrás-rendszergazdák által meg lehet újítani a megadott befejezési dátummal rendelkező összes hozzárendelést. Emellett tagokat is kezdeményezhető a kérések az önkiszolgáló [meghosszabbítása vagy megújítása hozzárendelések](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Hozzárendelés állapotok

A PIM használata Azure-erőforrások két különböző hozzárendelés állapota megjelenő rendelkezik a **aktív szerepkörök** lapján a **saját szerepkörök**, **szerepkörök**, és **tagok**PIM nézetét. Ezek az állapotok a következők:

- Kiosztva
- Aktiválva

Tagság szereplő megtekintésekor **aktív szerepkörök**, használhatja az értéket a **állapot** oszlop különbséget tenni a felhasználóknak, akik **hozzárendelt** aktívként és felhasználók, amelyek **aktiválva** egy jogosult hozzárendelés, és a rendszer most már aktív.

## <a name="next-steps"></a>További lépések

[A Privileged Identity Manager szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md)
