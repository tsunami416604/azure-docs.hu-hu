---
title: Az Azure Active Directory identitásvédelem szószedete
description: Az Azure Active Directory identitásvédelem szószedete
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232345"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Az Azure Active Directory identitásvédelem szószedete

### <a name="at-risk-user"></a>Veszélyben (Felhasználó)
Egy vagy több aktív kockázatészleléssel rendelkező felhasználó. 

### <a name="atypical-sign-in-location"></a>Atipikus bejelentkezési hely
Olyan földrajzi helyről való bejelentkezés, amely nem jellemző az adott felhasználóra, hasonló felhasználókra vagy a bérlőre.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Az Azure Active Directory biztonsági modulja, amely a szervezet identitásait érintő kockázatészlelések és potenciális biztonsági rések összevont nézetét biztosítja.

### <a name="conditional-access"></a>Feltételes hozzáférés
Az erőforrásokhoz való hozzáférés biztosítására vonatkozó házirend. Feltételes hozzáférési szabályok az Azure Active Directoryban tárolódnak, és az Azure AD által kiértékelt, mielőtt hozzáférést biztosít az erőforráshoz.  A példaszabályok közé tartozik a hozzáférés korlátozása a felhasználó helye, az eszköz állapota vagy a felhasználó hitelesítési módszere alapján.

### <a name="credentials"></a>Hitelesítő adatok
A helyi és hálózati erőforrásokhoz való hozzáféréshez használt azonosítást és azonosítási igazolást tartalmazó információk. A hitelesítő adatok közé tartoznak például a felhasználónevek és jelszavak, az intelligens kártyák és a tanúsítványok.

### <a name="event"></a>Esemény
Az Azure Active Directoryban egy tevékenység rekordja.

### <a name="false-positive-risk-detection"></a>Hamis pozitív (kockázatészlelés)
Az Identity Protection-felhasználó által manuálisan beállított kockázatészlelési állapot, amely azt jelzi, hogy a kockázatészlelés ki lett vizsgálva, és helytelenül lett megjelölve kockázatészlelésként.

### <a name="identity"></a>Identitás
Olyan személy vagy szervezet, amelyet hitelesítéssel kell ellenőrizni olyan kritériumok alapján, mint a jelszó vagy a tanúsítvány.

### <a name="identity-risk-detection"></a>Identitáskockázat-észlelés
Az Azure AD-esemény, amely az Identity Protection által rendellenesként megjelölt, és azt jelezheti, hogy egy identitás feltört.

### <a name="ignored-risk-detection"></a>Figyelmen kívül hagyva (kockázatészlelés)
Az Identity Protection felhasználó által manuálisan beállított kockázatészlelési állapot, amely azt jelzi, hogy a kockázatészlelés szervizelési művelet nélkül le van zárva.

### <a name="impossible-travel-from-atypical-locations"></a>Lehetetlen utazás atipikus helyekről
A kockázatészlelés akkor aktiválódik, ha ugyanazon felhasználó két bejelentkezését észleli, ha legalább az egyik egy atipikus bejelentkezési helyről származik, és ahol a bejelentkezések közötti idő rövidebb, mint az a minimális idő, amely et fizikailag meg kell tennie a közöttük lévő fizikai utazáshoz. Helyek.  

### <a name="investigation"></a>Vizsgálat
A kockázatok észlelésével kapcsolatos tevékenységek, naplók és egyéb releváns információk áttekintése annak eldöntéséhez, hogy szükség van-e javítási vagy kockázatcsökkentési lépésekre, annak megértése, hogy az identitás sérült-e, és ha annak módját, valamint annak megértése, hogy a feltört identitást használták.

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok
A kockázatészlelés akkor aktiválódik, amikor az aktuális felhasználói hitelesítő adatokat (felhasználónevet és jelszót) a kutatók nyilvánosan közzéteszik a Sötét weben.

### <a name="mitigation"></a>Kezelés
Olyan művelet, amely korlátozza vagy megszünteti a támadó azon képességét, hogy kihasználja a feltört identitást vagy eszközt anélkül, hogy az identitást vagy eszközt biztonságos állapotba állítaná. A kockázatcsökkentés nem oldja meg az identitáshoz vagy eszközhöz társított korábbi kockázatészleléseket.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Olyan hitelesítési módszer, amely két vagy több hitelesítési módszert igényel, amelyek tartalmazhatnak valamit, amivel a felhasználó rendelkezik, egy ilyen tanúsítványt; valamit, amit a felhasználó ismer, például felhasználóneveket, jelszavakat vagy jelmondatokat; fizikai attribútumok, például ujjlenyomatok; és személyes tulajdonságok, például személyes aláírás.

