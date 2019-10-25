---
title: A kockázatok javítása és a felhasználók tiltásának feloldása Azure AD Identity Protection
description: Ismerje meg az aktív kockázatok észlelésével kapcsolatos lehetőségeket.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61fe0e14fbb6c6251b3bf19843b9c065df60dd86
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887519"
---
# <a name="remediate-risks-and-unblock-users"></a>Kockázatok szervizelése és a felhasználók blokkolásának feloldása

A [vizsgálat](howto-identity-protection-investigate-risk.md)befejezése után el kell végeznie a kockázat javítását vagy a felhasználók blokkolásának feloldását. A szervezeteknek lehetősége van arra is, hogy az automatizált szervizelést is engedélyezzék a [kockázati szabályzatok](howto-identity-protection-configure-risk-policies.md)használatával. A szervezeteknek meg kell próbálnia lezárva az összes olyan kockázati észlelést, amelyet az adott időszakban a szervezete számára kényelmesnek tart. A Microsoft a lehető legrövidebb időn belül javasolja az események bezárását, mivel a kockázatok kezelésekor az idő számít.

## <a name="remediation"></a>Szervizkiszolgáló

Minden aktív kockázati észlelés hozzájárul a felhasználói kockázati szint nevű érték kiszámításához. A felhasználói kockázati szint egy kijelző (alacsony, közepes és magas), amely a fiók biztonságának valószínűségét jelzi. Rendszergazdaként az összes kockázati észlelést le szeretné állítani, hogy az érintett felhasználók többé ne legyenek kitéve.

Bizonyos veszélyforrások észlelését az Identity Protection "lezárt (rendszer)" jelöléssel jelöli, mert az események már nem a kockázatnak vannak meghatározva.

A rendszergazdák a következő lehetőségek közül választhatnak:

- Önszervizelés kockázati házirenddel
- Jelszó manuális alaphelyzetbe állítása
- Felhasználói kockázat elvetése
- Egyéni kockázati észlelések manuális lezárása

### <a name="self-remediation-with-risk-policy"></a>Önszervizelés kockázati házirenddel

Ha lehetővé teszi a felhasználók számára az önkiszolgálást, az Azure Multi-Factor Authentication (MFA) és az önkiszolgáló jelszó-visszaállítást (SSPR) az Ön kockázati házirendjeiben, akkor feloldhatók a kockázatok észlelése esetén. Ezeket az észleléseket a rendszer lezártnak tekinti. A felhasználóknak előzőleg regisztrálniuk kell az Azure MFA-t és a SSPR-t ahhoz, hogy a rendszer felderítse a kockázatot.

Előfordulhat, hogy bizonyos észlelések nem növelik a kockázatokat arra a szintre, ahol a felhasználó önszervizelése kötelező, de a rendszergazdáknak továbbra is értékelniük kell ezeket az észleléseket. A rendszergazdák meghatározhatják, hogy további mértékek szükségesek, például [blokkolja a hozzáférést a helyekről](../conditional-access/howto-conditional-access-policy-location.md) , vagy csökkentheti a szabályzatok elfogadható kockázatát.

### <a name="manual-password-reset"></a>Jelszó manuális alaphelyzetbe állítása

Ha a jelszó alaphelyzetbe állítása a felhasználói kockázati házirend használatával nem lehetséges, a rendszergazdák egy manuális jelszó-visszaállítással zárhatók ki az összes kockázati észlelést a felhasználó számára.

A rendszergazdák két lehetőséget kapnak a felhasználók jelszavának alaphelyzetbe állításakor:

- **Ideiglenes jelszó létrehozása** – ideiglenes jelszó létrehozásával azonnal visszaállíthatja az identitást biztonságos állapotba. Ehhez a metódushoz kapcsolatba kell lépnie az érintett felhasználókkal, mert tudniuk kell, hogy mi az ideiglenes jelszó. Mivel a jelszó ideiglenes, a rendszer a következő bejelentkezéskor kéri a felhasználót, hogy változtassa meg a jelszót valami újat.

- **A jelszó alaphelyzetbe állításának megkövetelése a felhasználótól** – a jelszavak alaphelyzetbe állítását igénylő felhasználók számára lehetővé teszi az önhelyreállítást az ügyfélszolgálat vagy a rendszergazda segítsége nélkül Ez a módszer csak az Azure MFA és a SSPR szolgáltatásban regisztrált felhasználókra vonatkozik. A nem regisztrált felhasználók esetében ez a lehetőség nem érhető el.

