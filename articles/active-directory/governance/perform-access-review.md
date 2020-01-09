---
title: Tekintse át a csoportokhoz való hozzáférést a hozzáférési felülvizsgálatok & alkalmazásaiban – Azure AD
description: Megtudhatja, hogyan tekintheti át a csoporttagok vagy az alkalmazások hozzáférését Azure Active Directory hozzáférési felülvizsgálatokban.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8393a1e4719137aa09233d0a87dabdf81d8ccb59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422460"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Csoportok és alkalmazások hozzáférésének áttekintése az Azure AD hozzáférési felülvizsgálatokban

A Azure Active Directory (Azure AD) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik az Azure AD-ben és más Microsoft Online Servicesben lévő csoportokhoz és alkalmazásokhoz való hozzáférést az Azure AD hozzáférési felülvizsgálatok szolgáltatásával.

Ez a cikk azt ismerteti, hogy a kijelölt véleményező hogyan hajt végre hozzáférési felülvizsgálatot egy csoport tagjai vagy egy alkalmazáshoz hozzáféréssel rendelkező felhasználók számára.

## <a name="open-the-access-review"></a>A hozzáférési felülvizsgálat megnyitása

A hozzáférési felülvizsgálat végrehajtásának első lépése a hozzáférési felülvizsgálat megkeresése és megnyitása.

1. Keresse meg a Microsoft e-mail-címét, amely arra kéri, hogy tekintse át a hozzáférést. Íme egy példa e-mailben a csoport hozzáférésének áttekintéséhez.

    ![Példa a Microsoft e-mail-címére a csoportokhoz való hozzáférés áttekintéséhez](./media/perform-access-review/access-review-email.png)

1. A hozzáférési felülvizsgálat megnyitásához kattintson a **Start Review (áttekintés** ) hivatkozásra.

Ha nem rendelkezik az e-mail-címmel, a következő lépésekkel megkeresheti a függőben lévő hozzáférési felülvizsgálatokat.

1. Jelentkezzen be a MyApps portálra a [https://myapps.microsoft.com](https://myapps.microsoft.com)címen.

    ![A MyApps-portál felsorolja azokat az alkalmazásokat, amelyekre jogosult](./media/perform-access-review/myapps-access-panel.png)

1. Az oldal jobb felső sarkában kattintson a felhasználó szimbólumára. Ekkor megjelenik a neve és az alapértelmezett szervezete. Ha egynél több szervezet szerepel a listán, válassza ki azt a szervezetet, amely a hozzáférési felülvizsgálatot kérte.

1. Kattintson a **hozzáférési felülvizsgálatok** csempére a függőben lévő hozzáférési felülvizsgálatok listájának megtekintéséhez.

    A csempe nem látható, ha nincsenek végrehajtandó hozzáférési felülvizsgálatok az adott szervezetre vonatkozóan, és ilyenkor semmilyen műveletre nincs szükség.

    ![Alkalmazások és csoportok függőben lévő hozzáférési felülvizsgálatok listája](./media/perform-access-review/access-reviews-list.png)

1. Kattintson a **megkezdés felülvizsgálati** hivatkozásra a végrehajtani kívánt hozzáférési felülvizsgálathoz.

## <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

Miután megnyitotta a hozzáférési felülvizsgálatot, megtekintheti azoknak a felhasználóknak a nevét, akiket felül kell vizsgálni.

Ha a kérést a saját hozzáférésének felülvizsgálatára kéri, a lap eltérő lesz. További információkért lásd: [hozzáférés áttekintése csoportokhoz vagy alkalmazásokhoz](review-your-access.md).

![Az áttekinteni kívánt felhasználókat felsoroló hozzáférési felülvizsgálat megnyitása](./media/perform-access-review/perform-access-review.png)

Két módon engedélyezheti vagy tilthatja le a hozzáférést:

- Egy vagy több felhasználó számára engedélyezheti vagy megtagadhatja a hozzáférést, vagy
- Elfogadhatja a rendszerre vonatkozó javaslatokat, amely a legegyszerűbb és leggyorsabb módszer.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Egy vagy több felhasználó hozzáférésének jóváhagyása vagy megtagadása

1. Tekintse át a felhasználók listáját, és döntse el, hogy jóváhagyja vagy megtagadja a folyamatos hozzáférést.

1. Egyetlen felhasználó hozzáférésének jóváhagyásához vagy megtagadásához kattintson a sorra egy ablak megnyitásához a végrehajtandó művelet megadásához. Több felhasználó hozzáférésének jóváhagyásához vagy megtagadásához vegyen fel ellenőrző jeleket a felhasználók mellett, majd kattintson az **X felhasználó ellenőrzése** gombra egy ablak megnyitásához a végrehajtandó művelet megadásához.

1. Kattintson a **jóváhagyás** vagy a **Megtagadás**gombra. Ha nem biztos benne, akkor kattintson a **nem tudom**gombra. Ez azt eredményezi, hogy a felhasználó megtartja a hozzáférést, de a kijelölt elemek megjelennek a naplókban.

    ![A jóváhagyást, megtagadást és nem ismerő beállításokat tartalmazó művelet ablaka](./media/perform-access-review/approve-deny.png)

1. Ha szükséges, adjon meg egy okot az **OK** mezőben.

    Előfordulhat, hogy a hozzáférési felülvizsgálat rendszergazdájának meg kell adnia a folyamatos hozzáférés vagy a csoporttagság jóváhagyásának okát.

1. Miután megadta a végrehajtandó műveletet, kattintson a **Mentés**gombra.

    Ha módosítani szeretné a választ, válassza ki a sort, és frissítse a választ. Jóváhagyhat például egy korábban megtagadott felhasználót, vagy megtagadhatja a korábban jóváhagyott felhasználókat. Bármikor módosíthatja a választ, amíg a hozzáférési felülvizsgálat véget nem ér.

    Ha több felülvizsgáló is van, a rendszer rögzíti a legutóbbi elküldött választ. Vegyünk például egy olyan példát, amelyben a rendszergazda két véleményezőt jelöl ki: Alice és Bob. Alice először megnyitja a hozzáférési felülvizsgálatot, és jóváhagyja a hozzáférést. A felülvizsgálat vége előtt Bob megnyitja a hozzáférési felülvizsgálatot, és megtagadja a hozzáférést. A rendszer a legutóbbi megtagadási választ rögzíti.

    > [!NOTE]
    > Ha a felhasználó hozzáférése megtagadva, a rendszer nem távolítja el azonnal. Ezeket a rendszer eltávolítja, ha a felülvizsgálat véget ért, vagy ha a rendszergazda leállítja a felülvizsgálatot.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Hozzáférés jóváhagyása vagy elutasítása javaslatok alapján

Ahhoz, hogy a hozzáférési felülvizsgálatok könnyebben és gyorsabban elérhetők legyenek, javaslatokat is biztosítunk, amelyek egyetlen kattintással elfogadhatók. A javaslatok a felhasználó bejelentkezési tevékenysége alapján jönnek létre.

1. A lap alján található kék sávban kattintson a **javaslatok elfogadása**elemre.

    ![Hozzáférés-felülvizsgálati lista megnyitása a javaslatok elfogadása gomb megjelenítéséhez](./media/perform-access-review/accept-recommendations.png)

    Ekkor megjelenik a javasolt műveletek összegzése.

    ![A javasolt műveletek összegzését megjelenítő ablak](./media/perform-access-review/accept-recommendations-summary.png)

1. A javaslatok elfogadásához kattintson **az OK** gombra.

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)