### <a name="offline-detection"></a>Kapcsolat nélküli észlelés
A rendellenességek észlelése és egy esemény kockázatának kiértékelése, például a bejelentkezési kísérlet után a tény, egy esemény, amely már megtörtént.

### <a name="policy-condition"></a>Házirend-feltétel
A biztonsági házirend egy része, amely meghatározza a házirendben szereplő vagy a házirendből kizárt entitásokat (csoportok, felhasználók, alkalmazások, eszközplatformok, eszközállapotok, IP-tartományok, ügyféltípusok).

### <a name="policy-rule"></a>Házirendszabály
A biztonsági házirend azon része, amely leírja a házirendet kiváltó körülményeket és a házirend aktiválásakor végrehajtott műveleteket.

### <a name="prevention"></a>Megelőzés
A szervezet károsodásának megelőzésére szolgáló művelet olyan személyazonossággal vagy eszközzel való visszaélés révén, amely ről gyaníthatóan vagy atudom, hogy veszélyben van. A megelőzési művelet nem biztosítja az eszközt vagy az identitást, és nem oldja meg a korábbi kockázatészleléseket.

### <a name="privileged-user"></a>Kiemelt jogosultságú (felhasználó)
Az a felhasználó, aki a kockázatészlelés időpontjában állandó vagy ideiglenes rendszergazdai engedélyekkel rendelkezett az Azure Active Directory egy vagy több erőforrásához, például globális rendszergazdához, számlázási rendszergazdához, szolgáltatásrendszergazdához, felhasználórendszergazdához és jelszóhoz. Rendszergazda. 

### <a name="real-time"></a>Valós idejű
Lásd: Valós idejű észlelés.

### <a name="real-time-detection"></a>Valós idejű észlelés
A rendellenességek észlelése és egy esemény kockázatának kiértékelése, például a bejelentkezési kísérlet, mielőtt az esemény folytatható.

### <a name="remediated-risk-detection"></a>Helyreállítva (kockázatészlelés)
Az Identity Protection által automatikusan beállított kockázatészlelési állapot, amely azt jelzi, hogy a kockázatészlelést az ilyen típusú kockázatészlelési szabványos szervizelési művelettel orvosolták. Ha például a felhasználói jelszó alaphelyzetbe áll, a rendszer automatikusan kiigazítja azokat a kockázatészleléseket, amelyek azt jelzik, hogy az előző jelszó feltörése történt.

### <a name="remediation"></a>Kockázatcsökkentés
Olyan identitás vagy eszköz biztonságossá tétele, amelyről korábban gyanítottak vagy ismerten feltörtek. A szervizelési művelet visszaállítja az identitást vagy az eszközt biztonságos állapotba, és feloldja az identitáshoz vagy eszközhöz társított korábbi kockázatészleléseket.

### <a name="resolved-risk-detection"></a>Megoldott (kockázatészlelés)
Az Identity Protection felhasználó által manuálisan beállított kockázatészlelési állapot azt jelzi, hogy a felhasználó megfelelő javítási műveletet hajtott végre az Identitásvédelemen kívül, és hogy a kockázatészlelést zártnak kell tekinteni.

### <a name="risk-detection-status"></a>Kockázatészlelési állapot
A kockázatészlelés tulajdonsága, amely jelzi, hogy az esemény aktív-e, és ha zárva van, a bezárásának oka.

### <a name="risk-detection-type"></a>Kockázatészlelés típusa
A kockázatészlelés egyik kategóriája, amely az eseményt kockázatosnak minősülő anomália típusát jelzi.

### <a name="risk-level-risk-detection"></a>Kockázati szint (kockázatészlelés)
A kockázatészlelés súlyosságának jelzése (magas, közepes vagy alacsony), hogy segítsen az Identity Protection felhasználóinak rangsorolni a szervezetre jelentett kockázat csökkentése érdekében tett műveleteket. 

