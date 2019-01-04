---
title: Az Azure Application Insights Profiler kapcsolatos problémák elhárítása |} A Microsoft Docs
description: Ez az oldal hibaelhárítási lépéseket és információk nyújtanak segítséget a fejlesztőknek szól, akik engedélyezése és használata az Application Insights profiler problémákat tapasztal, amikor tartalmazza.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: fc30dc928afbee0de2cd0d11a147c08b9f47cef1
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024620"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Engedélyezése és megtekintése az Application Insights Profiler kapcsolatos problémák elhárítása

## <a id="troubleshooting"></a>Általános hibaelhárítási tippek

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>Profilok csak lesznek feltöltve, ha az alkalmazásnak a kérelmek a Profilkészítő futtatása közben
Application Insights Profiler a profiler-adatokat gyűjti össze a két perc minden egyes óráért, vagy ha a [ **profil most** ](app-insights-profiler-settings.md?toc=/azure/azure-monitor/toc.json) gombjának megnyomásakor a **Application Insights Profiler konfigurálása**lapot. Azonban a profilkészítési adatokat is csatlakoztatható egy kérelmet, amely a profiler futása közben történt, amikor csak feltöltése. 

A profiler ír nyomkövetési üzenetek és egyéni eseményeket az application insights-erőforrást. Az események használatával módját fut-e a profiler. Ha úgy véli, hogy a profiler kell fut és nyomok rögzítését, de nem találja azokat a teljesítmény oldalon, ellenőrizheti, hogyan fut-e a profiler:

1. Keresse meg a nyomkövetési üzenetek és egyéni eseményeket az Application Insights-erőforrásra a profiler által küldött. Ez a keresési karakterlánc segítségével keresse meg a vonatkozó adatokat:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Íme egy példa a két keresést az alábbi képernyőképen két különböző AI erőforrások közül. 
    
    * Az a bal oldalon látható: egy alkalmazás, amely nem első kérelmek, a Profilkészítő futtatása közben. Láthatja, hogy az üzenet, hogy a feltöltés nem volt tevékenység miatt megszakadt. 

    * A példában a jobb oldalon láthatja, hogy a profiler elindult, és egyéni eseményeket, amikor azt észlelte, hogy kérelmeket, és ismételje meg a profiler futása közben küldött. Ha a ServiceProfilerSample egyéni esemény jelenik meg, az azt jelenti, a profiler nyomkövetési csatlakozik egy kérelmet, és megtekintheti a nyomkövetést az Application Insights teljesítmény oldalról.

    * Ha egyáltalán semmilyen telemetriai adatot sem látja, akkor a profiler nem fut. Olvassa el a hibaelhárítási szakaszok az adott alkalmazás típusának, a jelen dokumentum az alábbi szeretne.  

     ![Keresés Profiler Telemetria][profiler-search-telemetry]

1. Ha a időszakban kérelmek a profiler futott, akkor győződjön meg arról, hogy a profiler engedélyezve van az alkalmazás részeként kéréseket kezeli. Néha alkalmazások több összetevőből áll, de a Profiler engedélyezve van csak bizonyos, nem az összes, az összetevőket. Az Application Insights Profiler konfigurálása lapján megtekintheti az összetevőket, amelyek már feltöltött nyomkövetéseket.

### <a name="other-things-to-check"></a>Más ellenőrizze az alábbiakat:
* Az alkalmazás fut, a .NET-keretrendszer 4.6.
* Ha a webalkalmazás egy ASP.NET Core-alkalmazást, futnia kell, hogy legalább az ASP.NET Core 2.0.
* Ha a megtekinteni kívánt adatok régebbi, mint egy pár hétig, próbálkozzon az Időszűrő korlátozása, és próbálkozzon újra. Nyomok hét nap után törlődnek.
* Győződjön meg arról, hogy proxyk vagy a tűzfal nem letiltotta a hozzáférést https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>A párhuzamos szálak számbavételi dupla

Bizonyos esetekben a teljes időmetrika a stack megjelenítőben több, mint a kérelem időtartama.

