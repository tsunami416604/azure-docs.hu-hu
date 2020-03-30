---
title: A kockázatok elhárítása és a felhasználók blokkolásának feloldása az Azure AD Identity Protection szolgáltatásban
description: Ismerje meg azokat a beállításokat, amelyekkel az aktív kockázatészlelések vannak.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382104"
---
# <a name="remediate-risks-and-unblock-users"></a>Kockázatok elhárítása és a felhasználók blokkolásának feloldása

A [vizsgálat](howto-identity-protection-investigate-risk.md)befejezése után lépéseket kell tennie a kockázat elhárítására vagy a felhasználók blokkolásának feloldására. A szervezeteknek lehetőségük van arra is, hogy [kockázati irányelveik](howto-identity-protection-configure-risk-policies.md)használatával automatikus szervizelést tegyenek lehetővé. A szervezeteknek meg kell próbálniuk lezárni az összes olyan kockázatészlelést, amelyet a szervezet számára kényelmes en-próba időszakon belül mutatnak be. A Microsoft azt javasolja, hogy a lehető leghamarabb zárják le az eseményeket, mert az idő számít a kockázatokkal való munka során.

## <a name="remediation"></a>Kockázatcsökkentés

Minden aktív kockázatészlelés hozzájárul a felhasználói kockázati szintnek nevezett érték kiszámításához. A felhasználói kockázati szint egy mutató (alacsony, közepes, magas) annak a valószínűségének, hogy egy fiók feltört. Rendszergazdaként szeretné, hogy az összes kockázatészlelések zárva, hogy az érintett felhasználók már nincsenek veszélyben.

Egyes kockázatok észlelések megjelölheti identity protection a "Zárt (rendszer)", mert az események már nem határozták meg, hogy kockázatos.

A rendszergazdák nak a következő lehetőségei kvandát választhatnak:

- Önrehabilitáció kockázati politikával
- Manuális jelszó-visszaállítás
- Felhasználói kockázat elvetése
- Az egyéni kockázatészlelések manuális lezárása

### <a name="self-remediation-with-risk-policy"></a>Önrehabilitáció kockázati politikával

Ha engedélyezi a felhasználóknak, hogy önjavító, az Azure multi-factor authentication (MFA) és az önkiszolgáló jelszó-visszaállítás (SSPR) a kockázati szabályzatok, akkor feloldása magukat, ha kockázatot észlel. Ezeket az észleléseket ezután lezártnak tekintik. A felhasználóknak korábban regisztrálniuk kell az Azure MFA-hoz és az SSPR-hez, hogy kockázat észlelésekor használhassák.

Előfordulhat, hogy egyes észlelések nem növelik a kockázatot arra a szintre, ahol a felhasználó önszervizelésre lenne szükség, de a rendszergazdáknak továbbra is értékelniük kell ezeket az észleléseket. A rendszergazdák megállapíthatják, hogy további intézkedésekre van szükség, például le kell zárni a [helyekről való hozzáférést,](../conditional-access/howto-conditional-access-policy-location.md) vagy csökkenteni kell az elfogadható kockázatot a szabályzatukban.

### <a name="manual-password-reset"></a>Manuális jelszó-visszaállítás

Ha a felhasználói kockázati házirend használatával történő jelszó-alaphelyzetbe állítás nem lehetséges, a rendszergazdák manuális jelszó-alaphelyzetbe állítással zárhatják le a felhasználó összes kockázatészlelését.

A rendszergazdák két lehetőséget kapnak a felhasználók jelszavainak alaphelyzetbe állításakor:

- **Ideiglenes jelszó létrehozása** – Ideiglenes jelszó létrehozásával azonnal visszaállíthatja az identitást biztonságos állapotba. Ehhez a módszerhez kapcsolatba kell lépniaz érintett felhasználókkal, mert tudniuk kell, hogy mi az ideiglenes jelszó. Mivel a jelszó ideiglenes, a következő bejelentkezés során a rendszer kéri, hogy módosítsa a jelszót valami újra.

- **A felhasználó nak a jelszó alaphelyzetbe állítása** – A jelszavak alaphelyzetbe állításának megkövetelése lehetővé teszi az önhelyreállítást anélkül, hogy kapcsolatba lépne az ügyfélszolgálattal vagy a rendszergazdával. Ez a módszer csak az Azure MFA és SSPR regisztrált felhasználókra vonatkozik. A még nem regisztrált felhasználók esetében ez a lehetőség nem érhető el.

