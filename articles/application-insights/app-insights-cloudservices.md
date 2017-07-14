---
title: Application Insights az Azure Cloud Servicesben | Microsoft Docs
description: "Webes és feldolgozói szerepkörök hatékony figyelése az Application Insightsszal"
services: application-insights
documentationcenter: 
keywords: WAD2AI, Azure Diagnostics
author: CFreemanwa
manager: carmonm
editor: alancameronwills
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.devlang: na
ms.tgt_pltfrm: ibiza
ms.topic: get-started-article
ms.workload: tbd
ms.date: 05/05/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: a5e5cc37c4635b78279a5e240603b6a728922eb8
ms.contentlocale: hu-hu
ms.lasthandoff: 05/31/2017


---
# Application Insights az Azure Cloud Servicesben
<a id="application-insights-for-azure-cloud-services" class="xliff"></a>
Az [Application Insightsszal][start]monitorozható a [Microsoft Azure felhőszolgáltatásbeli alkalmazások](https://azure.microsoft.com/services/cloud-services/) rendelkezésre állása, teljesítménye, hibái és használata az Application Insights SDK-iból származó adatok és a felhőszolgáltatások [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics)-adatainak kombinálása révén. A széles körben elérhető módon működő alkalmazások teljesítményével és hatékonyságával kapcsolatos visszajelzések birtokában tájékozott döntéseket hozhat a fejlesztés irányát illetően az egyes fejlesztési fázisokban.

![Példa](./media/app-insights-cloudservices/sample.png)

## Előkészületek
<a id="before-you-start" class="xliff"></a>
A következők szükségesek:

* Egy [Microsoft Azure](http://azure.com)-előfizetés. Jelentkezzen be egy Microsoft-fiókkal – ez tartozhat a Windowshoz, az XBox Live-hoz vagy egyéb Microsoft felhőszolgáltatásokhoz. 
* A Microsoft Azure eszközök 2.9-es vagy újabb verziója
* A Developer Analytics Tools 7.10-es vagy újabb verziója

## Első lépések
<a id="quick-start" class="xliff"></a>
A felhőszolgáltatás Application Insightsszal való figyelésének leggyorsabb és legegyszerűbb módja, ha kiválasztja ezt a lehetőséget, amikor közzéteszi a szolgáltatást az Azure-ban.

![Példa](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

Ez a beállítás futásidőben utasítja az alkalmazást, és biztosítja az összes szükséges telemetriát, amely a kérések, kivételek és függőségek figyeléséhez szükséges a webes szerepkörében, valamint a feldolgozói szerepkörökből származó teljesítményszámlálók figyeléséhez. A rendszer az alkalmazás által létrehozott diagnosztikai nyomkövetéseket is továbbítja az Application Insightsnak.

Ha csak ennyire van szüksége, már kész is van! A következő lépések [az alkalmazás mérőszámainak megtekintése](app-insights-metrics-explorer.md), [az adatok lekérdezése az Analytics használatával](app-insights-analytics.md), valamint esetleg egy [irányítópult](app-insights-dashboards.md) beállítása. Esetleg érdemes lehet [rendelkezésre állási teszteket](app-insights-monitor-web-app-availability.md) beállítania, és [kódot hozzáadnia a weboldalakhoz](app-insights-javascript.md) a teljesítmény figyeléséhez a böngészőben.

Azonban további lehetőségeket is elérhet:

* Különféle összetevőkből küldhet adatokat, és konfigurációkat készíthet az erőforrások szétválasztásához.
* Egyéni telemetriát adhat hozzá az alkalmazásból.

Ha ezek a lehetőségek érdekesek lehetnek az Ön számára, olvasson tovább.

## Az Application Insights révén utasított Alkalmazás minta
<a id="sample-application-instrumented-with-application-insights" class="xliff"></a>
Tekintse meg ezt a [mintaalkalmazást](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), amelyben az Application Insights egy, az Azure-ban futó felhőszolgáltatáshoz lett hozzáadva két feldolgozói szerepkörrel. 

Az alábbiak bemutatják, hogyan alakíthatja át hasonlóképp a saját felhőszolgáltatás-projektjét.

## Erőforrások és erőforráscsoportok tervezése
<a id="plan-resources-and-resource-groups" class="xliff"></a>
Az alkalmazásból származó telemetria tárolása, elemzése és megjelenítése az Application Insights típusú Azure-erőforrásban valósul meg. 

Mindegyik erőforrás egy erőforráscsoportba tartozik. Az erőforráscsoportok segítségével a költségek kezelése, a csapattagok hozzáférési jogosultságainak kiosztása vagy a frissítések telepítése egyetlen koordinált tranzakció keretében hajtható végre. Például [írható olyan szkript, amellyel egyetlen tevékenység keretében helyezhető üzembe](../azure-resource-manager/resource-group-template-deploy.md) egy Azure Cloud Services felhőszolgáltatás és az azt figyelő Application Insights-erőforrások.

### Az összetevők erőforrásai
<a id="resources-for-components" class="xliff"></a>
Az ajánlott séma egy külön erőforrás létrehozása az alkalmazás mindegyik összetevőjéhez – azaz mindegyik webes és feldolgozói szerepkörhöz. Mindegyik összetevő külön elemezhető, de létrehozható egy [irányítópult](app-insights-dashboards.md) is, amellyel a fő diagramok az összes összetevőből egy felületre hozhatóak, így együtt figyelheti és összevetheti azokat. 

Egy másik séma szerint a telemetria küldhető több szerepkörből is ugyanabba az erőforrásba, azonban [hozzá kell adni egy dimenzió tulajdonságot mindegyik telemetriaelemhez](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer), amely azonosítja annak forrásszerepkörét. E szerint a séma szerint a mérőszám-diagramok, például a kivételeké, normál esetben a különféle szerepkörök összesített mennyiségeit mutatják, azonban a diagram igény szerint szegmentálható a szerepkör-azonosítók alapján. A keresések ugyanezen dimenziók mentén szűrhetőek. Ebben a változatban valamennyivel könnyebb minden egyszerre áttekinteni, azonban a szerepkörök szintjén adódhatnak kevéssé egyértelmű helyzetek.

A böngészőtelemetria általában ugyanabban az erőforrásban jelenik meg, mint a kiszolgálóoldali webes szerepköre.

Foglalja a különféle összetevők Application Insights-erőforrásait egyetlen erőforráscsoportba. Így könnyebben kezelhetőek majd együtt. 

### A fejlesztési, tesztelési és éles környezetek elkülönítése
<a id="separating-development-test-and-production" class="xliff"></a>
Ha már egy következő funkcióhoz fejleszt egyéni eseményeket, miközben a korábbi verzió még éles üzemben működik, érdemes lehet a fejlesztési telemetriát egy külön Application Insights-erőforrásba küldeni. Máskülönben nehéz lesz megtalálni a teszttelemetriát az élő oldal által beküldött hatalmas adatmennyiségben.

Az ilyen helyzetek elkerülése érdekében hozzon létre külön erőforrásokat mindegyik buildkonfigurációhoz vagy „bélyeghez” (fejlesztés, teszt, éles ...) a rendszerben. Helyezze az egyes buildkonfigurációk erőforrásait külön erőforráscsoportokba. 

Annak érdekében, hogy a telemetria a megfelelő erőforrásokba érkezzen, beállíthatja, hogy az Application Insights SDK különböző kialakítási kulcsokat használjon a buildkonfigurációtól függően. 

## Application Insights-erőforrás létrehozása mindegyik szerepkörhöz
<a id="create-an-application-insights-resource-for-each-role" class="xliff"></a>
Ha úgy döntött, hogy külön erőforrást hoz létre mindegyik szerepkörhöz – és esetleg egy külön készletet az egyes buildkonfigurációkhoz is –, a legegyszerűbb, ha mindegyiket az Application Insights portálon hozza létre. (Ha sokszor hoz létre erőforrásokat, [automatizálhatja a folyamatot](app-insights-powershell.md).)

1. Hozzon létre egy új Application Insights-erőforrást az [Azure Portalon][portal]. Az alkalmazás típusánál válassza az ASP.NET alkalmazás lehetőséget. 

    ![Kattintson az Új, majd az Application Insights lehetőségre](./media/app-insights-cloudservices/01-new.png)
2. Fontos észrevenni, hogy mindegyik erőforrást egy kialakítási kulcs azonosítja. Erre később még szükség lehet majd, ha manuálisan szeretné konfigurálni vagy jóváhagyni az SDK konfigurációját.

    ![Kattintson a Tulajdonságok elemre, válassza ki a kulcsot, és nyomja le a ctrl+C billentyűkombinációt.](./media/app-insights-cloudservices/02-props.png) 

## Azure Diagnostics beállítása az egyes szerepkörökhöz
<a id="set-up-azure-diagnostics-for-each-role" class="xliff"></a>
Ezzel a beállítással figyelheti az alkalmazást az Application Insightsszal. Webes szerepkörök esetében ez biztosítja a teljesítményfigyelést, a riasztásokat és a diagnosztikát, valamint a használat elemzését. Más szerepkörök esetében keresheti és figyelheti az Azure diagnosztikai eredményeit, például az újraindításokat, a teljesítményszámlálókat és a System.Diagnostics.Trace meghívásait. 

1. A Visual Studio Solution Explorerben (Megoldáskezelő) a &lt;YourCloudService&gt;, Roles (Szerepkörök) területen nyissa meg az egyes szerepkörök tulajdonságait.
2. A **Configuration** (Konfiguráció) területen válassza a **Send diagnostics data to Application Insights** (Diagnosztikai adatok küldése az Application Insightsba) beállítást, és válassza ki a korábban létrehozott megfelelő Application Insights-erőforrást.

Ha úgy döntött, hogy külön Application Insights-erőforrást használ mindegyik buildkonfigurációhoz, előbb válassza ki a konfigurációt.

![Az egyes Azure-szerepkörök tulajdonságaiban konfigurálja az Application Insightsot.](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

Ennek eredményeképp az Application Insights kialakítási kulcsai be lesznek szúrva a(z) `ServiceConfiguration.*.cscfg` nevű fájlokba. ([Mintakód](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).

Ha meg szeretné változtatni az Application Insightsba küldött diagnosztikai információk szintjét, ezt [a `.cscfg` fájlok közvetlen szerkesztésével](app-insights-azure-diagnostics.md) teheti meg.

## <a name="sdk"></a>Az SDK telepítése az egyes projektekben
Ezzel a beállítással megnyílik a lehetőség, hogy egyéni üzleti telemetriát vegyen fel bármely szerepkörbe az alkalmazás használatának és teljesítményének szorosabb elemzése céljából.

A Visual Studióban konfigurálja külön az Application Insights SDK-t az egyes felhőalkalmazás-projektekhez.

1. **Webes szerepkörök**: Kattintson jobb gombbal a projektre, és válassza a **Configure Application Insights** (Application Insights konfigurálása) vagy a **Add > Application Insights telemetry** (Hozzáadás > Application Insights Telemetria) lehetőséget.

2. **Feldolgozói szerepkörök**: 
 * Kattintson a jobb gombbal a projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
 * Adja hozzá az [Application Insights a Windows-kiszolgálókon](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) modult.

    ![Az „Application Insights” kifejezés keresése](./media/app-insights-cloudservices/04-ai-nuget.png)

3. Állítsa be az SDK konfigurációját, hogy adatokat küldjön az Application Insights-erőforrásba.

    Egy megfelelő indítási függvényben állítsa be a .cscfg fájl konfigurációs beállításából származó kialakítási kulcsot:
 
    ```C#
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    Ezt tegye meg az alkalmazás minden szerepköre esetében. Lásd az alábbi példákat:
   
   * [Webes szerepkör](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [Feldolgozói szerepkör](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [Weblapok esetében](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. Állítsa be, hogy a rendszer az ApplicationInsights.config fájlt mindig a kimeneti könyvtárba másolja. 
   
    (A .config fájlban lévő üzenetek azt tanácsolják majd, hogy ide helyezze a kialakítási kulcsot. A felhőalkalmazások esetében azonban jobb azt a .cscfg fájlból megadni. Ezzel biztosítható, hogy a szerepkör megfelelően legyen azonosítva a portálon.)

#### Az alkalmazás futtatása és közzététele
<a id="run-and-publish-the-app" class="xliff"></a>
Futtassa az alkalmazást, és jelentkezzen be az Azure-ba. Nyissa meg a létrehozott Application Insights-erőforrásokat, és külön adatpontok jelennek meg a [Keresésben](app-insights-diagnostic-search.md), valamint összesített adatok a [Metrikaböngészőben](app-insights-metrics-explorer.md). 

Adjon hozzá további telemetriát (lásd az alábbi szakaszokat), majd tegye közzé az alkalmazást, hogy élő diagnosztikai adatokat és használati visszajelzéseket kapjon. 

#### Nincs adat?
<a id="no-data" class="xliff"></a>
* Az egyes események megtekintéséhez nyissa meg a [Keresés][diagnostic] csempét.
* Az alkalmazás segítségével nyisson meg különböző oldalakat, hogy létrejöjjön némi telemetria.
* Várjon néhány másodpercet, és kattintson a Frissítés lehetőségre.
* Lásd: [Hibaelhárítás][qna].

## Azure diagnosztikai események megtekintése
<a id="view-azure-diagnostic-events" class="xliff"></a>
Az [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) információit itt találja az Application Insightsban:

* A teljesítményszámlálók egyéni mérőszámokként jelennek meg. 
* A Windows eseménynaplók nyomkövetésekként és egyéni eseményekként jelennek meg.
* Az alkalmazásnaplók, ETW-naplók és egyéb diagnosztikai infrastruktúra-naplók nyomkövetésként jelennek meg.

A teljesítményszámlálók és az eseményszámok megtekintéséhez nyissa meg a [Metrikaböngészőt](app-insights-metrics-explorer.md), és vegyen fel egy új diagramot:

![Az Azure diagnosztikai adatai](./media/app-insights-cloudservices/23-wad.png)

A [Keresés](app-insights-diagnostic-search.md) vagy egy [Analytics-lekérdezés](app-insights-analytics-tour.md) használatával kereshet az Azure Diagnostics által küldött különféle nyomkövetési naplókban. Például tételezzük fel, hogy egy nem kezelt kivétellel rendelkezik, amely egy szerepkör összeomlását és újrahasznosítását okozta. Ezek az információk a Windows eseménynaplójában, az Alkalmazás csatornában jelennek meg. A Keresés használatával megtekintheti a megfelelő hibabejegyzést a Windows eseménynaplójában, és lekérheti a kivétel teljes hívásláncát. Ennek segítségével pedig megtalálhatja a probléma alapvető okát.

![Azure Diagnostics-keresés](./media/app-insights-cloudservices/25-wad.png)

## További telemetria
<a id="more-telemetry" class="xliff"></a>
Az alábbi szakaszokban bemutatjuk, hogyan gyűjthető további telemetria az alkalmazás különféle rétegeiből.

## Kérések nyomon követése a feldolgozói szerepkörökből
<a id="track-requests-from-worker-roles" class="xliff"></a>
A webes szerepkörökben a kérések modulja automatikusan gyűjti a HTTP-kérésekkel kapcsolatos adatokat. Az alapértelmezett gyűjtési viselkedés felülírásával kapcsolatban lásd: [minta MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

A feldolgozói szerepkörökhöz intézett hívások teljesítményének rögzítése a HTTP-kérésekkel megegyező módon történő nyomkövetéssel hajtható végre. Az Application Insightsban a Kérés telemetriatípus a megnevezett kiszolgálóoldali műveletek egységeit méri, amelyeknek mérhető az idejük, és külön-külön sikerülhetnek vagy meghiúsulhatnak. Az SDK automatikusan rögzíti a HTTP-kéréseket, de saját kód beszúrásával a feldolgozói szerepkörökhöz intézett hívásokat is nyomon követheti.

Lásd a kérések jelentésére kialakított két minta feldolgozói szerepkört: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) és [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## Kivételek
<a id="exceptions" class="xliff"></a>
A nem kezelt kivételek a különféle webalkalmazás-típusokból való gyűjtésével kapcsolatban lásd: [Kivételek figyelése az Application Insightsban](app-insights-asp-net-exceptions.md).

A minta webes szerepkör MVC5 és Web API 2 vezérlőkkel rendelkezik. A két vezérlőtől származó nem kezelt kivételeket a rendszer a következő kezelőkkel rögzíti:

* Az MVC5-vezérlőkhöz az [itt](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) beállított [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs)
* A Web API 2-vezérlőkhöz az [itt](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) beállított [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs)

A feldolgozói szerepkörök esetében a kivételek kétféleképp követhetők nyomon:

* TrackException(ex)
* Ha hozzáadta az Application Insights nyomkövetés-figyelő NuGet-csomagot, használhatja a **System.Diagnostics.Trace** parancsot a kivételek naplózásához. [Mintakód.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## Teljesítményszámlálók
<a id="performance-counters" class="xliff"></a>
A rendszer alapértelmezés szerint az alábbi számlálókat gyűjti:

    * \Process(??APP_WIN32_PROC??)\% A processzor kihasználtsága
    * \Memory\Available Bytes
    * \.NET CLR-kivételek (??APP_CLR_PROC??)\# az összes kivétel közül másodpercenként
    * \Process(??APP_WIN32_PROC??)\Private Bytes
    * \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
    * \Processor(_Total)\% Processor Time

Webes szerepkörök esetében a rendszer az alábbi számlálókat is gyűjti:

    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Megadhat további egyéni vagy egyéb Windows-teljesítményszámlálókat is az ApplicationInsights.config szerkesztésével, [ahogy ebben a példában is](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Teljesítményszámlálók](./media/app-insights-cloudservices/OLfMo2f.png)

## A feldolgozói szerepkörök korrelált telemetriája
<a id="correlated-telemetry-for-worker-roles" class="xliff"></a>
Ez részletes diagnosztikai információt biztosít, mivel láthatja, mi vezetett egy kérés hibájához vagy késéséhez. A webes szerepkörök esetében az SDK automatikusan beállítja a korrelációt a vonatkozó telemetriával. A feldolgozói szerepkörök esetében az egyéni telemetriainicializáló használatával megadhat egy közös Operation.Id környezeti attribútumot az összes telemetriához ennek érdekében. Így egy pillantásra láthatja, hogy a késési/meghibásodási problémát egy függőség vagy a kód okozza. 

Ezt a következőképpen teheti meg:

* Állítsa a korrelációs azonosítót egy CallContext környezetre, ahogy [itt](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36) látható. Ebben az esetben a kérés azonosítóját használjuk korrelációs azonosítóként.
* Adja hozzá a TelemetryInitializer egyéni implementációját az Operation.Id a fent megadott korrelációs azonosítóra történő beállításához. Íme egy példa: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Adja hozzá az egyéni telemetriainicializálót. Ezt az ApplicationInsights.config fájlban teheti meg, vagy kódszinten az [itt](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233) látható módon.

Ennyi az egész! A portál felülete már eleve úgy van kialakítva, hogy az összes kapcsolódó telemetria egy pillantással áttekinthető legyen:

![Korrelált telemetria](./media/app-insights-cloudservices/bHxuUhd.png)

## Ügyfél-telemetria
<a id="client-telemetry" class="xliff"></a>
[A JavaScript SDK-t a weboldalaihoz adva][client] böngészőalapú telemetriát gyűjthet, például a lapmegtekintések számát, lapbetöltési időket és szkriptkivételeket, valamint egyéni telemetriát írhat a lapok szkriptjeibe.

## Rendelkezésre állási tesztek
<a id="availability-tests" class="xliff"></a>
[Beállíthat webes teszteket][availability] annak biztosításához, hogy az alkalmazás mindig elérhető és válaszkész legyen.

## Az összes elem együttes megjelenítése
<a id="display-everything-together" class="xliff"></a>
Annak érdekében, hogy átfogó képet kaphasson a rendszerről, a főbb figyelési diagramokat összegyűjtheti egy [irányítópultra](app-insights-dashboards.md). Például hozzáadhatja az egyes szerepkörök kérés- és hibaszámait. 

Ha a rendszer egyéb Azure-szolgáltatásokat (például Stream Analytics) is tartalmaz, ezeknek a figyelési diagramjait is beillesztheti. 

Ha rendelkezik ügyfél-mobilalkalmazással, a megfelelő kód beszúrásával egyéni eseményeket küldhet a fő felhasználói műveletekre vonatkozóan, és létrehozhat egy [HockeyApp-hidat](app-insights-hockeyapp-bridge-app.md). [Analytics](app-insights-analytics.md)-lekérdezések létrehozásával megjelenítheti az események számát, és rögzítheti őket az irányítópulton.

## Példa
<a id="example" class="xliff"></a>
[Ez a példa](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) egy olyan szolgáltatást figyel, amely egy webes és két feldolgozói szerepkörrel rendelkezik.

## „A metódus nem található” kivétel az Azure Cloud Servicesben futó rendszeren
<a id="exception-method-not-found-on-running-in-azure-cloud-services" class="xliff"></a>
A .NET 4.6-os verziójára készítette el az alkalmazást? Az Azure Cloud Services szerepkörei nem támogatják automatikusan a 4.6-os verziót. [Telepítse a 4.6-os verziót mindegyik szerepkörön](../cloud-services/cloud-services-dotnet-install-dotnet.md), mielőtt futtatná az alkalmazást.

## Videó
<a id="video" class="xliff"></a>

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## Következő lépések
<a id="next-steps" class="xliff"></a>
* [Azure Diagnostics-diagnosztikák Application Insightsba való küldésének konfigurálása](app-insights-azure-diagnostics.md)
* [Application Insights-erőforrások létrehozásának automatizálása](app-insights-powershell.md)
* [Az Azure Diagnostics-diagnosztikák automatizálása](app-insights-powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 

