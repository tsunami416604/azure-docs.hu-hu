---
title: A Windows 10 központi beállításainak hivatkozása – Azure Active Directory
description: A Windows 10-ben esr-rel beszkószált vagy biztonsági másolatot tartalmazó beállítások
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7abc402f1fc2e449e7aac5effdb01b6b941100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672628"
---
# <a name="windows-10-roaming-settings-reference"></a>Windows 10 roaming beállítások referenciája

Az alábbi lista a Windows 10-ben barangolandó vagy biztonsági másolatot követő beállításokat tartalmazza. 

## <a name="devices-and-endpoints"></a>Eszközök és végpontok

Az alábbi táblázatban a windows 10-es szinkronizálási, biztonsági mentési és visszaállítási keretrendszer által támogatott eszközök és fióktípusok összegzését ismerteti.

| Számla típusa és működése | Asztal | Mobiltelefon |
| --- | --- | --- |
| Azure Active Directory: szinkronizálás |Igen |Nem |
| Azure Active Directory: biztonsági mentés/visszaállítás |Nem |Nem |
| Microsoft-fiók: szinkronizálás |Igen |Igen |
| Microsoft-fiók: biztonsági mentés/visszaállítás |Nem |Igen |

## <a name="what-is-backup"></a>Mi az erősítés?

A Windows beállításai általában alapértelmezés szerint szinkronizálódnak, de egyes beállításokról csak biztonsági másolat készül, például az eszközön telepített alkalmazások listája. A biztonsági mentés csak mobileszközökre van, és jelenleg nem érhető el a vállalati állapotroaming-felhasználók számára. A biztonsági másolat Microsoft-fiókot használ, és a beállításokat és az alkalmazásadatokat a OneDrive-on tárolja. Ha egy felhasználó letiltja a szinkronizálást az eszközön a Beállítások alkalmazás használatával, a szokásosan szinkronizált alkalmazásadatok csak biztonsági mentésre lesznek szükség. A biztonsági mentési adatok csak az új eszköz első futtatása során érhetők el a visszaállítási műveleten keresztül. A biztonsági mentések az eszköz beállításokon keresztül tilthatók le, és a felhasználó OneDrive-fiókján keresztül kezelhetők és törölhetők.

## <a name="windows-settings-overview"></a>A Windows beállításai – áttekintés

A következő beállításcsoportok érhetők el a végfelhasználók számára a beállítások szinkronizálásának engedélyezéséhez/letiltásához a Windows 10-es eszközökön.

* Téma: asztali háttér, felhasználói csempe, tálca pozíció, stb. 
* Internet Explorer beállításai: böngészési előzmények, beírt URL-címek, kedvencek stb. 
* Jelszavak: Windows hitelesítő adatok kezelője, beleértve a Wi-Fi profilokat 
* Nyelvi beállítások: helyesírási szótár, rendszernyelvi beállítások 
* Könnyű kezelés: narrátor, képernyő-billentyűzet, nagyító 
* Egyéb Windows-beállítások: lásd a Windows beállításainak részleteit
* A Microsoft Edge böngésző beállításai: Microsoft Edge kedvencek, olvasási lista és egyéb beállítások

