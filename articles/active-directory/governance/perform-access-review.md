---
title: Csoportokhoz való hozzáférés áttekintése & alkalmazások hozzáférési felülvizsgálatokban – Azure AD
description: Megtudhatja, hogyan tekintheti meg a csoporttagok vagy az alkalmazások hozzáférésének az Azure Active Directory-hozzáférési felülvizsgálatok.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128451"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Csoportokhoz és alkalmazásokhoz való hozzáférés áttekintése az Azure AD-hozzáférés-felülvizsgálatokban

Az Azure Active Directory (Azure AD) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik a csoportokhoz és alkalmazásokhoz való hozzáférést az Azure AD-ben és más Microsoft Online Services-ben az Azure AD-hozzáférés-felülvizsgálatok nevű funkcióval.

Ez a cikk azt ismerteti, hogy egy kijelölt véleményező hogyan végez hozzáférési felülvizsgálatot egy csoport vagy egy alkalmazáshoz hozzáféréssel rendelkező felhasználók számára.

## <a name="open-the-access-review"></a>A hozzáférési felülvizsgálat megnyitása

A hozzáférés-felülvizsgálat végrehajtásának első lépése a hozzáférési felülvizsgálat megkeresése és megnyitása.

1. Keressen egy e-mailt a Microsofttól, amely a hozzáférés áttekintését kéri. Íme egy példa e-mail, amely egy csoport hozzáférését tekinti át.

    ![Példa a Microsoft e-mail-címre a csoporthoz való hozzáférés áttekintéséhez](./media/perform-access-review/access-review-email.png)

1. Kattintson a **Véleményezés indítása** hivatkozásra a hozzáférési ellenőrzés megnyitásához.

Ha nem rendelkezik az e-maillel, az alábbi lépések végrehajtásával megtalálhatja a függőben lévő hozzáférési ellenőrzéseket.

1. Jelentkezzen be a MyApps [https://myapps.microsoft.com](https://myapps.microsoft.com)portálra a .

    ![A MyApps portál felsorolja azokat az alkalmazásokat, amelyekhez engedéllyel rendelkezik](./media/perform-access-review/myapps-access-panel.png)

1. Az oldal jobb felső sarkában kattintson a felhasználó szimbólumára. Ekkor megjelenik a neve és az alapértelmezett szervezete. Ha egynél több szervezet szerepel a listán, válassza ki azt a szervezetet, amely a hozzáférési felülvizsgálatot kérte.

1. Kattintson az **Access-ellenőrzések** csempére a függőben lévő hozzáférési ellenőrzések listájának megtekintéséhez.

    A csempe nem látható, ha nincsenek végrehajtandó hozzáférési felülvizsgálatok az adott szervezetre vonatkozóan, és ilyenkor semmilyen műveletre nincs szükség.

    ![Függőben lévő hozzáférés-felülvizsgálatok listája alkalmazásokhoz és csoportokhoz](./media/perform-access-review/access-reviews-list.png)

1. Kattintson a végrehajtani kívánt hozzáférés-ellenőrzés **véleményezésének áttekintése** hivatkozásra.

## <a name="perform-the-access-review"></a>A hozzáférés-ellenőrzés végrehajtása

Miután megnyitotta a hozzáférési felülvizsgálatot, láthatja azoknak a felhasználóknak a nevét, akiket felül kell vizsgálni.

Ha a kérés a saját hozzáférésének áttekintése, az oldal máshogy fog kinézni. További információt a [Hozzáférés áttekintése saját maga csoportokhoz vagy alkalmazásokhoz című témakörben talál.](review-your-access.md)

![Nyílt hozzáférésű felülvizsgálat, amely felsorolja azát a felhasználókat, akiket felül kell vizsgálni](./media/perform-access-review/perform-access-review.png)

Kétféleképpen hagyhatja jóvá vagy tagadhatja meg a hozzáférést:

- Jóváhagyhatja vagy megtagadhatja a hozzáférést egy vagy több felhasználó számára, vagy
- Elfogadhatja a rendszerjavaslatokat, ami a legegyszerűbb és leggyorsabb módja.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Hozzáférés jóváhagyása vagy megtagadása egy vagy több felhasználó számára

1. Tekintse át a felhasználók listáját, és döntse el, hogy jóváhagyja vagy elutasítja a folyamatos hozzáférést.

1. Egyetlen felhasználó hozzáférésének jóváhagyásához vagy megtagadásához kattintson a sorra egy ablak megnyitásához a végrehajtandó művelet megadásához. Több felhasználó hozzáférésének jóváhagyásához vagy megtagadásához jelölje be a felhasználókat, majd kattintson az **X felhasználó(k) áttekintése** gombra az ablak megnyitásához a végrehajtandó művelet megadásához.

1. Kattintson **a Jóváhagyás** vagy **a Megtagadás gombra.** Ha nem biztos benne, kattintson **a Nem tudom**gombra. Ezzel a felhasználó megőrizheti hozzáférését, de a kijelölés megjelenik a naplónaplókban.

    ![A Jóváhagyás, a Megtagadás és a Nem ismeret beállítási lehetőségeket tartalmazó műveletablak](./media/perform-access-review/approve-deny.png)

1. Ha szükséges, írjon be egy okot az **Ok** mezőbe.

    Előfordulhat, hogy a hozzáférés-felülvizsgálat rendszergazdája megkövetelheti, hogy adja meg a folyamatos hozzáférés vagy a csoporttagság jóváhagyásának okát.

1. Miután megadta a végrehajtandó műveletet, kattintson a **Mentés gombra.**

    Ha módosítani szeretné a választ, jelölje ki a sort, és frissítse a választ. Jóváhagyhat például egy korábban elutasított felhasználót, vagy megtagadhat egy korábban jóváhagyott felhasználót. A válasz bármikor módosítható, amíg a hozzáférési felülvizsgálat véget nem ér.

    Ha több véleményező van, a felvétel az utolsó elküldött választ rögzíti. Vegyünk egy példát, amikor a rendszergazda két véleményezőt jelöl ki – Alice és Bob. Alice először megnyitja a hozzáférési felülvizsgálatot, és jóváhagyja a hozzáférést. Az ellenőrzés befejezése előtt Bob megnyitja a hozzáférési felülvizsgálatot, és megtagadja a hozzáférést. Az utolsó megtagadási válasz az, ami rögzítésre kerül.

    > [!NOTE]
    > Ha egy felhasználó nak megtagadja a hozzáférést, a rendszer nem távolítja el azonnal. A rendszer eltávolítja őket, ha az ellenőrzés véget ért, vagy amikor a rendszergazda leállítja az értékelést.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Hozzáférés jóváhagyása vagy megtagadása javaslatok alapján

Annak érdekében, hogy a hozzáférési felülvizsgálatok egyszerűbbek és gyorsabbak legyenek az Ön számára, olyan javaslatokat is kínálunk, amelyeket egyetlen kattintással elfogad. A javaslatok a felhasználó bejelentkezési tevékenysége alapján jönnek létre.

1. A lap alján lévő kék sávon kattintson a **Javaslatok elfogadása gombra.**

    ![Nyílt hozzáférés-felülvizsgálati lista a Javaslatok elfogadása gombbal](./media/perform-access-review/accept-recommendations.png)

    Megjelenik az ajánlott műveletek összegzése.

    ![Az ajánlott műveletek összegzését megjelenítő ablak](./media/perform-access-review/accept-recommendations-summary.png)

1. A javaslatok elfogadásához kattintson az **Ok** gombra.

## <a name="next-steps"></a>További lépések

- [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése](complete-access-review.md)
