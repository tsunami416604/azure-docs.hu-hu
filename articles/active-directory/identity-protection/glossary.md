---
title: Az Azure Active Directory Identity Protection-szószedet |} A Microsoft Docs
description: Azure Active Directory Identity Protection Glossary
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend, szószedet kezelése
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 7dbb127c14b8a4d809d82cf14eb0cf76c1c4698b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478348"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection Glossary
### <a name="at-risk-user"></a>Veszélyben (felhasználó)
Egy felhasználó egy vagy több aktív kockázati eseményekhez. 

### <a name="atypical-sign-in-location"></a>Szokatlan bejelentkezési helye
Egy jelentkezzen be, amely nem jellemző, a konkrét felhasználó, hasonló felhasználók vagy a bérlő földrajzi helyről.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Biztonsági modul az Azure Active Directory kockázati események és a szervezet identitásait érintő lehetséges biztonsági résekről nézetet.

### <a name="conditional-access"></a>Feltételes hozzáférés
A szabályzat az erőforrásokhoz való hozzáférés biztonságossá tételéhez. Feltételes hozzáférési szabályok vannak tárolva az Azure Active Directoryban, és értékeli ki az Azure AD az erőforráshoz való hozzáférést megelőzően.  Példa szabály jogosultságkészlet hozzáférést a felhasználó helye, Eszközállapot, vagy felhasználói hitelesítési módszert.

### <a name="credentials"></a>Hitelesítő adatok
Azonosító és a hozzáférés a helyi és a hálózati erőforrásokhoz használt azonosító adatokat. Hitelesítő adatok példák a felhasználónevek és jelszavak, intelligens kártyák és tanúsítványok.

### <a name="event"></a>Esemény
Egy rekord egy tevékenység az Azure Active Directoryban.

### <a name="false-positive-risk-event"></a>Vakriasztások (kockázati esemény)
Az Identity Protection felhasználói, jelezve, hogy a kockázati esemény vizsgálták, és nem megfelelően lett megjelölt egy kockázati esemény által manuálisan beállított kockázati esemény állapota.

### <a name="identity"></a>Identitás
Egy személy vagy jogalany, történő hitelesítést, például a jelszó vagy tanúsítvány feltételek alapján, ellenőrizni kell.

### <a name="identity-risk-event"></a>Identitás kockázati esemény
AAD-esemény tanul Identity Protection által lett megjelölve, és arra utalhat, hogy az identitás biztonsága sérült.

### <a name="ignored-risk-event"></a>A rendszer figyelmen kívül hagyja (kockázati esemény)
Egy kockázati esemény állapotának manuálisan állította Identity Protection felhasználóként, amely azt jelzi, hogy a kockázati esemény anélkül, hogy a javítási művelet befejeződik.

### <a name="impossible-travel-from-atypical-locations"></a>Bejelentkezés szokatlan helyekről való lehetetlen utazás
Egy kockázati esemény aktiválódik, ha ugyanaz a felhasználó két bejelentkezést észlelt, amennyiben legalább az egyik helyről egy szokatlan bejelentkezési, és ahol a bejelentkezések között eltelt idő rövidebb, mint a minimális időt vesz igénybe a fizikailag továbbítani a helyek között.  

### <a name="investigation"></a>Vizsgálat
Tekintse át a tevékenységeket, naplók és egyéb kapcsolódó információkat, döntse el, hogy szervizelési és kockázatcsökkentési lépések szükségesek, hogy egy kockázati esemény kapcsolatos folyamata ismertetése Ha, és hogyan az identitás feltörték, és megismerheti a identitás biztonsága sérült lett megadva.

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok
Egy kockázati esemény aktiválódik, ha az aktuális felhasználói hitelesítő adatok (felhasználónév és jelszó) találhatók, nyilvánosan a sötét webes által közzétett a kutatók munkáját.

### <a name="mitigation"></a>Kezelés
Egy műveletet korlátozható vagy megszüntetheti a támadó lehetőségét kihasználni egy sérült biztonságú identitás vagy az eszköz visszaállítja a identitás vagy az eszköz biztonságos állapotban. A megoldás nem oldja meg a identitás vagy az eszköz társított előző kockázati események.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Két vagy több hitelesítési módszereket, amelyek magukban foglalhatják valami igényel a felhasználó hitelesítési módszerként, az ilyen tanúsítvánnyal rendelkezik; Hiba a felhasználó ismer, például felhasználónevek, jelszavak vagy pass mondatok; fizikai attribútumait, például egy ujjlenyomatot; és a személyes attribútumait, például a személyes aláírás.

