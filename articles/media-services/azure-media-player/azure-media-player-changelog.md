---
title: Az Azure Media Player changelog-ja
description: Az Azure Media Player changelog.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 15f8a3ac8c2777b3a878de92db495e559f64ad20
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726549"
---
# <a name="changelog"></a>Változatlan #

## <a name="224-official-update-february-22-2019"></a>2.2.4 (hivatalos frissítés 2019. február 22.) ##

### <a name="bug-fixes-224"></a>Hibajavítások 2.2.4 ###

- [Hibajavítás] Nem, nem, nem, nem, nem, nem [Kisegítő lehetőségek] Egy elérhető fantomlap eltávolítása a hibaképernyő megírásakor
- [Hibajavítás] Nem, nem, nem, nem, nem, nem Javítottuk az "M" gyorsbillentyűt az IE11 és az Edge számára
- [Hibajavítás] Nem, nem, nem, nem, nem, nem Kijavítottuk a CEA708 feliratok kivételét
- [Hibajavítás] Nem, nem, nem, nem, nem, nem Kijavítottuk az Edge böngésző videófagyasztási problémájait

### <a name="changes-224"></a>Változások 2.2.4 ###

- - Nem, nem, nem, nem, nem, Nem, nem, nem, nem, nem, nem Amikor töredékvisszafejtési hiba történik, a lejátszó újrapróbálkozik az aktuális és a különböző töredékek, hogy visszaszerezze a lejátszást
- - Nem, nem, nem, nem, nem, Nem, nem, nem, nem, nem, nem Az AMP toleránsabbá tette az egymást átfedő video- vagy hangtöredékeket

## <a name="223-official-update-january-9-2019"></a>2.2.3 (hivatalos frissítés 2019. január 9.) ##

### <a name="features"></a>Szolgáltatások ###

- [Szolgáltatás] [HLS] Hozzáadva a Safari HLS lejátszásának hangsáv-menüje

### <a name="bug-fixes-223"></a>Hibajavítások 2.2.3 ###

- [Hibajavítás] Nem, nem, nem, nem, nem, nem [Kisegítő lehetőségek] Élő közvetítés közben az "élő" gomb nem választható ki a billentyűzet használatával
- [Hibajavítás] Nem, nem, nem, nem, nem, nem Javítottunk 0x0400003-es hamis pozitív eredményt a sikertelen MSE-teszt miatt
- [Hibajavítás] Nem, nem, nem, nem, nem, nem Kijavítottunk egy problémát, amely miatt a videó lefagyhatott az élő közvetítés indításakor

### <a name="changes-223"></a>Változások 2.2.3 ###

- - Nem, nem, nem, nem, nem, Nem, nem, nem, nem, nem, nem További információk a naplóban a jobb diagnosztika engedélyezéséhez
- - Nem, nem, nem, nem, nem, Nem, nem, nem, nem, nem, nem Ha egynél több bitráta érhető el ugyanazon a képernyőfelbontáson, az összes bitráta választható

## <a name="222-official-update"></a>2.2.2 (hivatalos frissítés) ##

### <a name="bug-fixes-222"></a>Hibajavítások 2.2.2 ###

- [Hibajavítás] Nem, nem, nem, nem, nem, nem Ha a lejátszó átmeneti hálózati kimaradásba ütközik, azonnal leállítja a lejátszást
- [Hibajavítás] Nem, nem, nem, nem, nem, nem [Kisegítő lehetőségek] A hibapárbeszédpanel billentyűzettel nem érhető el
- [Hibajavítás] Nem, nem, nem, nem, nem, nem Végtelen pörgető jelenik meg, ha csak hanganyagot játszik le a nem támogatott hiba helyett

### <a name="changes-222"></a>Változások 2.2.2 ###

- - Nem, nem, nem, nem, nem, [AMP] honosított karakterláncokat adott hozzá a hirdetési felhasználói felülethez

## <a name="221-official-update"></a>2.2.1 (hivatalos frissítés) ##

### <a name="features-221"></a>Jellemzők 2.2.1 ###

- [Szolgáltatás] [CMAF] A HLS CMAF támogatása

### <a name="bug-fixes"></a>Hibajavítások ###

- [Hibajavítás] [AMP] nem tisztázott időzítők az újrapróbálkozási logikában, ami lejátszási hibákat eredményez
- [Hibajavítás] Nem, nem, nem, nem, nem, nem [Firefox] véget ért esemény nem lőttek a Firefox és a Chrome, amikor megállt az élő program
- [Hibajavítás] Nem, nem, nem, nem, nem, nem A setsource után megjelenő vezérlők akkor is, ha a vezérlők hamisra vannak állítva a lejátszó beállításaiban

### <a name="changes"></a>Változások ###