![Beállítások szinkronizálása](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Ez a cikk a 2015 júliusában Windows 10 rendszerrel indított Microsoft Edge Legacy HTML-alapú böngészőre vonatkozik. A cikk nem vonatkozik az új Microsoft Edge Chromium-alapú böngésző re január 15-én, 2020. Az új Microsoft Edge szinkronizálási viselkedéséről további információt a Microsoft Edge Sync című cikkben [talál.](/deployedge/microsoft-edge-enterprise-sync)

A Microsoft Edge böngészőbeállítási csoportját (kedvencek, olvasólista) a végfelhasználók a Microsoft Edge böngésző beállítások menüjében engedélyezhetik vagy letilthatják.

![Fiók](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Windows 10-es vagy újabb verzió esetén az Internet Explorer beállítási csoportját (kedvencek, begépelt URL-címek) a végfelhasználók az Internet Explorer Beállítások menüpontján keresztül engedélyezhetik vagy letilthatják. 

![Beállítások](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>A Windows beállításainak részletei

A következő táblázatban a Beállítások csoport oszlopegyéb bejegyzései olyan beállításokra hivatkoznak, amelyekletilthatók a Beállítások > fiókok > A beállítások szinkronizálása > Egyéb Windows-beállítások. 

A Beállítások csoport oszlopban szereplő belső bejegyzések olyan beállításokra és alkalmazásokra utalnak, amelyek csak az alkalmazáson belüli szinkronizálásról, illetve a teljes eszköz szinkronizálásának letiltásáról tilthatók le a mobileszköz-kezelés (MDM) vagy a Csoportházirend-beállítások használatával.
Azok a beállítások, amelyek nem barangolnak vagy szinkronizálnak, nem tartoznak csoporthoz.

| Beállítások | Asztal | Mobiltelefon | Csoport |
| --- | --- | --- | --- |
| **Számlák**: fiókkép |szinkronizálás |X |Téma |
| **Fiókok**: egyéb fiókbeállítások |X |X | |
| **Fejlett mobil szélessávú**: Internetkapcsolat megosztása hálózati név (lehetővé teszi a mobil Wi-Fi hotspotok bluetooth-on keresztül történő automatikus felfedezését) |X |X |Jelszavak |
| **Alkalmazásadatok**: az egyes alkalmazások szinkronizálhatják az adatokat |biztonsági másolat szinkronizálása |biztonsági másolat szinkronizálása |Belső |
| **Alkalmazáslista**: a telepített alkalmazások listája |X |biztonsági mentés |Egyéb |
| **Bluetooth**: az összes Bluetooth-beállítás |X |X | |
| **Parancssor**: Parancssor "Alapértelmezések" beállításai |szinkronizálás |X |Belső |
| **Hitelesítő adatok**: Hitelesítő adatok tárolója |szinkronizálás |szinkronizálás |jelszó |
| **Dátum, idő és régió:** automatikus idő (internetes időszinkronizálás) |szinkronizálás |szinkronizálás |language |
| **Dátum, idő és régió:** 24 órás |szinkronizálás |X |language |
| **Dátum, idő és régió**: dátum és idő |szinkronizálás |X |language |
| **Dátum, idő és régió**: időzóna | |X |language |
| **Dátum, idő és régió**: nyári időszámítás |szinkronizálás |X |language |
| **Dátum, idő és régió**: ország/régió |szinkronizálás |X |language |
| **Dátum, idő és régió:** a hét első napja |szinkronizálás |X |language |
| **Dátum, idő és régió**: régió formátuma (területi beállítás) |szinkronizálás |X |language |
| **Dátum, idő és régió:** rövid dátum |szinkronizálás |X |language |
| **Dátum, idő és régió:** hosszú dátum |szinkronizálás |X |language |
| **Dátum, idő és régió:** rövid idő |szinkronizálás |X |language |
| **Dátum, idő és régió:** hosszú idő |szinkronizálás |X |language |
| **Asztali személyre szabás:** asztali téma (háttér, rendszerszín, alapértelmezett rendszerhangok, képernyőkímélő) |szinkronizálás |X |Téma |
| **Asztali személyre szabás:** diavetítés tapéta |szinkronizálás |X |Téma |
| **Asztali személyre szabás:** tálcabeállítások (pozíció, automatikus elrejtés stb.) |szinkronizálás |X |Téma |
| **Asztali személyre szabás:** kezdőképernyő elrendezése |X |biztonsági mentés | |
| **Eszközök**: megosztott nyomtatók, amelyekhez csatlakozott |X |X |egyéb |
| **Microsoft Edge böngésző:** olvasási lista |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző**: kedvencek |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző**: legnépszerűbb webhelyek <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző**: beírt URL-ek <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző**: kedvencek sávbeállításai <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző:** az otthoni gomb megjelenítése <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző**: leugró ablakok blokkolása <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző:** kérdezze meg, mi a teendő az egyes letöltés <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző:** felajánlja, hogy mentse a jelszavakat <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző:** küldés nem követi kedélykérelmek <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző:** űrlapbejegyzések mentése <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző:** a keresési és webhelyjavaslatok megjelenítése <sup> [a beírássorán [1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző**: cookie-k preferenciája <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző:** a webhelyek menthetik a védett médialicenceket az eszközömön <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Microsoft Edge böngésző**: képernyőolvasó beállítása <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |Belső |
| **Kontrasztos :** Be vagy Ki |szinkronizálás |X |könnyű hozzáférés |
| **Kontrasztos :** Témabeállítások |szinkronizálás |X |könnyű hozzáférés |
| **Internet Explorer**: lapok megnyitása (URL és cím) |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: olvasási lista |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: beírt URL-címek |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: böngészési előzmények |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: kedvencek |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: kizárt URL-címek |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: kezdőlapok |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: tartományjavaslatok |szinkronizálás |szinkronizálás |Internet Explorer |
| **Billentyűzet**: a felhasználók be- és kikapcsolhatják a képernyő-billentyűzetet |szinkronizálás |X |könnyű hozzáférés |
| **Billentyűzet**: kapcsolja be a ragadós igen (alapértelmezés szerint ki) |szinkronizálás |X |könnyű hozzáférés |
| **Billentyűzet**: a szűrőbillentyűk bekapcsolása (alapértelmezés szerint kivan kapcsolva) |szinkronizálás |X |könnyű hozzáférés |
| **Billentyűzet**: kapcsológombok bekapcsolása (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |könnyű hozzáférés |
| **Internet Explorer**: domain Nyelv: Kínai (CHS) QWERTY - lehetővé teszi az önálló tanulás |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY - dinamikus jelöltrangsor engedélyezése |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY - char-set egyszerűsített kínai |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY - char-set hagyományos kínai |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY - fuzzy pinyin |szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelv**: CHS QWERTY - fuzzy párok |szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelv**: CHS QWERTY - teljes pinyin |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY - dupla pinyin |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY - olvasás auto korrekció |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY - C/E kapcsológomb, shift |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY - C/E kapcsológomb, Ctrl |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS WUBI - egykarakteres beviteli mód |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS WUBI - mutatja a fennmaradó kódolása a jelölt |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS WUBI - hangjelzés, ha a 4-kódolás érvénytelen |szinkronizálás |X |Nyelv |
| **Nyelv**: CHT Bopomofo - tartalmazza a CJK Ext-A-t |szinkronizálás |X |Nyelv |
| **Nyelv**: Japán írás-író gépész - prediktív gépelés és egyéni szavak |szinkronizálás |szinkronizálás |Nyelv |
| **Nyelv**: Koreai (KOR) IME |X |X |Nyelv |
| **Nyelv**: kézírás-felismerés |X |X |Nyelv |
| **Nyelv**: nyelvi profil |szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelv**: helyesírás-ellenőrzés - automatikus javítás és kiemelés elírások |szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelv**: billentyűs hangszerek listája |szinkronizálás |biztonsági mentés |Nyelv |
| **Zárolási képernyő:** a zárolási képernyő összes beállítása |X |X | |
| **Nagyító:** be- vagy kikapcsolása (fő kapcsoló) |X |X |Könnyű elérés |
| **Nagyító**: az inverzió színének be- és kikapcsolása (alapértelmezés szerint) |szinkronizálás |X |Könnyű elérés |
| **Nagyító**: követés - kövesse a billentyűzet fókuszát |szinkronizálás |X |Könnyű elérés |
| **Nagyító**: követés - kövesse az egérmutatót |szinkronizálás |X |Könnyű elérés |
| **Nagyító**: indítás, amikor a felhasználók bejelentkeznek (alapértelmezés szerint kivan) |szinkronizálás |X |Könnyű elérés |
| **egér:** változtatni a méretét egérkurzor |szinkronizálás |X |egyéb |
| **egér:** változtatni a színét egérkurzor |szinkronizálás |X |egyéb |
| **Egér**: minden egyéb beállítás |X |X | |
| **Narrátor:** gyorsindítás |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a felhasználók módosíthatják a Narrátor beszédhangmagasságát |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a felhasználók be- és kikapcsolhatják a Narrátort, és tippeket olvashatnak a gyakori elemekhez (alapértelmezés szerint be) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a felhasználók be- vagy kikapcsolhatják, hogy hallják-e a beírt karaktereket (alapértelmezés szerint be) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a felhasználók be- vagy kikapcsolhatják, hogy hallják-e a beírt szavakat (alapértelmezés szerint be) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: beszúrási kurzor a Narrátor után (alapértelmezés szerint be) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a Narrátor kurzorának vizuális kiemelésének engedélyezése (alapértelmezés szerint be) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: hangjelzések lejátszása (alapértelmezés szerint bekapcsolva) |szinkronizálás |X |Könnyű elérés |
| **Narrátor:** az érintőbillentyűzet billentyűinek aktiválása az ujj felemelésekekénél (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |Könnyű elérés |
| **Könnyű hozzáférés:** állítsa be a villogó kurzor vastagságát |szinkronizálás |X |Könnyű elérés |
| **Könnyű hozzáférés:** háttérképek eltávolítása (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |Könnyű elérés |
| **Tápellátás és alvó állapot**: minden beállítás |X |X | |
| **Kezdőképernyő személyre szabása:** kiemelő szín (csak telefonon) |X |szinkronizálás |Téma |
| **Gépelés**: helyesírási szótár |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés:** hibásan írt szó automatikus javítása |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés**: a hibásan írt szavak kiemelése |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés**: szövegjavaslatok megjelenítése gépelés közben |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés**: szóköz hozzáadása szövegjavaslat kiválasztása után |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés**: adjon hozzá egy időszakot, miután duplán érintse meg a szóközt |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés:** minden mondat első betűjének nagybetűsé tétele |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés:** használja az összes nagybetűt, amikor duplán koppintok a shift billentyűre |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés:** a billentyűhangok lejátszása, ahogy gépelek |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés**: az érintőbillentyűzet személyre szabási adatai |szinkronizálás |biztonsági mentés |Nyelv |
| **Wi-Fi**: Wi-Fi profilok (csak WPA) |szinkronizálás |szinkronizálás |Jelszavak |

###### <a name="footnote-1"></a>1. lábjegyzet

A Windows Creators Update minimálisan támogatott operációsrendszer-verziója (Build 15063). 

## <a name="next-steps"></a>További lépések

Áttekintést a [vállalati állami barangolás áttekintése című témakörben talál.](enterprise-state-roaming-overview.md)