### <a name="offline-detection"></a>A kapcsolat nélküli észlelése
Az észlelés a rendellenességeket és értékelés céljából használják a kockázat egy esemény, például a bejelentkezési kísérlet az a tény, az esemény bekövetkezése után.

### <a name="policy-condition"></a>A házirend feltétele
Biztonsági szabályzat, amely meghatározza az entitásokat (csoportok, felhasználók, alkalmazások, az eszközplatformokat, Eszközállapotok, IP-címtartományok, ügyféltípusok) része a házirendben, vagy abból kizárni.

### <a name="policy-rule"></a>Szabályzatbeli szabály
Egy biztonsági szabályzatot, amely ismerteti a körülmények, indítsa el a szabályzatot, és a szabályzat akkor aktiválódik, amikor szinteknél végrehajtott műveleteket a része.

### <a name="prevention"></a>Megelőzés
A szervezethez való visszaélés keresztül identitás vagy eszköz sérülés elkerülése érdekében művelet gyanús, vagy tudja, hogy sérülhet. A megelőzési művelet nem védi meg az eszköz vagy az identitást, és nem oldja meg az előző kockázati események.

### <a name="privileged-user"></a>Kiemelt (felhasználó)
A felhasználó, amely egy kockázati esemény idején egy vagy több erőforrás-állandó vagy ideiglenes rendszergazdai jogosultságokkal rendelkezik az Azure Active Directoryban, például a globális rendszergazda, számlázási rendszergazda, szolgáltatás-rendszergazda, felhasználói rendszergazdája és rendszergazdai jelszót. 

### <a name="real-time"></a>Valós idejű
Tekintse meg a valós idejű észlelését.

### <a name="real-time-detection"></a>Valós idejű észlelése
Folytassa a tegye a rendellenességek észlelését, és a egy eseményt, például a bejelentkezési kísérlet az esemény előtt kockázatát próbaidőszakában engedélyezett.

### <a name="remediated-risk-event"></a>Kijavítva (kockázati esemény)
Egy kockázati esemény állapotának automatikusan állítja az Identity Protection jelzi, hogy a kockázati esemény szervizelt standard javítási művelet használatával az ilyen típusú kockázati esemény volt. Például amikor a felhasználó jelszavát, az számos kockázati események, amelyek jelzik, hogy feltörték-e a korábbi jelszót automatikusan megtörténjen.

### <a name="remediation"></a>Szervizelés
Egy művelet biztonságos identitás vagy egy eszköz, amely korábban gyanít vagy ismert legyen feltörni. A javítási művelet visszaállítja a identitás vagy az eszköz biztonságos állapotban, és oldja fel az előző kockázati események a identitás vagy az eszköz társítva.

### <a name="resolved-risk-event"></a>Megoldott (kockázati esemény)
Egy kockázati esemény állapotának az Identity Protection felhasználó manuálisan állította, jelezve, hogy a felhasználó elvégez egy megfelelő korrekciós műveletet identitásvédelem kívül, és kell tekinteni a kockázati esemény lezárva.

### <a name="risk-event-status"></a>Kockázati esemény állapota
Egy kockázati esemény tulajdonsága, amely azt jelzi, hogy aktív-e az eseményt, és ha zárva, akkor lezárásának okát.

### <a name="risk-event-type"></a>Kockázati esemény típusa
A kockázati esemény, a kockázatos venni az eseményt kiváltó anomáliadetektálási típusának egy kategóriát.

### <a name="risk-level-risk-event"></a>Kockázati szint (kockázati esemény)
A szervezet kockázatának csökkentése érdekében egy (magas, közepes vagy alacsony), az Identity Protection dolgozó felhasználók rangsorolhatja a műveletek a kockázati esemény súlyosságát jelzi. 

### <a name="risk-level-sign-in"></a>Kockázati szint (sign-in)
Jelzése (magas, közepes vagy alacsony) annak a valószínűségét, hogy egy adott bejelentkezéshez, valaki más próbálja használni a felhasználó identitását.

