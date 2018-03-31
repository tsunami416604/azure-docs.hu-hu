---
title: Az Azure Mobile Engagement hibaelhárítási útmutatója - leküldéses/Reach
description: Azure Mobile Engagement felhasználói beavatkozás és értesítési problémák elhárítása
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3f1886b7-1fdd-47f4-b6b0-d79f158d5ef3
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bc94353e60288ba1acfb1855b3a7b416a03e73bc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Hibaelhárítási útmutató a leküldési és az elérési problémákhoz
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

A következőkben lehetséges problémák merülhetnek fel az Azure Mobile Engagement milyen információkat küld a felhasználóknak.

## <a name="push-failures"></a>Leküldéses hibák
### <a name="issue"></a>Probléma
* Leküldéses értesítések nem használhatók (az alkalmazás, alkalmazás, vagy mindkettő).

### <a name="causes"></a>Okok
* Sokszor leküldéses hiba arra utal, hogy Azure Mobile Engagement, Reach vagy az Azure Mobile Engagement egy másik speciális szolgáltatás nem megfelelően integrálva van, vagy hogy frissítés szükséges az SDK-ban a egy ismert probléma az új operációs rendszer vagy az eszköz platformja.
* Csak egy alkalmazást a leküldéses és csak egy kívüli alkalmazás leküldéses meghatározni, ha valami probléma a az alkalmazás vagy kívüli alkalmazás teszteléséhez.
* Tesztelje a felhasználói felület és az API-t, hogy milyen további hibainformációk elérhető hibaelhárítás céljából mindkét helyen.
* Alkalmazásból leküldéses értesítések nem fog működni, kivéve, ha mind az Azure Mobile Engagement, és a Reach integrálva vannak az SDK-ban.
* Leküldéses értesítések nem fog működni, ha tanúsítványokat érvénytelenek, vagy termék vs használ. Fejlesztői megfelelően (csak iOS esetén). (**Megjegyzés:** "kívüli alkalmazás" leküldéses értesítések előfordulhat, hogy nem lehet kézbesíteni az iOS, ha mindkét (fejlesztés) a fejlesztési és éles (termék) az alkalmazás telepítve ugyanarra az eszközre, mivel a biztonsági jogkivonat a tanúsítványhoz tartozó verzióiban előfordulhat, hogy érvényteleníti Apple. A probléma megoldásához távolítsa el az alkalmazás fejlesztői és termék verziója, telepítse újra az eszköz csak egy verziója.)
* Alkalmazás leküldéses kívüli számok kezeli, másképp különböző platformokon (iOS Android-nál kevesebb információt jeleníti meg, ha a natív leküldéses értesítések le vannak tiltva az eszközön, az API-t is biztosítanak a felhasználói felület több információt leküldéses statisztikák).
* Alkalmazásból leküldéses értesítések blokkolhatja az ügyfelek operációs rendszer szintjén (iOS és Android).
* Alkalmazásból a leküldéses értesítések megjelennek a Azure Mobile Engagement felhasználói felületén le van tiltva, ha nem megfelelően integrált, de a API csendes meghiúsulhat.
* Alkalmazás leküldéses értesítések nem fog működni, kivéve, ha mind az Azure Mobile Engagement, és a Reach integrálva vannak az SDK-ban.
* GCM és ADM leküldéses értesítések nem fog működni, kivéve, ha az Azure Mobile Engagement és az adott kiszolgálói integrálva vannak az SDK-ban (csak Android esetén).
* Az alkalmazás és kívüli alkalmazás leküldéses értesítések kell vizsgálni, külön-külön határozzák meg, ha egy leküldéses vagy Reach probléma.
* A leküldéses értesítések követelmény, hogy az alkalmazás meg van nyitva fogadható alkalmazásban.
* Az alkalmazás leküldéses értesítések általában telepítő alkalmazás szolgáltatás aktiválása vagy a letiltási info kódelemmel szűrni.
* Ha egy egyéni kategória használja az alkalmazásbeli értesítésekben megjelenítendő Reach, kövesse a megfelelő életciklus-értesítést, ellenkező esetben az értesítés nem lehet kiüríteni amikor a felhasználó üzenet bezárásához.
* Ha nincs befejezési dátum kampány kezdődik, és egy eszköz megkapja a az alkalmazás értesítést, de nem jelenik meg, még, továbbra is a felhasználói fog kapja meg az értesítés a következő alkalommal jelentkeznek be az alkalmazásba, akkor is, ha manuálisan befejezi a kampányt biztosítja.
* Problémák leküldéses API-val győződjön meg arról, hogy valóban szeretné leküldéses API-t használó helyett Reach API (mivel Reach API több gyakran használt), és, hogy a "tartalom" és "bejelentő" paraméterek nem zavaró vannak.
* Tesztelje a leküldéses kampány eszközzel mindkét keresztül Wi-Fi és a 3G elkerülése érdekében a hálózati kapcsolat, a problémák lehetséges forrásaként.

