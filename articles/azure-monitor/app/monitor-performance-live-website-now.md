---
title: Élő ASP.NET-webapp figyelése az Azure Application Insights segítségével | Microsoft Docs
description: Megfigyelheti egy webhely teljesítményét annak ismételt üzembe helyezése nélkül. Az ASP.NET webes üzemeltetett alkalmazások a helyszíni vagy virtuális gépeken működik.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: 333edfc4041e7ab0dfbe6d45f306b1450e6b9946
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103146"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-status-monitor"></a>Eszköz webalkalmazások az Application Insights Állapotfigyelőt futásidejű

Egy élő webalkalmazást a kód módosítása vagy ismételt telepítése nélkül is kialakíthat az Azure Application Insights használatával. Ehhez [Microsoft Azure](https://azure.com)-előfizetésre van szükség.

Állapotmonitor segítségével alakítsa ki az IIS-ben futó .NET-alkalmazás a helyszínen vagy egy virtuális Gépre.

- Ha az alkalmazást helyezünk üzembe az Azure app services, hajtsa végre a [ezek az utasítások](azure-web-apps.md).
- Ha az alkalmazás egy Azure virtuális Gépen van üzembe helyezve, az Application Insights-figyelést az Azure Vezérlőpultján válthat.
- (Külön cikkek érhetők el az [élő J2EE-webalkalmazások](java-live.md) és az [Azure Cloud Services](../../azure-monitor/app/cloudservices.md) kialakításáról.)


![Képernyőkép az App Insights áttekintése gráfok sikertelen kérelmek, a kiszolgáló válaszidejét és a kiszolgálói kérelmekre vonatkozó információkat tartalmazó](./media/monitor-performance-live-website-now/overview-graphs.png)

Háromféleképpen alkalmazhatja az Application Insights szolgáltatást a .NET-webalkalmazásokra:

* **Felépítési idő:** [Az Application Insights SDK hozzáadása] [ greenbrown] a webalkalmazás kódjához.
* **Futási idő:** Webalkalmazását a kiszolgálón, az alább ismertetett, újraépítése és újratelepítése az kód nélkül.
* **Mindkettő:** Az SDK beépítése a webalkalmazás kódját, és a futásidejű bővítményeket is érvényesek. Így mindkét lehetőség előnyeivel élhet.

Itt található egy összefoglaló az egyes módszerek eredményeiről:

|  | Felépítési idő | Futási idő |
| --- | --- | --- |
| Kérések és kivételek |Igen |Igen |
| [Részletes kivételek](../../azure-monitor/app/asp-net-exceptions.md) | |Igen |
| [Függőségek diagnosztikája](../../azure-monitor/app/asp-net-dependencies.md) |.NET 4.6+ esetén, kevésbé részletesen |Igen, teljes részletesség: eredménykódok, SQL-parancsszöveg, HTTP-parancsok|
| [Rendszerteljesítmény-számlálók](../../azure-monitor/app/performance-counters.md) |Igen |Igen |
| [API egyéni telemetriához][api] |Igen |Nem |
| [Nyomkövetési napló integrációja](../../azure-monitor/app/asp-net-trace-logs.md) |Igen |Nem |
| [Lapmegtekintések és felhasználói adatok](../../azure-monitor/app/javascript.md) |Igen |Nem |
| Szükség van a kód ismételt felépítésére |Igen | Nem |



## <a name="monitor-a-live-iis-web-app"></a>Élő IIS-webapp figyelése

Ha az alkalmazás egy IIS-kiszolgálón fut, engedélyezze az Application Insightst az Állapotfigyelő használatával.

1. Az IIS-webkiszolgálón jelentkezzen be rendszergazdai hitelesítő adatokkal.
2. Ha az Application Insights állapotfigyelő még nincs telepítve, [töltse le és futtassa a telepítőt](#download)
3. Az Állapotfigyelőben válassza ki a megfigyelni kívánt telepített webappot vagy webhelyet. Jelentkezzen be az Azure-beli hitelesítő adataival.

    Konfigurálja az erőforrást, amelyben az eredményeket látni szeretné az Application Insights portálon. (Általában az a legjobb megoldás, ha létrehoz egy új erőforrást. Meglévő erőforrást akkor válasszon, ha már rendelkezik [webes tesztekkel][availability] vagy [ügyfélfigyeléssel][client] az alkalmazáshoz.) 

    ![Válasszon egy alkalmazást és egy erőforrást.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Indítsa újra az IIS-t.

    ![Válassza az Újraindítás gombot a párbeszédpanel tetején.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    A webszolgáltatása rövid időre megszakad.

## <a name="customize-monitoring-options"></a>A megfigyelési beállítások testreszabása

Az Application Insights engedélyezése DLL-eket és az ApplicationInsights.config fájlt adja hozzá a webapphoz. A [.config fájl szerkesztésével](../../azure-monitor/app/configuration-with-applicationinsights-config.md) bizonyos beállítások módosíthatók.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Az Application Insights ismételt engedélyezése az alkalmazás ismételt közzétételekor

Mielőtt újra közzéteszi az alkalmazást, fontolja [az Application Insights hozzáadását a kódhoz a Visual Studióban][greenbrown]. Ezzel részletesebb telemetriához jut, és egyéni telemetriát is írhat.

Ha anélkül szeretné újra közzétenni az alkalmazást, hogy a kódhoz hozzáadná az Application Insightst, vegye figyelembe, hogy az üzembehelyezési folyamat törölheti a DLL-eket és az ApplicationInsights.config fájlt a közzétett webhelyről. Ezért:

1. Ha szerkesztette az ApplicationInsights.config fájlt, készítsen róla egy másolatot, mielőtt újra közzéteszi az alkalmazást.
2. Tegye közzé újra az alkalmazást.
3. Engedélyezze újra az Application Insights-figyelést. (Használja a megfelelő módszert: vagy az Azure-webapp vezérlőpultját, vagy egy IIS-gazdagép Állapotfigyelőjét.)
4. Állítsa vissza a .config fájlon végrehajtott szerkesztéseket.


## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="cant-connect-no-telemetry"></a>Nem tud csatlakozni? Nem működik a telemetria?

* Nyissa meg [a szükséges kimenő portokat](../../azure-monitor/app/ip-addresses.md#outgoing-ports) a kiszolgálója tűzfalán, hogy az Állapotfigyelő működhessen.

### <a name="unable-to-login"></a>Nem sikerült a bejelentkezés

* Ha állapotfigyelője nem lehet bejelentkezni, tegye a parancssori telepítés helyett. Jelentkezzen be a rendszerállapotkulcsot gyűjtése megpróbál Állapotfigyelőt, de adhat meg ez a parancs segítségével manuálisan: 
```
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Nem sikerült betölteni a fájlt vagy a szerelvény "System.Diagnostics.DiagnosticSource"

Előfordulhat, hogy megjelenik a hibaüzenet Alkalmazásvé Insights engedélyezése után. Ennek oka az, a telepítő a DLL-re, a bin könyvtárban váltja fel.
Megoldásához frissítse a web.config:

```
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Mi a probléma nyomon követ [Itt](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Alkalmazások diagnosztikai üzenetek

* Nyissa meg az Állapotfigyelőt, és válassza ki az alkalmazását a bal oldali panelen. Ellenőrizze, hogy vannak-e diagnosztikai üzenetek ehhez az alkalmazáshoz a „Konfigurációs értesítések” szakaszban:

  ![A Teljesítmény panel megnyitása a kérelem, a válaszidő, a függőség és egyéb adatok megtekintéséhez](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Részletes naplók

* Alapértelmezés szerint az állapotfigyelő, diagnosztikai naplók kimenete: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* A kimenet részletes naplók, módosítsa a konfigurációs fájlban: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` , és adja hozzá `<add key="TraceLevel" value="All" />` , a `appsettings`.
Ezután indítsa újra az állapotfigyelő.

### <a name="insufficient-permissions"></a>Nem megfelelő engedélyek
  
* Ha a kiszolgálón „elégtelen engedélyekkel” kapcsolatos üzenet jelenik meg, próbálja meg a következőt:
  * Az IIS-kezelőben válassza ki az alkalmazáskészletét, nyissa meg a **Speciális beállítások** elemet, és a **Folyamatmodell** területen jegyezze fel az identitást.
  * A Számítógép-kezelés vezérlőpulton adja ezt az identitást a Teljesítményfigyelő felhasználói csoporthoz.

### <a name="conflict-with-systems-center-operations-manager"></a>Ütközés a Systems Center Operations Managerrel

* Ha MMA/SCOM (Systems Center Operations Manager) van telepítve a kiszolgálón, néhány verzió esetében ütközés léphet fel. Távolítsa el az SCOM-ot és az Állapotfigyelőt is, és telepítse újra a legújabb verziókat.

### <a name="failed-or-incomplete-installation"></a>Nem sikerült vagy nem teljes telepítése

Állapotmonitor nélküli telepítés során nem sikerül, ha a sikerült left, a nem teljes telepítését, amely nem tudja elhárítani az állapotfigyelő. Ehhez a manuális újraindítás szükséges.

Az alkalmazás könyvtárában található fájlok törléséhez:
- Minden olyan DLL-EK, a bin könyvtárban kezdődik vagy "Microsoft.AI." vagy a "Microsoft.ApplicationInsights.".
- A DLL-re, a bin könyvtárban "Microsoft.Web.Infrastructure.dll"
- A DLL-re, a bin könyvtárban "System.Diagnostics.DiagnosticSource.dll"
- Az alkalmazás könyvtárába távolítsa el a "App_Data\packages"
- Az alkalmazás könyvtárába távolítsa el a "applicationinsights.config."


### <a name="additional-troubleshooting"></a>További hibaelhárítás

* További részletek [hibaelhárítási][qna].

## <a name="system-requirements"></a>Rendszerkövetelmények
Operációs rendszeri támogatás az Application Insights Állapotfigyelőhöz a kiszolgálón:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

a legújabb szervizcsomaggal és a .NET-keretrendszer 4.5-ös verziójával

Az ügyféloldalon: Windows 7, 8, 8.1 és 10, szintén a .NET-keretrendszer 4.5

A rendszer az IIS támogatja: Az IIS 7, 7.5, 8, 8.5 (az IIS szükség)

## <a name="automation-with-powershell"></a>Automatizálás a PowerShell használatával
A PowerShell a saját IIS-kiszolgálón való használatával elindíthatja és leállíthatja a figyelést.

Először importálja az Application Insights-modult:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Derítse ki, melyik alkalmazások állnak megfigyelés alatt:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Választható) A webalkalmazás neve.
* Megjeleníti mindegyik webalkalmazás (vagy elnevezett alkalmazás) Application Insights figyelési állapotát ezen az IIS-kiszolgálón.
* Az `ApplicationInsightsApplication` elemet adja vissza mindegyik alkalmazáshoz:

  * `SdkState==EnabledAfterDeployment`: Alkalmazás megfigyelés alatt áll, és a futási időben lett kialakítva, az állapotfigyelő eszköz vagy által `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: Az alkalmazás nincs kialakítva az Application Insights. Vagy soha nem lett kialakítva, vagy a futásidejű figyelés le van tiltva az Állapotfigyelő eszközzel vagy a `Stop-ApplicationInsightsMonitoring` eszközzel.
  * `SdkState==EnabledByCodeInstrumentation`: Az alkalmazás által az SDK hozzáadása a forráskódja lett kialakítva. Az SDK-ja nem frissíthető és nem állítható le.
  * `SdkVersion` az alkalmazás figyeléséhez használt verziót jeleníti meg.
  * `LatestAvailableSdkVersion` a NuGet-katalógusban jelenleg elérhető verziót jeleníti meg. Ha az alkalmazást erre a verzióra szeretné frissíteni, használja a következőt: `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` Az alkalmazás neve az IIS-ben
* `-InstrumentationKey` Azon Application Insights-erőforrás kulcsa, ahol az eredményeket meg szeretné jeleníteni.
* Ez a parancsmag csak olyan alkalmazásokra van hatással, amelyek még nincsenek kialakítva – vagyis amelyek esetében az SdkState==NotInstrumented.

    A parancsmag nincs hatással a már kialakított alkalmazásokra. Nem számít, hogy azok a beépítési időben az SDK a kódhoz adásával, vagy a futásidőben a parancsmag korábbi használatával lettek kialakítva.

    Az alkalmazás kialakításához használt SDK-verzió a kiszolgálóra legutóbb letöltött verzió.

    A legújabb verzió letöltéséhez használja az Update-ApplicationInsightsVersion parancsot.
* Siker esetén az `ApplicationInsightsApplication` elemet adja vissza. Sikertelenség esetén nyomkövetést naplóz a stderrben.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Az alkalmazás neve az IIS-ben
* `-All` Leállítja minden alkalmazás megfigyelését ezen az IIS-kiszolgálón, amely esetében az `SdkState==EnabledAfterDeployment`
* Leállítja a megadott alkalmazások megfigyelését, és eltávolítja a kialakítást. Csak olyan alkalmazásokhoz működik, amelyek futásidőben lettek kialakítva az Állapotfigyelés eszközzel vagy a Start-ApplicationInsightsApplication paranccsal. (`SdkState==EnabledAfterDeployment`)
* Az ApplicationInsightsApplication elemet adja vissza.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: Az IIS-webalkalmazás nevére.
* `-InstrumentationKey`(Választható.) Ezzel módosíthatja az erőforrást, amelynek az alkalmazás telemetriája el lesz küldve.
* Ez a parancsmag:
  * frissíti az elnevezett alkalmazást a gépre legutóbb letöltött SDK-verzióra. (Csak akkor működik, ha `SdkState==EnabledAfterDeployment`)
  * Ha megad egy kialakítási kulcsot, újrakonfigurálja az elnevezett alkalmazást, hogy telemetriát küldjön az erőforrásnak ezzel a kulccsal. (Akkor működik, ha `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* letölti a legújabb Application Insights SDK-t a kiszolgálóra.

## <a name="questions"></a>Kérdések az Állapotfigyelővel kapcsolatban

### <a name="what-is-status-monitor"></a>Mi az Állapotfigyelő?

Egy asztali alkalmazás, amelyet az IIS-webkiszolgálón kell telepítenie. Segít a webalkalmazások kialakításában és konfigurálásában. 

### <a name="when-do-i-use-status-monitor"></a>Mire használhatom az Állapotfigyelőt?

* Bármely, az IIS-kiszolgálón futtatott, akár már futó webalkalmazások beállításához.
* További telemetria engedélyezéséhez olyan webalkalmazások számára, amelyeket [az Application Insights SDK-val állítottak össze](../../azure-monitor/app/asp-net.md) a fordítás során. 

### <a name="can-i-close-it-after-it-runs"></a>A futtatás után bezárhatom?

Igen. Az alkalmazást a kiválasztott webhelyek beállításának befejezése után be lehet zárni.

Az alkalmazás önmagától nem gyűjt telemetriai adatokat, csupán a webalkalmazásokat konfigurálja, és néhány engedélyt állít be.

### <a name="what-does-status-monitor-do"></a>Milyen műveleteket hajt végre az Állapotfigyelő?

Ha kiválaszt egy webalkalmazást, amelyet az Állapotfigyelővel szeretne beállítani:

* Letölti és elhelyezi az Application Insights-szerelvényeket és az ApplicationInsights.config fájlt a webalkalmazás bináris fájljainak mappáját.
* A függőségi hívások összegyűjtéséhez engedélyezi a CLR-profilkészítést.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Application Insights SDK melyik verzióját telepíti az állapotfigyelő?

Jelen pillanatban állapotfigyelője csak az Application Insights SDK-verziók 2.3-as vagy 2.4 telepíthetők.

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Az alkalmazás frissítésekor minden alkalommal futtatnom kell az Állapotfigyelőt?

Nem, ha az ismételt üzembe helyezés növekményesen történik. 

Ha a közzététel során a „Meglévő fájlok törlése” lehetőséget választja, az Application Insights konfigurálásához újból futtatnia kell az Állapotfigyelőt.

### <a name="what-telemetry-is-collected"></a>A rendszer milyen telemetriai adatokat gyűjt?

Olyan alkalmazások esetén, amelyeket az Állapotfigyelővel kizárólag futásidőben állít be:

* HTTP-kérések
* Függőségi hívások
* Kivételek
* Teljesítményszámlálók

A fordítási során már kiépített alkalmazások esetén:

 * Folyamatszámlálók.
 * Függőségi hívások (.NET 4.5); függőségi hívásokban visszaadott értékek (.NET 4.6).
 * Kivételhíváslánc-értékek.

[További információ](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download"></a>Állapotmonitor letöltése

- Töltse le és futtassa a [állapotfigyelő telepítőjét](https://go.microsoft.com/fwlink/?LinkId=506648)
- Vagy futtassa a [Webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx) , és keresse meg benne az Application Insights Állapotfigyelőt.

## <a name="next"></a>Következő lépések

A telemetriai adatok megtekintése:

* [A metrikák áttekintése](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez
* [Események és naplók keresése][diagnostic] a problémák diagnosztizálásához
* [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez
* [Irányítópultok létrehozása](../../azure-monitor/app/app-insights-dashboards.md)

További telemetriai funkciók hozzáadása:

* [Létrehozhat webes teszteket][availability] annak biztosításához, hogy a hely elérhető maradjon.
* [Webesügyfél-telemetriát adhat hozzá][usage], hogy lássa a weblapkód kivételeit, és nyomkövetési hívásokat szúrhasson be.
* [Application Insights SDK-t adhat a kódhoz][greenbrown], hogy nyomkövetési és naplóhíváskat szúrhasson be

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
