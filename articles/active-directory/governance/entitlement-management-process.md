---
title: Az Azure AD tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory-folyamat és az e-mail értesítések kérése
description: Ismerje meg a folyamat egy hozzáférési csomaghoz és e-mail-értesítések küldése az Azure Active Directory tagjogosultság-kezelés (előzetes verzió).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/26/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ab18c8f165fc30636cd05091be1181743f9972d
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873639"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Az Azure AD tagjogosultság-kezelés (előzetes verzió) folyamat és az e-mail-értesítések kérése

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Amikor a felhasználó kérést küld egy hozzáférés-csomagot, egy a folyamat elindul, hogy a kérelmet. Az Azure AD tagjogosultság-kezelés is küld e-mail-értesítések jóváhagyók és kérelmezőktől a hozzáférést a folyamat során a fontos eseményeket bekövetkezésekor.

Ez a cikk ismerteti a folyamat, és az e-mail-értesítéseket küldött.

## <a name="request-process"></a>Kérelem folyamata

Egy hozzáférés-csomag hozzáférést igénylő felhasználó hozzáférési kérést küldhet. A szabályzat konfigurációjától függően a kérés jóváhagyást lehet szükség. Kérelem jóváhagyása, ha egy folyamat elindul, a felhasználói hozzáférés hozzárendelése az egyes erőforrások a hozzáférés-csomagban. Az alábbi ábrán látható a folyamat és a különböző állapotok áttekintését.

![Jóváhagyási folyamat ábrája](./media/entitlement-management-process/request-process.png)

| Állapot | Leírás |
| --- | --- |
| Elküldve | Felhasználó kérést küld. |
| Jóváhagyásra váró elemek | Ha a házirend-hozzáférés csomag jóváhagyást igényel, függőben lévő jóváhagyási kérés helyezi át. |
| Elévült | Ha nincsenek jóváhagyók tekintse át a kérést a jóváhagyási kérelem időkorláton belül, a kérelem lejár. Próbálkozzon újra, a felhasználónak kell küldje el újra a kérelmet. |
| Elutasítva | Jóváhagyó elutasítja a kérést. |
| Approved | Jóváhagyó jóváhagyja a kérést. |
| Kézbesítés | Felhasználó rendelkezik **nem** már hozzárendelt a hozzáférés-csomagban lévő összes erőforrást. Ha egy külső felhasználót, a felhasználó még nem érhető el az erőforrás-könyvtár, és az engedélyek kérés elfogadva. |
| Kézbesítve | Felhasználó rendelkezik lett hozzárendelve hozzáférés a hozzáférés-csomagban lévő összes erőforrást. |
| Kiterjesztett hozzáférés | Bővítmények a szabályzat engedélyezett, ha a felhasználó terjeszteni a hozzárendelést. |
| Hozzáférés lejárt | Felhasználó hozzáférését a hozzáférés csomag érvényessége lejárt. Érhet el újra, a felhasználónak kell igényelnie. |

## <a name="email-notifications"></a>E-mail-értesítések

Ha Ön a jóváhagyó, e-mail-értesítések, jóvá kell hagynia a hozzáférési kérelem során, és a hozzáférési kérelem befejezése után a rendszer elküld. Ha a kérelmező, e-mail-értesítések, amelyek jelzik a kérés állapotát a rendszer elküld. Az alábbi ábrán látható, ha ezek az e-mail értesítések küldése.

![A jogosultság e-mail folyamat](./media/entitlement-management-process/email-notifications.png)

A következő táblázat részletesen ezek e-mail-értesítések.

| # | E-mail tárgya | Elküldésekor | Címzett |
| --- | --- | --- | --- |
| 1 | Szükséges művelet: Hozzáférési kérelem áttekintése a *[kérelmező]* való *[hozzáférési csomag]* által *[date]* | Ha a kérelmező kérést egy hozzáférés-csomag | Az összes jóváhagyók |
| 2 | Szükséges művelet: Hozzáférési kérelem áttekintése a *[kérelmező]* való *[hozzáférési csomag]* által *[date]* | X nap elteltével a jóváhagyási kérelem időtúllépése | Az összes jóváhagyók |
| 3 | Feladatállapot-értesítés: *[kérelmező]* a hozzáférési kérelmet *[hozzáférési csomag]* lejárt | Ha a jóváhagyók nem hagyja jóvá vagy utasítsa hozzáférési kérelem belül a kérés időtartama | Kérelmező |
| 4 | Feladatállapot-értesítés: *[kérelmező]* hozzáférési kérelmet *[hozzáférési csomag]* befejezése | Ha az első jóváhagyó jóváhagyja vagy megtagadja a hozzáférési kérelem | Az összes jóváhagyók |
| 5 | Nem kapott hozzáférést *[hozzáférési csomag]* | Ha a kérelmező lett megtagadta a hozzáférést a hozzáférés-csomaghoz | Kérelmező |
| 6 | Most már rendelkezik hozzáféréssel *[hozzáférési csomag]*  | Ha a kérelmező hozzáférést kapott a hozzáférés-csomagban minden erőforráshoz | Kérelmező |
| 7 | Hozzáférési *[hozzáférési csomag]* nap X múlva lejár | X nap elteltével a hozzáférés csomaghoz hozzáféréssel a kérelmező lejár | Kérelmező |
| 8 | Hozzáférési *[hozzáférési csomag]* lejárt | Ha a kérelmező hozzáférés hozzáférési csomagjára lejár | Kérelmező |