## <a name="push-testing"></a>Leküldéses tesztelése
### <a name="issue"></a>Probléma
* Leküldéses értesítések küldhetők egy adott eszközhöz alapján egy eszköz azonosítót.

### <a name="causes"></a>Okok
* Teszteszközök telepítő eltérően az egyes platformokhoz készült, de egy eseményt, amely egy vizsgálati eszköz az alkalmazásban, és keres az Eszközazonosítót a portálon kell működnie az Eszközazonosítót az összes platformra kereséséhez.
* Teszteszközök működnek másképp a vs idfa-JÁT. Idfv fogja elvégezni (csak iOS esetén). lehetővé.

## <a name="push-customization"></a>Leküldéses testreszabása
### <a name="issue"></a>Probléma
* Speciális elem nem fog működni a tartalom leküldéses (jelvények, gyűrű, alaplemezzel, kép, stb.).
* Leküldéses értesítések hivatkozások nem működnek (kívül, az alkalmazásban, hogy egy webhely, alkalmazás helyre).
* Leküldéses statisztika megjelenítése, hogy egy leküldéses nem küldött annyi személyek elvárt (túl sok vagy nem elegendő).
* Leküldéses ismétlődik, és kétszer kapott.
* Nem lehet regisztrálni vizsgálati eszköz az Azure Mobile Engagement leküldéses értesítések (a saját termék vagy fejlesztői alkalmazással).

### <a name="causes"></a>Okok
* Egy adott helyen alkalmazásban csatolása szükséges "kategóriák" (csak Android esetén).
* Felhasználók átirányítása egy másik helyre a leküldéses értesítés kattintás után a részletes hivatkozási sémák kell létrehozni és az alkalmazás és az eszköz operációs rendszere nem a Mobile Engagement közvetlenül kezeli. (**Megjegyzés:** alkalmazásból értesítések nem hivatkozhat közvetlenül az IOS-alkalmazás helyek csak az Android tudja.)
* Külső kép kiszolgálók képesnek kell lenniük a HTTP: "GET" és "HEAD" a nagy vonalakban tekinti leküldéses értesítések működéséhez (csak Android esetén).
* A kódban tiltsa le az Azure Mobile Engagement-ügynököt, a billentyűzet megnyitásakor, és a kód az Azure Mobile Engagement ügynök újra kell aktiválniuk, miután a billentyűzet le van zárva, így a billentyűzet nem jelennek meg az értesítés (csak iOS esetén).
* Bizonyos elemek nem működik a teszt szimulációja, de csak valós kampányok (jelvények, gyűrű, alaplemezzel, kép, stb.).
* Kiszolgálóoldali adatok nem naplóz, amikor a gomb segítségével "teszt" leküldéses értesítések. Adatok csak a rendszer naplózza a valódi leküldéses kampányokra.
* A probléma azonosítása érdekében elhárítása: tesztelje, szimulálása, és egy valódi kampányt, mivel ezek mindegyike kissé eltérően működik.
* A "alkalmazás" és "minden alkalommal" kampányok ütemezett idő hosszúsága is érvényesíti kézbesítési számok, mivel a kampány csak akkor kézbesíti a felhasználók "alkalmazás" a kampány futtatása közben (és az értesítéseket "kívüli alkalmazás" saját eszköz beállítás rendelkező felhasználók).
* Hogyan Android és iOS kezelik kívül alkalmazásbeli értesítésekben közötti különbségek megnehezíti a az alkalmazás verziója Android és iOS leküldéses statisztikáinak közvetlenül összehasonlítására. Android további információk az operációs rendszer szintű értesítést, mint iOS. Android jelentések natív értesítést kapott, kattintott, vagy az értesítési központ törölt, de iOS nem készít jelentést ezt az információt, kivéve, ha az értesítési kattint. 
* A legfőbb oka, hogy a "megnyomott" számok nem ugyanaz, mint más, mint az "i" számok kampányok elérni, hogy "az alkalmazás" és "kívül" értesítések számlálási másképp. "Az alkalmazás" értesítések a Mobile Engagement kezeli, de "alkalmazás" verzióról értesítéseket az értesítési központ, az eszköz operációs rendszere a kezel.

