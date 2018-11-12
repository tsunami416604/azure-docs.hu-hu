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
ms.component: pim
ms.date: 11/01/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e7204c223681b9a33c439b0d9fc653167422384a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011697"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Az Azure AD-címtárbeli szerepkörökhöz tartozó biztonsági riasztások konfigurálása az PIM-ben

Az Azure AD Privileged Identity Management (PIM) riasztásokat állít elő, ha bármi gyanúsat vagy nem biztonságos tevékenységre van a környezetben. Riasztást vált ki, amikor megjelenik a PIM-irányítópulton. Válassza ki a riasztást a felhasználók vagy szerepkörök a riasztást kiváltó felsoroló jelentés megtekintéséhez.

![A PIM biztonsági riasztások – képernyőkép](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Biztonsági riasztások

Ez a szakasz felsorolja az összes biztonsági riasztás címtárszerepkörökkel, hogyan háríthatja el a mellett, hogy hogyan. Súlyosság a következő jelentése van:

* **Magas**: azonnali beavatkozást igényel egy szabályzat megsértése miatt.
* **Közepes**: nem igényelnek azonnali intézkedést, de lehetséges szabályzat megsértését jelzi.
* **Alacsony**: nem igényelnek azonnali intézkedést, de javasol a előnyösebb házirend módosítását.

### <a name="roles-are-being-assigned-outside-of-pim"></a>PIM-en kívül folyamatban van hozzárendelve

| | |
| --- | --- |
| **Súlyosság** | Magas |
| **Miért jelenik meg ez a riasztás?** | Rendszerjogosultságú szerepkör-hozzárendelést a PIM-en kívül nem megfelelően figyeli, és aktív támadás utalhat. |
| **Hogyan lehet kijavítani?** | Tekintse át a felhasználókat a listában, és azokat eltávolítsa a kiemelt szerepkörökhöz rendelt PIM-en kívül. |
| **Megelőzési** | Vizsgálja meg, ahol felhasználók hozzárendeli a kiemelt szerepkörökhöz PIM-en kívül, és onnan jövőbeli hozzárendelések letiltása. |
| **A portálon kockázatcsökkentési művelet** | A kiemelt szerepkörű eltávolítja a fiókot. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>A kiemelt szerepkörű lehetséges elavult fiókok

| | |
| --- | --- |
| **Súlyosság** | Közepes |
| **Miért jelenik meg ez a riasztás?** | Fiókok, amelyek nem változtak meg a jelszavát nemrég lehet szolgáltatás vagy a megosztott fiókok, amelyek nem tart. Ezek a fiókok, a kiemelt szerepkörű ki téve a támadók számára. |
| **Hogyan lehet kijavítani?** | Tekintse át a fiókok a listában. Ha már nincs szükségük hozzáférést, távolítsa el őket a rendszerjogosultságú szerepkörökhöz. |
| **Megelőzési** | Győződjön meg arról, hogy a felhasználók, amelyekről tudja, hogy a jelszó megváltozása esetén, amelyek a megosztott fiókok vannak Elforgatás erős jelszavakat. </br>Rendszeresen tekintse át a fiókok a hozzáférési felülvizsgálatok segítségével a kiemelt szerepkörökhöz, és már nem szükséges szerepkör-hozzárendelések eltávolítása. |
| **A portálon kockázatcsökkentési művelet** | A kiemelt szerepkörű eltávolítja a fiókot. |

### <a name="users-arent-using-their-privileged-roles"></a>Felhasználó nem használja a rendszerjogosultságú szerepkörökhöz

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért jelenik meg ez a riasztás?** | Nincs szükségük a kiemelt szerepkörökhöz rendelt felhasználók növeli a támadások esélyét. Emellett akkor is egyszerűbb a támadók számára a fiókok, amelyek aktívan nem használt maradna maradnak. |
| **Hogyan lehet kijavítani?** | Tekintse át a felhasználókat a listában, és azokat eltávolítsa a kiemelt szerepkörökhöz, nincs szükség. |
| **Megelőzési** | Kiemelt szerepkörök csak hozzárendelése egy üzleti indoklás rendelkező felhasználók. </br>Ütemezés rendszeres hozzáférési felülvizsgálatok győződjön meg arról, hogy a felhasználók még a hozzáférésre van szükségük. |
| **A portálon kockázatcsökkentési művelet** | A kiemelt szerepkörű eltávolítja a fiókot. |
| **Eseményindító** | Akkor aktiválódik, ha egy felhasználó egy adott időtartamot a szerepkör aktiválása nélkül. |
| **Napok száma** | Ezzel a beállítással, 0 és 100 közötti, amely a felhasználó megnyithatja a szerepkör aktiválása nélküli napok száma.|

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

### <a name="roles-dont-require-mfa-for-activation"></a>Szerepkörök nem igénylik a többtényezős hitelesítés az aktiváláshoz

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért jelenik meg ez a riasztás?** | MFA, nélkül feltört felhasználói aktiválhatja a kiemelt szerepkörökhöz. |
| **Hogyan lehet kijavítani?** | Tekintse át a szerepkörök és [többtényezős hitelesítés](pim-how-to-change-default-settings.md) minden szerepkörhöz. |
| **Megelőzési** | [Többtényezős hitelesítés](pim-how-to-change-default-settings.md) minden szerepkörhöz.  |
| **A portálon kockázatcsökkentési művelet** | Lehetővé teszi az emelt szintű szerepkör aktiválása szükséges MFA. |

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
