---
title: A barangolás beállítási referencia Windows 10 |} Microsoft Docs
description: Forrásul vagy a biztonsági mentés a Windows 10-beállítások teljes listáját.
services: active-directory
keywords: a vállalati állapothordozás, a windows-felhő
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 741b76935b5a6d9b2a6869ef57caa0ac0dc6351a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="windows-10-roaming-settings-reference"></a>Windows 10 roaming beállítások referenciája
Forrásul vagy a biztonsági mentés a Windows 10-beállítások teljes listáját a következő: 

## <a name="devices-and-endpoints"></a>Eszközök és -végpontok
Az alábbi táblázat az eszközök és a szinkronizálás, a biztonsági másolat, által támogatott fióktípusok összefoglalása, és állítsa vissza a Windows 10-keretrendszer.

| Fiók típusa és művelet | Asztal | Mobiltelefon |
| --- | --- | --- |
| Az Azure Active Directory: szinkronizálás |Igen |Nem |
| Az Azure Active Directory: biztonsági mentés/visszaállítás |Nem |Nem |
| Microsoft-fiók: szinkronizálás |Igen |Igen |
| Microsoft-fiók: biztonsági mentés/visszaállítás |Nem |Igen |

## <a name="what-is-backup"></a>Mi az a biztonsági mentési?
Windows-beállítások általában szinkronizálása alapértelmezés szerint, de egyes beállítások csak biztonsági, például az eszközön telepített alkalmazások listáját. Biztonsági mentés van a mobileszközökhöz készült jelenleg nem érhető el, és csak a felhasználók a vállalati Állapothordozás. Biztonsági mentés Microsoft-fiókot használ, és a beállítások értékeit és alkalmazásadatok tárolja a onedrive-on. Ha a felhasználó letiltása szinkronizálási eszköz, a beállítási alkalmazást használ, általában Szinkronizáló alkalmazásadatok csak biztonsági mentési válik. Biztonsági mentési adatok csak a visszaállítási művelet során a first run Experience összetevő egy új eszköz keresztül érhető el. Biztonsági másolatok is lehet tiltja, hogy az eszköz beállítások és kezelhető és a felhasználó OneDrive-fiókja keresztül törölték.

## <a name="windows-settings-overview"></a>Windows-beállítások áttekintése
A következő beállítások csoportok állnak rendelkezésre a végfelhasználók által engedélyezendő/letiltandó szinkronizálása a Windows 10 rendszerű eszközökön.

* Téma: asztali hátterét, felhasználói csempe, tálca elhelyezését, stb. 
* Az Internet Explorer beállításainak: böngészési előzmények, beírt URL-címeket, a Kedvencek közé, stb. 
* Jelszavak: [Windows hitelesítő adatok széfjét](https://technet.microsoft.com/library/jj554668.aspx), beleértve a Wi-Fi profilok 
* Nyelvi beállítások: helyesírási szótár nyelvi beállításait 
* Könnyű kezelés: Narrátor, Nagyító, képernyő-billentyűzet 
* Egyéb Windows-beállítások: lásd: a Windows-beállítások részletei

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-syncyoursettings.png)

Edge böngésző beállítás (Kedvencek, olvasási lista) csoport szinkronizálása is engedélyezhető vagy letiltható Edge böngésző beállításai menüpont a végfelhasználók által.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-edge.png)

A Windows 10-es verzió 1803 vagy újabb, az Internet Explorer beállítási csoportja (a Kedvencek közé, beírt URL-címek) szinkronizálása is engedélyezhető vagy letiltható az Internet Explorer beállításainak menüpont a végfelhasználók által. 

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Windows-beállítások részletei
Az alábbi táblázatban a csoport oszlop más bejegyzései hivatkozik letiltható beállítások a beállítások > fiókok > Beállítások szinkronizálása > más Windows-beállításait. 

A csoport oszlop belső bejegyzései tekintse meg a beállításokat és alkalmazásokat, amelyek csak letiltható az alkalmazás maga belül, vagy ha letiltja a szinkronizálást a mobileszköz-kezelés (MDM) vagy a csoportházirend segítségével a teljes eszköz szinkronizálása.
Beállítások, amelyek nem barangolás vagy a szinkronizálás nem fog tartozni csoporthoz.

