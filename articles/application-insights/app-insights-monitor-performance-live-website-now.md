---
title: Élő ASP.NET-webapp figyelése az Azure Application Insights segítségével | Microsoft Docs
description: Megfigyelheti egy webhely teljesítményét annak ismételt üzembe helyezése nélkül. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: abc3d5832cd85cb3297077f2d661ec8fe32fde9e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105291"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Webalkalmazások futásidejű kialakítása az Application Insights használatával

Egy élő webalkalmazást a kód módosítása vagy ismételt telepítése nélkül is kialakíthat az Azure Application Insights használatával. Ha az alkalmazásokat egy helyszíni IIS-kiszolgáló működteti, telepítse az Állapotfigyelőt. Ha Azure-webalkalmazások, illetve egy Azure VM-en futnak, az Azure vezérlőpultján bekapcsolhatja az Application Insights-figyelést. (Külön cikkek érhetők el az [élő J2EE-webalkalmazások](app-insights-java-live.md) és az [Azure Cloud Services](app-insights-cloudservices.md) kialakításáról.) Ehhez [Microsoft Azure](https://azure.com)-előfizetésre van szükség.

![Képernyőkép az App Insights áttekintése gráfok sikertelen kérelmek, a kiszolgáló válaszidejét és a kiszolgálói kérelmekre vonatkozó információkat tartalmazó](./media/app-insights-monitor-performance-live-website-now/overview-graphs.png)

Háromféleképpen alkalmazhatja az Application Insights szolgáltatást a .NET-webalkalmazásokra:

* **Felépítési idő:**[Adja az Application Insights SDK-t][greenbrown] a webalkalmazás kódjához.
* **Futási idő:** A webalkalmazását a kiszolgálón, az alábbiakban leírtak szerint, a kód újraépítése és újratelepítése nélkül alakíthatja ki.
* **Mindkettő:** Az SDK-t beépítheti a webalkalmazás-kódba, és alkalmazhatja a futásidejű bővítményeket is. Így mindkét lehetőség előnyeivel élhet.

Itt található egy összefoglaló az egyes módszerek eredményeiről:

|  | Felépítési idő | Futási idő |
| --- | --- | --- |
| Kérések és kivételek |Igen |Igen |
| [Részletes kivételek](app-insights-asp-net-exceptions.md) | |Igen |
| [Függőségek diagnosztikája](app-insights-asp-net-dependencies.md) |.NET 4.6+ esetén, kevésbé részletesen |Igen, teljes részletesség: eredménykódok, SQL-parancsszöveg, HTTP-parancsok|
| [Rendszerteljesítmény-számlálók](app-insights-performance-counters.md) |Igen |Igen |
| [API egyéni telemetriához][api] |Igen |Nem |
| [Nyomkövetési napló integrációja](app-insights-asp-net-trace-logs.md) |Igen |Nem |
| [Lapmegtekintések és felhasználói adatok](app-insights-javascript.md) |Igen |Nem |
| Szükség van a kód ismételt felépítésére |Igen | Nem |


## <a name="monitor-a-live-azure-web-app"></a>Élő Azure-webapp figyelése

Ha az alkalmazás Azure-webszolgáltatásként fut, a következőképpen kapcsolható be a figyelése:

* Válassza az Application Insights szolgáltatást az alkalmazás vezérlőpultján az Azure-ban.

    ![Az Application Insights beállítása egy Azure-webapphoz](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* Amikor megnyílik az Application Insights összegző lapja, kattintson a lap alján lévő hivatkozásra a teljes Application Insights-erőforrás megnyitásához.

    ![Az Application Insights elérése kattintással](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[Felhőben és virtuális gépeken futó alkalmazások figyelése](app-insights-overview.md).

### <a name="enable-client-side-monitoring-in-azure"></a>Az ügyféloldali figyelés engedélyezése az Azure-ban

Ha engedélyezte az Application Insights szolgáltatást az Azure-ban, felveheti a lapmegtekintéseket és a felhasználók telemetriai adatait.

1. Válassza a Beállítások > Alkalmazásbeállítások lehetőséget.
2.  Az alkalmazásbeállításoknál adjon meg egy új kulcs-érték párt: 
   
    Kulcs: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Érték: `true`
3. **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

Ezzel elhelyezte minden weblapon az Application Insights JavaScript SDK-t.

## <a name="monitor-a-live-iis-web-app"></a>Élő IIS-webapp figyelése

Ha az alkalmazás egy IIS-kiszolgálón fut, engedélyezze az Application Insightst az Állapotfigyelő használatával.

1. Az IIS-webkiszolgálón jelentkezzen be rendszergazdai hitelesítő adatokkal.
2. Ha az Application Insights Állapotfigyelő még nincs telepítve, töltse le és futtassa az [Állapotfigyelő telepítőjét](https://go.microsoft.com/fwlink/?LinkId=506648) (vagy futtassa a [Webplatform-telepítőt](https://www.microsoft.com/web/downloads/platform.aspx), és keresse meg benne az Application Insights Állapotfigyelőt).
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

* Nyissa meg [a szükséges kimenő portokat](app-insights-ip-addresses.md#outgoing-ports) a kiszolgálója tűzfalán, hogy az Állapotfigyelő működhessen.

* Nyissa meg az Állapotfigyelőt, és válassza ki az alkalmazását a bal oldali panelen. Ellenőrizze, hogy vannak-e diagnosztikai üzenetek ehhez az alkalmazáshoz a „Konfigurációs értesítések” szakaszban:

  ![A Teljesítmény panel megnyitása a kérelem, a válaszidő, a függőség és egyéb adatok megtekintéséhez](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Ha a kiszolgálón „elégtelen engedélyekkel” kapcsolatos üzenet jelenik meg, próbálja meg a következőt:
  * Az IIS-kezelőben válassza ki az alkalmazáskészletét, nyissa meg a **Speciális beállítások** elemet, és a **Folyamatmodell** területen jegyezze fel az identitást.
  * A Számítógép-kezelés vezérlőpulton adja ezt az identitást a Teljesítményfigyelő felhasználói csoporthoz.
* Ha MMA/SCOM (Systems Center Operations Manager) van telepítve a kiszolgálón, néhány verzió esetében ütközés léphet fel. Távolítsa el az SCOM-ot és az Állapotfigyelőt is, és telepítse újra a legújabb verziókat.
* Állapot figyelő naplók alapértelmezés szerint ezen a helyen található: "C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log"
* Lásd: [Hibaelhárítás][qna].

## <a name="system-requirements"></a>Rendszerkövetelmények
Operációs rendszeri támogatás az Application Insights Állapotfigyelőhöz a kiszolgálón:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

a legújabb szervizcsomaggal és a .NET-keretrendszer 4.5-ös verziójával

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
* További telemetria engedélyezéséhez olyan webalkalmazások számára, amelyeket [az Application Insights SDK-val állítottak össze](app-insights-asp-net.md) a fordítás során. 

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

## <a name="next"></a>Következő lépések

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
[usage]: app-insights-javascript.md
