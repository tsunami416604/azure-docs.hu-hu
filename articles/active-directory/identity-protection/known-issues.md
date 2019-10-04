---
title: Gyakori kérdések és ismert problémák az Identity Protectionben (frissítve) a Azure Active Directoryban | Microsoft Docs
description: Gyakori kérdések és ismert problémák az Identity Protectionben (frissítve) Azure Active Directoryban.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe7125174129752e6d6dbe0e00d01d4f32755333
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126092"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Gyakori kérdések és ismert problémák az Identity Protectionben (frissítve) Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Felhasználói kockázattal kapcsolatos ismert problémák bezárása

A **felhasználói kockázat** elvetése a klasszikus Identity Protection szolgáltatásban a felhasználó kockázati előzményeiben szereplő tulajdonost az **Azure ad**-be állítja be az Identity Protectionben (frissítve).

A **felhasználói kockázat** elvetése az Identity Protectionben (frissített) a felhasználó kockázati előzményeiben lévő tulajdonost állítja be az Identity Protectionben (frissítve)  **\<a rendszergazda nevére a felhasználó\>** paneljére mutató hiperhivatkozással.

Létezik egy aktuálisan ismert probléma, ami késéseket okoz a felhasználói kockázat elbocsátási folyamatában. Ha a "felhasználói kockázati szabályzattal" rendelkezik, ez a szabályzat a "felhasználói kockázat elvetése" gombra kattintást követően néhány percen belül leállítja a felhasználókat. Azonban ismert késések vannak az UX-felhasználók "kockázat állapotának" frissítésével. Megkerülő megoldásként frissítse az oldalt a böngésző szintjén, hogy megtekintse a "kockázati állapot" nevű legújabb felhasználót.

## <a name="risky-users-report-known-issues"></a>A kockázatos felhasználók ismert problémákat jelentettek

A **Felhasználónév** mezőben lévő lekérdezések megkülönböztetik a kis-és nagybetűket, míg a **Name (név** ) mező lekérdezései a kis-és nagybetűket használják.

A **dátumok megjelenítésének módosítása,** hogy elrejtse a **kockázat utolsó frissítésének** oszlopát. Az oszlop olvasásához kattintson a kockázatos felhasználók panel tetején található **oszlopok** elemre.

A klasszikus Identity Protection **összes eseményének** bezárásakor a kockázati észlelések állapota lezárva **(megoldva)** állapotú.

## <a name="risky-sign-ins-report-known-issues"></a>Kockázatos bejelentkezések – ismert problémák

A kockázatkezelési **megoldás** a **kockázatalapú szabályzat által vezérelt MFA-t használó felhasználók számára**állítja be az állapotot.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Miért nem tudom beállítani a saját kockázati szinteket az egyes kockázati észlelésekhez?

Az Identity Protection kockázati szintjei az észlelés pontosságán és a felügyelt gépi tanuláson alapulnak. A felhasználók által megjelenített felhasználói élmény testreszabásához a rendszergazda belefoglalhat vagy kizárhat bizonyos felhasználókat/csoportokat a felhasználói kockázat és a bejelentkezési kockázati szabályzatok alapján.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Miért nem egyezik meg a bejelentkezés helye, ha a felhasználó valóban be van jelentkezve?

Az IP térinformatikai leképezés az iparági szintű kihívás. Ha úgy érzi, hogy a bejelentkezési jelentésben szereplő hely nem egyezik meg a tényleges hellyel, forduljon a támogatási szolgálathoz. 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hogyan működik az Identity Protection visszajelzési mechanizmusai?

**Sérült biztonság megerősítése** (bejelentkezéskor) – tájékoztatja Azure AD Identity Protection arról, hogy a bejelentkezést nem az identitás tulajdonosa végezte el, és a rendszer kompromisszumot jelez.

- A visszajelzés kézhezvétele után a bejelentkezési és a felhasználói kockázati állapotot a rendszer visszaigazolja a feltört és akockázati szintre.

- Ezen felül a kockázatkezelési rendszerek jövőbeli fejlesztései esetében is biztosítjuk az információkat a gépi tanulási rendszerekhez.

    > [!NOTE]
    > Ha a felhasználó már szervizelve van, ne kattintson a **Biztonság megerősítése** elemre, mert a bejelentkezési és a felhasználói kockázati állapotot a rendszer visszaigazolja a feltört és a kockázati szint **magas**szintre való visszalépésével.

**Biztonság megerősítése** (bejelentkezéskor) – tájékoztatja Azure AD Identity Protection arról, hogy a bejelentkezést az identitás tulajdonosa végezte, és nem jelent kompromisszumot.

- A visszajelzés kézhezvétele után a bejelentkezés (nem a felhasználó) kockázati állapotát a **Biztonság megerősítésére** és a kockázati szintre **-** helyezi át.

- Ezen felül a kockázatkezelési rendszerek jövőbeli fejlesztései esetében is biztosítjuk az információkat a gépi tanulási rendszerekhez.

    > [!NOTE]
    > Ha úgy véli, hogy a felhasználó nem sérül, a felhasználói szinten utasítsa el a **felhasználói kockázatot** , nem pedig a megerősített **széf** használatát a bejelentkezési szinten. A felhasználói szintű felhasználói **kockázat** elvetése bezárja a felhasználói kockázatot és az összes múltbeli kockázatos bejelentkezést és kockázati észlelést.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Miért jelenik meg egy alacsony (vagy magasabb) kockázati pontszámmal rendelkező felhasználó, még akkor is, ha nincsenek kockázatos bejelentkezések vagy kockázati észlelések az Identity Protectionben?

Tekintettel arra, hogy a felhasználói kockázat kumulatív jellegű, és nem jár le, a felhasználó akkor is csökkentheti a felhasználói kockázatot, ha az Identity Protection szolgáltatásban nincsenek közelmúltbeli kockázatos bejelentkezések vagy kockázati észlelések. Ez akkor fordulhat elő, ha a felhasználó egyetlen rosszindulatú tevékenysége túllépte az időkeretet, amely a kockázatos bejelentkezések és a kockázatok észlelésének részleteit tárolja. Nem jár le a felhasználói kockázat, mert a rossz szereplőkkel kapcsolatban is ismertek voltak az ügyfelek környezetében, több mint 140 napon keresztül, a támadás feltörése előtt. Az ügyfelek áttekinthetik a felhasználó kockázati idővonalát, hogy megtudja, miért van a veszélye annak, hogy a felhasználó a következővel rendelkezik:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Miért van a bejelentkezés "a bejelentkezési kockázat (aggregált)" pontszáma, amikor a hozzá társított észlelések alacsony vagy közepes kockázattal rendelkeznek?

A magas összesített kockázati pontszám a bejelentkezés más szolgáltatásain alapulhat, vagy az a tény, hogy a bejelentkezéshez egynél több észlelés történt. A bejelentkezés pedig akkor is előfordulhat, ha a bejelentkezési kockázat (aggregált) közepes, még akkor is, ha a bejelentkezéshez kapcsolódó észlelések nagy kockázatot jelentenek. 
