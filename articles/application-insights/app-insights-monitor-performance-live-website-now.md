---
title: "Élő ASP.NET-webapp figyelése az Azure Application Insights segítségével | Microsoft Docs"
description: "Megfigyelheti egy webhely teljesítményét annak ismételt üzembe helyezése nélkül. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 917f54248f4c9277caa3cf09d92f78593a901e89
ms.openlocfilehash: fd76f40f5a34b6adf9c6ec3bded604d59b6baa72


---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Webalkalmazások futásidejű kialakítása az Application Insights használatával


Egy élő webalkalmazást a kód módosítása vagy ismételt telepítése nélkül is kialakíthat az Azure Application Insights használatával. Ha az alkalmazásokat egy helyszíni IIS-kiszolgáló működteti, telepítse az Állapotfigyelőt; vagy ha Azure-webalkalmazások, illetve egy Azure VM-en futnak, telepítheti az Application Insights bővítményt. (Külön cikkek érhetők el az [élő J2EE-webalkalmazások](app-insights-java-live.md) és az [Azure Cloud Services](app-insights-cloudservices.md) kialakításáról.) Ehhez [Microsoft Azure](http://azure.com)-előfizetésre van szükség.

![mintadiagramok](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Háromféleképpen alkalmazhatja az Application Insights szolgáltatást a .NET-webalkalmazásokra:

* **Felépítési idő:** [Adja az Application Insights SDK-t][greenbrown] a webalkalmazás kódjához.
* **Futási idő:** A webalkalmazását a kiszolgálón, az alábbiakban leírtak szerint, a kód újraépítése és újratelepítése nélkül alakíthatja ki.
* **Mindkettő:** Az SDK-t beépítheti a webalkalmazás-kódba, és alkalmazhatja a futásidejű bővítményeket is. Így mindkét lehetőség előnyeivel élhet.

Itt található egy összefoglaló az egyes módszerek eredményeiről:

|  | Felépítési idő | Futási idő |
| --- | --- | --- |
| Kérések és kivételek |Igen |Igen |
| [Részletes kivételek](app-insights-asp-net-exceptions.md) | |Igen |
| [Függőségek diagnosztikája](app-insights-asp-net-dependencies.md) |.NET 4.6+ esetén, kevésbé részletesen |Igen, teljes részletesség: eredménykódok, SQL-parancsszöveg, HTTP-parancsok|
| [Rendszerteljesítmény-számlálók](app-insights-performance-counters.md) |Igen |Igen |
| [API egyéni telemetriához][api] |Igen | |
| [Nyomkövetési napló integrációja](app-insights-asp-net-trace-logs.md) |Igen | |
| [Lapmegtekintések és felhasználói adatok](app-insights-javascript.md) |Igen | |
| Nincs szükség a kód ismételt felépítésére |Nem | |


## <a name="monitor-a-live-azure-web-app"></a>Élő Azure-webapp figyelése

Ha az alkalmazás Azure-webszolgáltatásként fut, a következőképpen kapcsolható be a figyelése:

* Válassza az Application Insights szolgáltatást az alkalmazás vezérlőpultján az Azure-ban.

    ![Az Application Insights beállítása egy Azure-webapphoz](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* Amikor megnyílik az Application Insights összegző lapja, kattintson a lap alján lévő hivatkozásra a teljes Application Insights-erőforrás megnyitásához.

    ![Az Application Insights elérése kattintással](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[Felhőben és virtuális gépeken futó alkalmazások figyelése](app-insights-azure.md).

## <a name="monitor-a-live-iis-web-app"></a>Élő IIS-webapp figyelése

Ha az alkalmazás egy IIS-kiszolgálón fut, engedélyezze az Application Insightst az Állapotfigyelő használatával.

1. Az IIS-webkiszolgálón jelentkezzen be rendszergazdai hitelesítő adatokkal.
2. Ha az Application Insights Állapotfigyelő még nincs telepítve, töltse le és futtassa az [Állapotfigyelő telepítőjét](http://go.microsoft.com/fwlink/?LinkId=506648).
3. Az Állapotfigyelőben válassza ki a megfigyelni kívánt telepített webappot vagy webhelyet. Jelentkezzen be az Azure-beli hitelesítő adataival.

    Konfigurálja az erőforrást, amelyben az eredményeket látni szeretné az Application Insights portálon. (Általában az a legjobb megoldás, ha létrehoz egy új erőforrást. Meglévő erőforrást akkor válasszon, ha már rendelkezik [webes tesztekkel][availability] vagy [ügyfélfigyeléssel][client] az alkalmazáshoz.) 

    ![Válasszon egy alkalmazást és egy erőforrást.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Indítsa újra az IIS-t.

    ![Válassza az Újraindítás gombot a párbeszédpanel tetején.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    A webszolgáltatása rövid időre megszakad.

## <a name="customize-monitoring-options"></a>A megfigyelési beállítások testreszabása

Az Application Insights engedélyezése DLL-eket és az ApplicationInsights.config fájlt adja hozzá a webapphoz. A [.config fájl szerkesztésével](app-insights-configuration-with-applicationinsights-config.md) bizonyos beállítások módosíthatók.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Az Application Insights ismételt engedélyezése az alkalmazás ismételt közzétételekor

Mielőtt újra közzéteszi az alkalmazást, fontolja [az Application Insights hozzáadását a kódhoz a Visual Studióban][greenbrown]. Ezzel részletesebb telemetriához jut, és egyéni telemetriát is írhat.

Ha anélkül szeretné újra közzétenni az alkalmazást, hogy a kódhoz hozzáadná az Application Insightst, vegye figyelembe, hogy az üzembehelyezési folyamat törölheti a DLL-eket és az ApplicationInsights.config fájlt a közzétett webhelyről. Ezért:

1. Ha szerkesztette az ApplicationInsights.config fájlt, készítsen róla egy másolatot, mielőtt újra közzéteszi az alkalmazást.
2. Tegye közzé újra az alkalmazást.
3. Engedélyezze újra az Application Insights-figyelést. (Használja a megfelelő módszert: vagy az Azure-webapp vezérlőpultját, vagy egy IIS-gazdagép Állapotfigyelőjét.)
4. Állítsa vissza a .config fájlon végrehajtott szerkesztéseket.


## <a name="troubleshooting-runtime-configuration-of-application-insights"></a>Az Application Insights futtatókörnyezet-konfigurációjának hibaelhárítása

### <a name="cant-connect-no-telemetry"></a>Nem tud csatlakozni? Nem működik a telemetria?

* Meg kell nyitnia [néhány kimenő portot](app-insights-ip-addresses.md#outgoing-ports) a kiszolgálója tűzfalán, hogy az Állapotfigyelő működhessen.

* Nyissa meg az Állapotfigyelőt, és válassza ki az alkalmazását a bal oldali panelen. Ellenőrizze, hogy vannak-e diagnosztikai üzenetek ehhez az alkalmazáshoz a „Konfigurációs értesítések” szakaszban:

  ![A Teljesítmény panel megnyitása a kérelem, a válaszidő, a függőség és egyéb adatok megtekintéséhez](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Ha a kiszolgálón „elégtelen engedélyekkel” kapcsolatos üzenet jelenik meg, próbálja meg a következőt:
  * Az IIS-kezelőben válassza ki az alkalmazáskészletét, nyissa meg a **Speciális beállítások** elemet, és a **Folyamatmodell** területen jegyezze fel az identitást.
  * A Számítógép-kezelés vezérlőpulton adja ezt az identitást a Teljesítményfigyelő felhasználói csoporthoz.
* Ha MMA/SCOM van telepítve a kiszolgálón, néhány verzió esetében ütközés léphet fel. Távolítsa el az SCOM-ot és az Állapotfigyelőt is, és telepítse újra a legújabb verziókat.
* Lásd: [Hibaelhárítás][qna].

## <a name="system-requirements"></a>Rendszerkövetelmények
Operációs rendszeri támogatás az Application Insights Állapotfigyelőhöz a kiszolgálón:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

a legújabb szervizcsomaggal és a .NET-keretrendszer 4.5-ös verziójával

Az ügyféloldalon Windows 7, 8, 8.1 és 10, szintén a .NET-keretrendszer 4.5-ös verziójával

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

    A parancsmag nincs hatással az akár beépítési időben az SDK a kódhoz adásával, akár a futásidőben a parancsmag korábbi használatával már kialakított alkalmazásokra.

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
* `-InstrumentationKey`(Választható.) Ezzel módosíthatja az erőforrást, amelynek az alkalmazás telemetriája el lesz küldve.
* Ez a parancsmag:
  * frissíti az elnevezett alkalmazást a gépre legutóbb letöltött SDK-verzióra. (Csak akkor működik, ha `SdkState==EnabledAfterDeployment`)
  * Ha megad egy kialakítási kulcsot, újrakonfigurálja az elnevezett alkalmazást, hogy telemetriát küldjön az erőforrásnak ezzel a kulccsal. (Akkor működik, ha `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* letölti a legújabb Application Insights SDK-t a kiszolgálóra.

## <a name="a-namenextanext-steps"></a><a name="next"></a>Következő lépések

A telemetriai adatok megtekintése:

* [A metrikák áttekintése](app-insights-metrics-explorer.md) a teljesítmény és a használat figyeléséhez
* [Események és naplók keresése][diagnostic] a problémák diagnosztizálásához
* [Elemzések](app-insights-analytics.md) az összetettebb lekérdezésekhez
* [Irányítópultok létrehozása](app-insights-dashboards.md)

További telemetriai funkciók hozzáadása:

* [Létrehozhat webes teszteket][availability] annak biztosításához, hogy a hely elérhető maradjon.
* [Webesügyfél-telemetriát adhat hozzá][usage], hogy lássa a weblapkód kivételeit, és nyomkövetési hívásokat szúrhasson be.
* [Application Insights SDK-t adhat a kódhoz][greenbrown], hogy nyomkövetési és naplóhíváskat szúrhasson be

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=Feb17_HO2-->