| Beállítások | Asztal | Mobiltelefon | Csoport |
| --- | --- | --- | --- |
| **Fiókok**: fiókképére |Szinkronizálás |X |Téma |
| **Fiókok**: más Fiókbeállítások |X |X | |
| **Mobil szélessávú speciális**: Internetkapcsolat megosztása (engedélyezi a Bluetooth mobil Wi-Fi elérési pontokhoz való automatikus felderítési) hálózati név |X |X |Jelszavak |
| **Az alkalmazásadatok**: az egyes alkalmazások adatok szinkronizálása |szinkronizálási biztonsági mentése |szinkronizálási biztonsági mentése |Belső |
| **Alkalmazáslistájában**: a telepített alkalmazások listájában |X |biztonsági mentés |Egyéb |
| **Bluetooth**: minden Bluetooth-beállítások |X |X | |
| **Parancssor**: parancssor "Alapértelmezett" beállítások |Szinkronizálás |X | |
| **Hitelesítő adatok**: hitelesítő adatok Széfje |Szinkronizálás |Szinkronizálás |jelszó |
| **Dátum, idő és terület**: automatikus idő (Internet az idő szinkronizálása) |Szinkronizálás |Szinkronizálás |nyelv |
| **Dátum, idő és terület**: 24 órás formátumban |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: dátuma és időpontja |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: időzóna | |X |nyelv |
| **Dátum, idő és terület**: nyári időszámítás |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: ország vagy régió |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: hét első napja |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: régió formátuma (nyelv) |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: rövid dátum |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: hosszú dátum |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: rövid időn belül |Szinkronizálás |X |nyelv |
| **Dátum, idő és terület**: hosszú idő |Szinkronizálás |X |nyelv |
| **Asztal személyre szabása**: asztali téma (háttér, rendszerszín, alapértelmezett rendszer hangok, képernyőkímélő) |Szinkronizálás |X |Téma |
| **Asztal személyre szabása**: diavetítési háttérkép |Szinkronizálás |X |Téma |
| **Asztal személyre szabása**: tálca beállítások (pozícióját, automatikus elrejtése, stb.) |Szinkronizálás |X |Téma |
| **Asztal személyre szabása**: Indítsa el a kezdőképernyő elrendezésének |X |biztonsági mentés | |
| **Eszközök**: megosztott nyomtatókhoz csatlakozott |X |X |egyéb |
| **Edge böngésző**: olvasási lista |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: Kedvencek |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: helyek felső <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: beírt URL-címek <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: Kedvencek beállításai <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: az otthoni gomb megjelenítése <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: előugró ablakok letiltása <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: Mi a teendő, ha mindegyik Rákérdezés <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: biztosítson a jelszavak mentése <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: küldési kérelmek nyomon követése nem <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: űrlap-bejegyzések mentése <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: keresés és a hely javaslatok megjelenítése beíráskor <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: cookie-k preferencia <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: lehetővé teszik a védett médiafájlok licencek mentse az eszközön helyek <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Edge böngésző**: beállítása képernyő olvasó <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Kontrasztos**: be- és kikapcsolása |Szinkronizálás |X |Könnyű kezelés |
| **Kontrasztos**: témák beállításai |Szinkronizálás |X |Könnyű kezelés |
| **Az Internet Explorer**: Nyissa meg a lapok (URL-cím és cím) |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: olvasási lista |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: beírt URL-címek |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: böngészési előzmények |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: Kedvencek |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: kizárt URL-címek |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: kezdőlapján |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: tartomány javaslatok |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Billentyűzet**: felhasználók is kapcsolja be/ki képernyő-billentyűzet |Szinkronizálás |X |Könnyű kezelés |
| **Billentyűzet**: kapcsolja be a kapcsolódó Igen (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Billentyűzet**: szűrő kulcsok bekapcsolása (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Billentyűzet**: állapotjelző bekapcsolása (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Az Internet Explorer**: tartomány nyelv: kínai (CHS) QWERTY - engedélyezése önálló tanulási |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - enable dinamikus jelölt prioritása |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY --karakterkészlet egyszerűsített kínai |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY --karakterkészlet hagyományos kínai |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - intelligens pinyin |Szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelvi**: CHS QWERTY - intelligens párok |Szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelvi**: CHS QWERTY - teljes pinyin |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - dupla pinyin |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - automatikus javítási olvasása |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY.-C/E kapcsoló kulcs, a shift |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - C/E kapcsoló kulcs, Ctrl |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS WUBI – egyetlen karakter bemeneti mód |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS WUBI - megjelenítése a fennmaradó kódolási pályázó |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS WUBI - e sípoló hangjelzést érvénytelen 4 kódolása |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHT Bopomofo - CJK Ext-A következők |Szinkronizálás |X |Nyelv |
| **Nyelvi**: a japán nyelvű Írásjegybevivő - prediktív gépelési és egyéni szavakat |Szinkronizálás |Szinkronizálás |Nyelv |
| **Nyelvi**: koreai (KOR) IME |X |X |Nyelv |
| **Nyelvi**: a kézírás-felismerés |X |X |Nyelv |
| **Nyelvi**: nyelvi profil |Szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelvi**: a helyesírás-ellenőrzési - automatikus javítási és kiemelési talál helyesírási hibát |Szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelvi**: billentyűzetek listája |Szinkronizálás |biztonsági mentés |Nyelv |
| **Zárolási képernyő**: összes zárolási képernyő beállításait |X |X | |
| **A Nagyító**: be- és kikapcsolása (fő toggle) |X |X |Könnyű kezelés |
| **A Nagyító**: invertálásának szín engedélyezése vagy letiltása (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **A Nagyító**: követés - Billentyűzetfókusz követése |Szinkronizálás |X |Könnyű kezelés |
| **A Nagyító**: követés - hajtsa végre az egérmutatót |Szinkronizálás |X |Könnyű kezelés |
| **A Nagyító**: Indítsa el a felhasználók bejelentkezésekor (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Egér**: egérmutatót méretének módosítása |Szinkronizálás |X |egyéb |
| **Egér**: egérmutatót színének módosítása |Szinkronizálás |X |egyéb |
| **Egér**: egyéb beállítások |X |X | |
| **A Narrátor**: Gyorsindítás |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: a felhasználók megváltoztathatják a Narrátor, és beszéljen térköz |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: felhasználók is be- és kikapcsolása mutatók közös elemek olvasása Narrátor (az alapértelmezés) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: felhasználók kapcsolhatja be és ki, hogy azok hallható beírt karakterek (az alapértelmezés) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: felhasználók kapcsolhatja be és ki, hogy azok hallható gépelt szavak (az alapértelmezés) |Szinkronizálás |X |Könnyű kezelés |
| **Narrátor**: a következő Narrátor insert kurzor rendelkezik (az alapértelmezés) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: Narrátor kurzor visual kiemelés engedélyezése (az alapértelmezés) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: hangjelzések lejátszása (az alapértelmezés) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: a touch billentyűzet aktiválása, ha Ön az ujját növekedési (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Könnyű kezelés**: állítsa be a parancssorablakba vastagsága |Szinkronizálás |X |Könnyű kezelés |
| **Könnyű kezelés**: távolítsa el a háttérképeket (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Kiemelt és alvó**: összes beállítás |X |X | |
| **Indítsa el a képernyő személyre szabása**: hangsúlyos szín (phone esetén) |X |Szinkronizálás |Téma |
| **Írja be**: helyesírási szótár |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: automatikus javítás word lett beírva. |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: helyesírási kiemelése |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: szöveg javaslatok megjelenítése beíráskor |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: szóköz hozzáadása után a kiválasztott szöveges javaslat |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: időszak után I koppintson duplán a szóköz hozzáadása |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: minden mondat első betűje |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: csupa nagybetűssé használja, ha szeretnék koppintson duplán a shift billentyűt |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: kulcs hangjelzés beíráskor |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: megszemélyesítési adatai touch billentyűzet |Szinkronizálás |biztonsági mentés |Nyelv |
| **Wi-Fi**: Wi-Fi profilok (csak WPA) |Szinkronizálás |Szinkronizálás |Jelszavak |

###### <a name="footnote-1"></a>1. lábjegyzet
Minimálisan támogatott operációs rendszer verziója a Windows Creators frissítés (Build 15063). 

## <a name="related-topics"></a>Kapcsolódó témakörök
* [Vállalati állapotának központi áttekintése](active-directory-windows-enterprise-state-roaming-overview.md)
* [Az Azure Active Directoryban a vállalati állapothordozás engedélyezése](active-directory-windows-enterprise-state-roaming-enable.md)
* [Beállítások és adatok hordozása – gyakori kérdések](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Csoport házirend és a mobileszköz-kezelési beállítások beállítások szinkronizálási szolgáltatás](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [hibaelhárítással](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
