---
title: A Azure Active Directory Identity Protection szolgáltatással kapcsolatos gyakori kérdések
description: Gyakori kérdések Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d22973867782ddb64ced2ac95e84c0b27a3addd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887597"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Gyakori kérdések az identitások védelméről Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Felhasználói kockázattal kapcsolatos ismert problémák bezárása

A **felhasználói kockázat elvetése** a klasszikus Identity Protectionben az **Azure ad**-ben a felhasználó kockázati előzményeiben szereplő tulajdonost állítja be.

A **felhasználói kockázat elvetése** az Identity Protection szolgáltatásban a felhasználó az Identity Protectionben a tulajdonos kockázati előzményeiben szereplő szereplőt állítja be, hogy **\<a rendszergazda nevét a felhasználó paneljének\>mutató hiperhivatkozással** .

Létezik egy aktuálisan ismert probléma, ami késést okoz a felhasználói kockázat elbocsátási folyamatában. Ha a "felhasználói kockázati szabályzattal" rendelkezik, ez a szabályzat a "felhasználói kockázat elvetése" gombra kattintást követően néhány percen belül leállítja a felhasználókat. Azonban ismert késések vannak az UX-felhasználók "kockázat állapotának" frissítésével. Megkerülő megoldásként frissítse az oldalt a böngésző szintjén, hogy megtekintse a "kockázati állapot" nevű legújabb felhasználót.

## <a name="risky-users-report-known-issues"></a>A kockázatos felhasználók ismert problémákat jelentettek

A **Felhasználónév** mezőben lévő lekérdezések megkülönböztetik a kis-és nagybetűket, míg a **Name (név** ) mező lekérdezései a kis-és nagybetűket használják.

A **dátumok megjelenítésének módosítása,** hogy elrejtse a **kockázat utolsó frissítésének** oszlopát. Az oszlop olvasásához kattintson a kockázatos felhasználók panel tetején található **oszlopok** elemre.

A klasszikus Identity Protection **összes eseményének** bezárásakor a kockázati észlelések állapota **lezárva (megoldva)** állapotú.

## <a name="risky-sign-ins-report-known-issues"></a>Kockázatos bejelentkezések – ismert problémák

A kockázatkezelési **megoldás** a **kockázatalapú szabályzat által vezérelt MFA-t használó felhasználók számára**állítja be az állapotot.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Miért nem tudom beállítani a saját kockázati szinteket az egyes kockázati észlelésekhez?

Az Identity Protection kockázati szintjei az észlelés pontosságán és a felügyelt gépi tanuláson alapulnak. A felhasználók által megjelenített felhasználói élmény testreszabásához a rendszergazda belefoglalhat vagy kizárhat bizonyos felhasználókat/csoportokat a felhasználói kockázat és a bejelentkezési kockázati szabályzatok alapján.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Miért nem egyezik meg a bejelentkezés helye, ha a felhasználó valóban be van jelentkezve?

Az IP térinformatikai leképezés az iparági szintű kihívás. Ha úgy érzi, hogy a bejelentkezési jelentésben szereplő hely nem felel meg a tényleges helynek, lépjen kapcsolatba a Microsoft ügyfélszolgálatával. 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hogyan működik az Identity Protection visszajelzési mechanizmusai?

**Biztonság megerősítése** (bejelentkezéskor) – tájékoztatja, Azure ad Identity Protection, hogy a bejelentkezést nem az identitás tulajdonosa végezte el, és a rendszer kompromisszumot jelez.

- A visszajelzés kézhezvétele után a bejelentkezési és a felhasználói kockázati állapotot a rendszer **visszaigazolja a feltört** **és a kockázati szintre.**

- Ezen felül a kockázatkezelési rendszerek jövőbeli fejlesztései esetében is biztosítjuk az információkat a gépi tanulási rendszerekhez.

    > [!NOTE]
    > Ha a felhasználó már szervizelve van, ne kattintson a **Biztonság megerősítése** elemre, mert a bejelentkezési és a felhasználói kockázati állapotot a rendszer **visszaigazolja** a feltört és a kockázati szint **magas**szintre való visszalépésével.

**Biztonság megerősítése** (bejelentkezéskor) – tájékoztatja Azure ad Identity Protection, hogy a bejelentkezést az identitás tulajdonosa végezte el, és nem jelent kompromisszumot.

- A visszajelzések kézhezvétele után a bejelentkezés (nem a felhasználó) kockázati állapota **megerősítve** , hogy a biztonság és a kockázati szint **-** .

- Ezen felül a kockázatkezelési rendszerek jövőbeli fejlesztései esetében is biztosítjuk az információkat a gépi tanulási rendszerekhez.

    > [!NOTE]
    > Ha úgy véli, hogy a felhasználó nem sérül, a felhasználói szinten utasítsa el a **felhasználói kockázatot** , nem pedig a **megerősített széf** használatát a bejelentkezési szinten. A felhasználói szintű felhasználói **kockázat elvetése** bezárja a felhasználói kockázatot és az összes múltbeli kockázatos bejelentkezést és kockázati észlelést.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Miért jelenik meg egy alacsony (vagy magasabb) kockázati pontszámmal rendelkező felhasználó, még akkor is, ha nincsenek kockázatos bejelentkezések vagy kockázati észlelések az Identity Protectionben?

Tekintettel arra, hogy a felhasználói kockázat kumulatív jellegű, és nem jár le, a felhasználó akkor is csökkentheti a felhasználói kockázatot, ha az Identity Protection szolgáltatásban nincsenek közelmúltbeli kockázatos bejelentkezések vagy kockázati észlelések. Ez a helyzet akkor fordulhat elő, ha a felhasználó egyetlen rosszindulatú tevékenysége az időkereten kívül esik, amelyre a kockázatos bejelentkezések és a kockázati észlelések részleteit tároljuk. Nem jár le a felhasználói kockázat, mert a rossz szereplőkkel kapcsolatban is ismertek voltak az ügyfelek környezetében, több mint 140 napon keresztül, a támadás feltörése előtt. Az ügyfelek áttekinthetik a felhasználó kockázati idővonalát, hogy megtudja, miért van a veszélye annak, hogy a felhasználó a következő helyen található: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Miért van a bejelentkezés "a bejelentkezési kockázat (aggregált)" pontszáma, amikor a hozzá társított észlelések alacsony vagy közepes kockázattal rendelkeznek?

A magas összesített kockázati pontszám a bejelentkezés más szolgáltatásain alapulhat, vagy az a tény, hogy a bejelentkezéshez egynél több észlelés történt. A bejelentkezés pedig akkor is előfordulhat, ha a bejelentkezési kockázat (aggregált) közepes, még akkor is, ha a bejelentkezéshez kapcsolódó észlelések nagy kockázatot jelentenek. 