- - Nem, nem, nem, nem, nem, [Élő feliratozás] A CEA-feliratok API-neve 608-ról 708-ra változott. További információ: [CEA708 Captions Settings](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (Hivatalos kiadás) ##

### <a name="features-220"></a>Jellemzők 2.2.0 ###

- [Szolgáltatás] [Azurehtml5JS] - Nem, nem, nem, nem, nem, [LiveCaption] CeA 708 feliratozási támogatás az Azurehtml5JS és a FlashSS technológiában a tiszta és AES-tartalmakért.

### <a name="bug-fixes-220"></a>Hibajavítások 2.2.0 ###

- [Hibajavítás] A Flash verziófelismerés nem működik a Chrome/Edge-ben

### <a name="changes-220"></a>Változások 2.2.0 ###

- - Nem, nem, nem, nem, nem, Nem, nem, nem, nem, nem, nem - Nem, nem, nem, nem, nem, nem, nem, nem, nem, nem Heurisztikus profilnév módosítása QuickStartive-ről LowLatency-re
- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, A Flash player módosítása a verziófelismeréshez, hogy lehetővé tegye az AES-tartalom lejátszását az új Adobe Flash frissítéssel.

## <a name="219-official-hotfix"></a>2.1.9 (hivatalos gyorsjavítás) ##

### <a name="bug-fixes-219"></a>Hibajavítások 2.1.9 ###

- [Hibajavítás] - Nem, nem, nem, nem, nem, Kivétel, amikor az élő közvetítések áttérnek a video on demand/live archívumokra

### <a name="changes-219"></a>Változások 2.1.9 ###

- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, -1, hogy ne kellne. Módosított Flash tech logika, hogy ne használja sharedbytearrays az AES visszafejtése, mint az Adobe blokkolta a használat, mint a Flash 30. Kérjük, vegye figyelembe, hogy a lejátszás csak akkor működik, ha az Adobe telepíti a Flash új verzióját a v30 hibája miatt. További részletekért tekintse meg az [ismert problémákat](azure-media-player-known-issues.md)

## <a name="218-official-update"></a>2.1.8 (hivatalos frissítés) ##

### <a name="bug-fixes-218"></a>Hibajavítások 2.1.8 ###

- [Hibajavítás] Spinner néha nem jelenik meg utáni keres és pre-play
- [Hibajavítás] A lejátszó nem indul el némítva, ha a beállítás engedélyezve van
- [Hibajavítás] A hangerő csúszka akkor jelenik meg, ha a vezérlők hamisra vannak állítva
- [Hibajavítás] A lejátszás időnként ismétlődik, amikor a felhasználó az élő szélre ugrik
- [Hibajavítás] [Firefox] Játékos alkalmanként dobja JavaScript kivétel betöltése
- [Hibajavítás] [Kisegítő lehetőségek] A Lejátszás/Szünet/Hangerő gomb elveszíti a fókuszkörvonalat, ha billentyűzetvezérlőkkel van kijelölve
- [Hibajavítás] Javítottuk, hogy a lejátszó memóriaszivárgása
- [Hibajavítás] Az src() hívása a játékos hibái után nem állítja vissza a forrást
- [Hibajavítás] - Nem, nem, nem, nem, nem, Az AMP állandó betöltési állapotban van, amikor a felhasználó az Élő gombra kattint az adás befejezése után
- [Hibajavítás] Nem, nem, nem, nem, nem, nem A lejátszó lefagy, és a lejátszás sikertelen, ha a böngésző a háttérbe marad.

### <a name="changes-218"></a>Változások 2.1.8 ###

- - Nem, nem, nem, nem, nem, Frissítve 0x0600001 errror megjelenítéséhez, ha Az AES tartalom lejátszása vissza a Flash 30, mivel ez nem támogatott ebben az időben. További részletekért tekintse meg az [ismert problémákat](azure-media-player-known-issues.md)
- - Nem, nem, nem, nem, nem, További újrapróbálkozások hozzáadása az élő forgatókönyvekhez, amikor a jegyzékfájl 404-es kérelmet küld, vagy üres jegyzékeket ad vissza.

## <a name="217-official-update"></a>2.1.7 (hivatalos frissítés) ##

### <a name="features-217"></a>Jellemzők 2.1.7 ###

- [Szolgáltatás] [AzureHtml5JS] Hozzáadott konfigurációs lehetőség az elavult adatok kiürítéséhez az adathordozó-forráspufferben

### <a name="bug-fixes-217"></a>Hibajavítások 2.1.7 ###

- [Hibajavítás] [Kisegítő lehetőségek] [Képernyőolvasó] Eltávolította az üres fejlécet a játékos tartalmazza, ha a cím nincs beállítva
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem Az AMP kivételt okoz az univerzális Windows-alkalmazásban való lejátszáskor
- [Hibajavítás] [OSX] setActiveTextTrack() nem működik a Safari oSx
- [Hibajavítás] - Nem, nem, nem, nem, nem, A játékos ártalmatlanítása és újrainicializálása után az élő élre kattintva kivételt képezakivétel
- [Hibajavítás] - Nem, nem, nem, nem, nem, Egyes eszközök aktuális ideje csonkolva
- [Hibajavítás] [DRM] javítás tartalmazza, hogy támogassa a lejátszást a böngészőkben, amelyek támogatják a több CENC DRM

### <a name="changes-217"></a>Változások 2.1.7 ###

- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, [Kisegítő lehetőségek] Nyelvi címke hozzáadása az összes mintához

## <a name="216-official-update"></a>2.1.6 (hivatalos frissítés) ##

### <a name="bug-fixes-216"></a>Hibajavítások 2.1.6 ###

- [Hibajavítás] Egy adott eszköz helytelen időtartamát megjelenítő AMP
- [Hibajavítás] [Fairplay-HLS] A Fairplay hibái nem terjednek a felhasználói felületen
- [Hibajavítás] Az AMP 2.1.5-ös ben figyelmen kívül hagyja az egyéni heurisztikus tulajdonságokat.

### <a name="changes-216"></a>Változások 2.1.6 ###

- - Nem, nem, nem, nem, nem, [FairplayDRM] Eltávolította a Cert-kérelem és a FairPlay licenckérelmének időidejét, hogy a PlayReady és a Widevine implementációkkal való paritás a paritást megtarthassa.
- - Nem, nem, nem, nem, nem, Misc heurisztikus fejlesztések az elmosódott tartalom elleni küzdelemben

### <a name="features-216"></a>Jellemzők 2.1.6 ###

- [Szolgáltatás] Hozzáadott támogatás mpd-time-cmaf formátum

## <a name="215-official-hotfix"></a>2.1.5 (hivatalos gyorsjavítás) ##

### <a name="bug-fixes-215"></a>Hibajavítások 2.1.5 ###

- [Hibajavítás] [Feliratok] VTT stílus nem teszi helyesen a játékos
- [Hibajavítás] [Kisegítő lehetőségek] Az Élő gombnak nincs ária címkéje

## <a name="214-official-update"></a>2.1.4 (hivatalos frissítés) ##

### <a name="bug-fixes-214"></a>Hibajavítások 2.1.4 ###

- [Hibajavítás] [Kisegítő lehetőségek] - Nem, nem, nem, nem, nem, A felhasználók nem tudnak a vezérlősávon a teljes képernyős gomb jobb oldalán található egyéni gombokra összpontosítani
- [Hibajavítás] [IE11] [Hangerősáv] A hangerő-felpakut vált a teljes videoképernyő villogása IE11-ben teljes képernyős módban
- [Hibajavítás] [Bőr| Kiürítés] A vezérlősáv és a hangerősáv előugró ablaka között megjelenő térköz
- [Hibajavítás] Nem, nem, nem, nem, nem, nem [Feliratok] A régi beágyazott sávok nem törlődnek, ha egy meglévő lejátszóforrást módosítanak
- [Hibajavítás] [Kisegítő lehetőségek] - Nem, nem, nem, nem, nem, A Képernyőolvasó helytelenül olvassa be a hangerőszabályzót
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Play Event alkalmanként nem tűz a Flash tech
- [Hibajavítás] Nem, nem, nem, nem, nem, nem - Nem, nem, nem, nem, nem, A lejátszás/szünet két kattintást igényel, ha a játékos fókuszban van, és teljes képernyős módban van
- [Hibajavítás] Nem, nem, nem, nem, nem, nem - Nem, nem, nem, nem, nem, Helytelen időtartam jelenik meg egy adott eszköz folyamatjelző sávján
- [Hibajavítás] [ Hirdetések] - Nem, nem, nem, nem, nem, nem, nem, nem, A VAST-elemző nem kezeli azokat a VAST-fájlt, amely nem rendelkezik folyamatjelző-eseménnyel
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, [AMP 2.1.1] Kijavítottuk a Hive SDN plugin támogatásával kapcsolatos problémát
- [Hibajavítás] [Kisegítő lehetőségek] A Narrátor felolvassa a "Midnight Mute button" (Éjféli elnémítás gomb) szöveget, ha a felhasználó a hangerőre fókuszál

### <a name="changes-214"></a>Változások 2.1.4 ###

- - Nem, nem, nem, nem, nem, [Kisegítő lehetőségek] [Kisegítő technológia] Gombok most már aria-live ingatlan, hogy javítsa a tapasztalat segítő technológia
- - Nem, nem, nem, nem, nem, [Kisegítő lehetőségek] [Hangerő gomb| Narrátor]A hangerőgomb kisegítő lehetőségeinek javítása a betabájzási funkció és a csúszka viselkedésének módosításával. Ezek a módosítások megkönnyítik a billentyűzet használói számára a lejátszó
- - Nem, nem, nem, nem, nem, Fokozott inaktivitási helyi menü időmeghosszabbítása 3-5 másodpercről
- - Nem, nem, nem, nem, nem, [Kisegítő lehetőségek] - Nem, nem, nem, nem, nem, nem, nem, nem, nem, nem Továbbfejlesztett fényességi kontrasztarány a legördülő menükben a feliratbeállításokban

## <a name="213-official-update"></a>2.1.3 (hivatalos frissítés) ##

### <a name="bug-fixes-213"></a>Hibajavítások 2.1.3 ###

- [Hibajavítás] [Dugó| Cím Overlay] Cím Overlay plugin dob JS kivételek AMP v2. X+
- [Hibajavítás] A Forráskészlet esemény akkor is elküldésre kerül a JavaScript-konzolra, ha a naplózás ki van kapcsolva
- [Hibajavítás] - Nem, nem, nem, nem, nem, A játékos időtippjei a játékos kontextusán kívül jelennek meg, amikor az egérmutatót mindkét végidőtartam-sáv fölé viszik
- [Hibajavítás] [Kisegítő lehetőségek] [Képernyőolvasó] A Narrátor a "Region Landmark" vagy a "Video Player Region Landmark" szöveget olvassa fel, ha a megtekintő a lejátszóra összpontosít
- [Hibajavítás] Nem, nem, nem, nem, nem, nem A lejátszó körvonala nem tiltható le CSS-en keresztül
- [Hibajavítás] [Kisegítő lehetőségek] Nem lehet a teljes lejátszóra összpontosítani, ha a felhasználó teljes képernyős módban van
- [Hibajavítás] - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, A bőr nem reagál a lokalizált LIVE szövegre japánul
- [Hibajavítás] - Nem, nem, nem, nem, nem, Az időtartam és az aktuális idő lelesz vágva, amikor a stream 60 perces > -[Hibajavítás][iPhone| Élő]lejátszó az aktuális idő/időtartam szövegét jeleníti meg a vezérlősávon
- [Hibajavítás] Nem, nem, nem, nem, nem, nem A játékos heurisztika API-k hívása JavaScript-kivételeket eredményez
- [Hibajavítás] [Natív Html5|iOS] Videotag tulajdonság "playsinline" nem terjed a játékos
- [Hibajavítás] [iOS|iframe] A lejátszó nem tud teljes képernyős állapotba lépni az iPhone-on, ha a lejátszó be van töltve egy iframe-be
- [Hibajavítás] Nem, nem, nem, nem, nem, nem - Nem, nem, nem, nem, nem, nem, nem, nem, nem, nem Az AMP mindig hibrid profillal működik, függetlenül a játékoslehetőségektől
- [Hibajavítás] [AMP| Win8.1]dob, ha a Win8.1 alkalmazásban található egy webview

### <a name="changes-213"></a>Változások 2.1.3 ###

- - Nem, nem, nem, nem, nem, Nem, nem, nem, nem, nem, nem CDN-végpontinformáció hozzáadva a FragmentDownloadComplete eseményben
- - Nem, nem, nem, nem, nem, Nem, nem, nem, nem, nem, nem - Nem, nem, nem, nem, nem, Továbbfejlesztett és optimalizált élő streamelési késleltetés

## <a name="212-official-hotfix"></a>2.1.2 (hivatalos gyorsjavítás) ##

### <a name="bug-fixes-212"></a>Hibajavítások 2.1.2 ####

- [Hibajavítás] [Kisegítő lehetőségek] [Windows Narrátor] A Narrátor felolvassa a "Progress midnight" szöveget, ha a felhasználó nak van egy folyamatjelző sávja, és az aktuális idő 0:00
- [Hibajavítás] [Skin]logo mérete nehezen kódolt JavaScript-kóddal
- [Kisegítő lehetőségek] - Nem, nem, nem, nem, nem, nem, nem A gyorsbillentyűk nincsenek engedélyezve, ha a lejátszóra kattintanak.

### <a name="changes-212"></a>Változások 2.1.2 ####

- - Nem, nem, nem, nem, nem, Nem, nem, nem, nem, nem, nem Naplólista URL-címe, ha a játékos nem tudja betölteni a jegyzékfájlt

### <a name="features-212"></a>Jellemzők 2.1.2 ###

- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, [Optimalizálás] Továbbfejlesztett lejátszóterhelés és indítási idők

## <a name="211-official-update"></a>2.1.1 (hivatalos frissítés) ##

### <a name="bug-fixes-211"></a>Hibajavítások 2.1.1 ####

- [Hibajavítás] [iOS] Az automatikus lejátszás hamis ra állítása végtelen pörgettyűt eredményez az iOS Safariban
- [Hibajavítás] A tartalom időtartamánál nagyobb időre való törekvés végtelen pörgettyűt eredményez
- [Hibajavítás] A gyorsbillentyűk több billentyűzetfülre van szükség ahhoz, hogy a lejátszó kontextusa működjön
- [Hibajavítás] Videó lefagy néhány másodpercig átméretezése után a játékos bizonyos eszközök
- [Hibajavítás] Végtelen tárcsa (után keres befejezi), ha a felhasználó nem több törekszik gyorsan
- [Hibajavítás] A vezérlősáv inaktivitás közben nincs elrejtve
- [Hibajavítás] Az AMP-t tartalmazó webapp megnyitása a weblap kétszeri betöltését okozhatja
- [Hibajavítás] Végtelen játék közben tartalom bizonyos eszközök keresztül Flash Tech
- [Hibajavítás] További beállítások menü nem jelenik meg a 3rd party plugins
- [Hibajavítás] [Bőr| Tube][Élő] Két élő ikon jelenik meg, ha a játékos egy program élő élvonalában van
- [Hibajavítás] - Nem, nem, nem, nem, nem, Az embléma nem tiltható le
- [Hibajavítás] [DD+ tartalom] Folyamatos pörgettyű jelenik meg a Dolby Digital hangsávot tartalmazó eszközökszámára
- [Hibajavítás] A legújabb AMP lefagy, amikor hangnyelvi sávokat vált az élő közvetítés során
- [Bug Fix] fix háttér eltűnése a tárcsa
- [Hibajavítás] Végtelen pörgettyű az AES flash token statikus minták hibajavítások

### <a name="changes-211"></a>Változások 2.1.1 ####

- - Nem, nem, nem, nem, nem, Hozzáadott hibakód widevine Https követelmény: a Chrome v58, widevine tartalmat `https://` kell betölteni / lejátszani a protokollon keresztül, különben lejátszás sikertelen lesz.
- - Nem, nem, nem, nem, nem, Hozzáadott ária címke betöltésére fonó, így segítő technológia narrátor "videó betöltése", ha a tartalom betöltése  

## <a name="210-official-release"></a>2.1.0 (Hivatalos kiadás) ##

### <a name="features-210"></a>Jellemzők 2.1.0 ###

- [Szolgáltatás] [AzureHtml5JS] VOD hirdetés támogatás a közép-utáni tekercsek hez
- [Szolgáltatás] - Nem, nem, nem, nem, nem, [AzureHtml5JS] Élő hirdetés támogatás a közepes utánra vonatkozó tekercsekhez
- [Szolgáltatás] Hozzáadott új bőr opció - AMP-flush
- [Szolgáltatás] Hozzáadott jobb ária címkék jobb integráció képernyőolvasók / segítő technológia
- [Szolgáltatás] - Nem, nem, nem, nem, nem, A felszín mostantól az összes ikont és gombot jól mutatja nagy kontrasztú módban

### <a name="bug-fixes-210"></a>Hibajavítások 2.1.0 ###

- [Hibajavítás] Kisegítő lehetőségek és felhasználói felületi javítások száma
- [Hibajavítás] Az AMP nem töltődik be megfelelően az IE9-ben

### <a name="changes-210"></a>Változások 2.1.0 ###

- - Nem, nem, nem, nem, nem, Átalakított DOM elemek játékos elhelyezésére hirdetések munka
- - Nem, nem, nem, nem, nem, CsS-ről SCSS-re váltott a bőr fejlesztéséhez
- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, Hozzáadott minta VOD hirdetések
- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, Hozzáadott minta a lejátszási sebességhez
- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, Hozzáadott minta Flush Skin

## <a name="200-beta-release"></a>2.0.0 (bétaverzió) ##

- [Change]frissítve VJS5
- [funkció] Hozzáadott új folyadék API a játékosok válaszkészség folyadék
- [Szolgáltatás] Lejátszási sebesség
- - Nem, nem, nem, nem, nem, CsS-ről SCSS-re váltson a bőrszámára

## <a name="183-official-hotfix-update"></a>1.8.3 (hivatalos gyorsjavítás-frissítés) ##

### <a name="bug-fixes-183"></a>Hibajavítások 1.8.3 ###

- [Hibajavítás] [AzureHtml5JS] A negatív DTS-sel rendelkező bizonyos eszközök nem fognak lejátszani a Chrome-ban

## <a name="182-official-hotfix-update"></a>1.8.2 (Hivatalos gyorsjavítás-frissítés) ##

### <a name="bug-fixes-182"></a>Hibajavítások 1.8.2 ###

- [Hibajavítás] [AzureHtml5JS] A magasabb hangbitsebességek nem játszódnak le az AzureHtml5JS-en keresztül

## <a name="181-official-update"></a>1.8.1 (hivatalos frissítés) ##

### <a name="bug-fixes-181"></a>Hibajavítások 1.8.1 ###

- [Hibajavítás] [iOS] A feliratok/feliratok nem jelennek meg a natív lejátszóban
- [Hibajavítás] Nem, nem, nem, nem, nem, nem CDN-fedezetű streamelési URL-címek, amelyek nem lejátszott hitelesítési tokenekkel vannak elfűzve
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem FairPlay hibakód hiányzik Tech ID (Bits [31-28] a ErrorCode) lásd hibakódok további részletek
- [Hibajavítás] - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, nem, nem PlayReady tartalom a Safariban végtelen tárcsa

### <a name="changes-181"></a>Változások 1.8.1 ###

- - Nem, nem, nem, nem, nem, [ Html5] Módosítsa a natív Html5 tech részletes naplókat, hogy tartalmazza a VideoTag eseményeit

## <a name="180-official-update"></a>1.8.0 (hivatalos frissítés) ##

### <a name="features-180"></a>Jellemzők 1.8.0 ###

- [Jellemzők] - Nem, nem, nem, nem, nem, nem, nem Hozzáadott FairPlay támogatás (további információ: [Védett tartalom)](azure-media-player-protected-content.md)

### <a name="bug-fixes-180"></a>Hibajavítások 1.8.0 ###

- [Hibajavítás] Nem, nem, nem, nem, nem, nem A felhasználókeresések nem váltanak ki várakozási eseményt, ha a hálózat szabályozása
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, A flash tech minőségének kiválasztása kivételt okoz
- [Hibajavítás] Nem, nem, nem, nem, nem, nem A minőség dinamikus kijelölése megjelenik a helyi menüben
- [Hibajavítás] - Nem, nem, nem, nem, nem, Nehéz kiválasztani a helyi menük utolsó menüelemét

### <a name="changes-180"></a>Változások 1.8.0 ###

- - Nem, nem, nem, nem, nem, Frissített lejátszó a Chrome EME jelenlegi követelményeinek megfelelően
- - Nem, nem, nem, nem, nem, Alapértelmezett techOrder megváltozott elhelyezésére új tech- html5FairPlayHLS [(lásd: Védett tartalom](azure-media-player-protected-content.md) további információkért)
- - Nem, nem, nem, nem, nem, [AzureHtml5JS] Engedélyezett MPEG-Dash lejátszás a Safariban
- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, Megváltozott a Multi-DRM minták a FairPlay befogadására

## <a name="174-official-hotfix-update"></a>1.7.4 (Hivatalos gyorsjavítás-frissítés) ##

### <a name="bug-fixes-174"></a>Hibajavítások 1.7.4 ###

- [Hibajavítás] Nem, nem, nem, nem, nem, nem Kék tagolás jelenik meg a seek handle körül, ha a felhasználó nak van kontextusa a lejátszó
- [Hibajavítás] [IE9] JavaScript kivétel dobott, amikor a játékos betöltve IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (Hivatalos gyorsjavítás-frissítés) ##

### <a name="bug-fixes-173"></a>Hibajavítások 1.7.3 ###

- [Hibajavítás] [AzureHtml5JS] Játékos időtúllépés a korlátozott hálózatokban

### <a name="changes-173"></a>Változások 1.7.3 ###

- - Nem, nem, nem, nem, nem, Webcrypto engedélyezése az Edge-en az AES-tartalom visszafejtéséhez
- - Nem, nem, nem, nem, nem, Az AMP heurisztika optimalizálása a gyorsítótárazott adattömbök figyelembevételével
- - Nem, nem, nem, nem, nem, [AzureHtml5JS] A heurisztikus optimalizálása a sávszélesség becslésének késleltetésének csökkentésével

## <a name="172-official-hotfix-update"></a>1.7.2 (Hivatalos gyorsjavítás-frissítés) ##

### <a name="features-172"></a>Jellemzők 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [Szolgáltatás] [AzureHtml5JS| Firefox] A Widevine lejátszásának engedélyezése az EME firefox 47+ számára
- [Szolgáltatás] Esemény hozzáadása a lejátszó dúsítódulatához
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Hibajavítások 1.7.2 ###

- [Hibajavítás] Kódolt Akamai CDN URL lekérdezési paraméterek nem megfelelően dekódolt
- [Hibajavítás] Kivétel jelenik meg a manifestPlayableWindowLength()
- [Hibajavítás] A megtekintő nem mindig kattinthat a videó lejátszása után, miután a videó újranézett
- [Hibajavítás] Az adaptív méretezés nem felel meg a gyors ablakméret-változásoknak
- [Hibajavítás] [Él| IE] Az adaptív méretezés nem lép érvénybe a width=x, height=auto oldalterhelésnél
- [Hibajavítás] [Android| Chrome] Chrome kér engedélyt a DRM-tartalom lejátszására, ha a tartalom nincs titkosítva
- [Hibajavítás] [Kisegítő lehetőségek] - Nem, nem, nem, nem, nem, A billentyűzetvezérlők nem jelölik ki megfelelően a helyi menü elemeit
- [Hibajavítás] [Kisegítő lehetőségek] Hiányzik a megjelenített szegély kontrasztos módban
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Az egérfelegér-figyelő nem lett eltávolítva a lejátszó dispose után kivételt okoz
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Jegyzékfájl URL-címének elemzési problémája kódolt szóközökkel
- [Hibajavítás] [iOS] Típushiba a tech.featuresVolumeControl kiértékelésekor

### <a name="changes-172"></a>Változások 1.7.2 ###

- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, nem, nem Áthelyezte a DRM-ellenőrzéseket a forrás beállítása után, hogy csak akkor ellenőrizze, ha a tartalom titkosítva van
- - Nem, nem, nem, nem, nem, -1, hogy ne kellne. A kulcskézbesítési kérelemből eltávolított, definiálatlan típus/egyszerű törzs
- - Nem, nem, nem, nem, nem, [Kisegítő lehetőségek] A Windows narrátor mostantól a "Media Player" szöveget olvassa el, ha a kontextus a lejátszón van a tulajdonságok helyett

## <a name="171-official-hotfix-update"></a>1.7.1 (Hivatalos gyorsjavítás-frissítés) ##

### <a name="features-171"></a>Jellemzők 1.7.1 ###

- [Szolgáltatás] Hozzáadott lehetőség hibrid heurisztikus profil (ez a profil alapértelmezés szerint be van állítva)

### <a name="bug-fixes-171"></a>Hibajavítások 1.7.1 ###

- [Hibajavítás] Az adaptív kialakítás nem a HTML5-szabvány szerint működik (width=100%, height=auto)
- [Hibajavítás] A szélesség és a magasság százalékos értékei nem a várt módon viselkednek az 1.7.0-s részben.

## <a name="170-official-update"></a>1.7.0 (hivatalos frissítés) ##

### <a name="features-170"></a>Jellemzők 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [Szolgáltatás] [AzureHtml5JS] - Nem, nem, nem, nem, nem, nem, nem, nem, Hozzáadott currentMediaTime() hogy a kódoló média idő az aktuális idő másodpercben
- [Szolgáltatás] - Nem, nem, nem, nem, nem, nem, nem, nem, Megvalósított letöltési telemetriai API-k videoBufferData() és audioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [Szolgáltatás] - Nem, nem, nem, nem, nem, nem, nem, nem, Hozzáadott "downloadbitratechanged" esemény
- [Szolgáltatás] A betöltési idő javult a lejátszó régebbi verzióihoz képest
- [Szolgáltatás] A hibákat a Rendszer a JavaScript-konzolra naplózza

### <a name="bug-fixes-170"></a>Hibajavítások 1.7.0 ###

- [Hibajavítás] Kódolt poszter URL-cím olyan lekérdezési karakterlánc-paraméterekkel, amelyek nem jelennek meg a lejátszóban
- [Hibajavítás] Kivétel történt, ha nincs tech betöltve és API erősítő. Player.poster() neve
- [Hibajavítás] Kivétel, amikor a függvények megpróbálnak hozzáférni a lejátszóhoz az ártalmatlanítás után
- [Hibajavítás] [Kisegítő lehetőségek] Hiányzó vázlat a progressdénysáv-keresőfejre való összpontosításról
- [Hibajavítás] [Kisegítő lehetőségek] A helyi menük árnyéka kontrasztos módban
- [Hibajavítás] [iOS] natív játékos WebVTT feliratok lejátszás nem működik
- [Hibajavítás] [AzureHtml5JS] Hiba 0x0100002 kell jelennie, amikor játszik HTTP stream HTTPS oldalon, hogy ahelyett, hozamok végtelen tárcsa eredményeként vegyes tartalom
- [Hibajavítás] [AzureHtml5JS] Hiányzó zárószegmens, amely hurokállapot-ellenőrzési hibát okoz, és végtelen pufferelési állapotot jelenít meg
- [Hibajavítás] [AzureHtml5JS] Helytelen hangsáv név a menüben a ManifestForLabel=false és a hárombetűs nyelvkódok használatakor
- [Hibajavítás] [AzureHtml5JS| Chrome] Érzékelt végtelen puffer állapot végén a tartalom által okozott lebegőpontos pontatlanság időtartama JavaScript a Chrome-ban
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Nem végzetes időszakos hiba egy pillanatra jelenik meg, amikor flash player létre
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Lejátszás sikertelen, ha a video-és audio-patakok használata különböző időskálák miatt kerekítéspontatlanság nem a "Fragment url (...) nem sikerült flvtag-ot létrehozni"
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, A jegyzékfájl URL-címének kódolt szóközökkel való elemzésével kapcsolatos problémák
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Hiányzó ellenőrzés annak megállapítására, hogy a Flash player verziója >= 11.4, amely hibát okoz a lejátszásban, ahelyett, hogy visszaesne a techOrder következő technológiájára
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, -1, hogy ne kellne. Problémák az aláhúzással ellátott AES tokenek elfogadásával
- [Hibajavítás] [SilverlightSS| Az OSX] "//" a protokoll (HTTP vagy HTTPS) helyett egy jegyzékfájl előhívását a rendszer végtelen tárcsaként eredményező helyi fájlként ismeri fel.

### <a name="changes-170"></a>Változások 1.7.0 ###

- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, nem, nem, nem, Egyesített SWF-parancsfájlok ("MSAdaptiveStreamingPlugin-osmf2.0.swf" és "StrobeMediaPlayback.2.0.swf") egyetlen SWF-fájlba, a "StrobeMediaPlayback.2.0.swf"
- - Nem, nem, nem, nem, nem, - Nem, nem, nem, nem, nem, nem, nem, nem, Frissített hibakód-propagálás, hogy pontosabb hibakódokat kapjon (pl. 404s most eredménye0x30200194 helyett általános hiba 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (hivatalos gyorsjavítás-frissítés) ##

### <a name="bug-fixes-163"></a>Hibajavítások 1.6.3 ###

- [Hibajavítás] JavaScript-futásidejű kivétel, ha a gyorsbillentyűk eseménykezelője a lejátszó ártalmatlanítása után kerül végrehajtásra
- [Hibajavítás] - Nem, nem, nem, nem, nem, [AzureHtml5JS] Nincs lejátszás mobileszközön mobilhálózaton
- [Hibajavítás] Frissítve Forge futtatni, mint a webes munkavállaló, hogy szabadítsa fel UI

## <a name="162-official-hotfix-update"></a>1.6.2 (Hivatalos gyorsjavítás-frissítés) ##

### <a name="features-162"></a>Jellemzők 1.6.2 ###

- [Szolgáltatás] További nyelvek hozzáadása a honosításhoz (további részleteka dokumentációban)

### <a name="bug-fixes-162"></a>Hibajavítások 1.6.2 ###

- [Hibajavítás] [IE9-10] Kattintson a területek körül a játékos minimalizált böngésző ablak miatt IE9/IE10 hiba, amely minimálisra csökkenti a window.blur()
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Aláhúzással ellátott AES-tokenek nem fogadása

## <a name="161-official-hotfix-update"></a>1.6.1 (Hivatalos gyorsjavítás-frissítés) ##

### <a name="bug-fixes-161"></a>Hibajavítások 1.6.1 ###

- [Hibajavítás] [Villog| Él,IE][SilverlightSS| IE] Nem tud összpontosítani más ui elemek bemenetek vagy más IE / Edge
- [Hibajavítás] AES lejátszás sikertelen, ha kovácsolni undefined
- [Hibajavítás] - Nem, nem, nem, nem, nem, [AzureHtml5JS| Chrome] A folyamatos pörgettyű nem játssza le a tartalmat az állapotellenőrző ciklusban
- [Hibajavítás] [IE9] console.log() nem támogatja az IE 9 kivételt okozó

## <a name="160-official-update"></a>1.6.0 (hivatalos frissítés) ##

### <a name="features-160"></a>Jellemzők 1.6.0 ###

- [Feature] 33%-os méretcsökkentése azuremediaplayer.min.js
- [Szolgáltatás] [AzureHtml5JS| Edge][Nem tesztelt] DD+ hangfolyamok támogatása az Edge-ben (a kezdeti választás után nincs kodekváltás). Az alkalmazásnak ekkor kell kiválasztania a megfelelő hangfolyamot.
- [Szolgáltatás] Gyorsbillentyű-vezérlők (további részletek a dokumentumokban)
- [Szolgáltatás] Haladás idő tipp lebeg az idő pontos keres
- [Szolgáltatás] Engedélyezze a bővítmények aszinkron észlelését, ha a setupDone metódus létezik a beépülő modulban

### <a name="bug-fixes-160"></a>Hibajavítások 1.6.0 ###

- [Hibajavítás] A memórianapló nem ürül a getMemoryLog(true)
- [Hibajavítás] A bitráta választómező visszaáll az egér mozgatásakor, ami problémát okoz az alacsonyabb bitráták egérvezérléssel történő kiválasztásával
- [Hibajavítás] Az applikátor Mac Office-összeomlása a DRM-ellenőrzés végrehajtásakor
- [Hibajavítás] CSS osztályok könnyen véletlenül felülírja
- [Hibajavítás] Nem, nem, nem, nem, nem, nem A felhasználói ügynök karakterlánc-böngészőjének frissítése az Edge
- [Hibajavítás] [AzureHtml5JS] A Feliratok gomb nem jelenik meg az eszköztáron az Edge(Win10) vagy a Chrome(Mac)
- [Hibajavítás] - Nem, nem, nem, nem, nem, [AzureHtml5JS| Chrome] InvalidStateError kivétel endOfStream() rövid videók on endOfStream() hívása
- [Hibajavítás] [Firefox] A Firefox által okozott DRM-figyelmeztetés eltávolítása a böngésző képességeinek ellenőrzésekor
- [Hibajavítás] [ Html5] A feliratok/feliratok nem jelennek meg progresszív mp4 tartalommal
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Az egyező időbélyeggel rendelkező üzenetek fordított sorrendben voltak naplózva
- [Hibajavítás] [Kisegítő lehetőségek] [Chrome| Firefox] Tab és válasszuk ellenőrzések automatikusan válassza ki az első menüpont
- [Hibajavítás] [Kisegítő lehetőségek] Tab billentyűvel vezéreli a hangerőgombot

### <a name="changes-160"></a>Változások 1.6.0 ###

- - Nem, nem, nem, nem, nem, Az AES visszafejtési idejének használata minőségi szintű kiválasztáskor
- - Nem, nem, nem, nem, nem, Az URL-újraíró frissítése a HLS v4 használata a HLS v3 előtt a többhangú adatfolyamok hoz
- - Nem, nem, nem, nem, nem, Állítsa a nativeControlsForTouch értéket hamis értékre alapértelmezettként (a megfelelő működéséhez hamisnak kell lennie)

## <a name="150-official-update"></a>1.5.0 (hivatalos frissítés) ##

### <a name="features-150"></a>Jellemzők 1.5.0 ###

- [Szolgáltatás] Fejlesztések az általános webes biztonság (megelőzése injekció, XSS, stb)
- [Szolgáltatás] SDN plugin integrációs horgok sourceset esemény és options.sdn
- [Szolgáltatás] Az 5XX és 4XX hibák robusztussági kezelése lejátszás közben

### <a name="bug-fixes-150"></a>Hibajavítások 1.5.0 ###

- [Hibajavítás] A CSS-minification frissítése a Html-entitás betűtípuskódjainak használatához a Unicode helyett
- [Hibajavítás] [AzureHtml5JS] A multi-DRM-tartalom mindig kiválasztja az első elem tokenjét a protectionInfo-ből, ami a második DRM sikertelensítését okozza
- [Hibajavítás] [AzureHtml5JS] Keresek soha nem fejeződik be, ha keres egy olyan területen, a hiányzó szegmensek.
- [Hibajavítás] [AzureHtml5JS| Edge] Előtaggal rögzített EME engedélyezése az Edge frissítésben a PlayReady lejátszáshoz
- [Hibajavítás] [AzureHtml5JS| Firefox] Update EME ellenőrzés, hogy a Firefox v42 + (az MSE) a tartalék silverlight a védett tartalom
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, A error.message frissítése számról részletes karakterláncra

### <a name="changes-150"></a>Változások 1.5.0 ###

- - Nem, nem, nem, nem, nem, A poszterek jelenleg csak abszolút URL-ként működnek.

## <a name="140-official-update"></a>1.4.0 (hivatalos frissítés) ##

### <a name="features-140"></a>Jellemzők 1.4.0 ###

- [Szolgáltatás] [AzureHtml5JS| Chrome] Egyszerű Widevine DRM támogatás
- [Szolgáltatás] [AzureHtml5JS] A 404/412 hibák robusztussági kezelése lejátszás közben

### <a name="bug-fixes-140"></a>Hibajavítások 1.4.0 ###

- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, A paraméterek érvényesítésének javítása

## <a name="130-official-update"></a>1.3.0 (hivatalos frissítés) ##

### <a name="features-130"></a>Jellemzők 1.3.0 ###

- [Szolgáltatás] [AzureHtml5JS] - Nem, nem, nem, nem, nem, nem, nem, nem, Hangváltás ugyanazzal a kodek multi-audio tartalommal

### <a name="bug-fixes-130"></a>Hibajavítások 1.3.0 ###

- [Hibajavítás] [AzureHtml5JS| Chrome] Időszakos végtelen tárcsa
- [Hibajavítás] [AzureHtml5JS| IE][Windows Phone] Kivétel, amely a Windows Phone-telefon lejátszási problémáit okozza
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Az automatikus lejátszás hamis nem sikerül további példányok esetén
- [Hibajavítás] A felhasználói felület menüjének méretezési problémái

## <a name="120-official-update"></a>1.2.0 (hivatalos frissítés) ##

### <a name="features-120"></a>Jellemzők 1.2.0 ###

- [Szolgáltatás] [AzureHtml5JS| Firefox] Támogatás, ha az MSE engedélyezve van
- [Szolgáltatás] Már nincs szükség az alkalmazásra, hogy elérési utakat biztosítson a tartalék tech binárisfájlokhoz (swf, xap). Az elérési út az Azure Media Player parancsfájlhoz viszonyítva érhető el.

### <a name="bug-fixes-120"></a>Hibajavítások 1.2.0 ###

- [Hibajavítás] [AzureHtml5JS| Chrome] A lejátszó az élő él mögé sodródik, amikor a játékos a háttérben
- [Hibajavítás] [AzureHtml5JS| Edge] A teljes képernyő nem működik
- [Hibajavítás] [AzureHtml5JS] A naplózás nincs megfelelően engedélyezve, ha meg vannak állítva a beállításokban
- [Hibajavítás] - Nem, nem, nem, nem, nem, Mind a "pufferelés" és a pufferelésikon megjelenik a várakozási esemény során
- [Hibajavítás] A lejátszás folytatásának engedélyezése, ha a kezdeti sávszélesség-kérelem sikertelen
- [Hibajavítás] A játékos nem töltődik be, ha nem definiált beállításokkal inicializálva
- [Hibajavítás] Amikor megpróbálja megsemmisíteni a lejátszót, miután már ártalmatlanították, vdata kivétel lép fel
- [Hibajavítás] A minőségi sáv ikonjai helytelenül vannak leképezve

## <a name="111-official-hotfix-update"></a>1.1.1 (Hivatalos gyorsjavítás-frissítés) ##

### <a name="bug-fixes-111"></a>Hibajavítások 1.1.1 ###

- [Hibajavítás] Régebbi IE teljes képernyős probléma
- [Hibajavítás] A bővítmények már nem felülíródnak

## <a name="110-official-update"></a>1.1.0 (hivatalos frissítés) ##

### <a name="features-110"></a>Jellemzők 1.1.0 ###

- [Szolgáltatás] Felhasználói felület honosítási karakterláncának frissítése

### <a name="bug-fixes-110"></a>Hibajavítások 1.1.0 ###

- [Hibajavítás] A Big Play gombnak nincs elég kontrasztja
- [Hibajavítás] Vizuális lap fókuszjelzője
- [Hibajavítás] Válassza ki a Bitráta menüt a megfelelő felbontási adatok használatával
- [Hibajavítás] További beállítások menü most dinamikusan méretezett
- [Hibajavítás] Különböző felhasználói felületi problémák

## <a name="100-official-release"></a>1.0.0 (Hivatalos kiadás) ##

### <a name="features-100"></a>Jellemzők 1.0.0 ###

- [Szolgáltatás] Alapvető kisegítő lehetőségek tesztelése a tabulátorvezérléshez, a fókuszvezérléshez, a képernyőolvasóhoz, a kontrasztos felhasználói felülethez
- [Szolgáltatás] Frissített felhasználói felület
- [Szolgáltatás] Fejlesztői naplózás
- [Szolgáltatás] API a feliratok/feliratok dinamikus beállításához
- [Szolgáltatás] Alapvető honosítási funkciók
- [Szolgáltatás] Hibakód-konszolidáció a technikusok között
- [Szolgáltatás] Új hibakód arra az okra, ha a bővítmények (például a Flash vagy a Silverlight) nincsenek telepítve
- [Szolgáltatás] [AzureHtml5JS] Alapvető diagnosztikai események megvalósítása

### <a name="bug-fixes-100"></a>Hibajavítások 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Hibajavítás] [AzureHtml5JS] Az MPD-frissítések élő lejátszásának fagyasztása, ha az időbélyegben kis pontatlanságok vannak
- [Hibajavítás] [AzureHtml5JS] Számos élő lejátszási probléma enyhítése
- [Hibajavítás] [AzureHtml5JS] Pufferek kiürítése, ha az ablakméret heurisztika be van kapcsolva, és nagyobb felbontású képernyőre lép
- [Hibajavítás] [AzureHtml5JS] A Chrome mostantól megfelelően mutatja a véget ért eseményt. A Chrome korábbi ismert problémája *nem&euro;küld megfelelően az&euro;AzureHtml5JS használatakor. Probléma van az alapul szolgáló böngészőben.*
- [Hibajavítás] [AzureHtml5JS] Letiltotta a Safarit ehhez a technológiához, hogy megoldhassa az *OSX Yosemite és az AzureHtml5JS technológia lejátszási problémájait. Vannak MSE megvalósítási problémák. Ideiglenes mérséklése:&euro;erő&euro;&euro;â&euro;œflashSSâ , â œsilverlightSSâ a tech érdekében e felhasználói ügynökök*
- [Hibajavítás] [FlashSS] loadstart a hiba után kiindulva

## <a name="020-beta"></a>0.2.0 (béta) ##

### <a name="features-020"></a>Jellemzők 0.2.0 ###

- [Szolgáltatás] Befejeződött a PlayReady és az AES tesztelése igény és élő használatra – lásd a kompatibilitási mátrixot
- [Szolgáltatás] A folytonossági hiány okának kezelése
- [Szolgáltatás] 2^53-nál nagyobb időbélyegek támogatása
- [Szolgáltatás] Az URL-lekérdezési paraméter megmarad a jegyzékfájl-kérelemben
- [Szolgáltatás] [Nem tesztelt] Heurisztikus `QuickStart` `HighQuality` profilok támogatása
- [Szolgáltatás] [Nem tesztelt] A videostream-információk felfedése bitsebességekhez, szélességhez és magassághoz az AzureHtml5JS és flashSS esetén
- [Szolgáltatás] [Nem tesztelt] Bitráta kiválasztása az AzureHtml5JS és a FlashSS szolgáltatásban (lásd: API dokumentáció)

### <a name="bug-fixes-020"></a>Hibajavítások 0.2.0 ###

- [Bug Fix] nagy lejátszás gomb most már megtekinthető wp8.1
- [Bug Fix] rögzített több élő lejátszás kérdések
- [Bug Fix] visszahangosítás gomb most működik a felhasználói felületen
- [Bug Fix] frissített felhasználói felület betöltési élménye az automatikus lejátszási módhoz
- [Hibajavítás] AMD betöltő probléma és metódusütközések meghatározása
- [Hibajavítás] WP 8.1 A Cordova App betöltési problémája
- [Hibajavítás] Védett tartalom lekérdezések platform/tech támogatott ProtectionType kiválasztásához a megfelelő tech lejátszáshoz.  Javítja a korábbi ismert probléma a "_PlayReady tartalom Chrome (asztali) / Safari 8 (OSX Yosemite) jelenleg nem tartalék silverlight játékos_"
- [Bug Fix] uncaught exception on WinServer 2012 R2 miatt Media Foundation nincs telepítve a gépen alapértelmezés szerint.  Kísérlet a HTML videotag API-k használatára, amelyek nincsenek megvalósítva, így hibát okoznak. A jelenlegi megoldás az, hogy elkapja a hibát, és hamis értéket ad vissza a hiba eldobása helyett.
- [Bug Fix] mindig kap az init szegmens után törekszik, vagy http nem, hogy megakadályozzák hibák lejátszás közben
- [Bug Fix] kapcsolja ki nyomon követése szimulált haladás és időfrissítések, ha hiba történt.
- [Bug Fix] távolítsa el a jobb klikk menü
- [Hibajavítás] [AzureHtml5JS] hibaüzenet nem jelenik meg, ha érvénytelen tokenkészlet van beállítva a PlayReady-tartalomhoz
- [Hibajavítás] [AzureHtml5JS] megy teljes képernyőn élő lejátszás közben nem vette ablak mérete heurisztika figyelembe
- [Hibajavítás] - Nem, nem, nem, nem, nem, nem, nem, nem, Eltávolított Strobe Media Player megjelenített üzeneteket, hogy csak az Azure Media Player üzenetek jelennek meg
- [Hibajavítás] [SilverlightSS] nem kapok "keresett" esemény, ha keresünk túl időtartama vagy kevesebb, mint 0

## <a name="010-beta-release"></a>0.1.0 (bétaverzió) ##

Első kiadás előtti

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)
