<properties
    pageTitle="Teljesítményhibák diagnosztizálása futó IIS-webhelyen | Microsoft Azure"
    description="Megfigyelheti egy webhely teljesítményét annak ismételt üzembe helyezése nélkül. Önállóan vagy az Application Insights SDK-val lekérheti a függőségek telemetriáját."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/09/2016"
    ms.author="awills"/>


# Az Application Insights Állapotfigyelő telepítése a webhely teljesítményének megfigyeléséhez

*Az Application Insights jelenleg még előzetes verziójú kiadásban érhető el.*

A Visual Studio Application Insights Állapotfigyelőjével diagnosztizálhatja az ASP.NET-alkalmazások kivételeit és teljesítményével kapcsolatos problémákat. 

![mintadiagramok](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

> [AZURE.TIP] Különálló cikkek érhetők el az [élő J2EE-webalkalmazások](app-insights-java-live.md) és az [Azure Cloud Services](app-insights-cloudservices.md) kialakításáról.


Háromféleképpen alkalmazhatja az Application Insights szolgáltatást az IIS-webalkalmazásaira:

* **Felépítési idő:** [Adja az Application Insights SDK-t][greenbrown] a webalkalmazás kódjához. Ez a következőket nyújtja:
 * Standard diagnosztikai és használati telemetria.
 * Az [Application Insights API][api] lehetővé teszi, hogy saját telemetriát írjon a részletes használat követéséhez vagy a problémák diagnosztizálásához.
* **Futási idő:** Az Állapotfigyelővel alakítsa ki a webalkalmazását a kiszolgálón.
 * Megfigyelheti a már futó webalkalmazásokat: nem kell újraépítenie vagy újra közzétennie azokat.
 * Standard diagnosztikai és használati telemetria.
 * Függőségek diagnosztikája&#151;megkeresheti a hibákat vagy gyenge teljesítményt, ahol az alkalmazás más összetevőket, például adatbázisokat, REST API-kat vagy más szolgáltatásokat használ.
 * Elháríthat bármilyen hibát telemetriával.
* **Mindkettő:** fordítsa le az SDK-t webalkalmazás-kódra, és futtassa az Állapotfigyelőt a webkiszolgálón.  Mindkét világból a legjobb:
 * Standard diagnosztikai és használati telemetria.
 * Függőségek diagnosztikája.
 * Az API-val egyéni telemetriát írhat.
 * Elháríthat bármilyen hibát az SDK-val és a telemetriával.


## Az Application Insights Állapotfigyelő telepítése

Ehhez [Microsoft Azure](http://azure.com)-előfizetésre van szükség.

### Ha az alkalmazás az IIS-kiszolgálóján fut

1. Az IIS-webkiszolgálón jelentkezzen be rendszergazdai hitelesítő adatokkal.
2. Töltse le és futtassa az [Állapotfigyelő telepítőjét](http://go.microsoft.com/fwlink/?LinkId=506648).
4. A telepítővarázslóban jelentkezzen be a Microsoft Azure-ba.

    ![Bejelentkezés az Azure-ba a Microsoft-fiókja hitelesítő adataival](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *Csatlakozási hibákat tapasztal? Lásd: [Hibaelhárítás](#troubleshooting).*

5. Válassza ki a megfigyelni kívánt telepített webalkalmazást vagy webhelyet, majd konfigurálja az erőforrást, amelyben az eredményeket látni szeretné az Application Insights portálon.

    ![Válasszon egy alkalmazást és egy erőforrást.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Általában új erőforrás és [erőforráscsoport][roles] konfigurálását választja.

    Ellenkező esetben használjon meglévő erőforrást, ha már beállított [webes teszteket][availability] a helyéhez, vagy [webes ügyfélfigyelést][ügyfél].

6. Indítsa újra az IIS-t.

    ![Válassza az Újraindítás gombot a párbeszédpanel tetején.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    A webszolgáltatása rövid időre megszakad.

6. Az ApplicationInsights.config be lett illesztve a megfigyelni kívánt webalkalmazásokba.

    ![Keresse meg a .config fájlt a webalkalmazás kódfájljai között.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   A web.config is változott.

#### Később (újra) szeretné konfigurálni?

A varázsló befejezése után bármikor újrakonfigurálhatja az ügynököt. Ezt akkor is használhatja, ha telepítette az ügynököt, de probléma volt a kezdeti beállítással.

![Kattintson a tálcán az Application Insights ikonra](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)


### Ha az alkalmazás egy Azure-webalkalmazást futtat

Az Azure-webalkalmazás vezérlőpultján adja hozzá az Application Insights bővítményt.

![A webalkalmazásban Beállítások, Bővítmények, Hozzáadás, Application Insights](./media/app-insights-monitor-performance-live-website-now/05-extend.png)


### Ha ez egy Azure-felhőszolgáltatási projekt

[Adjon hozzá parancsfájlokat a webes és feldolgozói szerepkörökhöz](app-insights-cloudservices.md).


## A teljesítmény-telemetria megtekintése

Jelentkezzen be [az Azure Portalra](https://portal.azure.com), keresse meg az Application Insights szolgáltatást és nyissa meg a létrehozott erőforrást.

![Válassza a Tallózás, Application Insights lehetőséget, majd válassza ki az alkalmazását](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Nyissa meg a Teljesítmény panelt a kérelem, a válaszidő, a függőség és egyéb adatok megtekintéséhez.

![Teljesítmény](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Kattintson ide a megjelenített részletek módosításához vagy új diagram hozzáadásához.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

## Függőségek

A Függőségi időtartam diagramon a hívások ideje látható az alkalmazás felől a külső összetevők, például adatbázisok, REST API-k vagy Azure Blob Storage felé.

A diagram különböző függőségek felé irányuló hívások alapján végzett szegmentálásához válassza ki a diagramot, kapcsolja be a Csoportosítást, majd válassza a Függőség, Függőség típusa vagy Függőségi teljesítmény lehetőséget.

Úgy is szűrheti a diagramot, hogy adott függőség-, típus- vagy teljesítménygyűjtőt lásson. Kattintson a Szűrők fülre.

## Teljesítményszámlálók

(Nem Azure-webalkalmazásokhoz.) Kattintson az áttekintési panelen a Kiszolgálók gombra, hogy megtekintse a kiszolgálóteljesítmény-számlálók diagramjait (például a CPU elfoglaltsága és a memóriahasználat diagramjait).

Adjon hozzá új diagramot, vagy kattintson valamely diagramra a megjelenített adatok módosításához. 

[Az SDK által jelentett teljesítményszámlálókat is módosíthatja](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3). 

## Kivételek

![Kattintson végig a kiszolgálókivételek diagramján](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Lefúrhat adott kivételekhez (az utolsó hét napból), valamint hívásláncokat és környezeti adatokat kérhet le.

## Mintavételezés

Ha az alkalmazása sok adatot küld, és az Application Insights SDK-t az ASP.NET 2.0.0-beta3 vagy újabb verziójához használja, működhet az adaptív mintavételezés funkció és lehet, hogy csak a telemetria valamely százalékát küldi el. [További tudnivalók a mintavételezésről.](app-insights-sampling.md)


## Hibaelhárítás

### Csatlakozási hibák

Meg kell nyitnia néhány kimenő portot a kiszolgálója tűzfalán, hogy az Állapotfigyelő működhessen:

+ Telemetria – ezekre mindig szükség van:
 +  `dc.services.visualstudio.com:80`
 +  `dc.services.visualstudio.com:443`
 +  `dc.applicationinsights.microsoft.com`
+ Konfiguráció – csak módosításkor szükséges:
 -  `management.core.windows.net:443`
 -  `management.azure.com:443`
 -  `login.windows.net:443`
 -  `login.microsoftonline.com:443`
 -  `secure.aadcdn.microsoftonline-p.com:443`
 -  `auth.gfx.ms:443`
 -  `login.live.com:443`
+ Telepítés:
 +  `packages.nuget.org:443`

Ez a lista időről időre változhat.

### Nem működik a telemetria?

  * Használja a helyét adatok létrehozásához.
  * Várjon néhány percet, amíg az adatok megérkeznek, majd kattintson a **Frissítés** gombra.
  * Az egyes események megtekintéséhez nyissa meg a Diagnosztikai keresést (a Keresés csempét). Az események gyakran láthatók a Diagnosztikai keresésben, mielőtt az összesített adatok megjelennek a diagramokban.
  * Nyissa meg az Állapotfigyelőt, és válassza ki az alkalmazását a bal oldali panelen.  Ellenőrizze, hogy vannak-e diagnosztikai üzenetek ehhez az alkalmazáshoz a „Konfigurációs értesítések” szakaszban:

  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Győződjön meg arról, hogy a kiszolgáló tűzfala lehetővé teszi a kimenő forgalmat a fenti portokon.
  * Ha a kiszolgálón „elégtelen engedélyekkel” kapcsolatos üzenet jelenik meg, próbálja meg a következőt:
    * Az IIS-kezelőben válassza ki az alkalmazáskészletét, nyissa meg a **Speciális beállítások** elemet, és a **Folyamatmodell** területen jegyezze fel az identitást.
    * A Számítógép-kezelés vezérlőpulton adja ezt az identitást a Teljesítményfigyelő felhasználói csoporthoz.
  * Ha MMA/SCOM van telepítve a kiszolgálón, néhány verzió esetében ütközés léphet fel. Távolítsa el az SCOM-ot és az Állapotfigyelőt is, és telepítse újra a legújabb verziókat.
  * Lásd: [Hibaelhárítás][qna].

## Rendszerkövetelmények

Operációs rendszeri támogatás az Application Insights Állapotfigyelőhöz a kiszolgálón:

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

a legújabb szervizcsomaggal és a .NET-keretrendszer 4.0-s és 4.5-ös verziójával

Az ügyféloldalon Windows 7, 8 és 8.1, szintén a .NET-keretrendszer 4.0-s és 4.5-ös verziójával

IIS-támogatás: IIS 7, 7.5, 8, 8.5 (az IIS kötelező)

## Automatizálás a PowerShell használatával

A PowerShell használatával elindíthatja és leállíthatja a figyelést.

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Választható) A webalkalmazás neve.
* Megjeleníti mindegyik webalkalmazás (vagy elnevezett alkalmazás) Application Insights figyelési állapotát ezen az IIS-kiszolgálón.

* Az `ApplicationInsightsApplication` elemet adja vissza mindegyik alkalmazáshoz:
 * `SdkState==EnabledAfterDeployment`: Az alkalmazás megfigyelés alatt áll, és a futási időben lett kialakítva, az Állapotfigyelő eszköz vagy a `Start-ApplicationInsightsMonitoring` által.
 * `SdkState==Disabled`: Az alkalmazás nincs kialakítva az Application Insights szolgáltatáshoz. Vagy soha nem lett kialakítva, vagy a futásidejű figyelés le van tiltva az Állapotfigyelő eszközzel vagy a `Stop-ApplicationInsightsMonitoring` eszközzel.
 * `SdkState==EnabledByCodeInstrumentation`: Az alkalmazás kialakításakor az SDK a forráskódhoz lett hozzáadva. Az SDK-ja nem frissíthető és nem állítható le.
 * `SdkVersion` – az alkalmazás figyeléséhez használt verziót jeleníti meg.
 * `LatestAvailableSdkVersion`– a NuGet-katalógusban jelenleg elérhető verziót jeleníti meg. Ha az alkalmazást erre a verzióra szeretné frissíteni, használja a következőt: `Update-ApplicationInsightsMonitoring`.

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
* `-InstrumentationKey` (Választható.) Ezzel módosíthatja az erőforrást, amelynek az alkalmazás telemetriája el lesz küldve.
* Ez a parancsmag:
 * frissíti az elnevezett alkalmazást a gépre legutóbb letöltött SDK-verzióra. (Csak akkor működik, ha `SdkState==EnabledAfterDeployment`)
 * Ha megad egy kialakítási kulcsot, újrakonfigurálja az elnevezett alkalmazást, hogy telemetriát küldjön az erőforrásnak ezzel a kulccsal. (Akkor működik, ha `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* letölti a legújabb Application Insights SDK-t a kiszolgálóra.

## Azure-sablon

Ha a webalkalmazás az Azure-ban található és Azure Resource Manager-sablonnal hozza létre az erőforrásait, az Application Insights konfigurálásához hozzáadhatja ezt az erőforrások csomóponthoz:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` – az Application Insights-erőforrás neve
* `myWebAppName` – a webalkalmazás azonosítója

## <a name="next"></a>Következő lépések

* [Létrehozhat webes teszteket][availability] annak biztosításához, hogy a hely elérhető maradjon.
* [Eseményeket és naplókat kereshet][diagnostic], amelyek segítenek a problémák diagnosztizálásában.
* [Webesügyfél-telemetriát adhat hozzá][usage], hogy lássa a weblapkód kivételeit és nyomkövetési hívásokat szúrhasson be.
* [Application Insights SDK-t adhat a webszolgáltatás kódjához][greenbrown], hogy nyomkövetési és naplóhíváskat szúrhasson be a kiszolgálókódba.

## Videó

#### Teljesítményfigyelés

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[ügyfél]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=jun16_HO2-->


