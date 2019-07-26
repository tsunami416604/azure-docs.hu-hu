---
title: Azure Active Directory Identity Protection Szószedet | Microsoft Docs
description: Azure Active Directory Identity Protection Glossary
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6751fe74bfd9b8a07aec2263582d9f1a4bc0b2ac
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333985"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection Glossary

### <a name="at-risk-user"></a>Veszélyeztetett (felhasználó)
Egy vagy több aktív kockázati eseményt használó felhasználó. 

### <a name="atypical-sign-in-location"></a>Szokatlan bejelentkezési hely
Egy földrajzi helyről való bejelentkezés, amely nem jellemző az adott felhasználóra, a hasonló felhasználókra vagy a bérlőre.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Azure Active Directory biztonsági modulja, amely összevont nézetet biztosít a kockázati eseményekről és a szervezet identitásait érintő lehetséges sebezhetőségekről.

### <a name="conditional-access"></a>Feltételes hozzáférés
Az erőforrásokhoz való hozzáférés biztosítására szolgáló szabályzat. A feltételes hozzáférési szabályokat a Azure Active Directory tárolja, és az Azure AD kiértékeli az erőforráshoz való hozzáférés megadását megelőzően.  Ilyen szabályok például a hozzáférés korlátozása a felhasználói hely, az Eszközállapot vagy a felhasználói hitelesítési módszer alapján.

### <a name="credentials"></a>Hitelesítő adatok
Információ, amely tartalmazza a helyi és hálózati erőforrásokhoz való hozzáféréshez használt azonosítás azonosítását és igazolását. A hitelesítő adatok például felhasználónevek és jelszavak, intelligens kártyák és tanúsítványok.

### <a name="event"></a>Esemény
Egy tevékenység rekordja Azure Active Directoryban.

### <a name="false-positive-risk-event"></a>Hamis pozitív (kockázati esemény)
Az Identity Protection felhasználója manuálisan állítja be a kockázati esemény állapotát, amely azt jelzi, hogy a kockázati eseményt megvizsgálták, és a rendszer helytelenül jelölték meg kockázati eseményként.

### <a name="identity"></a>Identitás
Olyan személy vagy entitás, amelyet hitelesítéssel kell ellenőrizni, például jelszó vagy tanúsítvány alapján.

### <a name="identity-risk-event"></a>Identity kockázati esemény
HRE-esemény, amely rendellenesként van megjelölve az Identity Protection által, és jelezheti, hogy az identitás biztonsága sérült.

### <a name="ignored-risk-event"></a>Figyelmen kívül hagyva (kockázati esemény)
Az Identity Protection felhasználója manuálisan állítja be a kockázati esemény állapotát, amely azt jelzi, hogy a kockázati esemény a Szervizelési művelet végrehajtása nélkül zárul le.

### <a name="impossible-travel-from-atypical-locations"></a>Nem lehet utazni szokatlan helyekről
Egy kockázati esemény akkor aktiválódik, ha ugyanahhoz a felhasználóhoz két bejelentkezést észlelt a rendszer, ahol legalább egy szokatlan bejelentkezési helyről van szó, és a bejelentkezések közötti idő rövidebb, mint a helyek közötti fizikai utazás minimális ideje.  

### <a name="investigation"></a>Vizsgálat
A kockázati eseményekkel kapcsolatos tevékenységek, naplók és egyéb kapcsolódó információk áttekintésének folyamata annak eldöntéséhez, hogy szükség van-e a szervizelésre vagy a kockázatcsökkentő lépésekre, meg kell ismernie, hogy az identitás biztonsága és módja, valamint a feltört identitás használatban volt.

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok
Az aktuális felhasználói hitelesítő adatok (Felhasználónév és jelszó) elküldésekor kiváltott kockázati esemény, amely a kutatók által a Dark web-ben nyilvánosan elérhető.

### <a name="mitigation"></a>Kezelés
Olyan művelet, amely korlátozza vagy megszünteti a támadók számára a sérült identitás vagy eszköz kihasználása az identitás vagy az eszköz biztonságos állapotba való visszaállítása nélkül. A mérséklés nem oldja meg az identitáshoz vagy eszközhöz társított korábbi kockázati eseményeket.

