---
title: A Windows 10 roaming beállítások referenciája |} A Microsoft Docs
description: Forrásul vagy a biztonsági másolat a Windows 10-es beállítások teljes listáját.
services: active-directory
keywords: Vállalati állapothordozás, a windows-felhő
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.openlocfilehash: 45bf554e457e48704684fe8ac3b32c29f3f646b2
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189282"
---
# <a name="windows-10-roaming-settings-reference"></a>Windows 10 roaming beállítások referenciája
Forrásul vagy a biztonsági másolat a Windows 10-es beállítások teljes listáját a következő: 

## <a name="devices-and-endpoints"></a>Eszközök és a végpontok
Az alábbi táblázat összefoglalja azokat az eszközöket és a szinkronizálás, a biztonsági mentési és által támogatott fióktípusok, és állítsa vissza a Windows 10-keretrendszer.

| Fiók típusa és a művelet | Asztal | Mobiltelefon |
| --- | --- | --- |
| Az Azure Active Directory: szinkronizálás |Igen |Nem |
| Az Azure Active Directory: biztonsági mentés/visszaállítás |Nem |Nem |
| Microsoft-fiók: szinkronizálás |Igen |Igen |
| Microsoft-fiók: biztonsági mentés/visszaállítás |Nem |Igen |

## <a name="what-is-backup"></a>Mi a biztonsági mentési?
Windows beállítások általában alapértelmezés szerint szinkronizálja, de az egyes beállítások csak készül biztonsági másolat, például az eszközön telepített alkalmazások listáját. Biztonsági mentés: a mobileszközök csak a és a jelenleg nem érhető el a felhasználók számára Enterprise State Roaming. Biztonsági mentés Microsoft-fiókot használ, és a beállítások és az alkalmazás adatokat tárolja a OneDrive-bA. Ha egy felhasználó letiltja az eszközön a gépház alkalmazás szinkronizálása, általában Szinkronizáló alkalmazásadatok csak biztonsági mentési válik. Biztonsági mentési adatok csak a visszaállítási művelet során az első futtatási élmény az új eszköz keresztül érhető el. Biztonsági másolatok is tiltható le az eszközbeállításokban és kezelhető és törli a felhasználó OneDrive-fiókon keresztül.

## <a name="windows-settings-overview"></a>Windows-beállítások – áttekintés
A következő beállítások csoportok szinkronizálását a Windows 10-es eszközök engedélyezése vagy letiltása a végfelhasználók számára érhetők el.

* Téma: asztali háttérkép, felhasználói csempe, tálca pozícióját, stb. 
* Az Internet Explorer beállításainak: böngészési előzményeket beírt URL-címeket, a Kedvencek, stb. 
* Jelszó: Windows hitelesítőadat-kezelő, beleértve a Wi-Fi profilok 
* Nyelvi beállítások: helyesírás-ellenőrzés szótárban, a rendszer nyelvi beállítások 
* Könnyű elérés: Narrátor, Nagyító, képernyő-billentyűzet 
* Egyéb Windows-beállítások: tekintse meg a Windows-beállítások részletei
* A Microsoft Edge böngésző beállításai: A Microsoft Edge-beli Kedvencek, olvassa és egyéb beállítások

