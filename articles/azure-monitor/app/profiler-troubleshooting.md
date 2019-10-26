---
title: Az Azure Application Insights Profiler problémáinak elhárítása | Microsoft Docs
description: Ez a cikk hibaelhárítási lépéseket és információkat tartalmaz a Application Insights Profiler engedélyezésével vagy használatával kapcsolatos problémákat okozó fejlesztők számára.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 7430f04846a1e66680f85f939854fd50a5df41e4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899977"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Az Application Insights Profiler engedélyezésével vagy megtekintésével kapcsolatos problémák elhárítása

## <a id="troubleshooting"></a>Általános hibaelhárítás

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>A profilok csak akkor lesznek feltöltve, ha a Profiler futtatása közben kérések vannak az alkalmazáshoz

Az Azure Application Insights Profiler óránként két percig gyűjti a profilkészítési adatokat. Emellett adatokat gyűjt, amikor kiválasztja a **profil most** gombot a **Application Insights Profiler konfigurálása** panelen. A profilkészítési adatgyűjtés azonban csak akkor történik meg, ha csatolható egy, a Profiler futása során megjelenő kérelemhez. 

A Profiler nyomkövetési üzeneteket és egyéni eseményeket ír a Application Insights erőforrásba. Ezen események használatával megtekintheti, hogyan fut a Profiler. Ha úgy gondolja, hogy a Profilernek futnia kell, és el kell látnia a nyomkövetéseket, de nem jelennek meg a **teljesítmény** ablaktáblán, megtekintheti, hogyan fut a Profiler:

1. Keressen nyomkövetési üzeneteket és a Profiler által a Application Insights-erőforrásnak küldött egyéni eseményeket. Ezt a keresési karakterláncot használhatja a releváns adatokat megkeresni:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Az alábbi ábrán két AI-erőforrásból származó keresések jelennek meg: 
    
   * A bal oldalon az alkalmazás nem kap kéréseket, amíg a Profiler fut. Az üzenet azt mutatja be, hogy a feltöltést nem a tevékenység miatt szakították meg. 

   * A jobb oldalon a Profiler megkezdte és elküldötte az egyéni eseményeket, amikor a Profiler futtatása közben történt kérelmeket észlelt. Ha a ServiceProfilerSample egyéni esemény jelenik meg, az azt jelenti, hogy a Profiler nyomkövetést csatolt egy kérelemhez, és megtekintheti a nyomkövetést a **Application Insights teljesítmény** ablaktáblán.

     Ha nem jelenik meg telemetria, a Profiler nem fut. A hibaelhárításhoz tekintse meg a jelen cikk későbbi, az adott alkalmazásra vonatkozó hibaelhárítási részeit.  

     ![A Profiler telemetria keresése][profiler-search-telemetry]

1. Ha a Profiler futtatása közben kérések történtek, győződjön meg arról, hogy a kérelmeket az alkalmazás azon része kezeli, amelyen engedélyezve van a Profiler. Habár az alkalmazások esetenként több összetevőből állnak, a Profiler csak néhány összetevő esetében engedélyezett. A **Application Insights Profiler konfigurálása** panelen megjelennek a feltöltött nyomkövetéseket tartalmazó összetevők.

### <a name="other-things-to-check"></a>További ellenőrzési dolgok
* Győződjön meg arról, hogy az alkalmazás a .NET-keretrendszer 4,6-es verzióját futtatja.
* Ha a webalkalmazás egy ASP.NET Core alkalmazás, legalább ASP.NET Core 2,0-at kell futtatnia.
* Ha a megtekinteni kívánt adatmennyiség régebbi, mint néhány hétig, próbálja meg korlátozni az időszűrőt, és próbálkozzon újra. A Nyomkövetések hét nap után törlődnek.
* Győződjön meg arról, hogy a proxyk vagy a tűzfal nem tiltotta le a https://gateway.azureserviceprofiler.net elérését.

### <a id="double-counting"></a>Dupla számlálás párhuzamos szálakban

Bizonyos esetekben a verem-megjelenítőben lévő teljes időmetrika meghaladja a kérelem időtartamát.

