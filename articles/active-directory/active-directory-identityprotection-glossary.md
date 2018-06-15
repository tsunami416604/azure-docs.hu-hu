---
title: Az Azure Active Directory-Identity Protection-szószedet |} Microsoft Docs
description: Azure Active Directory Identity Protection Glossary
services: active-directory
keywords: az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend, szószedet kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a9cc4a914091c91f4b2ae1c152644fe8e01747e8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713391"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection Glossary
### <a name="at-risk-user"></a>Fennáll a veszélye (felhasználó)
A felhasználó egy vagy több aktív kockázati események. 

### <a name="atypical-sign-in-location"></a>Bejelentkezés szokatlan bejelentkezési helye
A bejelentkezés, amely nincs az adott felhasználó, hasonló felhasználók vagy a bérlő a jellemző földrajzi helyről.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
A biztonsági modul az Azure Active Directoryban, amely a kockázati eseményekről és egy szervezet identitásait érintő lehetséges biztonsági rések egyesített nézetét biztosítja.

### <a name="conditional-access"></a>Feltételes hozzáférés
Egy házirend erőforrásokhoz való hozzáférés biztosítása érdekében. Feltételes hozzáférési szabályai tárolódnak az Azure Active Directory és az erőforráshoz való hozzáférés megadása előtt az Azure AD értékeli ki.  Például a szabályok a következők korlátozza a hozzáférést a felhasználó helye, a Eszközállapot vagy a felhasználó hitelesítési módszer alapján.

### <a name="credentials"></a>Hitelesítő adatok
Azonosításra és igazolása helyi való hozzáférés és a hálózati erőforrásokhoz való használt azonosító adatokat. A hitelesítő adatok többek között a felhasználónevek és jelszavak, az intelligens kártyák és a tanúsítványok.

### <a name="event"></a>Esemény
Az Azure Active Directoryban tevékenység egy olyan rekordot.

### <a name="false-positive-risk-event"></a>A vakriasztások (kockázat esemény)
A kockázat eseményállapot manuálisan Identity Protection felhasználó, amely azt jelzi, hogy a kockázati esemény történt a vizsgált, és a kockázati események megjelölt helytelenül lett beállítva.

### <a name="identity"></a>Identitás
Személy vagy vállalat hitelesítést, például a jelszóval vagy tanúsítvánnyal feltétel alapján történő ellenőrizni kell.

### <a name="identity-risk-event"></a>Identitás kockázat esemény
Az AAD esemény Identity Protection által rendellenes tevékenységként lett megjelölve, és arra utalhat, hogy identitás feltörték.

### <a name="ignored-risk-event"></a>Figyelmen kívül hagyva (kockázat esemény)
A kockázat eseményállapot állítsa be manuálisan egy Identity Protection felhasználónak, amely azt jelzi, hogy a kockázati esemény nélkül a szervizelési művelet befejeződik.

### <a name="impossible-travel-from-atypical-locations"></a>Lehetetlen odautazás bejelentkezés szokatlan helyekről
A kockázat ugyanaz a felhasználó két bejelentkezést észlelt, amennyiben legalább az egyik helyről a rendellenes bejelentkezési, és egyes esetekben a bejelentkezések közötti ideje rövidebb, mint a minimális időbe telne között fizikailag továbbítani által elindított esemény.  

### <a name="investigation"></a>Vizsgálat
A tevékenységek, a naplókat, és olyan releváns információkat eldöntheti, hogy szervizelési vagy enyhítési lépések szükségesek, a kockázat eseményhez kapcsolódó véleményezési eljárás megértéséhez, ha, és hogyan identitásának biztonsági szempontból sérült, és a sérült biztonságú identitás használatának megismerése.

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok
A kockázat aktuális felhasználói hitelesítő adatok (felhasználónév és jelszó) találhatók, a kutatói által nyilvánosan sötét webes közzétéve által elindított esemény.

