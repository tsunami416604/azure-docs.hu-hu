---
title: Folyamatkérés & értesítések – Azure AD-jogosultságok kezelése
description: Ismerje meg a hozzáférési csomag kérelmezési folyamatát, és hogy mikor küldi el az e-mail értesítéseket az Azure Active Directory jogosultságkezelésében.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128524"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Folyamat- és e-mail-értesítések kérése az Azure AD-jogosultságok kezelésében

Amikor egy felhasználó kérelmet küld egy hozzáférési csomaghoz, egy folyamat megkezdi a hozzáférési kérelem kézbesítését. Az Azure AD jogosultságkezelés e-mail értesítéseket küld a jóváhagyóknak és a kérelmezőknek, ha a folyamat során kulcsfontosságú események történnek. Ez a cikk ismerteti a kérelem folyamatát, és az e-mail értesítéseket, amelyek et küldött.

## <a name="request-process"></a>Folyamat kérése

Egy hozzáférési csomaghoz hozzáférést igénylő felhasználó hozzáférési kérelmet küldhet. A házirend konfigurációjától függően a kérelem jóváhagyásra szorulhat. A kérelem jóváhagyásakor egy folyamat megkezdi a felhasználói hozzáférés hozzárendelését a hozzáférési csomag minden egyes erőforrásához. Az alábbi ábra áttekintést nyújt a folyamatról és a különböző állapotokról:

![Jóváhagyási folyamat diagramja](./media/entitlement-management-process/request-process.png)

| Állapot | Leírás |
| --- | --- |
| Submitted (Elküldve) | A felhasználó kérelmet küld. |
| Jóváhagyásra vár | Ha egy hozzáférési csomag házirendje jóváhagyást igényel, a kérelem függőben lévő jóváhagyásra kerül. |
| Lejárt | Ha egyetlen jóváhagyó sem hagyja jóvá a kérelmet a jóváhagyási kérelem időeltein belül, a kérelem lejár. Az ismételt próbálkozáshoz a felhasználónak újra el kell küldenie a kérelmet. |
| Megtagadva | A jóváhagyó visszautasít ja a kérést. |
| Approved | A jóváhagyó jóváhagyja a kérelmet. |
| Szállít | A felhasználó **nem** kapott hozzáférést a hozzáférési csomag összes erőforrásához. Ha ez egy külső felhasználó, a felhasználó még nem fért hozzá az erőforráskönyvtárhoz. Előfordulhat, hogy nem fogadták el a hozzájárulási kérést sem. |
| Kézbesítve | A felhasználó nak hozzáférése van a hozzáférési csomag összes erőforrásához. |
| Hozzáférés bővített | Ha a házirendben engedélyezett a bővítmény, a felhasználó meghosszabbította a hozzárendelést. |
| A hozzáférés lejárt | A felhasználó hozzáférése a hozzáférési csomaghoz lejárt. Ahhoz, hogy újra hozzáférjen, a felhasználónak kérelmet kell benyújtania. |

## <a name="email-notifications"></a>E-mail-értesítések

Ha Ön jóváhagyó, e-mail-értesítéseket kap, amikor jóvá kell hagynia egy hozzáférési kérelmet. A hozzáférési kérelem befejezésekor is kap értesítéseket. E-mailes értesítéseket is küldünk, amelyek jelzik a kérés állapotát, ha Ön kérelmező.

