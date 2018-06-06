---
title: Jogosult hozzárendelések és az Azure Privileged Identity Management a láthatósága |} Microsoft Docs
description: Útmutatás szerint jogosult az erőforrás-szerepkörök tagjainak hozzárendeléséhez a PIM használatakor.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: bb1f9c63d4e01f3927b1c58a68742e107cf9331d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699641"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Jogosult hozzárendelések és a Privileged Identity Management láthatósága

Privileged Identity Management (PIM) az Azure erőforrás-szerepkörök kritikus Azure-erőforrások rendelkező szervezeteknek magasabb szintű biztonságot nyújt. Erőforrás-rendszergazdák a PIM segítségével szerint jogosult az erőforrás-szerepkörök tagjainak hozzárendeléséhez. További információ a különböző hozzárendelési típusok és a hozzárendelés állapota az alábbi szakaszok az Azure erőforrás-szerepkörök. 

## <a name="assignment-types"></a>Hozzárendelés típusa

Az Azure-erőforrások PIM biztosít két különböző hozzárendelés-típus:

- Jogosult
- Aktív

Jogosult hozzárendelések a felhasználót a szerepkörbe egy műveletet a szerepkör használatához szükséges. Műveletek közé tartozik a többtényezős hitelesítés-ellenőrzés sikeres, így üzleti indoklásának vagy jóváhagyást kér a kijelölt jóváhagyóknak.

Aktív hozzárendelések nem feltétlenül szükséges a szerepkör használandó bármely művelet elvégzésére tag. Aktív hozzárendelt tagok mindig a szerepkörhöz hozzárendelt jogosultságával rendelkeznek.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

Erőforrás-rendszergazdák az egyes hozzárendelés két lehetőségek közül választhatnak, a szerepkör PIM beállításainak konfigurálása során. Ezek a beállítások lesznek az alapértelmezett maximális időtartam, ha tagja a PIM szerepkör van hozzárendelve. 

A rendszergazda a hozzárendelés típusok közül választhat:

- Állandó jogosult hozzárendelés engedélyezése
- Állandó aktív hozzárendelés engedélyezése

Vagy a rendszergazda a hozzárendelés típusok közül választhat:

- Jogosult hozzárendelések után lejár
- Az aktív hozzárendelések elévülnek a jelzett idő elteltével:

Ha egy erőforrás-rendszergazda úgy dönt, **engedélyezése végleges jogosult hozzárendelés** vagy **engedélyezése végleges aktív hozzárendelést**, állandó rendelhet hozzá az összes rendszergazda, tag hozzárendeli az erőforrás tagságát.

Ha egy erőforrás-rendszergazda úgy dönt, **jogosult hozzárendelések után lejár** vagy **aktív hozzárendelések után lejár**, az erőforrás-rendszergazda felügyeli a hozzárendelés élettartama azzal, hogy, hogy minden hozzárendelések rendelkeznek a megadott kezdő és záró dátumát.

> [!NOTE] 
> A megadott Záródátum rendelkező vonatkozó összes hozzárendelést is megújítani az erőforrás-rendszergazdák. Emellett tagok is kezdeményezhető a kérelmek önkiszolgáló [kiterjesztése vagy megújítása hozzárendelések](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Hozzárendelés állapota

Az Azure-erőforrások PIM rendelkezik két különböző hozzárendelési megjelenő a **aktív szerepkör** lapra a **a szerepkörök**, **szerepkörök**, és **tagok**a PIM nézetei. Ezeket az állapotokat a következők:

- Kiosztva
- Aktiválva

A tagság szerepel-e megtekintésekor **aktív szerepkör**, használhatja az értéket a **állapot** oszlop megkülönböztetni a felhasználók, akik **hozzárendelt** aktívként és felhasználók, amelyek **aktív** egy jogosult hozzárendelés lesznek, és most aktív.

## <a name="next-steps"></a>További lépések

[A Privileged Identity Manager szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md)
