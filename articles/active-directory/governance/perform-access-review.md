---
title: Tekintse át a hozzáférési csoportokat, vagy a hozzáférési felülvizsgálatok – Azure Active Directory-alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan tekintse át a csoport tagjai elérésére vagy alkalmazás-hozzáférés az Azure Active Directory hozzáférési felülvizsgálatok.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42fe972b79ecd9bcee65d0664c5d13da02d2238
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470610"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Tekintse át a hozzáférési csoportokhoz vagy alkalmazásokhoz az Azure AD hozzáférési felülvizsgálatokkal

Az Azure Active Directory (Azure AD) egyszerűbbé teszi a vállalatok csoportokhoz és alkalmazásokhoz való hozzáférés kezelése az Azure AD és más Microsoft Online Services szolgáltatás az Azure AD hozzáférési felülvizsgálatok.

Ez a cikk bemutatja, hogyan kijelölt felülvizsgálót elvégzi a hozzáférési felülvizsgálat egy csoporthoz vagy alkalmazáshoz való hozzáféréssel rendelkező felhasználók tagjai.

## <a name="open-the-access-review"></a>Nyissa meg a hozzáférési felülvizsgálatba

Hozzáférési felülvizsgálat végrehajtása első lépése, hogy keresse meg és nyissa meg a hozzáférési felülvizsgálatot.

1. Keresse meg a Microsoft, amely rákérdez, hogy tekintse át a hozzáférést egy e-mailt. Íme egy példa e-mailt, tekintse át a hozzáférés egy csoportra.

    ![E-mailek hozzáférési felülvizsgálat](./media/perform-access-review/access-review-email.png)

1. Kattintson a **felülvizsgálat indítása** nyissa meg a hozzáférési felülvizsgálat mutató hivatkozást.

Ha nem rendelkezik az e-mailt, Észreveheti, hogy a függőben lévő hozzáférési felülvizsgálatok az alábbi lépéseket.

1. Jelentkezzen be a MyApps portálról, [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![MyApps portálról](./media/perform-access-review/myapps-access-panel.png)

1. Az oldal jobb felső sarkában kattintson a felhasználó szimbólumára. Ekkor megjelenik a neve és az alapértelmezett szervezete. Ha egynél több szervezet szerepel a listán, válassza ki azt a szervezetet, amely a hozzáférési felülvizsgálatot kérte.

1. Kattintson a **hozzáférési felülvizsgálatokkal** csempére kattintva megjelenítheti a függőben lévő hozzáférési felülvizsgálatok listáját.

    A csempe nem látható, ha nincsenek végrehajtandó hozzáférési felülvizsgálatok az adott szervezetre vonatkozóan, és ilyenkor semmilyen műveletre nincs szükség.

    ![A hozzáférési felülvizsgálatok listája](./media/perform-access-review/access-reviews-list.png)

1. Kattintson a **felülvizsgálat megkezdése** hivatkozásra a hozzáférési felülvizsgálatot végrehajtására vonatkozó szándékát.

## <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

Miután megnyitotta a hozzáférési felülvizsgálatot, az adatgyűjtés kiterjed a felhasználók, akik át kell tekinteni.

Ha a kérés a saját hozzáférés felülvizsgálata, az oldal fog kinézni. További információkért lásd: [hozzáférés felülvizsgálata maga a csoportokat vagy alkalmazásokat](review-your-access.md).

![Hozzáférési felülvizsgálat végrehajtása](./media/perform-access-review/perform-access-review.png)

Két módon, hogy jóváhagyja vagy megtagadja a hozzáférést:

- Jóváhagyhatja vagy minden kérést külön-külön, megtagadása vagy
- Elfogadhatja a rendszer javaslatok, amelyek a legkönnyebb és leggyorsabb módja.

### <a name="approve-or-deny-access-for-each-request"></a>Jóváhagyja vagy megtagadja a hozzáférést az egyes kérések

1. Tekintse át a felhasználók számára a jóvá vagy utasítsa el a folyamatos hozzáférési kell-e.

1. Jóváhagyása, vagy minden egyes kérés elutasítása, kattintson a sor megnyílik egy ablak, adja meg az elvégzendő műveletet.

1. Kattintson a **jóváhagyása** vagy **megtagadása**. Ha bizonytalan, rákattinthat **nem tudjuk**. Ez a felhasználó azt a hozzáférés fenntartása azt eredményezi, de a kijelölt fog szerepelni a vizsgálati naplók.

    ![Hozzáférési felülvizsgálat végrehajtása](./media/perform-access-review/approve-deny.png)

1. Ha szükséges, adja meg az okot a **OK** mezőbe.

    A rendszergazda a hozzáférési felülvizsgálat szükség lehet, hogy Önnek kell letöltenie a folyamatos hozzáférés vagy csoporttagság jóváhagyása okát.

1. Miután megadta az elvégzendő műveletet, kattintson a **mentése**.

    Ha szeretné módosítani a választ, válassza ki a sort, és adott válasz frissítése. Például a korábban letiltott felhasználó jóváhagyása vagy egy korábban már jóváhagyott felhasználó hozzáférésének megtagadása. A válasz mindaddig, amíg a hozzáférési felülvizsgálat befejeződött bármikor módosíthatja.

    Ha több felülvizsgálók, a legutóbbi beküldött választ van rögzítve. Fontolja meg egy példa, ha a rendszergazda az két felülvizsgálók – Alice, Bob és jelöl ki. Alice először nyitja meg a hozzáférési felülvizsgálatot, és hagyja jóvá a hozzáférést. A felülvizsgálat befejezése előtt Bálint megnyitja a hozzáférési felülvizsgálatot, és megtagadja a hozzáférést. Az utolsó megtagadása a válasz az, hogy mi van rögzítve.

    > [!NOTE]
    > Ha egy felhasználótól megtagadja a hozzáférést, azonnal ezek nem törlődnek. Ha a felülvizsgálat befejeződött, vagy ha egy rendszergazda leállítja a felülvizsgálatot eltávolítja.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Hagyja jóvá vagy nem engedélyezi a hozzáférést a javaslatok alapján

Ahhoz, hogy a hozzáférési felülvizsgálatok egyszerűbben és gyorsabban az Ön számára, is biztosítunk javaslatok, amelyek elfogadhatja egyetlen kattintással. A javaslatok jönnek létre a felhasználói bejelentkezési tevékenységek alapján.

1. Kattintson a lap alján, a kék sáv **ajánlatok elfogadása**.

    ![Ajánlatok elfogadása](./media/perform-access-review/accept-recommendations.png)

    Láthatja, hogy a javasolt művelet összegzését.

    ![Összefoglaló ajánlatok elfogadása](./media/perform-access-review/accept-recommendations-summary.png)

1. Kattintson a **Ok** , fogadja el a javaslatokat.

## <a name="next-steps"></a>További lépések

- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md)