### <a name="multi-factor-authentication"></a>Többtényezős hitelesítés
Olyan hitelesítési módszer, amelynek két vagy több hitelesítési módszerre van szüksége, ami magában foglalhatja a felhasználó által birtokolt, ilyen tanúsítványokat is. amit a felhasználó ismer, például felhasználóneveket, jelszavakat vagy pass-kifejezéseket; fizikai attribútumok, például ujjlenyomatok; és személyes attribútumok, például személyes aláírások.

### <a name="offline-detection"></a>Offline észlelés
A rendellenességek észlelése és a kockázat kiértékelése, például bejelentkezési kísérlet a tény után, egy már megtörtént esemény után.

### <a name="policy-condition"></a>Házirend-feltétel
Egy biztonsági szabályzat része, amely meghatározza az entitásokat (csoportokat, felhasználókat, alkalmazásokat, eszköz platformokat, eszköz-állapotokat, IP-tartományokat, ügyfél-típusokat) a szabályzatban, vagy ki van zárva belőle.

### <a name="policy-rule"></a>Szabályzatbeli szabály
A biztonsági szabályzat azon része, amely leírja a szabályzatot kiváltó körülményeket, valamint a szabályzat indításakor végrehajtott műveleteket.

### <a name="prevention"></a>Megelőzés
Egy olyan művelet, amely megakadályozza a szervezet sérülését egy olyan személyazonosság vagy eszköz általi visszaélés miatt, amely gyaníthatóan vagy ismeri a veszélyt. A megelőzési műveletek nem védik az eszközt vagy az identitást, és nem oldják fel az előző kockázati eseményeket.

### <a name="privileged-user"></a>Kiemelt jogosultságú (felhasználó)
A kockázati esemény időpontjában a felhasználó állandó vagy ideiglenes rendszergazdai engedéllyel rendelkezett a Azure Active Directory egy vagy több erőforrásához, mint például a globális rendszergazda, a számlázási rendszergazda, a szolgáltatás-rendszergazda, a felhasználói rendszergazda és a jelszó-rendszergazda. 

### <a name="real-time"></a>Valós idejű
Lásd: valós idejű észlelés.

### <a name="real-time-detection"></a>Valós idejű észlelés
A rendellenességek észlelése és a kockázat kiértékelése olyan eseményeknél, mint például a bejelentkezési kísérlet, mielőtt az esemény továbblép.

### <a name="remediated-risk-event"></a>Szervizelés (kockázati esemény)
Az Identity Protection automatikusan beállítja a kockázati esemény állapotát, ami azt jelzi, hogy a kockázati eseményt az adott típusú kockázati esemény esetében a standard szervizelési művelettel szervizelték. Ha például a rendszer alaphelyzetbe állítja a felhasználói jelszót, számos kockázati esemény automatikusan szervizelésre kerül, amely jelzi, hogy az előző jelszót feltörték.

### <a name="remediation"></a>Szervizelés
Egy olyan művelet, amely olyan identitás vagy eszköz védelmét teszi biztonságossá, amely korábban gyanúba esett vagy ismert volt. Egy szervizelési művelet visszaállítja az identitást vagy az eszközt egy biztonságos állapotba, és feloldja az identitáshoz vagy eszközhöz társított korábbi kockázati eseményeket.

### <a name="resolved-risk-event"></a>Megoldott (kockázati esemény)
Az Identity Protection felhasználója manuálisan állítja be a kockázati esemény állapotát, amely azt jelzi, hogy a felhasználó megfelelő szervizelési műveletet végzett az Identity Protectionn kívül, és a kockázati eseményt zártnak kell tekinteni.

### <a name="risk-event-status"></a>Kockázati esemény állapota
Egy kockázati esemény tulajdonsága, amely azt jelzi, hogy az esemény aktív-e, és ha be van zárva, a Bezárás oka.

### <a name="risk-event-type"></a>Kockázati esemény típusa
A kockázati Esemény kategóriája, amely azt jelzi, hogy milyen típusú anomália okozta az eseményt kockázatos.

### <a name="risk-level-risk-event"></a>Kockázati szint (kockázati esemény)
A kockázati esemény súlyosságának jelzése (magas, közepes vagy alacsony), amely segíti az Identity Protection-felhasználók számára, hogy a szervezetük kockázatának csökkentése érdekében az általuk végrehajtott műveleteket rangsorolják. 

