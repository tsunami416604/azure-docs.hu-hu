---
title: Kérelmek feldolgozása és e-mail-értesítések az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Ismerje meg a hozzáférési csomag kérelmezési folyamatát, valamint azt, hogy a rendszer mikor küldjön e-mail-értesítéseket Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ce3b2cb72a7b57e556e09264cb5bd421eda0f
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173757"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Kérelmek feldolgozása és e-mail-értesítések az Azure AD-jogosultságok kezelésében

Amikor egy felhasználó kérelmet küld egy hozzáférési csomagnak, a rendszer elindít egy folyamatot a kérelem kézbesítéséhez. Az Azure AD-jogosultságok kezelése az e-mailes értesítéseket is elküldi a jóváhagyóknak és a kérelmezőknek, ha a folyamat során fontos események történnek.

Ez a cikk a kérelmek folyamatát, valamint az elküldött e-mail-értesítéseket ismerteti.

## <a name="request-process"></a>Kérelem folyamata

Egy hozzáférési csomaghoz hozzáféréssel rendelkező felhasználónak hozzáférési kérelmet kell küldenie. A házirend konfigurációjától függően előfordulhat, hogy a kérelem jóváhagyást igényel. A kérések jóváhagyásakor a folyamat megkezdi a felhasználói hozzáférés hozzárendelését a hozzáférési csomagban lévő egyes erőforrásokhoz. Az alábbi ábrán a folyamat és a különböző állapotok áttekintése látható.

![Jóváhagyási folyamat diagramja](./media/entitlement-management-process/request-process.png)

| Állami | Leírás |
| --- | --- |
| Beküldött | A felhasználó kérelmet küld. |
| Jóváhagyás függőben | Ha egy hozzáférési csomagra vonatkozó házirend jóváhagyást igényel, a kérés függőben lévő jóváhagyásra kerül. |
| Lejárt | Ha egyetlen jóváhagyó sem hagyja jóvá a kérést a jóváhagyási kérelem időkorlátján belül, a kérelem lejár. Ha újra próbálkozik, a felhasználónak újra el kell küldenie a kérést. |
| Megtagadva | A jóváhagyó megtagadja a kérelmet. |
| Approved | A jóváhagyó jóváhagyja a kérelmet. |
| Szállít | A felhasználó **nem** rendelt hozzá hozzáférést a hozzáférési csomag összes erőforrásához. Ha ez egy külső felhasználó, akkor előfordulhat, hogy a felhasználó még nem fér hozzá az erőforrás-címtárhoz, és elfogadta a hozzájárulási kérést. |
| Kézbesítve | A felhasználó hozzá lett rendelve a hozzáférési csomag összes erőforrásához. |
| Hozzáférés kiterjesztve | Ha a házirendben engedélyezve vannak a bővítmények, a felhasználó kibővítette a hozzárendelést. |
| A hozzáférés lejárt | A felhasználó hozzáférési csomaghoz való hozzáférése lejárt. Ha újra szeretné elérni a hozzáférést, a felhasználónak el kell küldenie egy kérést. |

## <a name="email-notifications"></a>E-mail-értesítések

Ha Ön jóváhagyó, a rendszer e-mail-értesítéseket küld, amikor a hozzáférési kérést jóvá kell hagynia, és a hozzáférési kérelem befejeződik. Ha Ön a kérelmező, a rendszer e-mail-értesítéseket küld, amelyek jelzik a kérés állapotát. Az alábbi ábra az e-mail-értesítések elküldésének időpontját mutatja be.

![Jogosultsági felügyeleti e-mail folyamat](./media/entitlement-management-process/email-notifications.png)

Az alábbi táblázat részletesebben ismerteti az e-mail-értesítéseket.