### <a name="dismiss-user-risk"></a>Felhasználói kockázat elvetése

Ha a jelszó alaphelyzetbe állítása nem lehetséges, mert például a felhasználót törölték, dönthet úgy, hogy elveti a felhasználói kockázatészleléseket.

Ha a **Felhasználói kockázat elvetése gombra**kattint, minden esemény lezárul, és az érintett felhasználó már nincs veszélyben. Mivel azonban ez a módszer nincs hatással a meglévő jelszóra, nem hozza vissza a kapcsolódó identitást biztonságos állapotba. 

### <a name="close-individual-risk-detections-manually"></a>Az egyéni kockázatészlelések manuális lezárása

Az egyes kockázatészlelések manuálisan is lezárhatók. A kockázatészlelések manuális bezárásával csökkentheti a felhasználói kockázati szintet. A kockázatészlelést általában manuálisan zárják le egy kapcsolódó vizsgálatra válaszul. Például, ha beszél a felhasználó azt mutatja, hogy az aktív kockázatészlelése már nem szükséges. 
 
A kockázatészlelések manuális lezárásakor az alábbi műveletek bármelyikét végrehajthatja a kockázatészlelés állapotának módosításához:

- A felhasználó feltörésének ellenőrzése
- Felhasználói kockázat elvetése
- Bejelentkezési biztonság megerősítése
- A bejelentkezés biztonságának ellenőrzése

## <a name="unblocking-users"></a>Felhasználók blokkolásának feloldása

A rendszergazda dönthet úgy, hogy letiltja a bejelentkezést a kockázati szabályzatuk vagy a vizsgálataik alapján. A blokkolás előfordulhat a bejelentkezés vagy a felhasználói kockázat alapján.

### <a name="unblocking-based-on-user-risk"></a>A blokkolás feloldása a felhasználói kockázat alapján

A felhasználói kockázat miatt letiltott fiókok blokkolásának feloldásához a rendszergazdák a következő lehetőségek közül választhatnak:

1. **Jelszó alaphelyzetbe állítása** – A felhasználó jelszavát alaphelyzetbe állíthatja.
1. **Felhasználói kockázat elvetése** – A felhasználói kockázati házirend blokkolja a felhasználót, ha elérte a hozzáférés blokkolására beállított felhasználói kockázati szintet. Csökkentheti a felhasználó kockázati szintjét a felhasználói kockázat elvetésével vagy a jelentett kockázatészlelések manuális bezárásával.
1. **A felhasználó kizárása a házirendből** – Ha úgy gondolja, hogy a bejelentkezési házirend aktuális konfigurációja problémákat okoz az egyes felhasználók számára, kizárhatja belőle a felhasználókat. További információt a Kizárások című témakörkizárások című témakörben található [Útmutató: Kockázati házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md#exclusions)című témakörben talál.
1. **Házirend letiltása** – Ha úgy gondolja, hogy a házirend-konfiguráció okozza a problémákat az összes felhasználó számára, letilthatja a házirendet. További információt a [Hogyan: Kockázati házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md)című témakörben talál.

### <a name="unblocking-based-on-sign-in-risk"></a>A bejelentkezési kockázat alapján a blokkolás feloldása

A bejelentkezési kockázaton alapuló fiók blokkolásának feloldásához a rendszergazdák a következő lehetőségek közül választhatnak:

1. **Bejelentkezés ismerős helyről vagy eszközről** – A letiltott gyanús bejelentkezések gyakori oka ismeretlen helyekről vagy eszközökről érkező bejelentkezési kísérletek. A felhasználók gyorsan megállapíthatják, hogy ez-e az oka annak, hogy megpróbálnak bejelentkezni egy ismerős helyről vagy eszközről.
1. **A felhasználó kizárása a házirendből** – Ha úgy gondolja, hogy a bejelentkezési házirend aktuális konfigurációja problémákat okoz az egyes felhasználók számára, kizárhatja belőle a felhasználókat. További információt a Kizárások című témakörkizárások című témakörben található [Útmutató: Kockázati házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md#exclusions)című témakörben talál.
1. **Házirend letiltása** – Ha úgy gondolja, hogy a házirend-konfiguráció okozza a problémákat az összes felhasználó számára, letilthatja a házirendet. További információt a [Hogyan: Kockázati házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintéséhez tekintse meg az [Azure AD Identity Protection áttekintését.](overview-identity-protection.md)