### <a name="risk-level-user-compromise"></a>Kockázati szint (a felhasználó biztonsági sérülés)
Jelzése (magas, közepes vagy alacsony) annak a valószínűségét, hogy az identitás biztonsága sérült.

### <a name="risk-level-vulnerability"></a>Kockázati szint (biztonsági)
Az Identity Protection dolgozó felhasználók rangsorolhatja a műveletek a biztonsági rés súlyossága megjelölése (magas, közepes vagy alacsony) tartanak kockázatának csökkentése érdekében a szervezet számára.

### <a name="secure-identity"></a>Biztonságos (identitás)
Például a jelszó módosítása vagy a gépet egy esetleg feltört identitás-tanúsítványlánchoz állapotának visszaállításához rendszerképének alaphelyzetbe állítása javítási művelet végrehajtása.

### <a name="security-policy"></a>Biztonsági házirend
Szabályok és feltétel gyűjteménye. Egy házirend például a felhasználók, csoportok, alkalmazások, eszközök, az eszközplatformokat, Eszközállapotok, IP-címtartományok és Auth2.0 ügyféltípusok is alkalmazható. Ha egy szabályzat engedélyezve van, értékeli, amikor a házirendben entitás egy erőforráshoz egy tokent ad ki.

### <a name="sign-in-v"></a>Jelentkezzen be a (v)
Az Azure Active Directory-identitásra való hitelesítéséhez.

### <a name="sign-in-n"></a>Sign-in (n)
A folyamat vagy az Azure Active Directory és az esemény, amely a művelet rögzíti az identitás hitelesítésére művelet.

### <a name="sign-in-from-anonymous-ip-address"></a>Bejelentkezés névtelen IP-címről
Egy kockázati esemény indul el, a sikeres bejelentkezést követően a névtelen proxy IP-címként azonosított IP-címet.

### <a name="sign-in-from-infected-device"></a>Bejelentkezés fertőzött eszközről
Egy kockázati esemény aktiválódik, ha egy bejelentkezési alhálózatából származó IP-címet, amelyről ismert, hogy az egy vagy több feltört eszközök, amelyek aktívan próbál egy robot-kiszolgálóval való kommunikációhoz használható.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Bejelentkezés gyanús tevékenységeket mutató IP-címről
Egy kockázati esemény indul el, miután a bejelentkezés sikeres IP-cím a nagy számú sikertelen bejelentkezési kísérlet között több felhasználói fiókot egy rövid időn át.

### <a name="sign-in-from-unfamiliar-location"></a>Bejelentkezés ismeretlen helyről
Egy kockázati esemény aktiválódik, ha egy felhasználó sikeresen jelentkezik be egy új helyről (IP szélességi és hosszúsági koordinátákkal és ASN-t).

### <a name="sign-in-risk"></a>Bejelentkezési kockázat
Tekintse meg a kockázati szint (sign-in)

### <a name="sign-in-risk-policy"></a>Bejelentkezési kockázati házirend
Feltételes hozzáférési szabályzatot, amely egy adott bejelentkezési kockázat és előre meghatározott feltételeknek és szabályoknak az alapján kockázatcsökkentő vonatkozik.

### <a name="user-compromise-risk"></a>Felhasználói biztonsági kockázat
Tekintse meg a kockázati szint (a felhasználó biztonsági sérülés)

### <a name="user-risk"></a>Felhasználói kockázat
Tekintse meg a kockázati szint (a felhasználó biztonsági sérülés).

### <a name="user-risk-policy"></a>Felhasználói kockázati házirend
Feltételes hozzáférési szabályzatot, amely úgy ítéli meg, a bejelentkezés, és előre meghatározott feltételeknek és szabályoknak az alapján kockázatcsökkentő vonatkozik.

### <a name="users-flagged-for-risk"></a>Kockázatosként megjelölt felhasználók
Kockázati események, amely aktív vagy elhárított rendelkező felhasználók

### <a name="vulnerability"></a>Biztonsági rés
A konfiguráció vagy a feltétel az Azure Active Directoryban, ami lehetővé teszi a könyvtár ki van téve a biztonsági rések és fenyegetések.

## <a name="see-also"></a>Lásd még
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