### <a name="mitigation"></a>Kezelés
Egy műveletet korlátozható vagy kiküszöbölheti a támadó lehetőségét a sérült biztonságú identitás vagy az eszköz visszaállítja a identitás vagy az eszköz biztonságos kihasználásához. A megoldás nem oldja meg a identitás vagy az eszköz társított előző kockázati eseményekről.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
A hitelesítési módszert, amelyhez két vagy több hitelesítési módszert, amelyek magukban foglalhatják valamit a felhasználó rendelkezik, ez a tanúsítvány; valamit a felhasználó ismer, például felhasználóneveket, jelszavakat vagy fázis kifejezések; fizikai attribútumait, például egy ujjlenyomat; és személyes attribútumait, például a személyes aláírás.

### <a name="offline-detection"></a>Kapcsolat nélküli észlelése
A rendellenességek észlelését és az esemény például a bejelentkezési kísérlet kockázatát értékelése után a tényt, az esemény, amely már elvégzett észlelését.

### <a name="policy-condition"></a>Házirend feltétel
Egy olyan biztonsági házirendet, amely meghatározza az entitások (csoportok, felhasználók, alkalmazások, eszközök, eszközök állapotai, IP-címtartományok, ügyféltípusokat) egy része szerepel a házirendben, illetve tiltani szeretné a azt.

### <a name="policy-rule"></a>Szabályzatbeli szabály
Egy olyan biztonsági házirendet, amely leírja a körülmények között, indítsa el a szabályzatot, és a házirend kiváltásakor végrehajtott műveleteket részét.

### <a name="prevention"></a>Megelőzés
Ahhoz, hogy a sérülés visszaélés identitás vagy eszköz a szervezet a művelet gyanús, vagy tudja, hogy utaló jeleket. Egy megelőzési művelet nem biztonságos, az eszköz vagy az identitás, és nem oldja meg az előző kockázati eseményekről.

### <a name="privileged-user"></a>Kiemelt (felhasználó)
Egy olyan felhasználó, a kockázat esemény időpontjában volt egy vagy több erőforrásokra állandó vagy ideiglenes rendszergazda engedélyeit az Azure Active Directoryban, például egy globális rendszergazda számlázási rendszergazda, a szolgáltatás-rendszergazdát, a felhasználó rendszergazda és a jelszókezelő. 

### <a name="real-time"></a>Valós idejű
Tekintse meg a valós idejű észlelése.

### <a name="real-time-detection"></a>Valós idejű észlelése
A tegye a rendellenességek észlelését és az esemény például a bejelentkezési kísérlet az esemény előtt kockázatbecslés engedélyezett a folytatáshoz.

### <a name="remediated-risk-event"></a>Kijavítva (kockázat esemény)
Azonosító adatok védelmét, a kockázat esemény a szabványos szervizelési művelet használata az ilyen típusú kockázat esemény lett kijavítva jelző által automatikusan beállított kockázati esemény állapota. Például amikor a felhasználó jelszavát, sok kockázati események, amelyek jelzik, hogy esetleges biztonsági sérülés esetén a korábbi jelszót vannak pótolja őket automatikusan.

### <a name="remediation"></a>Szervizelés
Egy műveletet, biztonságos identitás vagy egy eszköz, amely korábban gyanús vagy megsértik ismert. A javítási művelet visszaállítja a identitás vagy az eszköz biztonságos, és oldja fel a identitás vagy az eszköz társított előző kockázati eseményekről.

### <a name="resolved-risk-event"></a>Megoldott (kockázat esemény)
A kockázat eseményállapot Identity Protection felhasználó manuálisan állítsa be, jelezve, hogy a felhasználó elvégez egy megfelelő szervizelési művelet kívül Identity Protection, és a kockázati esemény tekintendő zárva.

### <a name="risk-event-status"></a>Kockázati eseményállapot
A kockázati események tulajdonságának, amely jelzi, hogy aktív-e az esemény, és ha bezárul, akkor lezárásának okát.

### <a name="risk-event-type"></a>Kockázati esemény típusa
A kockázat esemény, az esemény akkor, ha kockázatos okozó anomáliadetektálási típusának kategóriáját.

### <a name="risk-level-risk-event"></a>Kockázati szint (kockázat esemény)
Identity Protection felhasználók priorizálhatja azokat a műveleteket a kockázat esemény súlyossága megjelölése (magas, közepes vagy alacsony) tartanak a kockázatok csökkentése a szervezet számára. 

### <a name="risk-level-sign-in"></a>Kockázati szint (bejelentkezés)
Jelzése (magas, közepes vagy alacsony) annak a valószínűségét, hogy az egy adott bejelentkezés, valaki más próbál használni, a felhasználó identitását.