Ez a helyzet akkor fordulhat elő, amikor két vagy több szál társítva egy kérelmet, és párhuzamosan működnek. Ebben az esetben a szálak teljes ideje több, mint az eltelt idő. Egy szálat a másik hajtható végre, előfordulhat, hogy lehet vár. A megjelenítő megpróbálja észlelni, ez a helyzet, hogy és érdekesnek Várakozás az áttekinthetőség kedvéért kihagyja, de azt oldalán túl sok információt jelenít meg hibák helyett hagyja ki, mit lehet, hogy kritikus fontosságú információkhoz.

Amikor párhuzamos szálak látható a nyomkövetés, határozza meg, mely beszélgetések várnak, hogy meg tudja állapítani, hogy a kritikus fontosságú a kérelem elérési útját. A legtöbb esetben a gyors állapotba kerül szál egyszerűen a más szálak vár. A más szálak összpontosít, és figyelmen kívül hagyja a várakozó szál az az idő.

### <a name="error-report-in-the-profile-viewer"></a>Hiba történt a jelentés a profil-megjelenítőben
A portál támogatási jegyet is küldhet. Győződjön meg arról, a korrelációs Azonosítót, a hibaüzenet tartalmazza.

## <a name="troubleshooting-profiler-on-app-services"></a>Profiler az App Services hibaelhárítása
### <a name="for-the-profiler-to-work-properly"></a>A Profilkészítő megfelelő működéséhez:
* A web app service-csomagot kell lennie az alapszintű csomag vagy újabb verziója.
* A webalkalmazás rendelkeznie kell Application Insights engedélyezését.
* A webalkalmazás kell rendelkeznie a **állítani az APPINSIGHTS_INSTRUMENTATIONKEY** alkalmazásbeállítást az azonos kialakítási kulcsot, amelyet az Application Insights SDK konfigurálva.
* A webalkalmazás kell rendelkeznie a **APPINSIGHTS_PROFILERFEATURE_VERSION** Alkalmazásbeállítás meghatározása, és a 1.0.0.
* A webalkalmazás kell rendelkeznie a **DiagnosticServices_EXTENSION_VERSION** Alkalmazásbeállítás definiálva, és állítsa az értékét KB. 3.
* A **ApplicationInsightsProfiler3** webjobs-feladat kell futnia. A webjobs-feladat ellenőrizheti a [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/), és nyissa meg a **WebJobs-irányítópulttal** az Eszközök menüben. Mint ApplicationInsightsProfiler2 hivatkozásra kattintva az alábbi képernyőképeken látható, a webjobs-feladatot, beleértve a napló részletes információkat tekinthet meg.

    Itt van szüksége a webjobs-feladat részleteinek megtekintéséhez kattintson a hivatkozásra: 

    ![a Profiler-webjobs-feladat]

    Itt látható az oldal részleteit jeleníti meg. A napló letöltése, és a mobilszolgáltatásokba csapatunk, ha nem számítható ki, miért érdemes a profiler nem működik.
    
    ![a Profiler-webjob-log]

### <a name="manual-installation"></a>Manuális telepítés

Profiler konfigurálásakor a webalkalmazás-beállítások végrehajtott frissítéseket. Ha a környezet számára szükséges, manuálisan alkalmazhatja a frissítések. Például lehet, hogy az alkalmazás a Web Apps környezetben fut a powerapps szolgáltatásra.

1. Az a **webes alkalmazás-vezérlő** ablaktáblán nyissa meg **beállítások**.
1. Állítsa be **.Net Framework version** való **v4.6**.
1. Állítsa be **Always On** való **a**.
1. Adja hozzá a **állítani az APPINSIGHTS_INSTRUMENTATIONKEY** beállítást, és állítsa az értékét a azonos kialakítási kulcsot, az SDK által használt alkalmazás.
1. Adja hozzá a **APPINSIGHTS_PROFILERFEATURE_VERSION** Alkalmazásbeállítás, és állítsa be az 1.0.0-s.
1. Adja hozzá a **DiagnosticServices_EXTENSION_VERSION** Alkalmazásbeállítás, és adja meg kb. 3.

