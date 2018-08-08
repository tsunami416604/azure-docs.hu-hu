---
title: Biztonsági riasztások konfigurálása |} A Microsoft Docs
description: 'Útmutató: biztonsági riasztások az Azure Privileged Identity Management bővítmény konfigurálása.'
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8df9bc7c332a83e9761ea71dddfbfbfaa3ae5154
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622153"
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Biztonsági riasztások konfigurálása az Azure AD Privileged Identity Management
## <a name="security-alerts"></a>Biztonsági riasztások
Azure Privileged Identity Management (PIM) riasztásokat állít elő, ha bármi gyanúsat vagy nem biztonságos tevékenységre van a környezetben. Riasztást vált ki, amikor megjelenik a PIM-irányítópulton. Válassza ki a riasztást a felhasználók vagy szerepkörök a riasztást kiváltó felsoroló jelentés megtekintéséhez.

![A PIM irányítópult biztonsági riasztások – képernyőkép](./media/pim-how-to-configure-security-alerts/PIM_security_dash.png)

| Riasztás | Severity | Eseményindító | Ajánlás |
| --- | --- | --- | --- |
| **PIM-en kívül folyamatban van hozzárendelve** |Magas |A felhasználó véglegesen hozzá volt rendelve egy kiemelt szerepkörű kívül a PIM-felületen. |Tekintse át a felhasználókat a listában és megszüntetése azokat az emelt szintű PIM-en kívül hozzárendelt szerepkörök. |
| **Szerepkörök aktiválása túl gyakran van folyamatban** |Közepes |Az azonos szerepkör túl sok újraaktiválás volt a beállításokban megadott időn belül. |Lépjen kapcsolatba a felhasználót, hogy miért ezek aktiválta a szerepkör Igen sokszor talál. Talán az időkorlát túl rövid a feladatok elvégzéséhez őket, vagy esetleg ezek parancsfájlokat használ automatikusan a szerepkört aktiváló. Ellenőrizze, hogy az aktiválási időtartamát, az adott szerepkör esetén értéke túl hosszú ahhoz, azok a feladatok elvégzéséhez. |
| **Szerepkörök nem többtényezős hitelesítés megkövetelése az aktiváláshoz** |Közepes |Nincsenek szerepkörök anélkül, hogy a többtényezős hitelesítés engedélyezve a beállításokban. |Hogy többtényezős hitelesítés a legtöbb magas szintű jogosultsággal rendelkező szerepkörök, de javasoljuk, hogy az MFA engedélyezése az összes szerepkör aktiválását. |
| **Felhasználó nem használja a rendszerjogosultságú szerepkörökhöz** |Alacsony |Nincsenek jogosult rendszergazdák, amely még nem aktiválta a közelmúltban szerepkörökhöz. |Határozza meg a felhasználók többé nem kell hozzáférést, a hozzáférési felülvizsgálat indítása. |
| **Nincsenek a túl sok a globális rendszergazdák** |Alacsony |Nincsenek több globális rendszergazdák, ajánlott. |Ha a globális rendszergazdák nagy számú, valószínű, hogy a felhasználók kihozhatják szükségük több engedélyt. Helyezze át a felhasználók kevesebb rendszerjogosultságú szerepkörökhöz, vagy jogosulttá némelyike helyett a szerepkörhöz tartozó állandó jelleggel hozzárendelt. |

### <a name="severity"></a>Severity
* **Magas**: azonnali beavatkozást igényel egy szabályzat megsértése miatt. 
* **Közepes**: nem igényelnek azonnali intézkedést, de lehetséges szabályzat megsértését jelzi.
* **Alacsony**: nem igényelnek azonnali intézkedést, de javasol a preferrable házirend módosítását.

## <a name="configure-security-alert-settings"></a>Biztonsági riasztási beállításainak konfigurálása
Testre szabhatja a PIM-a környezetet és biztonsági célok biztonsági riasztásokat. Kövesse az alábbi lépéseket a beállítások panel eléréséhez:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és válassza ki a **Azure AD Privileged Identity Management** csempére az irányítópulton.
2. Válassza ki **kiemelt szerepkörök felügyelt** > **beállítások** > **riasztási beállítások**.
   
    ![Biztonsági riasztások beállítások megnyitása](./media/pim-how-to-configure-security-alerts/PIM_security_settings.png)

### <a name="roles-are-being-activated-too-frequently-alert"></a>A riasztás "Szerepkörök vannak aktiválása folyamatban túl gyakran"
Ezt a riasztást aktivál, ha egy felhasználó aktiválja a azonos kiemelt szerepkörű meghatározott időn belül többször is feldolgozza. Beállíthatja, hogy az adott időszakban és az aktiválások száma is.

* **Aktiváció-megújítási időkeret**: Adja meg a nap, óra, perc és másodperc az adott időszakban segítségével nyomon követheti a gyanús megújítás szeretné.
* **Az aktiválás megújítások száma**: Adja meg az aktiválás, 2 és 100 közötti figyelembe összefügg a riasztás a kiválasztott időtartamon belül. Ez módosítható beállítását, vagy húzza a csúszkát, a szövegmezőbe írja be egy számot.

### <a name="there-are-too-many-global-administrators-alert"></a>A riasztás "There are túl sok a globális rendszergazdák"
A PIM ezt a riasztást aktivál, ha két különböző feltételek teljesülnek-e, és mindkettő konfigurálhatja. Először egy bizonyos küszöbértéket, a globális rendszergazdák eléréséhez. Bizonyos aránya az összes szerepkör-hozzárendelések második, a globális rendszergazdák kell lennie. Ha csak megfelel ezeknek a méréseknek egyik, a riasztás nem jelenik meg.  

* **A globális rendszergazdák minimális száma**: Adja meg a globális rendszergazdák, 2 és 100 közötti skálán, hogy érdemes-e egy nem biztonságos összeg számát.
* **Globális rendszergazdák aránya**: Adja meg a rendszergazdák és a globális rendszergazdák aránya, 0 %-ról 100 %-os, ez a környezetben nem biztonságos.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>A riasztás "Rendszergazdák nem rendszerjogosultságú szerepkörökhöz használja"
Ezt a riasztást aktivál, ha egy felhasználó egy adott időtartamot a szerepkör aktiválása nélkül.

* **A napok számát**: 0 – 100, egy felhasználói szerepkör aktiválása nélkül is lépjen a napok számát adja meg.

## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