### <a name="dismiss-user-risk"></a>Felhasználói kockázat elvetése

Ha a jelszó alaphelyzetbe állítása nem lehetséges, mert például a felhasználó törölve lett, dönthet úgy, hogy elveti a felhasználói kockázatok észlelését.

Ha a **felhasználói kockázat elvetése**gombra kattint, az összes esemény be van zárva, és az érintett felhasználó már nem veszélyeztetett. Mivel azonban ez a módszer nem befolyásolja a meglévő jelszót, nem vonja vissza a kapcsolódó identitást biztonságos állapotba. 

### <a name="close-individual-risk-detections-manually"></a>Egyéni kockázati észlelések manuális lezárása

Az egyes kockázati észleléseket manuálisan is lezárhatja. A kockázati észlelések manuális bezárásával csökkentheti a felhasználói kockázati szintet. A kockázati észlelések általában manuálisan vannak bezárva egy kapcsolódó vizsgálatra válaszul. Ha például egy felhasználóval beszél, az aktív kockázatok észlelése többé nem szükséges. 
 
A kockázati észlelések manuális bezárásakor dönthet úgy, hogy a következő műveletek bármelyikét elvégezte a kockázatkezelés állapotának módosításához:

- A felhasználó biztonságának megerősítése
- Felhasználói kockázat elvetése
- Biztonságos bejelentkezés megerősítése
- A bejelentkezés biztonságának megerősítése

## <a name="unblocking-users"></a>Felhasználók blokkolásának feloldása

A rendszergazdák dönthetnek úgy, hogy a kockázatkezelési házirend vagy a vizsgálatok alapján blokkolják a bejelentkezést. A bejelentkezés vagy a felhasználói kockázat alapján egy blokk is előfordulhat.

### <a name="unblocking-based-on-user-risk"></a>Felhasználói kockázat alapján történő blokkolás feloldása

A felhasználói kockázat miatt letiltott fiókok blokkolásának feloldásához a rendszergazdák a következő lehetőségek közül választhatnak:

1. **Jelszó alaphelyzetbe állítása** – alaphelyzetbe állíthatja a felhasználó jelszavát.
1. **Felhasználói kockázat elvetése** – a felhasználói kockázati házirend letilt egy felhasználót, ha elérte a hozzáférés-blokkoló beállításhoz beállított felhasználói kockázati szintet. Csökkentheti a felhasználók kockázati szintjét a felhasználói kockázat megszüntetésével vagy a jelentett kockázati észlelések manuális lezárásával.
1. **A felhasználó kizárása a szabályzatból** – ha úgy gondolja, hogy a bejelentkezési szabályzat jelenlegi konfigurációja problémákat okoz bizonyos felhasználók számára, kizárhatja a felhasználókat. További információ [: a kockázatkezelési házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md#exclusions)című cikk kizárásai.
1. **Házirend letiltása** – ha úgy gondolja, hogy a házirend-konfiguráció az összes felhasználónál problémát okoz, letilthatja a szabályzatot. További információt a [kockázatkezelési házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md)című cikkben talál.

### <a name="unblocking-based-on-sign-in-risk"></a>A bejelentkezési kockázat alapján történő blokkolás feloldása

A bejelentkezési kockázaton alapuló fiókok blokkolásának feloldásához a rendszergazdák a következő lehetőségek közül választhatnak:

1. **Bejelentkezés ismerős helyről vagy eszközről** – a tiltott gyanús bejelentkezések gyakori oka az ismeretlen helyekről vagy eszközökről érkező bejelentkezési kísérletek. A felhasználók gyorsan meghatározhatják, hogy ez az ok a blokkolás oka, ha megpróbál bejelentkezni egy ismerős helyről vagy eszközről.
1. **A felhasználó kizárása a szabályzatból** – ha úgy gondolja, hogy a bejelentkezési szabályzat jelenlegi konfigurációja problémákat okoz bizonyos felhasználók számára, kizárhatja a felhasználókat. További információ [: a kockázatkezelési házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md#exclusions)című cikk kizárásai.
1. **Házirend letiltása** – ha úgy gondolja, hogy a házirend-konfiguráció az összes felhasználónál problémát okoz, letilthatja a szabályzatot. További információt a [kockázatkezelési házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md)című cikkben talál.

## <a name="next-steps"></a>Következő lépések

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview-identity-protection.md).