### <a name="too-many-active-profiling-sessions"></a>Túl sok aktív profilkészítési munkamenetek

Engedélyezheti a Profiler jelenleg legfeljebb négy Azure web apps és az azonos service-csomag futó üzembe helyezési pontok. További web Apps alkalmazások, mint egy app service-csomag futó rendelkezik, egy a profiler által okozott Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException jelenhet meg. A profiler minden webes alkalmazáshoz külön-külön fut, és próbálja meg elindítani a minden alkalmazás ETW-munkamenetet. De van néhány ETW olyan munkamenetek, amelyek egy időben lehet aktív. A Profiler webjobs-feladat túl sok aktív profilkészítési munkamenetek jelent, ha néhány webes alkalmazás áthelyezése egy másik service-csomagot.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Központi telepítési hiba: Könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok

Ha az a Web Apps erőforrás-webalkalmazás újraalkalmaz a Profiler engedélyezve van, a következő üzenetet láthatja:

*Könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok*

Ez a hiba akkor fordul elő, ha futtatja a Web Deploy parancsfájlokat, illetve az Azure DevOps-üzembe helyezési folyamatba. A megoldás a következő további telepítési paramétereket adhat hozzá a Web Deploy feladathoz:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ezeket a paramétereket törölje a mappát, amely az Application Insights Profiler használja, és az ismételt üzembehelyezési folyamat feloldása. A Profiler-példányt, amely jelenleg fut, nem befolyásolják.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hogyan állapítható meg, hogy fut-e az Application Insights Profiler?

Profiler fut egy folyamatos webjobs-feladat a webalkalmazásban. Megnyithatja a webes alkalmazás-erőforrást a [az Azure portal](https://portal.azure.com). Az a **WebJobs** ablaktáblán állapotának ellenőrzéséhez **ApplicationInsightsProfiler**. Ha ez nem fut, nyissa meg a **naplók** további információért.

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>A Profiler és a WAD-problémák hibaelhárítása

Három dolgot ellenőrizheti, hogy a profiler által WAD megfelelően konfigurálva. Először ellenőrizze, hogy a WAD-konfiguráció üzembe helyezett tartalma várt. Másodszor, ellenőriznie kell WAD továbbítja a megfelelő Rendszerállapotkulcsot a profiler parancssorban. Harmadik ellenőrizheti a profiler naplófájl, ha profiler lefutott, de az első hiba megtekintéséhez. 

A WAD konfigurálásához használt beállítások ellenőrzéséhez meg kell jelentkezzen be a virtuális Gépre, és nyissa meg a naplófájlt, ezen a helyen: 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
Ezt a fájlt a kereshet a "WadCfg" karakterláncot, és megtalálhatja a beállításokat, amelyek a virtuális gép konfigurálása WAD lettek átadva. Ellenőrizze, hogy a profiler-gyűjtő által használt Rendszerállapotkulcsot helyességéről.

A második a parancssorban indítsa el a profiler használt ellenőrizheti. A következő fájl tartalmazza a nyitja meg a profiler argumentumokat.
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
Ellenőrizze, hogy helyesen szerepel-e a rendszerállapotkulcsot a profiler parancssorban. 

Harmadik az elérési utat a fenti config.json fájl használata esetén ellenőrizze a profiler naplófájlt. A profiler által használt beállításokat és a profiler az állapot és a hibaüzenetek jelző ladicí informace megjelenik. Ha a profiler fut, amíg az alkalmazás kéréseket fogad, akkor ez az üzenet jelenik meg: A Rendszerállapotkulcsot észlelhető tevékenység. Amikor a nyomkövetési adatok feltöltése folyamatban van, akkor ezt az üzenetet jelenik meg: Nyomkövetési feltöltése megkezdődött. 


[profiler-search-telemetry]:./media/app-insights-profiler-troubleshooting/Profiler-Search-Telemetry.png
[a Profiler-webjobs-feladat]:./media/app-insights-profiler-troubleshooting/Profiler-webjob.png
[a Profiler-webjob-log]:./media/app-insights-profiler-troubleshooting/Profiler-webjob-log.png