### <a name="risk-level-sign-in"></a>Kockázati szint (bejelentkezés)
Egy adott bejelentkezés valószínűségének (magas, közepes vagy alacsony) jele, hogy valaki más megpróbálja használni a felhasználó identitását.

### <a name="risk-level-user-compromise"></a>Kockázati szint (felhasználói kompromisszum)
Az identitás veszélyének valószínűsége (magas, közepes vagy alacsony).

### <a name="risk-level-vulnerability"></a>Kockázati szint (sebezhetőség)
A biztonsági rés súlyossági szintjének (magas, közepes vagy alacsony) jelzése, hogy az Identity Protection-felhasználók rangsorolják a szervezete kockázatának csökkentése érdekében végrehajtott műveleteket.

### <a name="secure-identity"></a>Biztonságos (identitás)
A feltört identitást nem sérült állapotra állíthatja vissza, például a jelszó módosítása vagy a számítógép-áttelepítési művelet.

### <a name="security-policy"></a>Biztonsági szabályzat
Házirend-szabályok és feltételek gyűjteménye. A szabályzatok alkalmazhatók olyan entitásokra, mint például a felhasználók, a csoportok, az alkalmazások, az eszközök, az eszközök platformok, az eszközök állapotai, az IP-tartományok és az Auth 2.0-ügyfél típusa. Ha a szabályzat engedélyezve van, akkor a rendszer akkor értékeli ki, ha a házirendben szereplő entitás jogkivonatot ad ki egy erőforráshoz.

### <a name="sign-in-v"></a>Bejelentkezés (v)
Azure Active Directory identitásának hitelesítése.

### <a name="sign-in-n"></a>Bejelentkezés (n)
A Azure Active Directory identitás hitelesítésének folyamata vagy művelete, valamint a műveletet rögzítő esemény.

### <a name="sign-in-from-anonymous-ip-address"></a>Bejelentkezés névtelen IP-címről
Az IP-címről a névtelen proxy IP-címként azonosított sikeres bejelentkezés után kiváltott kockázati esemény.

### <a name="sign-in-from-infected-device"></a>Bejelentkezés fertőzött eszközről
Olyan kockázati esemény, amely akkor aktiválódik, ha a bejelentkezés egy IP-címről származik, amelyet egy vagy több sérült eszköz használ, amely aktívan próbálkozik a bot-kiszolgálóval való kommunikációra.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Bejelentkezés gyanús tevékenységű IP-címről
Egy olyan kockázati esemény, amely sikeres bejelentkezés után egy olyan IP-címről váltott ki, amely nagy számú sikertelen bejelentkezési kísérletet adott rövid idő alatt több felhasználói fiók között.

### <a name="sign-in-from-unfamiliar-location"></a>Bejelentkezés ismeretlen helyről
Olyan kockázati esemény, amely akkor aktiválódik, amikor egy felhasználó sikeresen bejelentkezik egy új helyről (IP, szélesség/hosszúság és ASN).

### <a name="sign-in-risk"></a>Bejelentkezési kockázat
Lásd: kockázati szint (bejelentkezés)

### <a name="sign-in-risk-policy"></a>Bejelentkezési kockázati házirend
Egy feltételes hozzáférési szabályzat, amely kiértékeli a kockázatot egy adott bejelentkezésre, és az előre meghatározott feltételek és szabályok alapján alkalmazza a kockázatcsökkentő tényezőket.

### <a name="user-compromise-risk"></a>Felhasználói kompromisszumos kockázat
Lásd: kockázati szint (felhasználói kompromisszum)

### <a name="user-risk"></a>Felhasználói kockázat
Lásd: kockázati szint (felhasználói kompromisszum).

### <a name="user-risk-policy"></a>Felhasználói kockázati házirend
Feltételes hozzáférési szabályzat, amely figyelembe veszi a bejelentkezést, és az előre meghatározott feltételek és szabályok alapján alkalmazza a korlátozásokat.

### <a name="users-flagged-for-risk"></a>Kockázatosként megjelölt felhasználók
Azok a felhasználók, akiknek aktív vagy szervizelt kockázati eseményei vannak

### <a name="vulnerability"></a>Biztonsági rés
A Azure Active Directory konfigurációját vagy feltételét, amely a könyvtárat kihasználja a biztonsági rések vagy fenyegetések ellen.

## <a name="see-also"></a>Lásd még
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
