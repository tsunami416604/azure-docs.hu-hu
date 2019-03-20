---
title: Az Azure Application Insights Profiler kapcsolatos problémák elhárítása |} A Microsoft Docs
description: Ez a cikk bemutatja a hibaelhárítási lépéseket és információk nyújtanak segítséget a fejlesztőknek szól, akik engedélyezése és használata az Application Insights Profiler problémákat tapasztal, amikor.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 6c96b7139787a3863b3f7a47949d9cdf20cc5021
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855673"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Engedélyezése és megtekintése az Application Insights Profiler kapcsolatos problémák elhárítása

## <a id="troubleshooting"></a>Általános hibaelhárítási tippek

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Csak akkor, ha nincsenek kérelmek az alkalmazáshoz, Profiler futása közben a rendszer feltölti a profilok

Az Azure Application Insights Profiler-két percig óránként profilkészítési adatokat gyűjt. Azt is gyűjti az adatokat kiválasztásakor a **profil most** gombra a **Application Insights Profiler konfigurálása** ablaktáblán. Azonban csak akkor kapcsolható egy kérelmet, amely a Profiler futása közben történt, a profilkészítési adatokat töltenek fel. 

Profiler ír nyomkövetési üzenetek és egyéni eseményeket az Application Insights-erőforrást. Ezek az események használatával hogyan Profiler fut-e. Ha úgy gondolja, hogy Profiler kell fut és nyomok rögzítését, de azok nem jelennek meg a **teljesítmény** panelen ellenőrizheti, hogy hogyan fut-e Profiler:

1. Keresse meg a nyomkövetési üzenetek és egyéni eseményeket küld az Application Insights-erőforrás Profiler. Ez a keresési karakterlánc segítségével keresse meg a vonatkozó adatokat:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Az alábbi képen keresések két AI-erőforrások két példa látható: 
    
   * Bal oldalán az alkalmazás nem kap kéréseket Profiler futása közben. Az üzenet tájékoztatja, hogy a feltöltés nem volt tevékenység miatt megszakadt. 

   * Jobb Profiler elindult, és egyéni eseményeket, amikor azt észlelte, hogy Profiler futása közben történt kérelmeket küldeni. Ha a ServiceProfilerSample egyéni esemény jelenik meg, az azt jelenti, hogy a Profiler nyomkövetési csatlakozik egy kérelmet, és megtekintheti a nyomkövetés a a **Application Insights teljesítmény** ablaktáblán.

     Ha nem működik a telemetria megjelenik, Profiler nem fut. A hibaelhárításhoz tekintse meg a hibaelhárítási szakaszok az adott alkalmazás típusának, a cikk későbbi részében.  

     ![Profiler telemetria keresése][profiler-search-telemetry]

1. Ha ott volt a kérelmek, miközben a Profiler futott, ellenőrizze, hogy a Profiler engedélyezve van az alkalmazás által a kérelmek kezelése. Bár az alkalmazások egyes esetekben több összetevőből áll, a Profiler engedélyezve van, a csak az egyes összetevői. A **Application Insights Profiler konfigurálása** ablaktáblán megjelennek azok az összetevőket, amelyek már feltöltött nyomkövetéseket.

### <a name="other-things-to-check"></a>Más ellenőrizze az alábbiakat
* Győződjön meg arról, hogy az alkalmazás fut-e a .NET-keretrendszer 4.6.
* If your web app is an ASP.NET Core application, it must be running at least ASP.NET Core 2.0.
* Ha a megtekinteni kívánt adatok régebbi, mint egy pár hétig, próbálkozzon az Időszűrő korlátozása, és próbálkozzon újra. Nyomok hét nap után törlődnek.
* Győződjön meg arról, hogy proxy vagy tűzfal van nem blokkolja a hozzáférést, https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>A párhuzamos szálak számbavételi dupla

Bizonyos esetekben a teljes időmetrika a stack megjelenítőben több, mint a kérelem időtartama.

Ez a helyzet akkor fordulhat elő, amikor két vagy több szál társítva egy kérelmet, és párhuzamosan működnek. Ebben az esetben a szálak teljes ideje több, mint az eltelt idő. Egy szálat a másik hajtható végre, előfordulhat, hogy lehet vár. A megjelenítő megpróbálja észlelni, ez a helyzet, hogy, és a kevésbé fontos Várakozás az áttekinthetőség kedvéért kihagyja. Ennek során azt hibák oldalán túl sok információt jelenít meg ahelyett, hagyja ki, mit lehet, hogy kritikus fontosságú információkhoz.

