---
title: "Az Azure Mobile Engagement hibaelhárítási útmutatók"
description: "Az Azure Mobile Engagement a hibaelhárítási útmutatója"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 93b5e3f4892f974bf9df28955956136528470e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement – hibaelhárítási útmutató
## <a name="introduction"></a>Bevezetés
A következő hibaelhárítási útmutató segítségével megtudhatja, hogy az alapvető okok néhány gyakran látható problémákat és a rendszer engedélyezi, hogy a saját. 

## <a name="general"></a>Általános kérdések
Általánosságban elmondható mindig ellenőrizze a következőket:

1. Győződjön meg arról, hogy lezajlott leírtak szerint az integrációhoz szükséges összes lépést a [bevezető oktatóanyagok](mobile-engagement-windows-store-dotnet-get-started.md)
2. A Platform SDK legújabb verzióját használja. 
3. Teszt tényleges eszközre és az emulátor, mivel egyes problémák csak az emulátorban. 
4. Ön nem elérte-e bármilyen korlátok/szabályozások a Mobile Engagement ismertetett [Itt](../azure-subscription-service-limits.md)
5. Ha nem tud csatlakozni a service Mobile Engagement háttérrendszeréhez vagy megnéz adatok nem betöltését folyamatosan majd ellenőrizze, hogy vannak-e nincs folyamatban lévő szolgáltatás incidensek ellenőrzésével [Itt](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>"A figyelő" problémák
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>A Monitor lapon jelenik meg az eszköz nem látható
A figyelő lapon láthatók a Mobile Engagement platform valós idejű csatlakoztatott eszközöket. Ha az emulátor és az eszköz hibakeresést, majd megtekintheti az itt legalább egy munkamenetet. Ha az alkalmazás került, majd megjelenik az aktív munkamenetek mérőműszer tükrözik a valós idejű platform csatlakoztatott eszközök. 

Ha nem Ön az eszköz a Monitor lapon akkor célszerű valószínűleg SDK-integráció hibát. Kijavítható néhány általános lépései a következők:

1. Győződjön meg arról, hogy a megfelelő kapcsolati karakterláncot használ a mobilalkalmazásban, és az SDK kulcsai című szakaszban, és nem a API kulcsai című szakaszban. A kapcsolati karakterláncot a mobilalkalmazás csatlakozik a Mobile Engagement-alkalmazást, amelyben látni fogja az eszköz a Monitor lapon példányát. 
2. Windows Platform - Ha az oldala felülírja a `OnNavigatedTo` metódust, hívja meg a `base.OnNavigatedTo(e)`.
3. Ha egy meglévő mobilalkalmazás rendszer integrálása a Mobile Engagement, akkor is biztosítható, hogy nem hiányoznak-e be a olyan lépéseket a speciális integrációs lépések megtekintésével [Itt](mobile-engagement-windows-store-integrate-engagement.md)
4. Győződjön meg arról, legalább egy képernyőt/tevékenység EngagementActivity dolgozik a platformtól függően a lap felülbírálásával küldi a [bevezető oktatóanyagok](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>A munkamenet megjelenítő Monitor lapon hivatkozva még akkor is rendelkezik kapcsolata megszakadt vagy esetén az alkalmazás bezárása / emulátor.
Ha Ön az egyetlen, a platform ezen a ponton csatlakozik, és az emulátor segítségével nyissa meg az alkalmazást majd valószínű emulátor régi stílusú miatt. Általában kell győződjön meg arról, hogy Ön térjen vissza a kezdőképernyőn a emulátor az alkalmazás munkamenet leválasztása sikeresen megtörtént. Emellett a Windows platformra, hibakeresése a Visual Studio során szükség lehet győződjön meg arról, hogy a Visual Studio programban, lépjen a **életciklus-események** egérrel a menüsoron, majd kattintson a **Suspend** valóban bezárásához a munkamenet. Lásd: [Windows oktatóanyag](mobile-engagement-windows-store-dotnet-get-started.md) részleteiről. 

## <a name="analytics-issues"></a>"Analytics" problémák
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>I nem jelennek meg adatok / Analytics lapon adatok frissítése
Analitikai adatok rendszeres időközönként újraszámítja, és eltarthat legfeljebb 24 órát a frissítést. Az adatok nem valós idejű, és látni fogja a 24 órás időszakban belül frissülnek.
Győződjön meg arról, azonban, hogy küldendő legalább egy képernyőit vagy tevékenységeit platform háttérkiszolgálóra által vagy felülbíráló legalább egy oldalt a `EngagementActivity` és a `SendActivity` explcitly. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Az elemzés lapon dátumérték eszköz helytelenül rögzített dátum és idő
Az adott időszakban az Analytics a dátumot a felhasználói eszközbeállítások alapul. Ezért figyeljen oda arra, hogy az eszköz rendelkezik-e a dátum helyesen beállítva. 

## <a name="segment-issues"></a>"Szegmens" problémák
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>A szegmens létrehozott és jelenik meg, mert szürkén jelenik meg, vagy nem jelennek meg adatok
Szegmens létrehozása jelenleg nem valós idejű. Az analitikai adatok összesített értéket és így is telhet, amíg legfeljebb 24 óra számítható egyszerre. Meg kell, próbálkozzon újra később, de eközben gondoskodnia kell arról, hogy a mobile apps szolgáltatásban valóban küld-e alapjául a szegmensek képező vannak. Például Ha egy esemény mondja ki a "PEL" nem küldött minden olyan mobileszközre által, akkor egy szegmens EventName létre szegmens adatokat így nem = PEL a feltételként. Azt is ellenőrizze annak biztosítása érdekében a mobilalkalmazás SDK-integrációjában megfelelően küldi az adatokat. 

## <a name="reach-or-push-notifications-issues"></a>"Elérni" vagy a leküldéses értesítésekkel kapcsolatos problémák
### <a name="my-push-messages-are-not-being-delivered"></a>A leküldéses üzenetek nem kézbesíthetők
1. Próbálja a vizsgálati eszköz először győződjön meg arról, hogy minden összetevő - mobilalkalmazás, SDK és a megfelelően csatlakoztatott, és leküldéses értesítéseket küldött értesítések küldése. 
2. Mindig értesítésküldés a legegyszerűbb "out-az-app" először egy kampányt, amely nem ütemezett keresztül, és sem rendelkezik a megadott célközönség-feltétel. Ez a újra igazolnia, hogy értesítést kapcsolat megfelelően működik-e. 
3. Problémák vannak az alkalmazásbeli értesítések kézbesítéséhez majd is esetén próbálja meg elküldeni először az alkalmazáson belüli out értesítések helyes első lépésként. 
4. Győződjön meg arról, hogy a "natív leküldés" megfelelően van beállítva a mobilalkalmazás számára. Platformtól függően ez akár tartalmazzon (Android, Windows) kulcsokat vagy tanúsítványokat (iOS). Lásd: [felhasználói felület – beállítások](mobile-engagement-user-interface-settings.md)
5. Alkalmazásból értesítések sikerült is blokkolja a felhasználó a mobil operációs rendszerek, győződjön meg arról, ez nem áll fenn. 
6. Győződjön meg arról, hogy nem állítja a *figyelmen kívül a célközönséget, leküldéssel fogják megkapni a felhasználók számára az API-n keresztül* beállítást a **kampány** egy Reach szakasza kampány, mivel ezzel biztosíthatja, hogy csak abban az esetben a leküldéses értesítések API-k keresztül küldött. 
7. Győződjön meg arról, hogy mindkét elkerülése érdekében a hálózati kapcsolat, a problémák lehetséges forrásaként Wi-Fi és a telefon operátor hálózaton keresztül csatlakoztatott eszközökkel a leküldéses kampány teszteli.
8. Győződjön meg arról, hogy a rendszer dátum/idő az eszköz/emulátorának megfelelő mert bármely olyan eszközről, amely szinkronizálva lesz is zavarják a leküldéses értesítési szolgáltatás értesítéseket. 

További platformspecifikus hibaelhárítás az alábbi utasításokat:

1. **iOS** 
   
   * Győződjön meg arról, hogy a tanúsítványok-e érvényes és leküldéshez leküldéses értesítések küldése iOS-hez. 
   * Győződjön meg arról, hogy megfelelően konfigurálja a *éles* tanúsítványt a Mobile Engagement-alkalmazáshoz. 
   * Győződjön meg arról, hogy a tesztelés egy *valódi, fizikai eszköz.* IOS-szimulátorban történő leküldéses üzenet nem dolgozható fel.
   * Győződjön meg arról, hogy a csomagazonosítót megfelelően van-e beállítva a mobilalkalmazásban. Az utasítások [Itt](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * Tesztelési, használjon Ad-Hoc"terjesztési a mobil létesítési profiljához. Nem lehet értesítést kapni, ha az alkalmazás fordítása a "Debug"
2. **Android**
   
   * Győződjön meg arról, hogy a helyes Projektszám megadott a mobilalkalmazás AndroidManifest.xml fájlhoz, amely \n karakter követ. 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Győződjön meg arról, hogy nem hiányoznak, vagy hibásan konfigurált azokat az engedélyeket az Android-jegyzékfájl 
   * Gondoskodjon arról, hogy a felvenni kívánt ügyfélalkalmazás Projektszám ugyanazt a fiókot a ahol portáltól a GCM kiszolgálói kulcsot. A két bármely eltérést kikerülését megakadályozza a leküldéses értesítések. 
   * Ha rendszer kap értesítést, de nem alkalmazásbeli tekintse át a [értesítések ikonjának megadása](mobile-engagement-android-get-started.md) valószínűleg a meg nem adja meg a megfelelő ikonra az Android Manifest fájl. 
   * Ha BigPicture értesítést küldenek, akkor győződjön meg arról, hogy ha külső kép kiszolgálóval rendelkezik, akkor azok képesnek kell lenniük az HTTP-támogatáshoz "GET" és "HEAD".
3. **Windows**
   
   * Győződjön meg arról, hogy társítva van az alkalmazás egy érvényes Windows áruház egy alkalmazásához. A Visual Studio - fog kattintson jobb gombbal a projekt és "Társítsa az Alkalmazásáruházból" beállítást, és válassza ki az alkalmazást létrehozta a Windows áruházban. A Windows Áruházbeli alkalmazás meg kell egyeznie egy adott portáltól a hitelesítő adatokat a natív a Mobile Engagement portál konfigurálása a.
   * Ha azért kapta, out-az-app leküldéses értesítések, de nem alkalmazáson belüli értesítések `EngagementOverlay` integrációs majd gondoskodjon arról, hogy a gyökérelem rács a lapon. Az első "Rács" elem az xaml-fájl hozzáadása két webes talál megtekinti a lapon EngagementOverlay használ. Ha meg szeretne keresni, ahol webes nézetek lesz beállítva, megadhatja a rács "EngagementGrid" nevű, ez azonban meg kell gondoskodjon arról, hogy elegendő magassága és szélessége által két további webes megtekinti amelyhez megjelenik az értesítés és a lenti, mint mint az alkalmazáson belüli értesítés:
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>A leküldéses értesítési/közlemény létrehozott/kampány, és akkor is, az elküldött me az értesítés, hogy azt "Aktív". Mit jelent?
A **kampány** létrehozott Mobile Engagement nevezik, mivel egy hosszú ideig tartó leküldéses értesítési jelentése szerint az új eszközök kapcsolódni a mobile engagement platform, azok automatikusan küldi az értesítés, Konfigurálja itt, mindaddig, amíg a kampány beállítása feltételnek megfelelnek. Ez az nem egy egy hibaüzenetet egyetlen értesítés beállítása. Később kell manuálisan kattintson a **Befejezés** gombra a kampány leáll, így nem további értesítések küldéséhez. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>A leküldéses kampány létrehozott és kapok értesítések sikeresen azonban megnyitok be az alkalmazást, amikor jelenik meg ezt az értesítést akkor is, ha korábban műveletet kiváltó azt előtt?
Valószínű, hogy a tesztelés során kerül sor, és ha az emulátorok vagy néhány teszteléséhez például TestFlight keretrendszer. Mi történik, ez, minden példány futtatása-alkalmazást, hogy egy új DeviceID beszerzése és és a háttérkiszolgáló kezelni egy új eszközt, és az értesítés küldése a Mobile Engagement platform okozó küldi el. 

## <a name="getting-support"></a>Keresztüli támogatás
Ha nem sikerül, a probléma megoldásához saját kezűleg akkor is:

1. Keresse meg a problémát a StackOverflow fórumon meglévő szálak és [MSDN fórum](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) , és ha nem, majd kérdése van. 
2. Ha megtalálta egy szolgáltatást, majd adja hozzá vagy szavazás a kérés található meg a [UserVoice fórum](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Ha rendelkezik a Microsoft támogatja a nyissa meg a támogatási incidensek azáltal, hogy a következő adatokat: 
   * Az Azure előfizetés-azonosító
   * Platform (pl. iOS, Android stb.)
   * Alkalmazásazonosító
   * A kampány Azonosítóját (a leküldéses értesítési problémák)
   * Eszközazonosító
   * Mobile Engagement SDK-verzió (pl. Android SDK v2.1.0)
   * A pontos hibaüzenet és forgatókönyv hiba részletei

