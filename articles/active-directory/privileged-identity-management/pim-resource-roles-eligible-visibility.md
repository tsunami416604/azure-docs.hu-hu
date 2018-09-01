---
title: Jogosult hozzárendelések és a PIM - Azure láthatósága |} A Microsoft Docs
description: Ismerteti, hogyan lehet hozzárendelni a tagokat, jogosult az Azure-erőforrásszerepkörök az Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 59d51ba8edadd1fd71255271623b144cab94fc97
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344283"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>Jogosult hozzárendelések és a PIM erőforrás láthatóságát

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

## <a name="azure-resource-role-approval-workflow"></a>Azure-erőforrás szerepkör jóváhagyási munkafolyamata

A jóváhagyási munkafolyamat PIM az Azure-erőforrások szerepköreihez tartozó a rendszergazdák további védelméhez, vagy kritikus erőforrásokhoz való hozzáférés korlátozása. A rendszergazdák, szerepkör-hozzárendelések az aktiváláshoz jóváhagyásra lehet szükség.

Az erőforrás hierarchia elképzelés az, csak az Azure-erőforrások szerepköreihez. Ez a hierarchia lehetővé teszi, hogy az öröklési szülőtároló található összes gyermek-erőforrás a szülőobjektum erőforrás lefelé szerepkör-hozzárendelések. 

Például: Bob, egy erőforrás-rendszergazda, használja a PIM módon megadott tagja jogosult Alice hozzárendelése a Contoso az előfizetésben tulajdonosi szerepkör. Ezt a hozzárendelést Alice a Contoso-előfizetésen belüli összes erőforrás csoport tárolók jogosult tulajdonosa. Alice az is az összes erőforrás (például virtuális gépek) jogosult tulajdonosa az előfizetés mindegyik erőforráscsoporton belül.

Tegyük fel, a Contoso-előfizetés három erőforráscsoportok vannak: a Fabrikam tesztelés, a Fabrikam fejlesztői és a Fabrikam gyárt. Ezeket az erőforráscsoportokat mindegyike tartalmaz egy virtuális gépen.

A PIM beállításai az egyes szerepkörökhöz erőforrás. Hozzárendelések ellentétben ezek a beállítások nem öröklődnek, és feltétlenül vonatkozik az erőforrás-szerepkör.

A példa folytatása: Bob PIM használ, amelyet aktiválnia kell a tulajdonosi szerepkör a Contoso előfizetési kérés jóváhagyási szereplő összes tag igényelnek. A Fabrikam éles erőforráscsoportban az erőforrások védelme érdekében Bob is jóváhagyásra van szüksége ennek az erőforrásnak a tulajdonos szerepkör tagjai. A Fabrikam tesztelési és a Fabrikam fejlesztői tulajdonos szerepkörök nem igényel jóváhagyási az aktiváláshoz.

Amikor Ágnes a Contoso előfizetés tulajdonosi szerepkörét aktiválását, a jóváhagyó musí schválit nebo elutasítja a saját kérelmet, mielőtt ő válik aktívvá a szerepkörben. Ha úgy dönt, hogy Ágnes [saját aktiválási hatókör](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) a Fabrikam éles erőforráscsoporthoz a jóváhagyó jóváhagyása vagy elutasítása túl a ezt a kérelmet kell. De ha Alice úgy dönt, hogy saját aktiválási egyik vagy mindkét Fabrikam teszt vagy a Fabrikam fejlesztői hatókör, jóváhagyás, nem szükséges.

A jóváhagyási munkafolyamat nem feltétlenül szükséges a szerepkör összes tagja számára. Példaként vegyünk egy forgatókönyvet, ahol a szervezet több szerződés hozzárendeli egy Azure-előfizetésben futó alkalmazás fejlesztésének kiküszöbölni hires. Erőforrás-rendszergazdaként azt szeretné, hogy az alkalmazottak jogosult hozzáférése nélkül jóváhagyás szükséges, de a szerződés hozzárendeli engedélyt kell kérnie. Jóváhagyási munkafolyamatok konfigurálása csak a szerződés hozzárendeli, létrehozhat egy egyéni biztonsági szerepkört, az alkalmazottak rendelt szerepkör ugyanazokkal az engedélyekkel. Megkövetelheti, hogy egyéni szerepkör aktiválásához jóváhagyás. [További információ az egyéni szerepkörök](pim-resource-roles-custom-role-policy.md).

## <a name="next-steps"></a>További lépések

- [Rendelje hozzá a PIM az Azure-erőforrásszerepkörök](pim-resource-roles-assign-roles.md)
