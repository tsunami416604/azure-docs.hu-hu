---
title: Gyakori kérdések az Azure Active Directory identitásvédelmével kapcsolatban
description: Gyakori kérdések az Azure AD identity Protection
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443571"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Gyakori kérdések az Identitásvédelem szolgáltatással kapcsolatban az Azure Active Directoryban

## <a name="dismiss-user-risk-known-issues"></a>A felhasználói kockázat ismert problémáinak elvetése

Utasítsa el a **felhasználói kockázatot** a klasszikus Identity Protection állítja a szereplő a felhasználó kockázati előzmények az Identitásvédelem az **Azure AD.**

Utasítsa el a **felhasználói kockázatot** az Identitásvédelem ben, és a felhasználó kockázati előzményeiben az identitásvédelemben szereplő szereplőt a rendszergazda ** \<nevére állítja be, a felhasználó paneljéhez\>mutató hivatkozással.**

Van egy aktuális ismert probléma okozza a késést a felhasználói kockázat elbocsátási folyamat. Ha rendelkezik "Felhasználói kockázati házirenddel", akkor ez a házirend a "Felhasználói kockázat elvetése" gombra való kattintástól perceken belül leállítja az alkalmazási kérelmet az elbocsátott felhasználókra. Vannak azonban ismert késések a ux frissítése a "kockázati állapot" az elbocsátott felhasználók. Kerülő megoldásként frissítse az oldalt a böngésző szintjén, hogy láthassa a legújabb felhasználó "Kockázati állapot" felhasználóját.

## <a name="risky-users-report-known-issues"></a>A kockázatos felhasználók ismert problémákat jelentenek

A **felhasználónév** mezőlekérdezései a kis- és nagybetűket, míg a **Név** mezőben lévő lekérdezések a kis- és nagybetűket függetlenek.

Váltás **A dátumok megjelenítése elrejti** a KOCKÁZAT UTOLSÓ FRISSÍTÉS **oszlopát.** Az oszlop felolvasásához kattintson az **Oszlopok** elemre a Kockázatos felhasználók panel tetején.

A klasszikus identitásvédelem **összes eseményének elvetése** a kockázatészlelés állapotát **Lezárt (feloldva)** állapotra állítja.

## <a name="risky-sign-ins-report-known-issues"></a>A kockázatos bejelentkezések ismert problémákat jelentenek

A kockázatészlelés **feloldása** a **kockázatalapú házirend által vezérelt, átadott többszintű éves kortól felelős felhasználók**állapotát állítja be.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-is-a-user-is-at-risk"></a>Miért van veszélyben a felhasználó?