Az alábbi ábrák azt mutatják, hogy mikor küldi el ezeket az e-mail értesítéseket a jóváhagyóknak vagy a kérelmezőnek. Az [e-mail értesítések táblázatra](entitlement-management-process.md#email-notifications-table) hivatkozva keresse meg a megfelelő számot a diagramokban megjelenő e-mail értesítésekhez.

### <a name="first-approvers-and-alternate-approvers"></a>Első jóváhagyók és alternatív jóváhagyók
Az alábbi ábra az első jóváhagyók és alternatív jóváhagyók tapasztalatait, valamint a kérési folyamat során kapott e-mail értesítéseket mutatja be:

![Első és alternatív jóváhagyók folyamatfolyamata](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Kérelmezők
Az alábbi ábra a kérelmezők tapasztalatait és a kérési folyamat során kapott e-mail értesítéseket mutatja be:

![A kérelmező folyamatfolyamata](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Kétlépcsős jóváhagyás
Az alábbi ábra az 1.

![Kétlépcsős jóváhagyási folyamat](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>E-mail értesítések táblázat
Az alábbi táblázat részletesebben ismerteti az egyes e-mail értesítéseket. Az e-mailek kezeléséhez szabályokat használhat. Az Outlook programban például szabályokat hozhat létre az e-mailek mappába való áthelyezéséhez, ha a tárgy ebből a táblázatból származó szavakat tartalmaz:

| # | E-mail tárgya | Amikor elküldték | Elküldve: |
| --- | --- | --- | --- |
| 1 | Szükséges művelet: A továbbított kérelem jóváhagyása vagy megtagadása *[dátum] szerint* | Ezt az e-mailt a rendszer elküldi az 1. | 1. szakasz alternatív jóváhagyói |
| 2 | Szükséges művelet: Kérés jóváhagyása vagy megtagadása *[dátum] szerint* | Ez az e-mail lesz elküldve az első jóváhagyó, ha eszkaláció le van tiltva, hogy tegyen lépéseket. | Első jóváhagyó |
| 3 | Emlékeztető: A kérelem jóváhagyása vagy elutasítása *[dátum]* szerint *a [kérelmező]* számára | Ez az emlékeztető e-mail az első jóváhagyónak lesz elküldve, ha az eszkaláció le van tiltva. Az e-mail arra kéri őket, hogy tegyenek lépéseket, ha nem. | Első jóváhagyó |
| 4 | A kérelem jóváhagyása vagy elutasítása *[időpont]* által *[dátum]* | Ezt az e-mailt a rendszer elküldi az első jóváhagyónak (ha az eszkaláció engedélyezve van) a művelet hez. | Első jóváhagyó |
| 5 | A művelethez emlékeztetőt kell kérni: A kérelem jóváhagyása vagy megtagadása *[dátum]* szerint *a [kérelmező]* számára | Ez az emlékeztető e-mail az első jóváhagyónak lesz elküldve, ha az eszkaláció engedélyezve van. Az e-mail arra kéri őket, hogy tegyenek lépéseket, ha nem. | Első jóváhagyó |
| 6 | A kérelem *lejárt [access_package]* | Ezt az e-mailt a rendszer elküldi az első jóváhagyónak és az 1. | Első jóváhagyó, 1. |
| 7 | A *[kérelmező]* által *a [access_package]* részére jóváhagyott kérelem | Ezt az e-mailt kérésre elküldjük az első jóváhagyónak és az 1. | Első jóváhagyó, 1. |
| 8 | A *[kérelmező]* által *a [access_package]* részére jóváhagyott kérelem | Ezt az e-mailt a rendszer elküldi a 2 szakaszos kérelem első jóváhagyójának és 1. | Első jóváhagyó, 1. |
| 9 | A kérelem megtagadva *a [access_package]* | Ezt az e-mailt a rendszer akkor küldi el a kérelmezőnek, amikor a kérésüket elutasítják. | Requestor (Kérelmező) |
| 10 | A kérés *[access_package]* esetén lejárt. | Ezt az e-mailt egy vagy kétlépcsős kérés végén küldjük el a kérelmezőnek. Az e-mail értesíti a kérelmezőt, hogy a kérelem lejárt. | Requestor (Kérelmező) |
| 11 | Szükséges művelet: Kérés jóváhagyása vagy megtagadása *[dátum] szerint* | Ez az e-mail a második jóváhagyónak lesz elküldve, ha az eszkaláció le van tiltva, hogy lépéseket tegyen. | Második jóváhagyó |
| 12 | A művelethez emlékeztetőt kell kérni: A kérelem jóváhagyása vagy megtagadása *[dátum] szerint* | Ez az emlékeztető e-mail a második jóváhagyónak lesz elküldve, ha az eszkaláció le van tiltva. Az értesítés arra kéri őket, hogy tegyenek lépéseket, ha még nem. | Második jóváhagyó |
| 13 | Szükséges művelet: A kérelem jóváhagyása vagy megtagadása *[dátum]* szerint *a [kérelmező]* számára | Ez az e-mail lesz elküldve a második jóváhagyó, ha eszkaláció engedélyezve van, hogy tegyen lépéseket. | Második jóváhagyó |
| 14 | A művelethez emlékeztetőt kell kérni: A kérelem jóváhagyása vagy megtagadása *[dátum]* szerint *a [kérelmező]* számára | Ez az emlékeztető e-mail a második jóváhagyónak lesz elküldve, ha az eszkaláció engedélyezve van. Az értesítés arra kéri őket, hogy tegyenek lépéseket, ha még nem. | Második jóváhagyó |
| 15 | Szükséges művelet: A továbbított kérelem jóváhagyása vagy megtagadása *[dátum] szerint* | Ezt az e-mailt a rendszer elküldi a 2. | 2. szakasz alternatív jóváhagyói |
| 16 | A *[kérelmező]* által *a [access_package]* részére jóváhagyott kérelem | Ezt az e-mailt a rendszer elküldi a második jóváhagyónak és a 2. | Második jóváhagyó, 2. |
| 17 | [access_package] esetén lejárt egy *kérelem.* | Ezt az e-mailt a kérés lejárta után küldjük el a második jóváhagyónak vagy alternatív jóváhagyónak. | Második jóváhagyó, 2. |
| 18 | Most már hozzáférhet *a [access_package]* | Ezt az e-mailt a rendszer elküldi a végfelhasználóknak, hogy elkezdhessék használni a hozzáférésüket. | Requestor (Kérelmező) |
| 19 | Hozzáférés kiterjesztése *[access_package]* számára *[dátum] szerint* | Ezt az e-mailt a rendszer a hozzáférés lejárta előtt elküldi a végfelhasználóknak. | Requestor (Kérelmező) |
| 20 | A hozzáférés *[access_package]* esetén véget ért. | Ezt az e-mailt a rendszer a hozzáférés lejárta után küldi el a végfelhasználóknak. | Requestor (Kérelmező) |

### <a name="access-request-emails"></a>Hozzáférési kérései e-mailek

Amikor egy kérelmező hozzáférési kérelmet küld egy jóváhagyási kérelemre konfigurált hozzáférési csomaghoz, a házirendhez hozzáadott összes jóváhagyó e-mailértesítést kap a kérelem részleteivel. Az e-mailben a következő részletek szerepelnek: a kérelmező neve szervezet, és az üzleti indoklás; és a kért hozzáférés kezdő és záró dátuma (ha rendelkezésre áll). A részletek között szerepel az is, hogy mikor nyújtották be a kérelmet, és mikor jár le a kérelem.

Az e-mail tartalmaz egy linket jóváhagyók kattintson, hogy menjen a My Access jóváhagyásához vagy megtagadásához a hozzáférési kérelmet. Itt van egy példa e-mail értesítést küldött az első jóváhagyó vagy a második jóváhagyó (ha a kétlépcsős jóváhagyás engedélyezve van) a hozzáférési kérelem teljesítéséhez:

![Csomaglevelezési kérelem jóváhagyása](./media/entitlement-management-shared/approver-request-email.png)

A jóváhagyók emlékeztető e-mailt is kaphatnak. Az e-mail megkéri a jóváhagyót, hogy hozzon döntést a kérésről. Itt van egy minta e-mail értesítést a jóváhagyó kap, hogy emlékeztesse őket, hogy tegyen lépéseket:

![Emlékeztető hozzáférési kérelem e-mail](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternatív jóváhagyók e-maileket kérnek

Ha az alternatív jóváhagyók beállítás engedélyezve van, és a kérelem még függőben van, a lesz továbbítva. Az alternatív jóváhagyók e-mailt kapnak a kérelem jóváhagyására vagy elutasítására. Az 1- és 2-es és a 2-es fázisban engedélyezheti az alternatív jóváhagyókat. Itt van egy minta e-mailt az értesítést a másodlagos jóváhagyók kap:

![Alternatív jóváhagyók e-mailt kérnek](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

A jóváhagyó és a helyettes jóváhagyók is jóváhagyhatják vagy elutasíthatják a kérelmet.

### <a name="approved-or-denied-emails"></a>Jóváhagyott vagy elutasított e-mailek

 Amikor egy jóváhagyó kap egy hozzáférési kérelmet a kérelmező által benyújtott, jóváhagyhatja vagy megtagadhatja a hozzáférési kérelmet. A jóváhagyónak üzleti indoklást kell hozzáadnia a döntéshez. Az alábbiakban egy mintae-mailt küldött a jóváhagyók és alternatív jóváhagyók után a kérelem jóváhagyása:

![Jóváhagyott kérelem a csomag e-mailjeinek elérésére](./media/entitlement-management-process/approver-request-email-approved.png)

Amikor egy hozzáférési kérelmet jóváhagynak, és azok hozzáférése ki van építve, e-mail értesítést küld a kérelmezőnek, hogy most már hozzáférhetnek a hozzáférési csomaghoz. Az alábbiakban egy minta-e-mail értesítést kap a kérelmezőnek, amikor hozzáférést kap egy hozzáférési csomaghoz:

![Jóváhagyott kérelmező hozzáférési kérelem e-mail címe](./media/entitlement-management-process/requestor-email-approved.png)

Ha egy hozzáférési kérelmet megtagadnak, a rendszer e-mailértesítést küld a kérelmezőnek. Itt van egy minta e-mail értesítést küldött a kérelmező, ha a hozzáférési kérelmet megtagadják:

![A kérelmező kérésére megtagadott e-mail](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>Kétlépcsős jóváhagyási hozzáférési kérelem e-mailek

Ha a kétlépcsős jóváhagyás engedélyezve van, legalább két jóváhagyónak jóvá kell hagynia a kérelmet, minden szakaszból egyet, mielőtt a kérelmező hozzáférést kaphatna.

Az első jóváhagyó megkapja a hozzáférési kérelem e-mail címét, és döntést hoz. Ha jóváhagyják a kérelmet, az 1. Itt van egy példa e-mailt az értesítést, hogy az elküldött, ha stage-1 befejeződött:

![Kétlépcsős hozzáférési kérelem e-mail](./media/entitlement-management-process/approver-request-email-2stage.png)

Miután az első vagy a másik jóváhagyó jóváhagyja a kérelmet az 1. A második szakaszban a második jóváhagyó megkapja a hozzáférési kérelem értesítő e-mailt. Miután a második jóváhagyó vagy alternatív jóváhagyók a 2.

### <a name="expired-access-request-emails"></a>Lejárt hozzáférési kérelem e-mailek

A hozzáférési kérelmek lejárhatnak, ha egyetlen jóváhagyó sem hagyta jóvá vagy nem tagadta meg a kérést. 

Amikor a kérelem eléri a beállított lejárati dátumot, és lejár, a jóváhagyók már nem hagyhatják jóvá vagy tagadhatják meg. Itt van egy példa e-mailt az értesítést küldött az összes első, második (ha a kétlépcsős jóváhagyás engedélyezve van), és alternatív jóváhagyók:

![A jóváhagyók lejárt hozzáférési kérelem e-mailcíme](./media/entitlement-management-process/approver-request-email-expired.png)

A rendszer e-mailértesítést is küld a kérelmezőnek, amelyben értesíti őket arról, hogy a hozzáférési kérelem lejárt, és hogy újra el kell küldeniük a hozzáférési kérelmet. Az alábbi ábra a kérelmező élményét és a hozzáférés kiterjesztését kérő e-mail-értesítéseket mutatja be:

![A kérelmező kiterjeszti a hozzáférési folyamat folyamatát](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Itt van egy minta e-mail értesítést küldött a kérelmező, ha a hozzáférési kérelem lejárt:

![Lejárt hozzáférési kérelem e-mail címe](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>További lépések

- [Hozzáférés kérése hozzáférési csomaghoz](entitlement-management-request-access.md)
- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)