## <a name="push-targeting"></a>Célcsoport-kezelési leküldéses
### <a name="issue"></a>Probléma
* A beépített célcsoport-kezelési nem a várt módon működik.
* Alkalmazásadatok címke célcsoport-kezelési nem működik megfelelően.
* Földrajzihely-célzó nem működik megfelelően.
* Nyelvi beállítások nem a várt módon működnek

### <a name="causes"></a>Okok
* Győződjön meg arról, hogy az Azure Mobile Engagement felhasználói felületén vagy a API app info címkék töltött fel.
* Sávszélesség-szabályozás a leküldési sebesség vagy a leküldési kvóta az alkalmazás szintjén, vagy korlátozza a célközönséget, a kampány szinten előfordulhat, hogy egy személy egy adott leküldéses fogadjon, még akkor is, ha megfelelnek-e a többi célcsoport-kezelési feltételeknek. 
* A "nyelv" beállítás különbözik attól a célcsoport-kezelési ország alapján vagy területi beállítás, amely egyben célcsoport-kezelési eltérő földrajzi helyhez alapján phone helyre vagy GPS helye alapján.
* Az "alapértelmezett nyelven" az üzenet küldési bármely ügyfélnek, aki nem rendelkezik az eszköz megadásához adja meg, hogy a másodlagos nyelveket.

## <a name="push-scheduling"></a>Leküldéses ütemezése
### <a name="issue"></a>Probléma
* Leküldéses ütemezés nem működik az elvárásoknak megfelelően (küldött túl korai vagy késleltetett).

### <a name="causes"></a>Okok
* Időzóna is az ütemezés, a problémák, különösen akkor, ha a végfelhasználó időzónájának használatával.
* Speciális leküldéses szolgáltatások késleltetheti-e leküldéses értesítések.
* Leküldéses értesítések célzó phone késleltetheti-beállításai (helyett App-Info címkék) alapján, mivel az Azure Mobile Engagement előfordulhat, hogy adatokat kér a telefon valós idejű leküldéses küldése előtt kell.
* A záró dátum nélküli létrehozott kampányok a leküldéses helyileg tárolja az eszközön, és szerepel, hogy a következő megnyitásakor az alkalmazás akkor is, ha a kampány manuálisan véget ér.
* Egyszerre több kampány indítása megvizsgálja a felhasználói bázis (változtassa meg kell kezdődnie egy kampány egyszerre legfeljebb négy, is csak az aktív felhasználókat, a cél, hogy a régi felhasználóknak nem kell beolvasandó) hosszabb időt vehet igénybe.
* Ha használja a "Mellőzés célközönség leküldéssel fogják megkapni a felhasználók számára az API-n keresztül" beállítást a Reach-kampány "Kampány" szakaszában automatikusan nem küldi el a kampány, szüksége lesz a küldje el manuálisan a Reach API-n keresztül.
* Ha egy egyéni kategória használja az alkalmazásbeli értesítésekben megjelenítendő Reach, kövesse a megfelelő életciklus-értesítést, ellenkező esetben az értesítés nem lehet kiüríteni amikor a felhasználó üzenet bezárásához.

