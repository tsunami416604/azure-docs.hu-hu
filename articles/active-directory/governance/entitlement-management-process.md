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
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199928"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Kérelmek feldolgozása és e-mail-értesítések az Azure AD-jogosultságok kezelésében

Amikor egy felhasználó kérelmet küld egy hozzáférési csomagnak, a folyamat megkezdi a hozzáférési kérelem kézbesítését. Az Azure AD-jogosultságok kezelése e-mail-értesítéseket küld a jóváhagyóknak és a kérelmezőknek, ha a folyamat során fontos események történnek. Ez a cikk a kérelmek folyamatát és az elküldött e-mailes értesítéseket ismerteti.

## <a name="request-process"></a>Kérelem folyamata

Egy hozzáférési csomaghoz hozzáféréssel rendelkező felhasználónak hozzáférési kérelmet kell küldenie. A házirend konfigurációjától függően előfordulhat, hogy a kérelem jóváhagyást igényel. A kérések jóváhagyásakor a folyamat megkezdi a felhasználói hozzáférés hozzárendelését a hozzáférési csomagban lévő egyes erőforrásokhoz. Az alábbi ábrán a folyamat és a különböző állapotok áttekintése látható:

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

Ha Ön jóváhagyó, a rendszer e-mail-értesítéseket küld, amikor a hozzáférési kérést jóvá kell hagynia, és a hozzáférési kérelem befejeződik. Ha Ön a kérelmező, a rendszer e-mail-értesítéseket küld, amelyek jelzik a kérés állapotát.

