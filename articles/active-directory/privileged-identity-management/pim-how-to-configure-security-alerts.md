---
title: Az Azure AD-címtárbeli szerepkörökhöz tartozó biztonsági riasztások konfigurálása a PIM |} A Microsoft Docs
description: Ismerje meg az Azure AD-címtárbeli szerepkörökhöz tartozó biztonsági riasztások konfigurálása az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/04/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2cb78d048559a011756362b58848c5e91ca40dd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172382"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Az Azure AD-címtárbeli szerepkörökhöz tartozó biztonsági riasztások konfigurálása az PIM-ben

Az Azure AD Privileged Identity Management (PIM) riasztásokat állít elő, ha bármi gyanúsat vagy nem biztonságos tevékenységre van a környezetben. Riasztást vált ki, amikor megjelenik a PIM-irányítópulton. Válassza ki a riasztást a felhasználók vagy szerepkörök a riasztást kiváltó felsoroló jelentés megtekintéséhez.

![A PIM biztonsági riasztások – képernyőkép](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Biztonsági riasztások

Ez a szakasz felsorolja az összes biztonsági riasztás címtárszerepkörökkel, hogyan háríthatja el a mellett, hogy hogyan. Súlyosság a következő jelentése van:

* **Magas**: Egy szabályzat megsértése miatt azonnali beavatkozást igényel.
* **Közepes**: Nem igényel azonnali intézkedést, de lehetséges szabályzat megsértését jelzi.
* **Alacsony**: Nem igényel azonnali intézkedést, de javasol a előnyösebb házirend módosítását.

### <a name="administrators-arent-using-their-privileged-roles"></a>A rendszergazdák nem rendszerjogosultságú szerepkörökhöz használja.

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért jelenik meg ez a riasztás?** | Nincs szükségük a kiemelt szerepkörökhöz rendelt felhasználók növeli a támadások esélyét. Emellett akkor is egyszerűbb a támadók számára a fiókok, amelyek aktívan nem használt maradna maradnak. |
| **Hogyan lehet kijavítani?** | Tekintse át a felhasználókat a listában, és azokat eltávolítsa a kiemelt szerepkörökhöz, nincs szükség. |
| **Megelőzési** | Kiemelt szerepkörök csak hozzárendelése egy üzleti indoklás rendelkező felhasználók. </br>Ütemezheti rendszeres [hozzáférési felülvizsgálatokkal](pim-how-to-start-security-review.md) , ellenőrizze, hogy a felhasználók továbbra is kell-e a hozzáférésüket. |
| **A portálon kockázatcsökkentési művelet** | A kiemelt szerepkörű eltávolítja a fiókot. |
| **Eseményindító** | Akkor aktiválódik, ha egy felhasználó egy adott időtartamot a szerepkör aktiválása nélkül. |
| **Napok száma** | Ezzel a beállítással, 0 és 100 közötti, amely a felhasználó megnyithatja a szerepkör aktiválása nélküli napok száma.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Szerepkörök nem többtényezős hitelesítés megkövetelése az aktiváláshoz

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért jelenik meg ez a riasztás?** | MFA, nélkül feltört felhasználói aktiválhatja a kiemelt szerepkörökhöz. |
| **Hogyan lehet kijavítani?** | Tekintse át a szerepkörök és [többtényezős hitelesítés](pim-how-to-change-default-settings.md) minden szerepkörhöz. |
| **Megelőzési** | [Többtényezős hitelesítés](pim-how-to-change-default-settings.md) minden szerepkörhöz.  |
| **A portálon kockázatcsökkentési művelet** | Lehetővé teszi az emelt szintű szerepkör aktiválása szükséges MFA. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>A bérlő nem rendelkezik Azure AD Premium P2

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért jelenik meg ez a riasztás?** | Az aktuális bérlő nem rendelkezik Azure AD Premium P2. |
| **Hogyan lehet kijavítani?** | Tekintse át a következőket [Azure AD-verziók](../fundamentals/active-directory-whatis.md). Frissítés az Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>A kiemelt szerepkörű lehetséges elavult fiókok

| | |
| --- | --- |
| **Súlyosság** | Közepes |
| **Miért jelenik meg ez a riasztás?** | Számlák a kiemelt szerepkörű az utolsó 90 napban nem módosított jelszó. Ezek a fiókok lehet szolgáltatás, vagy a megosztott fiókok nem alatt marad, és amelyek sebezhetővé. |
| **Hogyan lehet kijavítani?** | Tekintse át a fiókok a listában. Ha már nincs szükségük hozzáférést, távolítsa el őket a rendszerjogosultságú szerepkörökhöz. |
| **Megelőzési** | Győződjön meg arról, hogy a felhasználók, amelyekről tudja, hogy a jelszó megváltozása esetén, amelyek a megosztott fiókok vannak Elforgatás erős jelszavakat. </br>Rendszeresen tekintse át a fiókok a kiemelt szerepkörök használatával [hozzáférési felülvizsgálatokkal](pim-how-to-start-security-review.md) és már nem szükséges szerepkör-hozzárendelések eltávolítása. |
| **A portálon kockázatcsökkentési művelet** | A kiemelt szerepkörű eltávolítja a fiókot. |
| **Ajánlott eljárások** | Megosztott, szolgáltatás, és a jelszó használatával hitelesíteni, és magas jogosultsági szintű felügyeleti szerepkörökhöz, például a globális rendszergazdai vagy biztonsági rendszergazda hozzárendelt vészelérési fiókok rendelkeznie kell a jelszavukat, az alábbi esetekben elforgatott:<ul><li>Egy biztonsági incidens való visszaélés vagy a felügyeleti hozzáférési jogosultságok biztonsági sérülése után</li><li>Miután bármely felhasználó jogosultságait változnak, hogy azok nem lesznek egy rendszergazda (például után egy rendszergazda hagyja informatikai vagy a szervezetből kilépő alkalmazott)</li><li>Rendszeres időközönként (például negyedévente vagy évente), akkor is, ha nem volt ismert illetéktelen behatolás vagy módosítás informatikai volt szükség létszámnövelésre</li></ul>Több felhasználó rendelkezik hozzáféréssel a fiókok hitelesítő adatait, mivel annak érdekében, hogy a kijelölt személyek van hátra a szerepkörökhöz már nem tud hozzáférni a fiókok hitelesítő adatait kell elforgatva. [További információ](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>PIM-en kívül folyamatban van hozzárendelve

| | |
| --- | --- |
| **Súlyosság** | Magas |
| **Miért jelenik meg ez a riasztás?** | Rendszerjogosultságú szerepkör-hozzárendelést a PIM-en kívül nem megfelelően figyeli, és aktív támadás utalhat. |
| **Hogyan lehet kijavítani?** | Tekintse át a felhasználókat a listában, és azokat eltávolítsa a kiemelt szerepkörökhöz rendelt PIM-en kívül. |
| **Megelőzési** | Vizsgálja meg, ahol felhasználók hozzárendeli a kiemelt szerepkörökhöz PIM-en kívül, és onnan jövőbeli hozzárendelések letiltása. |
| **A portálon kockázatcsökkentési művelet** | A kiemelt szerepkörű eltávolítja a fiókot. |

### <a name="there-are-too-many-global-administrators"></a>Nincsenek a túl sok a globális rendszergazdák

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért jelenik meg ez a riasztás?** | Globális rendszergazda a legmagasabb szintű rendszerjogosultságú szerepkört. Ha megsérül egy globális rendszergazdai, a támadó hozzáfér az összes engedélyeiket, mely a a teljes rendszer veszélyben. |
| **Hogyan lehet kijavítani?** | Tekintse át a felhasználók a listán, és távolítsa el azokat, amelyek nem feltétlenül kell a globális rendszergazdai szerepkörrel. </br>Ezek a felhasználók alacsonyabb kiemelt szerepkörök hozzárendelése. |
| **Megelőzési** | A szükséges legalacsonyabb jogosultsági szintű szerepkört rendelje hozzá a felhasználók. |
| **A portálon kockázatcsökkentési művelet** | A kiemelt szerepkörű eltávolítja a fiókot. |
| **Eseményindító** | Akkor aktiválódik, ha két különböző feltételek teljesülnek-e, és mindkettő konfigurálhatja. Először egy bizonyos küszöbértéket, a globális rendszergazdák eléréséhez. Bizonyos aránya az összes szerepkör-hozzárendelések második, a globális rendszergazdák kell lennie. Ha csak megfelel ezeknek a méréseknek egyik, a riasztás nem jelenik meg. |
| **A globális rendszergazdák minimális száma** | Ezzel a beállítással a globális rendszergazdák száma 2 és 100 közötti skálán, hogy érdemes-e nem biztonságos összeget. |
| **Globális rendszergazdák aránya** | Ezzel a beállítással a rendszergazdák, a 0 % 100 %-a környezetben nem biztonságos, globális rendszergazdák, akik minimális aránya. |

### <a name="roles-are-being-activated-too-frequently"></a>Szerepkörök aktiválása túl gyakran van folyamatban

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért jelenik meg ez a riasztás?** | Több aktiválások ugyanaz a felhasználó által kiemelt ugyanarra a szerepkörre egy bejelentkezési támadások. |
| **Hogyan lehet kijavítani?** | Tekintse át a felhasználókat a listában, és ellenőrizze, hogy a [aktiválási időtartamát](pim-how-to-change-default-settings.md) esetében a kiemelt szerepkörű van beállítva, amíg a feladatok elvégzéséhez őket. |
| **Megelőzési** | Ügyeljen arra, hogy a [aktiválási időtartamát](pim-how-to-change-default-settings.md) a kiemelt szerepkörökhöz tartozó értéke túl hosszú ahhoz, a felhasználók számára a feladatok elvégzéséhez.</br>[Többtényezős hitelesítés](pim-how-to-change-default-settings.md) a kiemelt szerepkörökhöz, amelyek több rendszergazda által megosztott fiókok számára. |
| **A portálon kockázatcsökkentési művelet** | – |
| **Eseményindító** | Akkor aktiválódik, ha egy felhasználó aktiválja a azonos kiemelt szerepkörű meghatározott időn belül többször is feldolgozza. Beállíthatja, hogy az adott időszakban és az aktiválások száma is. |
| **Aktiváció-megújítási időkeret** | Ezzel a beállítással a nap, óra, perc, és a második az adott időszakban használni kívánt gyanús megújítás nyomon követéséhez. |
| **Az aktiválás megújítások száma** | Ezzel a beállítással az aktiválás, 2 és 100 közötti figyelembe összefügg a riasztás a kiválasztott időtartamon belül a számát. Ez módosítható beállítását, vagy húzza a csúszkát, a szövegmezőbe írja be egy számot. |

## <a name="configure-security-alert-settings"></a>Biztonsági riasztási beállításainak konfigurálása

Testre szabhatja a PIM-a környezetet és biztonsági célok biztonsági riasztásokat. Nyissa meg a biztonsági riasztások beállításai az alábbi lépéseket követve:

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **beállítások** , majd **riasztások**.

    ![Biztonsági riasztások beállítások megnyitása](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Kattintson a riasztás nevét. a riasztás beállításainak konfigurálása.

    ![Biztonsági riasztások beállításai](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD directory szerepkör-beállítások konfigurálása](pim-how-to-change-default-settings.md)