### <a name="risk-level-user-compromise"></a>Kockázati szint (felhasználói sérült biztonság esetén)
Jelzése (magas, közepes vagy alacsony) annak a valószínűségét, hogy identitás megtámadták.

### <a name="risk-level-vulnerability"></a>Kockázati szint (biztonsági rés)
A biztonsági rés priorizálhatja azokat a műveleteket Identity Protection felhasználók súlyossága megjelölése (magas, közepes vagy alacsony) tartanak a kockázatok csökkentése a szervezet számára.

### <a name="secure-identity"></a>Biztonságos (identitás)
Például a jelszó módosítása vagy a gép, egy biztonsági szempontból sértetlen állapotának visszaállításához a potenciálisan veszélyeztetett identitás különösen szervizelési művelet végrehajtása.

### <a name="security-policy"></a>Biztonsági házirend
Szabályok és az állapot gyűjteménye. Egy házirend entitások, például a felhasználók, csoportok, alkalmazások, eszközök, eszközök, eszköz állapotok, IP-címtartományok és Auth2.0 ügyféltípusokat alkalmazhatja. A házirend engedélyezve van, amikor a házirendben entitás erőforrás jogkivonatot ad ki lesznek kiértékelve.

### <a name="sign-in-v"></a>Jelentkezzen be (v)
A hitelesítés az identitás, az Azure Active Directoryban.

### <a name="sign-in-n"></a>Bejelentkezés (n)
A folyamat vagy egy Azure Active Directory és az esemény, amely a művelet rögzíti a személyazonossága hitelesítésének művelettel.

### <a name="sign-in-from-anonymous-ip-address"></a>Jelentkezzen be a névtelen IP-cím
A kockázat esemény után egy sikeres bejelentkezés névtelen proxy IP-címként azonosított IP-címről következik be.

### <a name="sign-in-from-infected-device"></a>Jelentkezzen be a fertőzött eszköz
A kockázat bejelentkezne származási IP-címet, amelyről ismert, hogy az egy vagy több feltört eszközök, amelyek aktívan próbál kommunikálni egy botnetes kiszolgálóhoz használható által elindított esemény.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Jelentkezzen be a következő IP-gyanús tevékenység
A kockázat az esemény akkor váltódik ki, miután egy sikeres bejelentkezés IP cím nagyszámú sikertelen bejelentkezési kísérlet között több felhasználói fiókot egy rövid időtartamra vonatkozóan.

### <a name="sign-in-from-unfamiliar-location"></a>Jelentkezzen be az ismeretlen helyről
A kockázat felhasználó sikeresen jelentkezik be egy új helyről (IP, szélesség/hosszúsági és ASN) által elindított esemény.

### <a name="sign-in-risk"></a>Bejelentkezési kockázat
Tekintse meg a kockázati szintjét (bejelentkezés)

### <a name="sign-in-risk-policy"></a>Bejelentkezési kockázati házirend
Feltételes hozzáférési szabályzatot, amely egy adott jelentkezik be a kockázat és azok mérséklési előre meghatározott feltételt és a szabályok alapján alkalmazza.

### <a name="user-compromise-risk"></a>Felhasználó biztonsági sérülés kockázata
Tekintse meg a kockázati szintjét (felhasználói sérült biztonság esetén)

### <a name="user-risk"></a>Felhasználói kockázat
Tekintse meg a kockázati szintjét (felhasználói sérült biztonság esetén).

### <a name="user-risk-policy"></a>Felhasználói kockázati házirend
Feltételes hozzáférési szabályzatot, amely úgy ítéli meg, a bejelentkezés, és azok mérséklési előre meghatározott feltételt és a szabályok alapján alkalmazza.

### <a name="users-flagged-for-risk"></a>Kockázatosként megjelölt felhasználók
Kockázati eseményekről, amelyek aktív vagy szervizelt rendelkező felhasználók

### <a name="vulnerability"></a>Biztonsági rés
A konfiguráció vagy a feltétel az Azure Active Directoryban, ami lehetővé teszi a könyvtár ki vannak téve a biztonsági rések vagy fenyegetéseket.

## <a name="see-also"></a>Lásd még
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