Az alábbi ábrák azt mutatják be, hogy az e-mail-értesítések küldése a jóváhagyóknak vagy a kérelmezőnek. A diagramokban megjelenő e-mail-értesítések megfelelő számának megkereséséhez hivatkozzon az [e-mail értesítések táblára](entitlement-management-process.md#email-notifications-table) .

### <a name="primary-approvers-and-alternate-approvers"></a>Elsődleges jóváhagyók és másodlagos jóváhagyók
Az alábbi ábrán az elsődleges jóváhagyók és az alternatív jóváhagyók, valamint a kérési folyamat során kapott e-mail-értesítések láthatók:

![Elsődleges és másodlagos jóváhagyók folyamatának folyamatábrája](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Kérelmezők
Az alábbi ábrán a kérelmező és a kérési folyamat során kapott e-mail-értesítések láthatók:

![Kérelmező folyamatának folyamatábrája](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>E-mail értesítések táblázata
Az alábbi táblázat részletesebben ismerteti az e-mail-értesítéseket. Az e-mailek kezeléséhez használhatja a szabályokat. Az Outlookban például létrehozhat olyan szabályokat, amelyekkel áthelyezheti az e-maileket egy mappába, ha a tárgy szavakat tartalmaz a táblázatból:

| # | E-mail tárgya | Küldésekor | Címzett |
| --- | --- | --- | --- |
| 1 | Szükséges művelet: a továbbított kérelem jóváhagyása vagy megtagadása a *[date]* alapján | Ezt az e-mailt a rendszer elküldi az 1. szakaszhoz tartozó másodlagos jóváhagyóknak (miután a kérést megadták) a művelet elvégzéséhez. | 1\. fázis – másodlagos jóváhagyó |
| 2 | Szükséges művelet: a (z *) [date]* jóváhagyása vagy elutasítása. | Ezt az e-mailt a rendszer a-1. fázis elsődleges jóváhagyóinak küldi el, ha az eszkaláció le van tiltva, hogy végrehajtsa a műveletet. | 1\. fázis – elsődleges jóváhagyó |
| 3 | Emlékeztető: a kérelem jóváhagyása vagy elutasítása *[date]* a *[kérelmező]* számára | Ezt az emlékeztető e-mailt a rendszer a 2. szintű elsődleges jóváhagyóknak küldi el, ha az eszkaláció le van tiltva, és csak akkor, ha még nem hajtottak végre műveletet. | 1\. fázis – elsődleges jóváhagyó |
| 4 | A kérelem jóváhagyása vagy elutasítása *[Time]* a következő *időpontban: [date]* | Ezt az e-mailt a rendszer elküldi az 1. fázis elsődleges jóváhagyóinak (ha engedélyezve van az eszkaláció) a művelet végrehajtásához. | 1\. fázis – elsődleges jóváhagyó |
| 5 | Szükséges művelet emlékeztető: a kérelem jóváhagyása vagy elutasítása *[date]* a *[kérelmező]* számára | Ezt az emlékeztető e-mailt az 1. szintű elsődleges jóváhagyóknak küldi el, ha engedélyezve van az eszkaláció, hogy csak akkor végezze el a műveletet, ha még nem hajtottak végre műveletet. | 1\. fázis – elsődleges jóváhagyó |
| 6 | A kérelem lejárt a következőhöz: *[access_package]* | Ezt az e-mailt a rendszer a kérelem lejárta után egy egyfázisú vagy többfázisú kérelemből álló 1. fázisú elsődleges jóváhagyónak és/vagy 2. szakasznak küldi el. | 1\. fázis – elsődleges jóváhagyó, 1. fázis – másodlagos jóváhagyó |
| 7 | Kérelem jóváhagyva a következőhöz: *[kérelmező]* *[access_package]* | A rendszer ezt az e-mailt küldi el az 1. szintű elsődleges jóváhagyóknak és/vagy az 1. lépésben, a kérelem befejezésekor. | 1\. fázis – elsődleges jóváhagyó, 1. fázis – másodlagos jóváhagyó |
| 8 | Kérelem jóváhagyva a következőhöz: *[kérelmező]* *[access_package]* | Ezt az e-mailt a rendszer a kétlépcsős kérések 1. és/vagy 2. szakaszának első lépéseit, csak az 1. fázis jóváhagyásakor küldi el. | 1\. fázis – elsődleges jóváhagyó, 1. fázis – másodlagos jóváhagyó |
| 9 | Kérelem elutasítva *[access_package]* | Ez az e-mail csak akkor lesz elküldve a kérelmezőnek, ha a kérelmét megtagadják | Kérelmező |
| 10 | A kérelem lejárt a következőhöz: *[access_package]* . | Ezt az e-mailt a rendszer a kérelem lejárta után egy egyfázisú vagy többfázisú kérelem végén küldi el a kérelmezőnek. | Kérelmező |
| 18 | Most már hozzáférhet *[access_package]* | Ezt az e-mailt a rendszer elküldi a végfelhasználóknak a hozzáférésük megkezdéséhez. | Kérelmező |
| 19 | A *[access_package]* elérésének kiterjesztése a következőre: *[date]* | Ezt az e-mailt a rendszer a végfelhasználók számára a hozzáférés lejárta előtt küldi el. | Kérelmező |
| 20 | A hozzáférés befejeződött a következőhöz: *[access_package]* . | Ezt az e-mailt a rendszer a felhasználók hozzáférésének lejárta után küldi el a végfelhasználóknak. | Kérelmező |

### <a name="access-request-emails"></a>Hozzáférési kérelem e-mail-címe

Ha egy kérelmező hozzáférési kérelmet küld a jóváhagyás megkövetelésére konfigurált hozzáférési csomaghoz, a Szabályzathoz hozzáadott összes jóváhagyó e-mailben értesítést fog kapni a kérelem részleteiről. A részletek közé tartozik a kérelmező neve, szervezete, a hozzáférési kezdési és befejezési dátum (ha meg van megadva), az üzleti indoklás, a kérelem elküldésekor, valamint a kérelem érvényességének lejárta után.

Az e-mail-cím tartalmaz egy hivatkozás-jóváhagyót is, ha a hozzáférési kérelem jóváhagyásához vagy elutasításához rákattint a Myaccess elemre. Íme egy példa e-mail-értesítésre, amelyet a rendszer a jóváhagyónak küld, amikor egy kérelmező hozzáférési kérelmet küld:

![Hozzáférési kérelem jóváhagyása a csomag e-mail-címéhez](./media/entitlement-management-shared/approver-request-email.png)

Az elsődleges jóváhagyók e-mail-értesítést is küldenek egy emlékeztetővel a művelet elvégzéséhez és a kérelem eldöntéséhez. Íme egy példa az értesítés elsődleges jóváhagyói számára, hogy emlékeztesse őket a művelet elvégzésére:

![Emlékeztető hozzáférési kérelem e-mail címe](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>Másodlagos jóváhagyó kérelmének e-mail-címe

Ha a másodlagos jóváhagyóknak való továbbítás engedélyezve van, a továbbítási házirend szerint, ha a kérés még függőben van, a rendszer továbbítja a kérést. A másodlagos jóváhagyó értesítő e-mailt kap a kérelem jóváhagyásához vagy elutasításához. Itt látható egy példa e-mailben a másodlagos jóváhagyók fogadására:

![Másodlagos jóváhagyó kérelmének e-mail-címe](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Mindkét esetben az elsődleges jóváhagyó és a másodlagos jóváhagyó is jóváhagyhatja vagy megtagadhatja a kérelmet.

### <a name="approved-or-denied-emails"></a>Jóváhagyott vagy megtagadott e-mailek

A kérelmező értesítést kap, ha a hozzáférési kérelmét jóváhagyják, és elérhetők a hozzáféréshez, vagy ha a hozzáférési kérelmük megtagadva. Ha egy jóváhagyó egy kérelmező által küldött hozzáférési kérelmet kap, akkor jóváhagyhatja vagy megtagadhatja a hozzáférési kérelmet. A jóváhagyónak üzleti indoklást kell adnia döntéséhez. Az alábbi példa az elsődleges vagy másodlagos jóváhagyóknak küldött e-mailt egy kérelem jóváhagyása után:

![Hozzáférési kérelem e-mail-címének áttekintése](./media/entitlement-management-process/approver-request-email-approved.png)

Egy hozzáférési kérelem jóváhagyása és a hozzájuk való hozzáférés kiépítés esetén e-mailben értesítést küld a kérelmezőnek, hogy hozzáférjenek a hozzáférési csomaghoz. Íme egy példa e-mail-értesítésre, amelyet a rendszer a kérelmezőnek küld, amikor hozzáférést kapnak egy hozzáférési csomaghoz:

![Lejárt hozzáférési kérelem e-mail címe](./media/entitlement-management-process/requestor-email-approved.png)

Hozzáférési kérelem elutasítása esetén e-mailben értesítést küldünk a kérelmezőnek. Íme egy példa e-mail-értesítésre, amelyet a rendszer a hozzáférési kérelmének megtagadásakor küld a kérelmezőnek:

![Kérelmező kérelmének megtagadott e-mail-címe](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>Lejárt hozzáférési kérelmekre vonatkozó e-mailek

A hozzáférési kérelmek lejárnak, ha egy jóváhagyó nem hagyta jóvá vagy nem utasította el a kérelmet. 

Ha a kérelem eléri a beállított lejárati dátumát, és lejár, akkor a jóváhagyók nem hagyják jóvá vagy nem tagadhatják meg a jóváhagyást. Az alábbi példa egy, az összes elsődleges és másodlagos jóváhagyónak küldött értesítésre vonatkozó e-mailt nyújt:

 ![A jóváhagyóak lejárt hozzáférési kérelem e-mail címe](./media/entitlement-management-process/approver-request-email-expired.png)

 E-mail-értesítést is küld a kérelmezőnek, értesíti arról, hogy a hozzáférési kérelmük lejárt, és újra el kell küldenie a hozzáférési kérést. Itt látható egy példa e-mail-értesítésre, amelyet a kérelmező a hozzáférési kérelmének lejárta után küld el:

![Kérelmező lejárt hozzáférési kérelem e-mail címe](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomaghoz való hozzáférés kérése](entitlement-management-request-access.md)
- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)