### <a name="review-access-request-emails"></a>Felülvizsgálat hozzáférési kérelem e-mailek

A kérelmező elküldi a hozzáférési kérelem egy hozzáférési csomaghoz, amely konfigurálva van a jóváhagyás megkövetelése, ha a szabályzatában megtörténik a kérelem részleteit tartalmazó e-mail-értesítést kapnak. Részletei tartalmazzák a kérelmező neve, a szervezet, eléréséhez a kezdő és záró dátumát, ha meg van adva, üzleti indoklás, amikor a kérelem el lett küldve, és amikor a kérelem lejár. Az e-mail tartalmaz egy hivatkozást, ahol a jóváhagyók jóváhagyhatják vagy megtagadhatják a hozzáférési kérelem. Íme a kérelmező által a hozzáférési kérelem jóváhagyó küldött minta e-mail értesítést.

![Felülvizsgálat hozzáférést kérő e-mailben](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Engedélyezett vagy megtagadott e-mailek

Kérelmezőktől a hozzáférést a rendszer értesíti, ha a hozzáférési kérelem jóváhagyott és az elérhető, vagy ha a hozzáférési kérés megtagadva. Jóváhagyó áttekinti a kérelmező által benyújtott hozzáférési kérést, ha azok jóváhagyhatják vagy megtagadhatják a hozzáférési kérelem. A jóváhagyó hozzáadása egy üzleti indoklás a döntést kell.

Hozzáférési kérelem jóváhagyásakor tagjogosultság-kezelés elindítja a hozzáférés csomagban az erőforrások a kérelmező hozzáférést adna folyamatán. Után a kérelmező hozzáférést kapott a hozzáférés-csomagban minden erőforráshoz, e-mailben értesítést a rendszer küld a kérelmezőnek, hogy azok hozzáférési kérés jóváhagyva és, hogy most már hozzáféréssel rendelkeznek a hozzáférés-csomag. Íme a hozzáférést egy hozzáférés-csomag megadásakor a kérelmezőnek küldött minta e-mail értesítést.

Amikor a rendszer megtagadja a hozzáférési kérést, a van a kérelmezőnek küldött e-mail-értesítést. Íme, ha a hozzáférési kérés megtagadva a kérelmezőnek küldött minta e-mail értesítést.

### <a name="expired-access-request-emails"></a>Hozzáférési kérelem e-mailek lejárt

Kérelmezők értesítést kap a hozzáférési kérelem lejárt. A kérelmező hozzáférési kérést küld, ha a kérés rendelkezik egy kérés időtartama, utána pedig lejár. Ha nincsenek jóváhagyók, akik be egy jóváhagyása vagy elutasítása döntés, a kérelem jóváhagyásra vár állapotban marad továbbra is. Ha a kérelem eléri a beállított elévülési ideje, a kérelem lejár, és már nem lehet engedélyezett vagy a jóváhagyó elutasította. Ebben az esetben a kérelem lejárt állapotba kerül. Egy lejárt kérelem már nem hagyható jóvá vagy nem megtagadva. E-mailben értesítést küld, amely a hozzáférési kérelem lejárt, és küldje el újra a hozzáférési kérelem van szükségük a kérelmezőnek. Íme, ha a hozzáférési kérelem lejárt a kérelmezőnek küldött minta e-mail értesítést.

![E-mail hozzáférés kérése lejárt](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>További lépések

- [Hozzáférés csomagjára hozzáférés kérése](entitlement-management-request-access.md)
- [Hagyja jóvá vagy utasítsa a hozzáférési kérelmek](entitlement-management-request-approve.md)