Amikor párhuzamos szálak látható a nyomkövetés, határozza meg, mely beszélgetések várnak, hogy meg tudja állapítani, hogy a kritikus fontosságú a kérelem elérési útját. A szál gyorsan állapotba kerül, általában a más szálak egyszerűen várakozik. A más szálak összpontosít, és figyelmen kívül hagyja a várakozó szál az az idő.

### <a name="error-report-in-the-profile-viewer"></a>Hiba történt a jelentés a profil-megjelenítőben
A portál támogatási jegyet is küldhet. Győződjön meg arról, a korrelációs Azonosítót, a hibaüzenet tartalmazza.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Az Azure App Service Profiler hibaelhárítása
A Profiler megfelelő működéséhez:
* A web app service-csomagot kell lennie az alapszintű csomag vagy újabb verziója.
* A webalkalmazás rendelkeznie kell Application Insights engedélyezését.
* A webalkalmazás kell rendelkeznie a **állítani az APPINSIGHTS_INSTRUMENTATIONKEY** alkalmazásbeállítást az azonos kialakítási kulcsot, amelyet az Application Insights SDK konfigurálva.
* A webalkalmazás kell rendelkeznie a **APPINSIGHTS_PROFILERFEATURE_VERSION** Alkalmazásbeállítás meghatározása, és a 1.0.0.
* A webalkalmazás kell rendelkeznie a **DiagnosticServices_EXTENSION_VERSION** definiált alkalmazás-beállítás és a ~ 3 értéket.
* A **ApplicationInsightsProfiler3** webjobs-feladat kell futnia. A webjobs-feladat ellenőrzése:
   1. Lépjen a [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. Az a **eszközök** menüjében válassza **WebJobs-irányítópulttal**.  
      A **WebJobs** panel nyílik meg. 
   
      ![profiler-webjob]   
   
   1. A webjobs-feladatot a naplóhoz, beleértve a részletek megtekintéséhez válassza ki a **ApplicationInsightsProfiler2** hivatkozásra.  
     A **folyamatos webjobs-feladat részletei** panel nyílik meg.

      ![profiler-webjob-log]

Nem számítható ki, miért Profiler nem működik, ha a napló letöltése, és küldje el a csapatával. 
    
### <a name="manual-installation"></a>Manuális telepítés

Profiler konfigurálásakor a webalkalmazás-beállítások végrehajtott frissítéseket. Ha a környezet számára szükséges, manuálisan alkalmazhatja a frissítéseket. Például lehet, hogy az alkalmazás a Web Apps környezetben fut a powerapps szolgáltatásra. Frissítések manuális alkalmazásához, tegye a következőket:

1. Az a **webes alkalmazás-vezérlő** ablaktáblán nyissa meg **beállítások**.

1. Állítsa be **.NET-keretrendszer verziója** való **v4.6**.

1. Állítsa be **Always On** való **a**.

1. Adja hozzá a **állítani az APPINSIGHTS_INSTRUMENTATIONKEY** beállítást, és állítsa az értékét a azonos kialakítási kulcsot, az SDK által használt alkalmazás.

1. Adja hozzá a **APPINSIGHTS_PROFILERFEATURE_VERSION** Alkalmazásbeállítás, és állítsa be az 1.0.0-s.

1. Adja hozzá a **DiagnosticServices_EXTENSION_VERSION** Alkalmazásbeállítás, és adja meg kb. 3.

### <a name="too-many-active-profiling-sessions"></a>Túl sok aktív profilkészítési munkamenetek

Engedélyezheti a Profiler jelenleg legfeljebb négy Azure web apps és az azonos service-csomag futó üzembe helyezési pontok. Ha négynél több web Apps alkalmazások egy app service-csomagban fut, a Profiler throw előfordulhat, hogy egy *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler minden webes alkalmazáshoz külön-külön fut, és próbálja meg elindítani egy esemény-nyomkövetése Windows (ETW) munkamenet minden alkalmazáshoz. De egyszerre csak korlátozott számú ETW-munkamenet lehet aktív. Ha a Profiler webjobs-feladat túl sok aktív profilkészítési munkamenetek jelentéseket, néhány webes alkalmazás áthelyezése egy másik service-csomag.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Központi telepítési hiba: Könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok

Ha az a Web Apps erőforrás-webalkalmazás a Profiler engedélyezve van újbóli, a következő üzenetet láthatja:

*Könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok*

Ez a hiba akkor fordul elő, ha futtatja a Web Deploy parancsfájlokat, illetve az Azure DevOps-üzembe helyezési folyamatba. A megoldás a következő további telepítési paramétereket adhat hozzá a Web Deploy feladathoz:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ezeket a paramétereket törölje a mappát, amely az Application Insights Profiler használja, és az ismételt üzembehelyezési folyamat feloldása. A Profiler-példányt, amely jelenleg fut, nem befolyásolják.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hogyan állapítható meg, hogy fut-e az Application Insights Profiler?

A webalkalmazás a folyamatos webjobs-feladatként futó Profiler. Megnyithatja a webes alkalmazás-erőforrást a [az Azure portal](https://portal.azure.com). Az a **WebJobs** ablaktáblán állapotának ellenőrzéséhez **ApplicationInsightsProfiler**. Ha ez nem fut, nyissa meg a **naplók** további információért.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Profiler és az Azure Diagnostics-problémák hibaelhárítása

  >**A profiler WAD legújabb verziójában a Cloud Services részét képező programhiba van.** Annak érdekében, hogy a profiler használni egy felhőalapú szolgáltatás, csak a támogatott AI SDK 2.7.2 verzióra. Az AI SDK újabb verziója használatakor kell vissza a 2.7.2 annak érdekében, hogy a profiler használja. Ha alacsonyabb szolgáltatásszintre váltásához az App Insights SDK verziója a Visual Studio használ, előfordulhat, hogy hibaüzenetet egy kötés átirányítási futásidőben. Ennek oka az, a "newVersion" Microsoft.ApplicationInsights web.config fájljában "2.7.2.0" értékre kell állítani az AI SDK-t, de alacsonyabb verziójúra változtatása nem frissíti automatikusan után.

Ha szeretné látni, hogy a Profiler megfelelően van-e konfigurálva az Azure Diagnostics által, tegye a következőket három: 
1. Először ellenőrizze, hogy az Azure Diagnostics konfigurálása a tartalmát, üzembe helyezett vannak-e a vártaknak. 

1. A második győződjön meg arról, hogy az Azure Diagnostics továbbítja-e a megfelelő Rendszerállapotkulcsot a Profiler parancssorban. 

1. Harmadik ellenőrizze a Profiler naplófájlt megtekintheti, hogy a Profiler lefutott, de hibát adott vissza. 

Az Azure Diagnostics konfigurálása során használt beállítások ellenőrzése:

1. Jelentkezzen be a virtuális gép (VM), és nyissa meg a naplófájlt, ezen a helyen. (C: meghajtó lehet vagy d: és a beépülő modul verziója eltérő lehet.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    vagy
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. A fájlban, kereshet a karakterlánc **WadCfg** lettek átadva a virtuális gép konfigurálása az Azure Diagnostics-beállítások megkeresése. Ellenőrizheti, hogy a Profiler-gyűjtő által használt a Rendszerállapotkulcsot helyes-e.

1. Ellenőrizze, hogy a Profiler indításához használt parancssori. A Profiler elindításához használt argumentumok a következő fájlban. (A meghajtó lehet c: és d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Győződjön meg arról, hogy helyesen szerepel-e a Rendszerállapotkulcsot a Profiler parancssorban. 

1. Az elérési út alapján az előző található *config.json* fájlt, a Profiler naplófájlban talál. A hibakeresési információ, amely azt jelzi, hogy a Profiler által használt beállításokat jeleníti meg. Állapot és a hibaüzenetek a Profiler is megjeleníti.  

    Profiler fut, amíg az alkalmazás kéréseket fogad, ha a következő üzenet jelenik meg: *Tevékenységet észlelt a Rendszerállapotkulcsot*. 

    A nyomkövetési adatok feltöltése folyamatban van, a következő üzenet jelenik meg: *Indítsa el a nyomkövetési feltölteni*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








