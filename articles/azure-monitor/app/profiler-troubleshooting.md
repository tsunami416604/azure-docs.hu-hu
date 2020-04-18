---
title: Az Azure Application Insights Profiler használatával kapcsolatos problémák elhárítása
description: Ez a cikk hibaelhárítási lépéseket és információkat tartalmaz, amelyek segítséget nyújtanak az Application Insights Profiler engedélyezésével vagy használatával kapcsolatos problémák kal.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 55bc4ff05b650884ef17e0de10d7156cbf458a9c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640948"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Az Application Insights Profiler engedélyezésével és megtekintésével kapcsolatos problémák elhárítása

## <a name="active-issues"></a>Aktív problémák

* Az Azure App Services támogatja a ASP.NET Core 3.x alkalmazások profilalkotását.

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Általános hibaelhárítás

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>A profilok feltöltése csak akkor történik meg, ha a Profiler futása közben az alkalmazáshoz érkező kérések

Az Azure Application Insights Profiler óránként két percig gyűjti a profilkészítési adatokat. Adatokat is gyűjt, ha kiválasztja a **Profil most** gombot az Application **Insights Profiler konfigurálása** ablaktáblában. De a profilkészítési adatok feltöltése csak akkor, ha csatolható egy kérelemhez, amely akkor történt, miközben Profiler futott. 

A Profiler nyomkövetési üzeneteket és egyéni eseményeket ír az Application Insights-erőforrásba. Ezekkel az eseményekkel megtekintheti, hogyan működik a Profiler. Ha úgy gondolja, hogy a Profiler-nek futtatnia és rögzítenie kell a nyomkövetéseket, de azok nem jelennek meg a **Teljesítmény** ablaktáblán, ellenőrizheti, hogyan fut a Profiler:

1. Keresse meg a profilozó által az Application Insights-erőforrásba küldött nyomkövetési üzeneteket és egyéni eseményeket. Ezzel a keresési karakterlánccal megtalálhatja a megfelelő adatokat:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Az alábbi képen két példa látható két AI-erőforrásból származó keresésre: 
    
   * A bal oldalon az alkalmazás nem fogad kéréseket, amíg a Profiler fut. Az üzenet ismerteti, hogy a feltöltés tanusította, mert nem történt tevékenység. 

   * A jobb oldalon a Profiler elindította és elküldte az egyéni eseményeket, amikor észlelte a Profiler futása közben történt kérelmeket. Ha a ServiceProfilerSample egyéni esemény jelenik meg, az azt jelenti, hogy a Profiler egy kérelemhez csatolt egy nyomkövetést, és megtekintheti a nyomkövetést az **Application Insights teljesítményablakában.**

     Ha nem jelenik meg telemetriai elem, a Profiler nem fut. A hibaelhárításról a cikk későbbi, adott alkalmazástípushoz tartozó hibaelhárítási szakaszokban olvashat.  

     ![Profilozó telemetria keresése][profiler-search-telemetry]

1. Ha voltak kérelmek, míg Profiler futott, győződjön meg arról, hogy a kérelmek et az alkalmazás azon része, amelyprofiler engedélyezve van. Bár az alkalmazások néha több összetevőből állnak, a Profiler csak néhány összetevőesetében engedélyezett. Az **Application Insights Profiler konfigurálása** ablaktábla megjeleníti a nyomkövetéseket feltöltött összetevőket.

### <a name="other-things-to-check"></a>Egyéb ellenőrizleg eljelölőnégyzetek
* Győződjön meg arról, hogy az alkalmazás a .
* Ha a webalkalmazás egy ASP.NET Core alkalmazás, legalább ASP.NET Core 2.0-s futtatásának kell lennie.
* Ha a megtekinteni kívánt adatok néhány hétnél régebbiek, próbálja meg korlátozni az időszűrőt, majd próbálkozzon újra. A nyomkövetések hét nap után törlődnek.
* Győződjön meg arról, hogy a proxyk vagy a tűzfal nem tiltotta le a hozzáférést a alkalmazáshoz. https://gateway.azureserviceprofiler.net
* A Profiler nem támogatott az ingyenes vagy megosztott alkalmazásszolgáltatási csomagokban. Ha az egyik ilyen csomagok, próbálja felskálázás az egyik az alapvető tervek és Profiler meg kell kezdeni e munkát.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Kétszeres beszámítás párhuzamos menetekben