Ha Ön Egy Azure AD Identity Protection ügyfél, menjen a [kockázatos felhasználók](howto-identity-protection-investigate-risk.md#risky-users) megtekintéséhez, és kattintson a veszélyeztetett felhasználó. Az alsó fiókban a "Kockázati előzmények" fül mutatja az összes olyan eseményt, amely a felhasználói kockázat változásához vezetett. A felhasználó kockázatos bejelentkezései megtekintéséhez kattintson a "Felhasználó kockázatos bejelentkezései" lehetőségre. A felhasználó összes kockázatészlelésének megtekintéséhez kattintson a "Felhasználó kockázatészlelése" lehetőségre.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Hogyan kaphatok jelentést egy adott típusú észlelésről?

Nyissa meg a kockázatészlelések nézetet, és szűrje az "Észlelés típusa" szerint. Ezután letöltheti ezt a jelentést a alkalmazásba. CSV vagy . JSON formátumban a **letöltés** gomb tetején. További információt a [Hogyan: Kockázat vizsgálata](howto-identity-protection-investigate-risk.md#risk-detections)című témakörben talál.

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Miért nem állíthatok be saját kockázati szinteket minden egyes kockázatészleléshez?

Az Identitásvédelem kockázati szintjei az észlelés pontosságán alapulnak, és felügyelt gépi tanulásunk on-thed. A felhasználók által bemutatott élmény testreszabásához a rendszergazda bizonyos felhasználókat/csoportokat kivonhat/kizárhat a felhasználói kockázat- és bejelentkezési kockázati házirendekből.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Miért nem egyezik meg a bejelentkezés helye ott, ahonnan a felhasználó valóban bejelentkezett?

Az IP-földrajzi helymeghatározás az egész iparágra kiterjedő kihívást jelent. Ha úgy érzi, hogy a bejelentkezési jelentésben szereplő hely nem egyezik meg a tényleges helyével, forduljon a Microsoft támogatási szolgálatához. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Hogyan zárhatom le a konkrét kockázatészleléseket, mint a régi felhasználói felületen?

Visszajelzést adhat a kockázatészlelésről, ha megerősíti, hogy a csatolt bejelentkezés sérültként vagy biztonságosként van.You can give feedback on risk detections by confirming the linked sign-in as compromised or safe. A bejelentkezési visszajelzés a bejelentkezési műveletekhez a bejelentkezésen végzett összes észlelésig szivárog. Ha be szeretné zárni azokat az észleléseket, amelyek nem kapcsolódnak bejelentkezéshez, a felhasználói szinten visszajelzést adhat. További információ: Útmutató: Kockázati visszajelzés küldése az [Azure AD Identity Protection alkalmazásban című témakörben.](howto-identity-protection-risk-feedback.md)

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Milyen messzire mehetek vissza az időben, hogy megértsem, mi történik a felhasználómmal?

- A [kockázatos felhasználók](howto-identity-protection-investigate-risk.md#risky-users) nézet e felhasználó kockázati pozícióját mutatja az összes korábbi bejelentkezés ek alapján. 
- A kockázatos bejelentkezések nézetben az elmúlt 30 nap ban kijelentett jelek [láthatók.](howto-identity-protection-investigate-risk.md#risky-sign-ins) 
- A [kockázatészlelési](howto-identity-protection-investigate-risk.md#risk-detections) nézet az elmúlt 90 napban végzett kockázatészleléseket mutatja.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Hogyan tudhatok meg többet egy adott észlelésről?

Minden kockázatészlelések dokumentált a [cikkben Mi a kockázat](concept-identity-protection-risks.md#risk-types-and-detection). Az Azure Portalon található észlelési felület melletti (i) szimbólum fölé mutatva többet is megtudhat az észlelésről.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hogyan működnek az Identitásvédelem visszajelzési mechanizmusai?

**Ellenőrizze a feltört** (bejelentkezéskor) – Tájékoztatja az Azure AD Identity Protection, hogy a bejelentkezés nem hajtotta végre az identitás tulajdonosa, és azt jelzi, hogy a kompromisszum.

- A visszajelzés kézhezvételekor a bejelentkezési és felhasználói kockázati állapotot **megerősített kockázatnak,** a kockázati szintet pedig **magas**szintre tesszük át.

- Emellett gépi tanulási rendszereinknek is biztosítjuk az információkat a kockázatértékelés jövőbeni javítása érdekében.

    > [!NOTE]
    > Ha a felhasználó már ki van állítva, ne kattintson **a Feltört megerősítése gombra,** mert a bejelentkezési és felhasználói kockázati állapotot **megerősített feltörtállapotba,** a kockázati szintet pedig **magasszintre**helyezi.

**Erősítse meg** a biztonságos (a bejelentkezés) – tájékoztatja az Azure AD Identity Protection, hogy a bejelentkezést hajtotta végre az identitás tulajdonosa, és nem jelent biztonsági feltörést.

- A visszajelzés kézhezvételekor a bejelentkezési (nem a felhasználó) kockázati állapotát **-** a Megerősített **biztonságos** szintre, a kockázati szintet pedig a rendszerre tesszük át.

- Emellett gépi tanulási rendszereinknek is biztosítjuk az információkat a kockázatértékelés jövőbeni javítása érdekében.

    > [!NOTE]
    > Ha úgy gondolja, hogy a felhasználó nem sérült, használja **a felhasználói kockázat elvetése** a felhasználói szinten használata helyett **megerősített biztonságos** a bejelentkezési szinten. A Utasítsa el a **felhasználói kockázatot** a felhasználói szinten bezárja a felhasználói kockázatot, és az összes korábbi kockázatos bejelentkezések és a kockázatészlelések.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Miért látok egy olyan felhasználót, akinek alacsony (vagy magasabb) kockázati pontszáma van, még akkor is, ha nem jelennek meg kockázatos bejelentkezések vagy kockázatészlelések az Identitásvédelemben?

Mivel a felhasználói kockázat kumulatív jellegű, és nem jár le, a felhasználó alacsony vagy magasabb szintű felhasználói kockázattal járhat, még akkor is, ha az Identitásvédelemben nem jelennek meg a közelmúltban kockázatos bejelentkezések vagy kockázatészlelések. Ez a helyzet akkor fordulhat elő, ha a felhasználó egyetlen rosszindulatú tevékenysége azon az időkereten túl történt, amelyre a kockázatos bejelentkezések és a kockázatészlelések adatait tároljuk. Nem járle a felhasználói kockázat, mert a rossz szereplők már ismert, hogy maradjon az ügyfelek környezetében több mint 140 nap mögött egy feltört identitás, mielőtt ramping fel a támadást. Az ügyfelek áttekinthetik a felhasználó kockázati idővonalát, hogy megértsék, miért van veszélyben a felhasználó a következő:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Miért van egy bejelentkezési egy "bejelentkezési kockázat (összesített)" pontszám magas, ha a hozzá társított észlelések alacsony vagy közepes kockázatú?

A magas összesített kockázati pontszám alapulhat a bejelentkezés más funkcióin, vagy azon a tényen, hogy egynél több észlelési indított, hogy a bejelentkezés. És fordítva, a bejelentkezési lehet egy bejelentkezési kockázat (összesített) a közepes akkor is, ha a bejelentkezéshez társított észlelések magas kockázatú. 
