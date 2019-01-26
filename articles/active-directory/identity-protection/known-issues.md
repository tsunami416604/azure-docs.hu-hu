---
title: Gyakori kérdések és (frissítve) az Azure Active Directory identity protection szolgáltatással kapcsolatos ismert problémák |} A Microsoft Docs
description: Gyakori kérdések és (frissítve) az Azure Active Directory identity protection szolgáltatással kapcsolatos ismert problémák.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: c9660dccf4929cb62e78dae1956d01b0d3ba8fcb
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913916"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Gyakori kérdések és (frissítve) az Azure Active Directory identity protection szolgáltatással kapcsolatos ismert problémák


## <a name="dismiss-user-risk"></a>Felhasználói kockázat elvetése

**Zárja be a felhasználói kockázat** Identity Protection beállítja az aktor a felhasználó kockázati előzményeit az Identity Protection (frissítés), a klasszikus **Azure ad-ben**.


**Zárja be a felhasználói kockázat** (frissítve) Identity Protection úgy állítja be az aktor a felhasználó kockázati előzmények az Identity Protection (frissítve) való **\<a rendszergazda nevű felhasználóhoz tartozó panelt mutató hivatkozás\>**.


## <a name="risky-users-report"></a>Kockázatos felhasználók jelentés

A lekérdezések a **felhasználónév** mező-és nagybetűk, a lekérdezések során a **neve** mező eset-agnosztikus.

Vizualizációtól **dátumok megjelenítése a következőképpen** elrejti a **kockázati utolsó frissített** oszlop. Oszlopban kattintson a szerepkörtagok **oszlopok** a kockázatos felhasználók panel tetején.

**Az összes esemény elvetését** Identity Protection a kockázati események állapotát állítja be a klasszikus **lezárva (Megoldva)**.

Ha megpróbál kattintva férhet hozzá a kockázatos felhasználók jelentés **kockázatos felhasználók jelentés** belül a kockázatos bejelentkezések jelentés a bejelentkezési rekord, egyes esetekben valószínűleg **probléma merült fel. Próbálja meg újra**. Megoldásához kattintson **alkalmaz** vagy **alaphelyzetbe** fel adatokat a kockázatos felhasználókat és a képernyő tetején.


## <a name="risky-sign-ins-report"></a>Kockázatos bejelentkezési jelentések

**Oldja meg** egy kockázati esemény állítja az állapot **felhasználók átadott kockázatalapú házirend által vezérelt MFA**.

**Alaphelyzetbe** a a **kockázatos bejelentkezések** jelentés nem szünteti meg az értékét a **kockázati esemény típusa**.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Miért nem állítható be a saját kockázati szintek minden kockázati esemény esetén?

Az Identity Protection kockázati szintek az észlelési pontosságának alapulnak, és a felügyelt gépi tanuláson alapuló. Testre szabhatja, milyen élmény felhasználók jelennek meg, rendszergazda is csoportok belefoglalása vagy kizárása bizonyos felhasználók vagy a felhasználói kockázat és bejelentkezési kockázati szabályzatainak.


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Miért egy bejelentkezési helye nem egyezik, a felhasználó valóban jelentkezett be a?

IP-Címek földrajzi hely meghatározásának leképezésének egy egész iparágra kiterjedő challenge. Ha úgy érzi, hogy a hely szerepel a bejelentkezési jelentések nem egyezik a tényleges helyet, vegye fel a kapcsolatot támogatási. 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hogyan működnek az Identity Protection visszajelzési funkcióját?

**Győződjön meg róla sérült a biztonsága** (az adott bejelentkezés) –, amely a bejelentkezés nem volt tájékoztatja az Azure AD Identity Protection az identitás tulajdonosa végzi, és azt jelzi, hogy a biztonsági sérülés.

- A visszajelzés fogadásakor a be- és a felhasználó kockázati állapotának mozgatjuk **megerősített biztonsága sérült** és kockázati szintjét **magas**.

- Ezenkívül a Microsoft biztosít az adatokat a machine learning-rendszerek, a kockázatfelmérés a jövőbeli fejlesztések.

    > [!NOTE]
    > Ha a felhasználó már elhárul, ne kattintson **sérült a biztonsága megerősítése** áthelyezi azt a be- és felhasználói kockázat állapota, mert **megerősített biztonsága sérült** és kockázati szintjét **magas**.

**Erősítse meg a biztonságos** (az adott bejelentkezés) – tájékoztatja az Azure AD Identity Protection, hogy a bejelentkezési identitás tulajdonosa által végrehajtott és nem azt biztonság sérülését.

- A visszajelzés fogadásakor a bejelentkezés (nem a felhasználó) mozgatjuk kockázati állapotának **megerősítve biztonságos** és a kockázati szintet **-**.

- Ezenkívül a Microsoft biztosít az adatokat a machine learning-rendszerek, a kockázatfelmérés a jövőbeli fejlesztések.

    > [!NOTE]
    > Ha úgy gondolja, hogy a felhasználó nem sérül, **felhasználói kockázat bezárása** használata helyett a felhasználó szintjén **megerősítve biztonságos** a bejelentkezési szintjén. A **felhasználói kockázat bezárása** a felhasználóra szint bezárja a felhasználói kockázat és a múltbeli kockázatos bejelentkezések és a kockázati események.



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Miért látok egy alacsony (vagy újabb) felhasználói kockázati pontszám, még akkor is, ha nincsenek kockázatos bejelentkezések vagy a kockázati események jelennek meg az Identity Protection?

A felhasználói kockázat összesített jellegűek, és nem jár le, egy felhasználó jogosult az alacsony, a felhasználói kockázat, vagy fent még akkor is, ha nincsenek közelmúltbeli kockázatos bejelentkezések vagy kockázati események Identity Protection látható. Ez akkor fordulhat elő, ha a felhasználó a csak rosszindulatú tevékenység került sor a kockázatos bejelentkezések és a kockázati események adatait tároljuk, amelynek időtartamon túl. Felhasználói kockázat nem tudjuk lejár, mert kártékony elemek az előzőekben ramping támadás mentése előtt mögött egy sérült biztonságú identitás több mint 140 nap az ügyfelek környezetben marad. Ügyfelek tekintheti át a felhasználó kockázati idővonalán tudni, miért érdemes a felhasználó veszélyben van a: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Miért érdemes a bejelentkezési rendelkezik "egy bejelentkezési kockázati (összesítés)" pontszám magas alacsony vagy közepes kockázat a hozzá társított észlelések esetén?

A magas összesített kockázati pontszám a bejelentkezés, vagy az a tény, hogy egynél több észlelési aktivált, hogy jelentkezzen be az egyéb jellemzőit alapulhatnak. És ezzel szemben egy bejelentkezési előfordulhat, hogy a bejelentkezési kockázat (összesített) a közepes akkor is, ha a bejelentkezéshez társított észlelések magas kockázatú. 
