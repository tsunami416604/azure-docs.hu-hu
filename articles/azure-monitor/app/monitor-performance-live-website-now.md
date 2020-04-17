---
title: Élő ASP.NET-webapp figyelése az Azure Application Insights segítségével | Microsoft Docs
description: Megfigyelheti egy webhely teljesítményét annak ismételt üzembe helyezése nélkül. Együttműködik ASP.NET helyszíni vagy virtuális gépeken üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: ba17ee275a744b88f2c76e7e3f99a1ac9cc8e758
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536828"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Eszköz webalkalmazások futásidőben az Application Insights Codeless Attach segítségével

> [!IMPORTANT]
> Az Állapotfigyelő használata már nem ajánlott. Azt váltotta fel az Azure Monitor Application Insights Ügynök (korábbi neve Status Monitor v2). Tekintse meg a [dokumentációt a helyszíni kiszolgálók központi telepítések](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) vagy [az Azure virtuális gép és a virtuális gép méretezési csoport központi telepítések.](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)

Egy élő webalkalmazást a kód módosítása vagy ismételt telepítése nélkül is kialakíthat az Azure Application Insights használatával. Ehhez [Microsoft Azure](https://azure.com)-előfizetésre van szükség.

Az Állapotfigyelő az IIS-ben vagy a virtuális gépben üzemeltetett .NET-alkalmazások eszköze.

- Ha az alkalmazás telepítve van az Azure Virtuálisgép vagy az Azure virtuálisgép-méretezési csoportban, kövesse [az alábbi utasításokat.](azure-vm-vmss-apps.md)
- Ha az alkalmazás telepítve van az Azure-alkalmazásszolgáltatásokban, kövesse [az alábbi utasításokat.](azure-web-apps.md)
- Ha az alkalmazás telepítve van egy Azure-beli virtuális gép, az Application Insights figyelése az Azure vezérlőpultról.
- (Az [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)műszerezéséről külön cikkek is találhatók.)


![Képernyőkép az App Insights áttekintő grafikonjairól, amelyek a sikertelen kérelmekre, a kiszolgálóválaszidőre és a kiszolgálói kérelmekre vonatkozó információkat tartalmazzák](./media/monitor-performance-live-website-now/overview-graphs.png)

Az Application Insights alkalmazást két útvonal közül választhat a .NET webalkalmazásokra:

* **Felépítési idő:** [Adja az Application Insights SDK-t][greenbrown] a webalkalmazás kódjához.
* **Futási idő:** A webalkalmazását a kiszolgálón, az alábbiakban leírtak szerint, a kód újraépítése és újratelepítése nélkül alakíthatja ki.

> [!NOTE]
> Ha buildidő-műszerezést használ, a futási idő-műszerezés akkor sem fog működni, ha be van kapcsolva.

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
2. Ha az Application Insights Állapotfigyelő még nincs telepítve, [töltse le és futtassa a telepítőt](#download).
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


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Hibaelhárítás

### <a name="confirm-a-valid-installation"></a>Érvényes telepítés megerősítése 

Íme néhány lépés, amelyet végrehajthat annak megerősítésére, hogy a telepítés sikeres volt.

- Ellenőrizze, hogy az applicationInsights.config fájl szerepel-e a célalkalmazás könyvtárában, és tartalmazza-e az ikey-t.

- Ha azt gyanítja, hogy az adatok [Analytics](../log-query/get-started-portal.md) hiányoznak futtathat egy egyszerű lekérdezést az Analytics-ben, hogy felsorolja az összes felhőszerepkört, amely jelenleg telemetriai adatokat küld.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Ha meg kell erősítenie, hogy az Application Insights sikeresen csatolva futtathatja a [Sysinternals Handle](https://docs.microsoft.com/sysinternals/downloads/handle) parancsot egy parancsablakban annak megerősítéséhez, hogy az applicationinsights.dll betöltést kapott az IIS.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Nem tud csatlakozni? Nem működik a telemetria?

* Nyissa meg [a szükséges kimenő portokat](../../azure-monitor/app/ip-addresses.md#outgoing-ports) a kiszolgálója tűzfalán, hogy az Állapotfigyelő működhessen.

### <a name="unable-to-login"></a>Nem lehet bejelentkezni

* Ha az Állapotfigyelő nem tud bejelentkezni, inkább egy parancssori telepítést végez. Az Állapotfigyelő megkísérli a bejelentkezést az ikey összegyűjtésére, de ezt manuálisan is megadhatja a következő paranccsal:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>A 'System.Diagnostics.DiagnosticSource' fájl vagy szerelvény nem tölthető be.

Előfordulhat, hogy ez a hiba az Application Insights engedélyezése után. Ennek az az oka, hogy a telepítő lecseréli ezt a dll-t a bin könyvtárban.
A web.config frissítésének javítása:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Mi nyomon ezt a kérdést [itt](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Alkalmazásdiagnosztikai üzenetek

* Nyissa meg az Állapotfigyelőt, és válassza ki az alkalmazását a bal oldali panelen.  Ellenőrizze, hogy vannak-e diagnosztikai üzenetek ehhez az alkalmazáshoz a „Konfigurációs értesítések” szakaszban:

  ![A Teljesítmény panel megnyitása a kérelem, a válaszidő, a függőség és egyéb adatok megtekintéséhez](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Részletes naplók

* Alapértelmezés szerint az Állapotfigyelő diagnosztikai naplókat a következő helyen adja ki:`C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Részletes naplók kimenetéhez módosítsa a konfigurációs `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` fájlt: `<add key="TraceLevel" value="All" />` és `appsettings`adja hozzá a hoz.
Ezután indítsa újra az állapotfigyelőt.

* Mivel az Állapotfigyelő egy .NET alkalmazás, a [.net nyomkövetést is engedélyezheti, ha hozzáadja a megfelelő diagnosztikát a konfigurációs fájlhoz.](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element) Bizonyos esetekben például hasznos lehet a hálózati szintű események megtekintése a [hálózati nyomkövetés konfigurálásával.](https://docs.microsoft.com/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Nincs elég engedély
  
* Ha a kiszolgálón „elégtelen engedélyekkel” kapcsolatos üzenet jelenik meg, próbálja meg a következőt:
  * Az IIS-kezelőben válassza ki az alkalmazáskészletét, nyissa meg a **Speciális beállítások** elemet, és a **Folyamatmodell** területen jegyezze fel az identitást.
  * A Számítógép-kezelés vezérlőpulton adja ezt az identitást a Teljesítményfigyelő felhasználói csoporthoz.

### <a name="conflict-with-systems-center-operations-manager"></a>Ütközés a Systems Center operations managerrel

* Ha MMA/SCOM (Systems Center Operations Manager) van telepítve a kiszolgálón, néhány verzió esetében ütközés léphet fel. Távolítsa el az SCOM-ot és az Állapotfigyelőt is, és telepítse újra a legújabb verziókat.

### <a name="failed-or-incomplete-installation"></a>Sikertelen vagy nem teljes telepítés

Ha az Állapotfigyelő a telepítés során meghibásodik, előfordulhat, hogy egy nem teljes telepítés marad, amelyből az Állapotfigyelő nem képes helyreállítani. Ehhez manuális alaphelyzetbe állításra van szükség.

Törölje az alkalmazáskönyvtárban található fájlok bármelyikét:
- A bin könyvtárban lévő dl-ek a "Microsoft.AI" kezdetű. vagy "Microsoft.ApplicationInsights".
- Ez a DLL a "Microsoft.Web.Infrastructure.dll" tárolókönyvtárban
- Ez a DLL a "System.Diagnostics.DiagnosticS.DiagnosticSource.dll" tárolókönyvtárban
- Az alkalmazáskönyvtárban távolítsa el a "App_Data\packages"
- Az alkalmazáskönyvtárból távolítsa el az "applicationinsights.config"


### <a name="additional-troubleshooting"></a>További hibaelhárítás

* Lásd: További [hibaelhárítás][qna].

## <a name="system-requirements"></a>Rendszerkövetelmények
Operációs rendszeri támogatás az Application Insights Állapotfigyelőhöz a kiszolgálón:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

a legújabb SP és .NET Framework 4.5 (Status Monitor épül ez a verzió a keretrendszer)

Az ügyféloldalon: Windows 7, 8, 8.1 és 10, szintén a .NET-keretrendszer 4.5-ös verziójával

IIS-támogatás: IIS 7, 7.5, 8, 8.5 (az IIS kötelező)

## <a name="automation-with-powershell"></a>Automatizálás a PowerShell használatával
A PowerShell a saját IIS-kiszolgálón való használatával elindíthatja és leállíthatja a figyelést.

Először importálja az Application Insights-modult:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Derítse ki, melyik alkalmazások állnak megfigyelés alatt:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Választható) A webalkalmazás neve.
* Megjeleníti mindegyik webalkalmazás (vagy elnevezett alkalmazás) Application Insights figyelési állapotát ezen az IIS-kiszolgálón.
* Az `ApplicationInsightsApplication` elemet adja vissza mindegyik alkalmazáshoz:

  * `SdkState==EnabledAfterDeployment`: Az alkalmazás megfigyelés alatt áll, és a futási időben lett kialakítva, az Állapotfigyelő eszköz vagy a `Start-ApplicationInsightsMonitoring` által.
  * `SdkState==Disabled`: Az alkalmazás nincs kialakítva az Application Insights szolgáltatáshoz. Vagy soha nem lett kialakítva, vagy a futásidejű figyelés le van tiltva az Állapotfigyelő eszközzel vagy a `Stop-ApplicationInsightsMonitoring` eszközzel.
  * `SdkState==EnabledByCodeInstrumentation`: Az alkalmazás kialakításakor az SDK a forráskódhoz lett hozzáadva. Az SDK-ja nem frissíthető és nem állítható le.
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

* `-Name`: A webalkalmazás neve az IIS-ben.
* `-InstrumentationKey`(Nem kötelező.) Ezzel módosíthatja azt az erőforrást, amelyre az alkalmazás telemetriai adatokat küld.
* Ez a parancsmag:
  * frissíti az elnevezett alkalmazást a gépre legutóbb letöltött SDK-verzióra. (Csak akkor működik, ha `SdkState==EnabledAfterDeployment`)
  * Ha megad egy kialakítási kulcsot, újrakonfigurálja az elnevezett alkalmazást, hogy telemetriát küldjön az erőforrásnak ezzel a kulccsal. (Akkor működik, ha `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* letölti a legújabb Application Insights SDK-t a kiszolgálóra.

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Kérdések az Állapotfigyelővel kapcsolatban

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

* Letölti és elhelyezi az Application Insights-összeállításokat és az ApplicationInsights.config fájlt a webalkalmazás bináris mappájába.
* A függőségi hívások összegyűjtéséhez engedélyezi a CLR-profilkészítést.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Az Application Insights SDK melyik verzióját telepíti az Állapotfigyelő?

Az Állapotfigyelő jelenleg csak az Application Insights SDK 2.3-as vagy 2.4-es verzióját telepítheti. 

Az Application Insights SDK 2.4-es verziója az [utolsó verzió, amely támogatja a .NET 4.0-t,](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) amely [2016 januárja](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/)volt az EOL. Ezért a .NET 4.0 alkalmazás instrumentumozására az Állapotfigyelő használható. 

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

## <a name="download-status-monitor"></a><a name="download"></a>Állapotfigyelő letöltése

- Az új [PowerShell-modul](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) használata
- Az [Állapotfigyelő telepítőjének](https://go.microsoft.com/fwlink/?LinkId=506648) letöltése és futtatása
- Vagy [futtassa a Web Platform Installer alkalmazást,](https://www.microsoft.com/web/downloads/platform.aspx) és keressen benne az Application Insights Állapotfigyelőre.

## <a name="next-steps"></a><a name="next"></a>További lépések

A telemetriai adatok megtekintése:

* [A metrikák áttekintése](../../azure-monitor/platform/metrics-charts.md) a teljesítmény és a használat figyeléséhez
* [Események és naplók keresése][diagnostic] a problémák diagnosztizálásához
* [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez

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