Ez a helyzet akkor fordulhat elő, ha két vagy több szál van társítva egy kérelemhez, és párhuzamosan működik. Ebben az esetben a szál teljes ideje meghaladja az eltelt időt. Lehet, hogy egy szál várakozik a másikra. A megjelenítő megpróbálja felderíteni ezt a helyzetet, és kihagyja a nem érdekes várakozást. Ennek során a téved túl sok információt jelenít meg, nem pedig kihagyhatja, hogy mi lehet a kritikus információ.

Ha a nyomkövetésekben párhuzamos szálak láthatók, határozza meg, hogy mely szálak várnak, hogy megtudja a kérés kritikus elérési útját. Általában az a szál, amely gyorsan befejeződik a várakozási állapotba, egyszerűen a többi szálra vár. Koncentráljon a többi szálra, és hagyja figyelmen kívül a várakozási szálak időpontját.

### <a name="error-report-in-the-profile-viewer"></a>Hibajelentés a profil megjelenítőben
Támogatási jegy küldése a portálon. Ügyeljen arra, hogy a hibaüzenetben szerepeljen a korrelációs azonosító.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>A Azure App Service Profiler hibáinak megoldása
A Profiler megfelelő működéséhez:
* A Web App Service-csomagnak alapszintű vagy magasabb szintűnek kell lennie.
* A webalkalmazásnak Application Insights engedélyezve kell lennie.
* A webalkalmazásnak a következő alkalmazás-beállításokkal kell rendelkeznie:

    |Alkalmazásbeállítás    | Value (Díj)    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | a Application Insights-erőforrás Rendszerállapotkulcsot    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* A **ApplicationInsightsProfiler3** -webjobs futnia kell. A webjobs megkeresése:
   1. Nyissa meg a [kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. Az **eszközök** menüben válassza a **webjobs irányítópult**elemet.  
      Megnyílik a **webjobs** panel. 
   
      ![Profiler – webjobs]   
   
   1. A webjobs részleteinek megtekintéséhez, beleértve a naplót, válassza a **ApplicationInsightsProfiler3** hivatkozást.  
     Megnyílik a **folyamatos Webjobs részletei** ablaktábla.

      ![Profiler-webjobs-log]

Ha nem tudja kideríteni, hogy a Profiler miért nem működik Önnek, letöltheti a naplót, és elküldheti a csapatnak segítségért, serviceprofilerhelp@microsoft.com. 
    
### <a name="manual-installation"></a>Manuális telepítés

A Profiler konfigurálásakor a rendszer frissíti a webalkalmazás beállításait. Ha a környezete megköveteli, manuálisan is alkalmazhatja a frissítéseket. Lehetséges például, hogy az alkalmazás a PowerApps Web Apps-környezetében fut. Frissítések manuális alkalmazása:

1. A **webalkalmazás-vezérlő** ablaktáblán nyissa meg a **Beállítások**lapot.

1. A **.NET-keretrendszer verziójának** beállítása: **v 4.6**.

1. A **mindig** be értékre állítása **.**
1. Az Alkalmazásbeállítások létrehozása:

    |Alkalmazásbeállítás    | Value (Díj)    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | a Application Insights-erőforrás Rendszerállapotkulcsot    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>Túl sok aktív profilkészítési munkamenet

Jelenleg a Profilert legfeljebb négy Azure Web Apps és üzembe helyezési pont használatával engedélyezheti, amelyek ugyanabban a szolgáltatáscsomag futnak. Ha több mint négy, egy app Service-csomagban futó webalkalmazása van, a Profiler a *Microsoft. ServiceProfiler. kivétellistájára. TooManyETWSessionException*. A Profiler külön fut az egyes webalkalmazásokhoz, és megkísérli az egyes alkalmazások Windows esemény-nyomkövetés (ETW) munkamenetének indítását. Egyszerre azonban csak korlátozott számú ETW-munkamenet lehet aktív. Ha a Profiler webjobs túl sok aktív profilkészítési munkamenetet jelez, helyezzen át néhány webalkalmazást egy másik szolgáltatáscsomag.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Telepítési hiba: a könyvtár nem üres:\\Home\\site\\wwwroot\\App_Data\\feladatok "

Ha a webalkalmazást egy olyan Web Apps erőforrásra telepíti újra, amelyen a Profiler engedélyezve van, akkor a következő üzenet jelenhet meg:

*A könyvtár nem üres:\\Home\\site\\wwwroot\\App_Data\\feladatok "*

Ez a hiba akkor fordul elő, ha a web Deploy szkriptből vagy az Azure DevOps üzembe helyezési folyamatból futtatja. A megoldás a következő további központi telepítési paramétereket adja hozzá a web Deploy feladathoz:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ezek a paraméterek törlik a Application Insights Profiler által használt mappát, és feloldják az újratelepítési folyamatot. Nincsenek hatással a jelenleg futó Profiler-példányra.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hogyan határozza meg, hogy a Application Insights Profiler fut-e?

A Profiler folyamatos webjobs fut a webalkalmazásban. A webalkalmazás-erőforrást a [Azure Portalban](https://portal.azure.com)nyithatja meg. A **webjobs** ablaktáblán tekintse meg a **ApplicationInsightsProfiler**állapotát. Ha nem fut, nyissa meg a **naplókat** , hogy további információkat kapjon.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>A Profiler és a Azure Diagnostics problémáinak elhárítása

>**A Profilerben szereplő hiba, amely a Cloud Services WAD-ban található.** A Cloud Services WAD (1.12.2.0) legújabb verziója az App ininsights SDK legújabb verzióival működik. A Cloud Service-gazdagépek automatikusan frissítik a WAD-t, de nem azonnal. A frissítés kényszerítéséhez újra üzembe helyezheti a szolgáltatást, vagy újraindíthatja a csomópontot.

Ha szeretné megtekinteni, hogy a Profiler megfelelően van-e konfigurálva a Azure Diagnostics, tegye a következő három dolgot: 
1. Először is ellenőrizze, hogy az üzembe helyezett Azure Diagnostics konfiguráció tartalma várható-e. 

1. Másodszor, győződjön meg arról, hogy Azure Diagnostics átadja a megfelelő Rendszerállapotkulcsot a Profiler parancssorban. 

1. Harmadszor, a Profiler naplófájljában ellenőrizze, hogy a Profiler futott-e, de hibát adott vissza. 

A Azure Diagnostics konfigurálásához használt beállítások ellenőrzését:

1. Jelentkezzen be a virtuális gépre (VM), majd nyissa meg a naplófájlt ezen a helyen. (A meghajtó lehet a c: vagy a d:, és a beépülő modul verziója eltérő lehet.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    vagy
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. A fájlban megkeresheti a **WadCfg** karakterláncot, hogy megkeresse a virtuális gép számára átadott beállításokat a Azure Diagnostics konfigurálásához. Megtekintheti, hogy helyes-e a Profiler-fogadó által használt Rendszerállapotkulcsot.

1. A Profiler elindításához használt parancssorban keresse meg a következőt:. A Profiler elindításához használt argumentumok a következő fájlban találhatók. (A meghajtó lehet c: vagy d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Ellenőrizze, hogy helyes-e a Rendszerállapotkulcsot a Profiler parancssorban. 

1. Az előző *config. JSON* fájlban található elérési út használatával keresse meg a Profiler naplófájlját. Megjeleníti a Profiler által használt beállításokat jelző hibakeresési információkat. Megjeleníti a Profiler állapot-és hibaüzeneteit is.  

    Ha a Profiler fut, miközben az alkalmazás fogadja a kérelmeket, a következő üzenet jelenik meg: a *rendszerállapotkulcsot által észlelt tevékenység*. 

    A nyomkövetés feltöltésekor a következő üzenet jelenik meg: a *nyomkövetés feltöltésének megkezdése*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[Profiler – webjobs]:./media/profiler-troubleshooting/Profiler-webjob.png
[Profiler-webjobs-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








