---
title: A Azure Active Directory Identity Protection szolgáltatással kapcsolatos gyakori kérdések
description: Gyakori kérdések Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443571"
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

### <a name="why-is-a-user-is-at-risk"></a>Miért van kockázat a felhasználó számára?

Ha Ön Azure AD Identity Protection ügyfél, lépjen a [kockázatos felhasználók](howto-identity-protection-investigate-risk.md#risky-users) nézetre, és kattintson a veszélyeztetett felhasználóra. A lap alján található "kockázati előzmények" lapon megjelenik az összes olyan esemény, amely a felhasználó kockázati változásához vezetett. Ha szeretné megtekinteni az összes kockázatos bejelentkezést a felhasználó számára, kattintson a "felhasználó kockázatos bejelentkezések" elemre. Ha meg szeretné tekinteni a felhasználó összes kockázati észlelését, kattintson a "felhasználó kockázati észlelése" elemre.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Hogyan szerezhetek be egy adott típusú észlelési jelentést?

Lépjen a kockázati észlelések nézetre, és szűrje az "észlelés típusa" értéket. Ezután letöltheti ezt a jelentést a alkalmazásban. CSV vagy. JSON-formátum a felül található **Letöltés** gomb használatával. További információ [: How to: kivizsgálás kockázat](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Miért nem tudom beállítani a saját kockázati szinteket az egyes kockázati észlelésekhez?

Az Identity Protection kockázati szintjei az észlelés pontosságán és a felügyelt gépi tanuláson alapulnak. A felhasználók által megjelenített felhasználói élmény testreszabásához a rendszergazda belefoglalhat vagy kizárhat bizonyos felhasználókat/csoportokat a felhasználói kockázat és a bejelentkezési kockázati szabályzatok alapján.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Miért nem egyezik meg a bejelentkezés helye, ha a felhasználó valóban be van jelentkezve?

Az IP térinformatikai leképezés az iparági szintű kihívás. Ha úgy érzi, hogy a bejelentkezési jelentésben szereplő hely nem felel meg a tényleges helynek, lépjen kapcsolatba a Microsoft ügyfélszolgálatával. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Hogyan zárhatók ki bizonyos kockázati észlelések, mint a régi felhasználói felületen?

Visszajelzéseket adhat a kockázati észlelésekről, ha a csatolt bejelentkezést sérültként vagy biztonságosként erősíti meg. A bejelentkezés során kapott visszajelzések a bejelentkezés során végzett összes észlelésre kiterjednek. Ha olyan észleléseket szeretne, amelyek nem kapcsolódnak a bejelentkezéshez, megadhatja a visszajelzéseket a felhasználói szinten. További információkért tekintse meg a következő cikket [: Hogyan lehet kockázati visszajelzést adni a Azure ad Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Meddig mehetek vissza az időben, hogy megértsük, mi történik a felhasználóval?

- A [kockázatos felhasználók](howto-identity-protection-investigate-risk.md#risky-users) nézet az összes korábbi bejelentkezés alapján jeleníti meg a felhasználó kockázatait. 
- A kockázatos [bejelentkezések](howto-identity-protection-investigate-risk.md#risky-sign-ins) nézet a kockázatos jeleket mutatja az elmúlt 30 napban. 
- A [kockázati észlelések](howto-identity-protection-investigate-risk.md#risk-detections) nézetben az elmúlt 90 napban elvégzett kockázati észlelések láthatók.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Hogyan tudhatok meg többet egy adott észlelésről?

Az összes kockázati észlelésről szóló cikkben a [Mi a kockázat](concept-identity-protection-risks.md#risk-types-and-detection). Ha többet szeretne megtudni az észlelésről, vigye a kurzort az észlelés melletti (i) szimbólumra az Azure Portalon.

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