![](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

A Microsoft Edge-böngészőbeállítások (Kedvencek, olvassa) csoportszinkronizálását is engedélyezhető vagy letiltható a végfelhasználók számára – Microsoft Edge böngésző beállításainak menüpont.

![](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

A Windows 10-es verzió 1803 vagy újabb, az Internet Explorer beállítási csoport (Kedvencek, beírt URL-címek) szinkronizálása is engedélyezhető vagy letiltható a végfelhasználók számára az Internet Explorer beállításainak menüelem keresztül. 

![](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Windows-beállítások részletei
Az alábbi táblázatban más bejegyzéseket a csoport oszlop hivatkozik beállításokat, amelyeket le lehet tiltani a beállítások > fiókok > Beállítások szinkronizálása > Other Windows-beállításait. 

Belső bejegyzéseket a csoport oszlopban tekintse meg a beállításokat és alkalmazásokat, amelyek csak letiltható az alkalmazáson belül, vagy tiltsa le a szinkronizálást a teljes eszköz mobileszköz-felügyelet (MDM) vagy a csoportházirend-beállítások szinkronizálása.
Nem hordozhatók a beállításokat, vagy szinkronizálási csoporthoz nem fog tartozni.

| Beállítások | Asztal | Mobiltelefon | Csoport |
| --- | --- | --- | --- |
| **Fiókok**: fiók kép |Szinkronizálás |X |Téma |
| **Fiókok**: egyéb Fiókbeállítások |X |X | |
| **Speciális mobil szélessávú**: Internetkapcsolat megosztása a hálózat nevét (lehetővé teszi, hogy a Bluetooth használatával mobil Wi-Fi elérési pontokhoz történő automatikus észlelés) |X |X |Jelszavak |
| **Az alkalmazásadatok**: egyéni alkalmazásokat is szinkronizálja az adatokat |szinkronizálási biztonsági mentés |szinkronizálási biztonsági mentés |Belső |
| **Alkalmazáslista**: a telepített alkalmazások listája |X |biztonsági mentés |Egyéb |
| **Bluetooth**: az összes Bluetooth-beállítások |X |X | |
| **Parancssor**: Parancsot a parancssorba "Alapértelmezett" beállítások |Szinkronizálás |X |Belső |
| **hitelesítő adatok**: Hitelesítő adatok Széfjét |Szinkronizálás |Szinkronizálás |jelszó |
| **Dátum, idő és régióját**: automatikus idő (Internet idő szinkronizálása) |Szinkronizálás |Szinkronizálás |language |
| **Dátum, idő és régióját**: 24 órás formátumban |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: dátum és idő |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: időzóna | |X |language |
| **Dátum, idő és régióját**: a nyári időszámítás |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: ország/régió |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: hét első napja |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: régió formátum (területi beállítás) |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: rövid dátum |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: hosszú dátum |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: rövid idő |Szinkronizálás |X |language |
| **Dátum, idő és régióját**: hosszú idő |Szinkronizálás |X |language |
| **Asztal személyre szabása**: asztali téma (háttérben, systémovou barvou, alapértelmezett rendszer hangok, képernyőkímélő) |Szinkronizálás |X |Téma |
| **Asztal személyre szabása**: diavetítés háttérkép |Szinkronizálás |X |Téma |
| **Asztal személyre szabása**: tálca beállítások (pozícióját, automatikus elrejtése, stb.) |Szinkronizálás |X |Téma |
| **Asztal személyre szabása**: start képernyő elrendezése |X |biztonsági mentés | |
| **Eszközök**: megosztott nyomtatókhoz, kapcsolódik |X |X |egyéb |
| **A Microsoft Edge böngésző**: könyvjelzőkhöz |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: Kedvencek |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: az első hely <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: beírt URL-címek <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: Kedvencek sáv beállítások <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: a kezdőlap gombjának megjelenítése <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: előugró ablakok letiltása <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: Mit tegyen az egyes letöltési kérdezzen <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: elérhetővé teheti a jelszavak mentése egyszerű <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: küldési kérelmek nyomon követése nem <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: űrlap bejegyzéseinek mentése <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: keresés és a hely javaslatok megjelenítése a szöveg beírása közben <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: cookie-kat szabályozó <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: lehetővé teszik a védett médiafájlok licencek mentse az eszköz helyek <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **A Microsoft Edge böngésző**: beállítás képernyőolvasó <sup> [[1]](#footnote-1)</sup> |Szinkronizálás |Szinkronizálás |Belső |
| **Kontrasztos**: Be- vagy kikapcsolása |Szinkronizálás |X |könnyű elérés |
| **Kontrasztos**: Témák beállításai |Szinkronizálás |X |könnyű elérés |
| **Az Internet Explorer**: Nyissa meg a lapok (URL-cím és cím) |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: könyvjelzőkhöz |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: beírt URL-címek |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: böngészési előzmények |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: Kedvencek |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: kizárt URL-címek |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: kezdőlapján |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Az Internet Explorer**: tartomány javaslatok |Szinkronizálás |Szinkronizálás |Internet Explorer |
| **Billentyűzet**: felhasználók is kapcsolja be/ki képernyő-billentyűzet |Szinkronizálás |X |könnyű elérés |
| **Billentyűzet**: kapcsolja be a kiemelt Igen (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |könnyű elérés |
| **Billentyűzet**: kapcsolja be a szűrő kulcsok (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |könnyű elérés |
| **Billentyűzet**: kapcsolja be az állapotjelző (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |könnyű elérés |
| **Az Internet Explorer**: tartomány nyelv: Kínai (CHS) QWERTY - engedélyezése helyi tanulási |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - enable dinamikus jelölt rangsorolása |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - char-set egyszerűsített kínai |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - char-set kínai (hagyományos) |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY – intelligens átírhatók |Szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelvi**: CHS QWERTY – intelligens párok |Szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelvi**: CHS QWERTY – teljes átírhatók |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - dupla átírhatók |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - olvasó automatikus javítás |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - C/E kapcsoló kulcsot, a SHIFT billentyűt |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS QWERTY - C/E kapcsoló kulcs, Ctrl |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS WUBI – egyetlen karakter beviteli mód |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS WUBI - megjelenítése a fennmaradó kódolási pályázó |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHS WUBI – 4 kódolási érvénytelen hangjelzés |Szinkronizálás |X |Nyelv |
| **Nyelvi**: CHT Bopomofo - CJK Ext-A belefoglalása |Szinkronizálás |X |Nyelv |
| **Nyelvi**: A japán nyelvű Írásjegybevivő – prediktív beírásával és az egyéni szavakat |Szinkronizálás |Szinkronizálás |Nyelv |
| **Nyelvi**: Koreai (KOR) időpontja |X |X |Nyelv |
| **Nyelvi**: a kézírás-felismerés |X |X |Nyelv |
| **Nyelvi**: nyelv profil |Szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelvi**: helyesírás-ellenőrzés – az automatikus javítás és a kiemelés elírások |Szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelvi**: billentyűzetek listája |Szinkronizálás |biztonsági mentés |Nyelv |
| **Zárolási képernyő**: összes zárolási képernyő beállításai |X |X | |
| **A Nagyító**: be- vagy kikapcsolása (fő váltógomb) |X |X |Könnyű kezelés |
| **A Nagyító**: szín invertálásának engedélyezése vagy letiltása (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **A Nagyító**: nyomon követése – kövesse a billentyűzetfókusz |Szinkronizálás |X |Könnyű kezelés |
| **A Nagyító**: nyomon követése – hajtsa végre az egérmutatót |Szinkronizálás |X |Könnyű kezelés |
| **A Nagyító**: kezdődik, amikor a felhasználók bejelentkezhetnek (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Egér**: egérmutatót méretének módosítása |Szinkronizálás |X |egyéb |
| **Egér**: egérmutatót színének módosítása |Szinkronizálás |X |egyéb |
| **Egér**: a többi beállítás |X |X | |
| **A Narrátor**: Gyorsindítás |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: a felhasználók megváltoztathatják a Narrátor, és beszéljen felébresztve |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: felhasználók is be- és kikapcsolása a Narrátor mutatók gyakori elemek olvasása (az alapértelmezés szerint) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: felhasználók is be- és kikapcsolása e is beírt karakterek nélkül (az alapértelmezés szerint) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: felhasználók is be- és kikapcsolása e azok hallható beírt szavakat (az alapértelmezés szerint) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: Narrátor következő beszúrása a kurzor rendelkezik (az alapértelmezés szerint) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: engedélyezése a Narrátor kurzor visual kiemelés (az alapértelmezés szerint) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: hangjelzések lejátszása (az alapértelmezés szerint) |Szinkronizálás |X |Könnyű kezelés |
| **A Narrátor**: a touch billentyűzet aktiválása, ha Ön helyezheti át az ujját (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Könnyű elérés**: állítsa be a parancssorablakba vastagsága |Szinkronizálás |X |Könnyű kezelés |
| **Könnyű elérés**: távolítsa el a háttérképeket (alapértelmezés szerint kikapcsolva) |Szinkronizálás |X |Könnyű kezelés |
| **Energiagazdálkodási és alvási**: összes beállítás |X |X | |
| **Indítsa el a képernyőn személyre szabása**: hangsúlyos színe (csak phone) |X |Szinkronizálás |Téma |
| **Írja be**: helyesírási szótár |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: automatikus javítás rosszul írta be a word |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: kijavítsa a hibásan leírt szavakat kiemelése |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: szöveg javaslatok megjelenítése a szöveg beírása közben |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: Adjon hozzá egy szóközt, egy szövegjavaslat kiválasztása után |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: vegyen fel egy időtartamot követően I koppinthat a szóköz |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: minden mondat első betűje |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: csupa nagybetűssé akkor használja, ha szeretnék koppinthat shift billentyűt |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: kulcs hang lejátszása a szöveg beírása közben |Szinkronizálás |biztonsági mentés |Nyelv |
| **Írja be**: data individuálního nastavení Pro érintőbillentyűzet |Szinkronizálás |biztonsági mentés |Nyelv |
| **Wi-Fi**: Wi-Fi profilok (csak WPA) |Szinkronizálás |Szinkronizálás |Jelszavak |

###### <a name="footnote-1"></a>1. lábjegyzet
Minimális Windows Creators Update (Build 15063) operációsrendszer-verziója támogatott. 

## <a name="next-steps"></a>További lépések

Áttekintéséhez lásd: [vállalati állapothordozás áttekintése](enterprise-state-roaming-overview.md).