### <a name="risk-level-sign-in"></a>Kockázati szint (bejelentkezés)
Annak a valószínűsége , hogy egy adott bejelentkezésnél valaki más próbálja használni a felhasználó identitását.

### <a name="risk-level-user-compromise"></a>Kockázati szint (felhasználói kompromisszum)
Annak a valószínűsége, hogy egy identitás sérült.

### <a name="risk-level-vulnerability"></a>Kockázati szint (sebezhetőség)
A biztonsági rés súlyosságának jelzése (magas, közepes vagy alacsony), amely segít az Identitásvédelem felhasználóinak rangsorolni a szervezetre jelentett kockázat csökkentése érdekében tett műveleteket.

### <a name="secure-identity"></a>Biztonságos (identitás)
A potenciálisan feltört identitás feltörése érdekében szükség lehet a javítási műveletekre, például jelszómódosításra vagy a gép újraképzésére.

### <a name="security-policy"></a>Biztonsági házirend
Házirendszabályok és feltételek gyűjteménye. A szabályzat olyan entitásokra alkalmazható, mint a felhasználók, csoportok, alkalmazások, eszközök, eszközplatformok, eszközállapotok, IP-tartományok és Auth2.0 ügyféltípusok. Ha egy házirend engedélyezve van, a rendszer kiértékeli, ha egy entitás szerepel a szabályzatban egy jogkivonatot egy erőforráshoz.

### <a name="sign-in-v"></a>Bejelentkezés (v)
Az Azure Active Directory identitásának hitelesítése.

### <a name="sign-in-n"></a>Bejelentkezés (n)
Az Azure Active Directoryban egy identitás hitelesítésének folyamata vagy művelete, valamint a műveletet rögzítő esemény.

### <a name="sign-in-from-anonymous-ip-address"></a>Bejelentkezés névtelen IP-címről
A kockázatészlelés után kiváltott sikeres bejelentkezés IP-címről, amely névtelen proxy IP-címként azonosított.

### <a name="sign-in-from-infected-device"></a>Bejelentkezés fertőzött eszközről
A kockázatészlelés akkor aktiválódik, ha egy bejelentkezés i. IP-címről származik, amelyről ismert, hogy egy vagy több feltört eszköz használja, és aktívan próbál kommunikálni egy robotkiszolgálóval.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Bejelentkezés gyanús tevékenységgel rendelkező IP-címről
A kockázatészlelés után kiváltott sikeres bejelentkezés egy IP-címről a nagy számú sikertelen bejelentkezési kísérletek több felhasználói fiókok rövid idő alatt.

### <a name="sign-in-from-unfamiliar-location"></a>Bejelentkezés ismeretlen helyről
A kockázatészlelés akkor aktiválódik, amikor a felhasználó sikeresen bejelentkezik egy új helyről (IP, Szélesség/hosszúság és ASN).

### <a name="sign-in-risk"></a>Bejelentkezési kockázat
Lásd kockázati szint (bejelentkezés)

### <a name="sign-in-risk-policy"></a>Bejelentkezési kockázati szabályzat
Feltételes hozzáférési szabályzat, amely kiértékeli a kockázatot egy adott bejelentkezésre, és előre meghatározott feltételek és szabályok alapján alkalmazza a megoldásokat.

### <a name="user-compromise-risk"></a>A felhasználók veszélyeztetésének kockázata
Lásd Kockázati szint (felhasználói kompromisszumot)

### <a name="user-risk"></a>Felhasználói kockázat
Lásd: Kockázati szint (felhasználói kompromisszumot).

### <a name="user-risk-policy"></a>Felhasználói kockázati házirend
Feltételes hozzáférési szabályzat, amely figyelembe veszi a bejelentkezést, és előre meghatározott feltételek és szabályok alapján alkalmazza a megoldásokat.

### <a name="users-flagged-for-risk"></a>Kockázatosként megjelölt felhasználók
Olyan felhasználók, akik nek aktív vagy helyreállított kockázatészlelésevan

### <a name="vulnerability"></a>Biztonsági rés
Konfiguráció vagy feltétel az Azure Active Directoryban, amely a címt kiváltja a biztonsági résekvagy fenyegetések számára.

## <a name="see-also"></a>Lásd még

- [Azure Active Directory identitásvédelem](../active-directory-identityprotection.md)