Bizonyos esetekben a teljes időmérő a veremmegjelenítőben több, mint a kérelem időtartama.

Ez a helyzet akkor fordulhat elő, ha két vagy több szál van társítva egy kéréshez, és párhuzamosan működnek. Ebben az esetben a teljes menetidő több, mint az eltelt idő. Lehet, hogy az egyik szál a másikon várakozik a teljesítendő szálra. A néző megpróbálja felismerni ezt a helyzetet, és kihagyja az érdektelen várakozást. Ennek során a túl sok információ megjelenítésének oldalán hibásodik, ahelyett, hogy kihagyna a kritikus információkat.

Amikor párhuzamos szálakat lát a nyomkövetésekben, határozza meg, hogy mely szálak várakoznak, hogy megállapíthassa a kérelem kritikus elérési útját. Általában a szál, amely gyorsan várakozik, egyszerűen csak a többi szálra vár. Koncentráljon a többi szálra, és hagyja figyelmen kívül a várakozó szálak idejét.

### <a name="error-report-in-the-profile-viewer"></a>Hibajelentés a profilmegjelenítőben
Küldjön be egy támogatási jegyet a portálon. Ügyeljen arra, hogy tartalmazza a korrelációs azonosítót a hibaüzenetből.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Profilozó hibaelhárítása az Azure App Service szolgáltatásban
A Profiler megfelelő működéséhez:
* A webalkalmazás-szolgáltatáscsomag nak alapszintű vagy magasabb szintűnek kell lennie.
* A webalkalmazásnak engedélyeznie kell az Application Insights szolgáltatást.
* A webalkalmazásnak a következő alkalmazásbeállításokkal kell rendelkeznie:

    |Alkalmazásbeállítás    | Érték    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey az Application Insights-erőforráshoz    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* Az **ApplicationInsightsProfiler3** webfeladatnak futnia kell. A webjob ellenőrzése:
   1. Tovább a [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. Az **Eszközök menüben** válassza a **WebJobs irányítópult lehetőséget.**  
      Megnyílik **a WebJobs** ablaktábla. 
   
      ![profiler-webjob]   
   
   1. A webfeladat részleteinek megtekintéséhez, beleértve a naplót, válassza az **ApplicationInsightsProfiler3** hivatkozást.  
     Megnyílik **a Folyamatos webfeladat részletei** ablaktábla.

      ![profiler-webjob-log]

Ha nem tudja kideríteni, hogy a Profiler miért nem működik önnek, letöltheti a naplót, és elküldheti a csapatunknak segítségért, serviceprofilerhelp@microsoft.com. 
    
### <a name="manual-installation"></a>Manuális telepítés

A Profiler konfigurálásakor a webalkalmazás beállításai frissülnek. Ha a környezet megköveteli, manuálisan is alkalmazhatja a frissítéseket. Egy példa lehet, hogy az alkalmazás fut a Web Apps környezetben powerapps. A frissítések manuális alkalmazása:

1. A **Webalkalmazás vezérlőpanelén** nyissa meg a **Beállítások lehetőséget.**

1. Állítsa a **.NET Framework verziót** **a 4.6-os verzióra.**

1. Állítsa **be mindig** be **.**
1. Hozza létre a következő alkalmazásbeállításokat:

    |Alkalmazásbeállítás    | Érték    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey az Application Insights-erőforráshoz    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Túl sok aktív profilkészítési munkamenet

Jelenleg legfeljebb négy Azure-webalkalmazáson és üzembe helyezési tárolóhelyen engedélyezheti a Profiler-t, amelyek ugyanabban a szolgáltatási csomagban futnak. Ha egy alkalmazásszolgáltatási csomagban négynél több webalkalmazás fut, előfordulhat, hogy a Profiler egy *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException programot*hajt le. A Profiler minden webalkalmazáshoz külön fut, és megkísérli elindítani az event tracing for Windows (ETW) munkamenetet az egyes alkalmazásokhoz. De korlátozott számú ETW ülés lehet aktív egy időben. Ha a Profiler webjoba túl sok aktív profilkészítési munkamenetet jelent, helyezzen át néhány webalkalmazást egy másik szolgáltatási csomagba.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Telepítési hiba: A könyvtár\\nem\\\\üres\\'D: otthoni webhely wwwroot App_Data\\feladatok"

Ha a webalkalmazást olyan Web Apps-erőforrásra helyezi üzembe, amelyen engedélyezve van a Profiler, a következő üzenet jelenhet meg:

*Directory Not Empty\\'D:\\home\\\\site\\wwwroot App_Data jobs"*

Ez a hiba akkor fordul elő, ha a webtelepítőt parancsfájlokból vagy az Azure DevOps központi telepítési folyamatából futtatja. A megoldás a következő további telepítési paraméterek hozzáadása a webes központi telepítési feladathoz:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ezek a paraméterek törlik az Application Insights Profiler által használt mappát, és feloldják az újraüzembe helyezési folyamat blokkolását. Nincsenek hatással a Profiler-példány, amely jelenleg fut.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hogyan állapíthatom meg, hogy az Application Insights Profiler fut-e?

A Profiler folyamatos webfeladatként fut a webalkalmazásban. Megnyithatja a webalkalmazás-erőforrást az [Azure Portalon.](https://portal.azure.com) A **WebJobs** ablaktáblán ellenőrizze az **ApplicationInsightsProfiler**állapotát. Ha nem fut, nyissa **meg a Naplók at,** hogy további információkat kapjon.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>A Profilozó és az Azure Diagnosztika hibáinak elhárítása

>**A hiba a profiler, hogy a hajók a WAD a Cloud Services ki lett javítva.** A WAD (1.12.2.0) felhőszolgáltatásokhoz készült legújabb verziója az App Insights SDK összes legújabb verziójával működik. A Cloud Service-üzemeltetés gazdagépei automatikusan frissítik a WAD-t, de az nem azonnali. A frissítés kényszerítéséhez újratelepítheti a szolgáltatást, vagy újraindíthatja a csomópontot.

Ha meg szeretné tudni, hogy a Profiler megfelelően van-e konfigurálva az Azure Diagnostics által, tegye a következő három dolgot: 
1. Először ellenőrizze, hogy az Azure Diagnostics konfiguráció, amely telepítve van, amit vár. 

1. Másodszor győződjön meg arról, hogy az Azure Diagnostics átadja a megfelelő iKey a Profiler parancssorban. 

1. Harmadszor, ellenőrizze a Profiler naplófájl, hogy a Profiler futott, de visszaadott egy hiba. 

Az Azure Diagnostics konfigurálásához használt beállítások ellenőrzése:

1. Jelentkezzen be a virtuális gépre (VM), majd nyissa meg a naplófájlt ezen a helyen. (A meghajtó lehet c: vagy d: és a plugin verzió eltérő lehet.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    vagy
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. A fájlban megkeresheti a **WadCfg** karakterláncot a virtuális gépnek átadott beállítások megkereséséhez az Azure Diagnostics konfigurálásához. Ellenőrizheti, hogy a Profiler-mosogató által használt iKey helyes-e.

1. Ellenőrizze a Profiler indításához használt parancssort. A Profiler indításához használt argumentumok a következő fájlban találhatók. (A meghajtó lehet c: vagy d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Ellenőrizze, hogy a Profiler parancssorban az iKey helyes-e. 

1. Az előző *config.json* fájlban található elérési utat használva ellenőrizze a Profiler naplófájlt. Megjeleníti a hibakeresési információkat, amelyek jelzik a Profiler által használt beállításokat. Azt is megjeleníti állapotát és hibaüzeneteket Profiler.  

    Ha a Profiler az alkalmazás kérései fogadása közben fut, a következő üzenet jelenik meg: *Az iKey-ből észlelt tevékenység*. 

    A nyomkövetés feltöltésekor a következő üzenet jelenik meg: *A nyomkövetés feltöltésének megkezdése*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Hálózati proxy- vagy tűzfalszabályok szerkesztése

Ha az alkalmazás proxyn vagy tűzfalon keresztül csatlakozik az internethez, előfordulhat, hogy szükség lehet a szabályok szerkesztésére, hogy az alkalmazás kommunikáljon az Application Insights Profiler szolgáltatással. Az Application Insights Profiler által használt IP-címeket az Azure Monitor szolgáltatáscímke tartalmazza.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








