---
title: Az Azure-erőforrások - jogosult hozzárendelések és erőforrás-láthatósági privileged Identity Management |} Microsoft Docs
description: Ismerteti, jogosult tagok erőforrás szerepkörökhöz rendelése.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>Jogosult hozzárendelések és láthatósága

Az Azure erőforrás-szerepkörök PIM kritikus Azure resoruces rendelkező szervezetek számára magasabb szintű biztonságot nyújt. A PIM az erőforrás-rendszergazdák, jogosult tagok erőforrás szerepkörök hozzárendelése lehetőséget nyújt. További tudnivalók a különböző hozzárendelés típusa és az Azure erőforrás-szerepkörök az alábbi állapotok. 

## <a name="assignment-types"></a>Hozzárendelés típusa

Az Azure-erőforrások PIM biztosít két különböző hozzárendelés-típus:

- Jogosult
- Aktív

Jogosult hozzárendelések a felhasználót a szerepkörbe egy műveletet a szerepkör használatához szükséges. Előfordulhat, hogy ilyen műveletek közé tartoznak, a multi-factor Authentication-ellenőrzés sikeres, a indoklás megadása és jóváhagyást kér a kijelölt jóváhagyóknak.

Aktív hozzárendelések nem igényelnek a tagot a szerepkör használandó bármely művelet elvégzésére. Aktív tagok mindig a szerepkör által biztosított jogosultságával rendelkeznek.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

Erőforrás-rendszergazdák kiválaszthatják, az egyes hozzárendelés két lehetőség közül szerepkör PIM beállításainak konfigurálásakor. Ezek a beállítások lesznek az alapértelmezett maximális időtartam, ha tagja a PIM szerepkör van hozzárendelve.

- Állandó jogosult hozzárendelés engedélyezése
- Állandó aktív hozzárendelést engedélyezése

vagy

- Jogosult hozzárendelések után lejár
- Aktív hozzárendelések után lejár

Ha az erőforrás-rendszergazda úgy dönt, hogy az "Allow állandó jogosult hozzárendelés" és/vagy az "Allow állandó active hozzárendelés", az összes rendszergazda, tag hozzárendeli az erőforrás lesz tud rendelni állandó tagságát.

Válassza a "Elévülési vonatkozó jogosult hozzárendelések után" és/vagy "Elévülési vonatkozó aktív hozzárendelések után" lehetővé teszi, hogy a hozzárendelés életciklus szabályozhatják alapján vonatkozó összes hozzárendelést rendelkező megadott kezdő és záró dátumát.

>[!NOTE] 
>A megadott Záródátum vonatkozó összes hozzárendelést is megújítani az erőforrás-rendszergazdák és tagjai kezdeményezhetnek önkiszolgáló kérelmek [kiterjesztése vagy megújítása hozzárendelések](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Hozzárendelés állapota

Az Azure-erőforrások PIM rendelkezik két különböző hozzárendelési a PIM nézetei a szerepkörök, a szerepkörök és a tagok "Aktív szerepkör" lapján jelennek meg. Ezeket az állapotokat a következők:

- Kiosztva
- Aktiválva

A tagság megtekintése szereplő "Aktív szerepkör" a "Állapot" oszlop lehetővé teszi megkülönböztetni a felhasználók, akik és a felhasználókat, hogy "aktív" jogosult hozzárendelés aktívnak "hozzárendelt" és a most aktív.

## <a name="next-steps"></a>További lépések

[A PIM szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md)