| # | E-mail tárgya | Küldésekor | Címzett |
| --- | --- | --- | --- |
| 1 | Szükséges művelet: Tekintse át a [ *kérelmező]* hozzáférési kérelmét a [ *hozzáférési csomag* ] [ *date]* értékkel. | Ha egy kérelmező hozzáférési csomagra vonatkozó kérelmet küld | Minden jóváhagyó |
| 2 | Szükséges művelet: Tekintse át a [ *kérelmező]* hozzáférési kérelmét a [ *hozzáférési csomag* ] [ *date]* értékkel. | A jóváhagyási kérelem időtúllépését megelőző X nap | Minden jóváhagyó |
| 3 | Állapotjelző értesítés: *[kérelmező]* hozzáférési kérelme *[hozzáférési csomag]* lejárt | Ha a jóváhagyók nem hagyják jóvá vagy nem tagadják meg a kérés időtartamán belüli hozzáférési kérelmet | Kérelmező |
| 4 | Állapotjelentés-értesítés: *[kérelmező]* a (z) *[hozzáférési csomag]* hozzáférési kérelme befejeződött | Ha az első jóváhagyó jóváhagyja vagy megtagadja a hozzáférési kérelmet | Minden jóváhagyó |
| 5 | *[Hozzáférési csomag]* hozzáférése megtagadva | Ha egy kérelmező megtagadta a hozzáférést a hozzáférési csomaghoz | Kérelmező |
| 6 | Most már hozzáférhet *[hozzáférési csomag]*  | Ha a kérelmező hozzáférést kapott a hozzáférési csomag összes erőforrásához | Kérelmező |
| 7 | A *[hozzáférési csomag]* érvényessége X nap múlva lejár | A kérelmezőnek a hozzáférési csomaghoz való hozzáférését megelőző X nap lejár | Kérelmező |
| 8 | A *[hozzáférési csomag]* elérése lejárt | Ha a kérelmező hozzáférési csomaghoz való hozzáférése lejár | Kérelmező |

### <a name="access-request-emails"></a>Hozzáférési kérelem e-mail-címe

Ha egy kérelmező hozzáférési kérelmet küld egy olyan hozzáférési csomaghoz, amely a jóváhagyás megkövetelésére van konfigurálva, a szabályzatban konfigurált összes jóváhagyó e-mailben értesítést kap a kérelem részleteiről. A részletek közé tartozik a kérelmező neve, szervezete, hozzáférés kezdő és befejező dátuma, ha meg van biztosítva, az üzleti indoklás, a kérelem elküldésekor, valamint a kérelem érvényességének lejárta után. Az e-mail tartalmaz egy hivatkozást, amelyben a jóváhagyók jóváhagyják vagy megtagadhatják a hozzáférési kérést. Íme egy példa e-mail-értesítésre, amelyet a rendszer a jóváhagyónak küld, amikor egy kérelmező hozzáférési kérelmet küld.

![Hozzáférési kérelem e-mail-címének áttekintése](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Jóváhagyott vagy megtagadott e-mailek

A kérelmező értesítést kap, ha a hozzáférési kérelmét jóváhagyják, és elérhetők a hozzáféréshez, vagy ha a hozzáférési kérelmük megtagadva. Ha egy jóváhagyó egy kérelmező által küldött hozzáférési kérelmet kap, akkor jóváhagyhatja vagy megtagadhatja a hozzáférési kérelmet. A jóváhagyónak üzleti indoklást kell adnia döntéséhez.

Hozzáférési kérelem jóváhagyása esetén a jogosultságok kezelése elindítja a kérelmező hozzáférésének megadását a hozzáférési csomag egyes erőforrásaihoz. Miután a kérelmező hozzáférést kapott a hozzáférési csomag összes erőforrásához, a rendszer e-mailben értesítést küld a kérelmezőnek, hogy a hozzáférési kérelmét jóváhagyták, és hogy most már hozzáférnek a hozzáférési csomaghoz. Íme egy példa e-mail-értesítésre, amelyet a rendszer a kérelmezőnek küld, amikor hozzáférést kapnak egy hozzáférési csomaghoz.

Hozzáférési kérelem elutasítása esetén e-mailben értesítést küldünk a kérelmezőnek. Íme egy példa e-mail-értesítésre, amelyet a rendszer elküld egy kérelmezőnek a hozzáférési kérelmük megtagadásakor.

### <a name="expired-access-request-emails"></a>Lejárt hozzáférési kérelmekre vonatkozó e-mailek

A kérelmező értesítést kap, ha a hozzáférési kérelem lejárt. Ha egy kérelmező hozzáférési kérést küld, a kérelem időtartama lejár. Ha nincsenek jóváhagyó/megtagadási döntést küldő jóváhagyók, a kérelem továbbra is függőben lévő jóváhagyási állapotban marad. Ha a kérelem eléri a beállított lejárati időtartamot, a kérelem lejár, és a jóváhagyók nem tudják jóváhagyni vagy megtagadni a kérést. Ebben az esetben a kérelem lejárt állapotba kerül. Egy lejárt kérelem már nem hagyható jóvá vagy nem tiltható le. A kérelmező e-mailben értesítést küld arról, hogy a hozzáférési kérelmük lejárt, és újra el kell küldenie a hozzáférési kérelmet. Itt látható egy példa e-mail-értesítésre, amelyet a kérelmező a hozzáférési kérelmük lejárta után küld el.

![Lejárt hozzáférési kérelem e-mail címe](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomaghoz való hozzáférés kérése](entitlement-management-request-access.md)
- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)
