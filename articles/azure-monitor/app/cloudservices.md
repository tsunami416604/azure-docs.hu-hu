---
title: Application Insights for Azure felhőszolgáltatások | Microsoft dokumentumok
description: Webes és feldolgozói szerepkörök hatékony figyelése az Application Insightsszal
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: ce794a7bd18635fddfa30056ab2d675dc138097d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276191"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights for Azure felhőszolgáltatásokhoz
[Az Application Insights][start] figyelheti [az Azure felhőszolgáltatási alkalmazások](https://azure.microsoft.com/services/cloud-services/) rendelkezésre állását, teljesítményét, hibáit és használatát az Application Insights SDK-k ból származó adatok és a felhőszolgáltatások Azure [Diagnostics-adatainak](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) kombinálásával. A széles körben elérhető módon működő alkalmazások teljesítményével és hatékonyságával kapcsolatos visszajelzések birtokában tájékozott döntéseket hozhat a fejlesztés irányát illetően az egyes fejlesztési fázisokban.

![Áttekintő irányítópult](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, a következőkre van szüksége:

* [Azure-előfizetés.](https://azure.com) Jelentkezzen be Microsoft-fiókjával Windows, Xbox Live vagy más Microsoft felhőszolgáltatásokhoz. 
* Microsoft Azure-eszközök 2.9-es vagy újabb verziói.
* Fejlesztői elemzési eszközök 7.10-es vagy újabb verziói.

## <a name="get-started-quickly"></a>Első lépések gyorsan
A felhőszolgáltatás Application Insightsszal való figyelésének leggyorsabb és legegyszerűbb módja, ha kiválasztja ezt a lehetőséget, amikor közzéteszi a szolgáltatást az Azure-ban.

![Példa diagnosztikai beállítások lapra](./media/cloudservices/azure-cloud-application-insights.png)

Ez a beállítás futásidőben instrumentális az alkalmazás, így az összes telemetriai, hogy a kérelmek figyeléséhez szükséges, kivételek és függőségek a webes szerepkörben. Azt is figyeli a teljesítményszámlálók a feldolgozói szerepkörök. Az alkalmazás által létrehozott diagnosztikai nyomkövetéseket az Application Insights is elküldi.

Ha ez a lehetőség minden amire szüksége van, akkor kész. 

A következő lépés az [alkalmazás mérőszámeinek megtekintése,](../../azure-monitor/app/metrics-explorer.md) [az adatok lekérdezése az Analytics szolgáltatással.](../../azure-monitor/app/analytics.md) 

A böngésző teljesítményének figyeléséhez célszerű lehet [rendelkezésre állási teszteket](../../azure-monitor/app/monitor-web-app-availability.md) beállítani, és [kódot adni a weblapokhoz.](../../azure-monitor/app/javascript.md)

A következő szakaszok a következő további lehetőségeket ismertetik:

* Különböző összetevőkből származó adatok küldése és konfigurációk létrehozása az erőforrások elkülönítéséhez.
* Egyéni telemetriát adhat hozzá az alkalmazásból.

## <a name="sample-app-instrumented-with-application-insights"></a>Az Application Insights-szal instrumentált mintaalkalmazás
Ebben a [mintaalkalmazásban](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)az Application Insights hozzá kerül egy felhőszolgáltatáshoz, amely két feldolgozói szerepkört üzemeltet az Azure-ban. 

A következő szakaszban megtudhatja, hogyan igazíthatja a saját felhőszolgáltatási projekt ugyanúgy.

## <a name="plan-resources-and-resource-groups"></a>Erőforrások és erőforráscsoportok tervezése
Az alkalmazás telemetriai adatait az alkalmazás tárolja, elemzi, és megjelenik egy Application Insights típusú Azure-erőforrásban. 

Mindegyik erőforrás egy erőforráscsoportba tartozik. Az erőforráscsoportok a költségek kezelésére, a csapattagok hozzáférésének megadására és a frissítések egyetlen összehangolt tranzakcióban történő telepítésére szolgálnak. Például írhat [egy parancsfájlt egy](../../azure-resource-manager/templates/deploy-powershell.md) Azure-felhőszolgáltatás és az Application Insights figyelési erőforrások egyetlen műveletben üzembe helyezéséhez.

### <a name="resources-for-components"></a>Az összetevők erőforrásai
Azt javasoljuk, hogy hozzon létre egy külön erőforrást az alkalmazás minden egyes összetevőjéhez. Ez azt, hogy hozzon létre egy erőforrást az egyes webes szerepkör és feldolgozói szerepkör. Az egyes összetevőket külön-külön is elemezheti, de létrehozhat egy [irányítópultot,](../../azure-monitor/app/overview-dashboard.md) amely az összes összetevő kulcsdiagramjait egyesíti, így egyetlen nézetben összehasonlíthatja és figyelheti őket. 

Egy másik megközelítés az, hogy a telemetriai adatokat egynél több szerepkörből ugyanabba az erőforrásba küldi, de [dimenziótulajdonságot ad hozzá minden olyan telemetriai elemhez,](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) amely azonosítja a forrásszerepkörét. Ebben a megközelítésben a metrikadiagramok, például a kivételek, általában a különböző szerepkörök számainak összesítését mutatják, de szükség esetén szegmentálhatja a diagramot a szerepkör-azonosító szerint. A kereséseket ugyanazzal a dimenzióval is szűrheti. Ez az alternatíva teszi egy kicsit könnyebb megtekinteni mindent egy időben, de ez is vezethet némi zavart a szerepek között.

A böngészőtelemetria általában ugyanabban az erőforrásban jelenik meg, mint a kiszolgálóoldali webes szerepköre.

Az Application Insights-erőforrások at a különböző összetevők egy erőforráscsoportba. Ez a megközelítés megkönnyíti a közös kezelésüket. 

### <a name="separate-development-test-and-production"></a>A fejlesztési, tesztelési és éles környezetek elkülönítése
Ha már egy következő funkcióhoz fejleszt egyéni eseményeket, miközben a korábbi verzió még éles üzemben működik, érdemes lehet a fejlesztési telemetriát egy külön Application Insights-erőforrásba küldeni. Ellenkező esetben nehéz lehet megtalálni a teszt telemetriai adatok között az élő webhely ről érkező összes forgalmat.

A helyzet elkerülése érdekében hozzon létre külön erőforrásokat a rendszer minden egyes buildkonfigurációjához vagy "bélyegzőjéhez" (fejlesztés, tesztelés, élesség és így tovább). Helyezze az egyes buildkonfigurációk erőforrásait külön erőforráscsoportokba. 

A telemetriai adatok küldése a megfelelő erőforrásokat, beállíthatja az Application Insights SDK úgy, hogy a build konfigurációjától függően egy másik instrumentation kulcs. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Application Insights-erőforrás létrehozása mindegyik szerepkörhöz

Ha úgy döntött, hogy hozzon létre egy külön erőforrást az egyes szerepkörök, és esetleg egy külön készlet minden build-konfigurációhoz, a legegyszerűbb, ha mindet az Application Insights-portálon hozza létre. Ha sokat hoz létre erőforrásokat, [automatizálhatja a folyamatot.](../../azure-monitor/app/powershell.md)

1. Az [Azure Portalon][portal]válassza az **Új** > **fejlesztői szolgáltatások** > **alkalmazáselemzési**lehetőséget.  

    ![Az Application Insights ablaktábla](./media/cloudservices/01-new.png)

1. Az **Alkalmazástípusa** legördülő listában válassza **ASP.NET webalkalmazás**lehetőséget.

Minden erőforrást egy instrumentation kulcs azonosít. Erre a kulcsra később szüksége lehet, ha manuálisan szeretné konfigurálni vagy ellenőrizni az SDK konfigurációját.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Azure Diagnostics beállítása az egyes szerepkörökhöz
Ezzel a beállítással figyelheti az alkalmazást az Application Insightsszal. Webes szerepkörök esetén ez a beállítás teljesítményfigyelést, riasztásokat, diagnosztikát és használati elemzést biztosít. Más szerepkörök, kereshet és figyelheti az Azure Diagnosztika, például az újraindítás, teljesítményszámlálók és a System.Diagnostics.Trace hívások. 

1. A Visual Studio Solution Explorer, ** \<a YourCloudService>**  >  **Roles**csoportban nyissa meg az egyes szerepkörök tulajdonságait.

1. A **konfigurációban**jelölje be a Diagnosztikai adatok küldése az **Application Insightsba** jelölőnégyzetet, majd jelölje be a korábban létrehozott Application Insights-erőforrást.

Ha úgy döntött, hogy külön Application Insights-erőforrást használ mindegyik buildkonfigurációhoz, előbb válassza ki a konfigurációt.

![Az Application Insights konfigurálása](./media/cloudservices/configure-azure-diagnostics.png)

Ennek az a hatása, hogy az Application Insights instrumentation kulcsokat szúr be a ServiceConfiguration nevű *fájlokba.\*. . cscfg*. Itt van a [minta kód](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Ha módosítani szeretné az Application Insightsnak küldött diagnosztikai adatok szintjét, ezt [közvetlenül a *.cscfg* fájlok szerkesztésével](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)teheti meg.

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Az SDK telepítése az egyes projektekben
Ezzel a beállítással egyéni üzleti telemetriai adatokat adhat hozzá bármely szerepkörhöz. A beállítás közelebbi elemzést nyújt az alkalmazás alkalmazásának és teljesítményének elemzéséről.

A Visual Studióban konfigurálja külön az Application Insights SDK-t az egyes felhőalkalmazás-projektekhez.

1. A **webes szerepkörök**konfigurálásához kattintson a jobb gombbal a projektre, majd válassza az **Application Insights konfigurálása** vagy **> Application Insights telemetriai adatok hozzáadása parancsot.**

1. **Feldolgozói szerepkörök**konfigurálása : 

    a. Kattintson a jobb gombbal a projektre, és válassza **a NuGet csomagok kezelése parancsot.**

    b. Adja hozzá az [Application Insights a Windows-kiszolgálókon](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) modult.

    ![Az „Application Insights” kifejezés keresése](./media/cloudservices/04-ai-nuget.png)

1. Az SDK konfigurálása adatok küldésére az Application Insights erőforrásba:

    a. Megfelelő indítási funkcióban állítsa be a instrumentációs kulcsot a *.cscfg* fájl konfigurációs beállításából:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Ismételje meg az "a" lépést az alkalmazás minden szerepkörénél. Lásd az alábbi példákat:
   
    * [Webes szerepkör](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Feldolgozói szerepkör](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Weblapok esetén](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Állítsa be, hogy az *ApplicationInsights.config* fájl mindig a kimeneti könyvtárba másolva legyen.

   A *.config* fájlban egy üzenet kéri, hogy helyezze oda a műszerezési kulcsot. A felhőalapú alkalmazások esetében azonban jobb, ha a *.cscfg* fájlból állítja be. Ez a megközelítés biztosítja, hogy a szerepkör megfelelően azonosítható a portálon.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Az Állapotfigyelő beállítása a teljes SQL-lekérdezések gyűjtésére (nem kötelező)

Ez a lépés csak akkor szükséges, ha teljes SQL-lekérdezéseket szeretne rögzíteni a .NET Framework rendszeren. 

1. A `\*.csdef` fájlban [Indítási feladat](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks) hozzáadása az egyes szerepkörhöz hasonló 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Töltse le [az InstallAgent.bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) és [az InstallAgent.ps1 fájlt,](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1)és helyezze őket az `AppInsightsAgent` egyes szerepkör-projektek mappájába. Győződjön meg arról, hogy másolja őket a kimeneti könyvtárba a Visual Studio fájltulajdonságain keresztül, vagy parancsfájlokat hozhat létre.

3. Az összes feldolgozói szerepkörön adjon hozzá környezeti változókat: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Az alkalmazás futtatása és közzététele

1. Futtassa az alkalmazást, és jelentkezzen be az Azure-ba. 

1. Nyissa meg a létrehozott Application Insights-erőforrásokat.

   Az egyes adatpontok a [Keresés][diagnostic]ben jelennek meg, az összesített adatok pedig a [Metrikus kezelőben.](../../azure-monitor/app/metrics-explorer.md)

1. Adjon hozzá további telemetriai adatokat (lásd a következő szakaszokat), majd tegye közzé az alkalmazást az élő diagnosztika és a használati visszajelzések lekérése érdekében. 

Ha nincsenek adatok, tegye a következőket:

1. Az egyes események megtekintéséhez nyissa meg a [Keresés csempét.][diagnostic]
1. Az alkalmazásban nyissa meg a különböző oldalakat, hogy létrehoz néhány telemetriai adatokat.
1. Várjon néhány másodpercet, majd kattintson a **Frissítés gombra.**  

További információk: [Hibaelhárítás][qna].

## <a name="view-azure-diagnostics-events"></a>Azure diagnosztikai események megtekintése
Az Azure [diagnosztikai](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) adatait az Application Insights ban találja a következő helyeken:

* A teljesítményszámlálók egyéni mérőszámokként jelennek meg. 
* A Windows eseménynaplók nyomkövetésekként és egyéni eseményekként jelennek meg.
* Az alkalmazásnaplók, ETW-naplók és egyéb diagnosztikai infrastruktúra-naplók nyomkövetésként jelennek meg.

A teljesítményszámlálók és az események számának megtekintéséhez nyissa meg a [Metrikakezelőt,](../../azure-monitor/app/metrics-explorer.md) és adja hozzá a következő táblázatot:

![Az Azure diagnosztikai adatai](./media/cloudservices/23-wad.png)

Ha az Azure Diagnostics által küldött különböző nyomkövetési naplók között szeretne keresni, használja a [Keresés](../../azure-monitor/app/diagnostic-search.md) vagy [az Analytics-lekérdezést.](../../azure-monitor/log-query/get-started-portal.md) Tegyük fel például, hogy van egy nem kezelt kivétel, amely egy szerepkör összeomlását és újrahasznosítását okozta. Ezek az információk a Windows eseménynaplójában, az Alkalmazás csatornában jelennek meg. A Keresés segítségével megtekintheti a Windows eseménynapló-hibát, és lekéri a kivétel teljes veremnyomatát. Ezzel segít megtalálni a probléma kiváltó okát.

![Azure Diagnostics keresés](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>További telemetria
A következő szakaszok ismertetik, hogyan kaphat további telemetriai adatokat az alkalmazás különböző aspektusaiból.

## <a name="track-requests-from-worker-roles"></a>Dolgozói szerepköröktől érkező kérelmek nyomon követése
A webes szerepkörökben a kérések modulja automatikusan gyűjti a HTTP-kérésekkel kapcsolatos adatokat. Példák at, hogyan lehet felülírni az alapértelmezett gyűjtemény viselkedését, lásd a [minta MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

A feldolgozói szerepkörökhöz intézett hívások teljesítményének rögzítése a HTTP-kérésekkel megegyező módon történő nyomkövetéssel hajtható végre. Az Application Insightsban a Kérés telemetriatípus a megnevezett kiszolgálóoldali műveletek egységeit méri, amelyeknek mérhető az idejük, és külön-külön sikerülhetnek vagy meghiúsulhatnak. Bár a HTTP-kérelmeket az SDK automatikusan rögzíti, beszúrhatja a saját kódját a feldolgozói szerepkörökbe érkező kérelmek nyomon követéséhez.

Tekintse meg a két minta feldolgozói szerepkört a kérelmek jelentéséhez: 
* [WorkerroleA (Dolgozói roleA)](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Kivételek
A különböző webalkalmazástípusokból származó kezeletlen kivételek gyűjtéséről az [Application Insights ban a kivételek figyelése](../../azure-monitor/app/asp-net-exceptions.md)című témakörben talál további információt.

A minta webes szerepkör MVC5 és Web API 2 vezérlőkkel rendelkezik. A két vezérlőtől származó nem kezelt kivételeket a rendszer a következő kezelőkkel rögzíti:

* Az MVC5 vezérlőkhöz beállított [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) beállítás [a példában látható módon](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [Az AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) a webes API 2-vezérlőkhöz úgy van [beállítva, ahogy az ebben a példában látható](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

A feldolgozói szerepkörök esetében a kivételeket kétféleképpen követheti nyomon:

* Használja a TrackException(ex) segítségével.
* Ha hozzáadta az Application Insights trace listener NuGet csomagot, használhatja system.Diagnostics.Trace [naplókivételek, ahogy az ebben a példában](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)látható.

## <a name="performance-counters"></a>Teljesítményszámlálók
A rendszer alapértelmezés szerint az alábbi számlálókat gyűjti:

* \Process(?? APP_WIN32_PROC??) \% Processzor idő
* \Memory\Available Bytes
* \.NET CLR-kivételek (??APP_CLR_PROC??)\# az összes kivétel közül másodpercenként
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Webes szerepkörök esetében a rendszer az alábbi számlálókat is gyűjti:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

További egyéni vagy más Windows-teljesítményszámlálókat adhat meg *az ApplicationInsights.config* fájl szerkesztésével, [amint az ebben a példában látható.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)

  ![Teljesítményszámlálók](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Dolgozói szerepkörök korrelált telemetriai adatai
A gazdag diagnosztikai élmény, megtekintheti, hogy mi vezetett egy sikertelen vagy nagy késésű kérelem. Webes szerepkörök esetén az SDK automatikusan korrelációt állít be a kapcsolódó telemetriai adatok között. 

A dolgozói szerepkörök nézetének eléréséhez egyéni telemetriai inicializáló használatával az összes telemetriai Operation.Id közös környezetattribútumot állíthat be. Ezzel egy pillantással megtekintheti, hogy a késés vagy a hiba problémát függőség vagy a kód okozta.Doing you lets you view at a glance whether the latency or failure issue caused by a dependency or your code. 

Ezt a következőképpen teheti meg:

* Állítsa be a korrelációs azonosítót egy CallContext-be, [ahogy az ebben a példában látható.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36) Ebben az esetben a kérelemazonosítót használjuk korrelációs azonosítóként.
* Egyéni Telemetriai-inicializálási implementáció hozzáadása, a Operation.Id a korábban beállított korrelációsazonosítóhoz való beállítása. Például: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Adja hozzá az egyéni telemetriainicializálót. Ezt megteheti az *ApplicationInsights.config* fájlban vagy a [példában látható](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)kódban.

## <a name="client-telemetry"></a>Ügyfél-telemetria
A böngészőalapú telemetriai adatok, például az oldalmegtekintések száma, az oldalbetöltési idők vagy a parancsfájlkivételek lekért, valamint az egyéni telemetriai adatok nak a lapparancsfájljaiba való írásához olvassa el [a JavaScript SDK hozzáadása a weblapokhoz][client]című témakört.

## <a name="availability-tests"></a>Rendelkezésre állási tesztek
Annak érdekében, hogy az alkalmazás élő és válaszkész maradjon, állítsa be a [webes teszteket.][availability]

## <a name="display-everything-together"></a>Az összes elem együttes megjelenítése
A rendszer átfogó képéhez a legfontosabb figyelési diagramokat együtt jelenítheti meg egy [irányítópulton.](../../azure-monitor/app/overview-dashboard.md) Például hozzáadhatja az egyes szerepkörök kérés- és hibaszámait. 

Ha a rendszer más Azure-szolgáltatásokat használ, például a Stream Analytics szolgáltatást, adja meg a figyelési diagramokat is. 

Ha rendelkezik ügyfél-mobilalkalmazással, használja az [App Centert](../../azure-monitor/learn/mobile-center-quickstart.md). [Analytics](../../azure-monitor/app/analytics.md)-lekérdezések létrehozásával megjelenítheti az események számát, és rögzítheti őket az irányítópulton.

## <a name="example"></a>Példa
[Ez a példa](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) egy olyan szolgáltatást figyel, amely egy webes és két feldolgozói szerepkörrel rendelkezik.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Az Azure felhőszolgáltatásaiban futó "metódus nem található" kivétel
A .NET 4.6-os verziójára készítette el az alkalmazást? A .NET 4.6 nem támogatott automatikusan az Azure felhőszolgáltatási szerepköreiben. Az alkalmazás futtatása előtt [telepítse a .NET 4.6-os rendszert az egyes szerepkörekre.](../../cloud-services/cloud-services-dotnet-install-dotnet.md)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>További lépések
* [Azure Diagnostics-diagnosztikák Application Insightsba való küldésének konfigurálása](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Az Application Insights-erőforrások automatikus létrehozása](../../azure-monitor/app/powershell.md)
* [Az Azure-diagnosztika automatizálása](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
