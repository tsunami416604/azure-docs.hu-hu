---
title: Application Insights az Azure cloud services |} A Microsoft Docs
description: Webes és feldolgozói szerepkörök hatékony figyelése az Application Insightsszal
services: application-insights
documentationcenter: ''
keywords: WAD2AI, Azure Diagnostics
author: mrbullwinkle
manager: carmonm
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.workload: tbd
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: d27c0e9570959e01267d83a768ead45b48b7cea1
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267232"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights az Azure cloud services
[Az Application Insights] [ start] figyelheti [Azure felhőszolgáltatásbeli alkalmazások](https://azure.microsoft.com/services/cloud-services/) a rendelkezésre állási, teljesítmény, hibák és használati adatok az Application Insights SDK-k egyesül [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) a cloud Services szolgáltatások adatait. A széles körben elérhető módon működő alkalmazások teljesítményével és hatékonyságával kapcsolatos visszajelzések birtokában tájékozott döntéseket hozhat a fejlesztés irányát illetően az egyes fejlesztési fázisokban.

![Áttekintő irányítópult](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, lesz szüksége:

* Egy [Azure](https://azure.com) előfizetés. A Microsoft-fiókjával jelentkezzen be Windows, az Xbox Live és más Microsoft-felhőszolgáltatásokhoz. 
* A Microsoft Azure eszközök 2.9-es vagy újabb.
* Developer Analytics Tools 7.10-es vagy újabb.

## <a name="get-started-quickly"></a>Gyorsan használatba vehető
A felhőszolgáltatás Application Insightsszal való figyelésének leggyorsabb és legegyszerűbb módja, ha kiválasztja ezt a lehetőséget, amikor közzéteszi a szolgáltatást az Azure-ban.

![Diagnosztikai beállítások (példa)](./media/cloudservices/azure-cloud-application-insights.png)

Ez a beállítás futásidőben, így kell figyelnie a kérések, kivételek és függőségek a webes szerepkörben az összes telemetriát az alkalmazás instruments. A feldolgozói szerepkörökből származó teljesítményszámlálók is figyeli. Az Application Insights bármely az alkalmazás által létrehozott diagnosztikai nyomkövetéseket is kapnak.

Ha ezt a beállítást kell, akkor végzett. 

A következő lépések [az alkalmazás mérőszámainak megtekintése](../../azure-monitor/app/metrics-explorer.md), [az adatok lekérdezése az Analytics](../../azure-monitor/app/analytics.md), és talán állítson be egy [irányítópult](../../azure-monitor/app/app-insights-dashboards.md). 

A böngészőben teljesítményének monitorozásához is érdemes beállítása [rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) és [adja hozzá a kódot weboldalaihoz](../../azure-monitor/app/javascript.md).

A következő részekben bemutatjuk a következő további beállításokat:

* Adatok küldése a különböző összetevőket, és a különálló erőforrásokat konfigurációkat készíthet.
* Egyéni telemetriát adhat hozzá az alkalmazásból.

## <a name="sample-app-instrumented-with-application-insights"></a>Application Insights révén utasított mintaalkalmazás
A jelen [mintaalkalmazás](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), az Application Insights adnak hozzá egy felhőalapú szolgáltatás, az Azure-ban üzemeltetett két feldolgozói szerepkörrel. 

A következő szakaszban megismerheti, hogyan alakíthatja át hasonlóképp a saját felhőszolgáltatás-projekt megegyező módon.

## <a name="plan-resources-and-resource-groups"></a>Erőforrások és erőforráscsoportok tervezése
Az alkalmazásból származó telemetria tárolása, elemzése és Application Insights típusú Azure-erőforrásban jelennek meg. 

Mindegyik erőforrás egy erőforráscsoportba tartozik. Az erőforráscsoportokkal költségek, hozzáférés biztosítása a csapattagok számára, és egyetlen koordinált tranzakció-frissítések központi telepítésének kezeléséhez. Ha például sikerült [írható olyan szkript, üzembe helyezéséhez](../../azure-resource-manager/resource-group-template-deploy.md) egy Azure-felhőszolgáltatásban és a egy műveletet az erőforrások figyelése az Application Insights.

### <a name="resources-for-components"></a>Az összetevők erőforrásai
Azt javasoljuk, hogy az alkalmazás minden egyes összetevője külön erőforrás létrehozása. Azt jelenti hozzon létre egy erőforrás mindegyik webes és feldolgozói szerepkör. Mindegyik összetevő külön elemezhető, de létrehozhat egy [irányítópult](../../azure-monitor/app/app-insights-dashboards.md) , amely egyesíti a fő diagramok az összes összetevőből, így Ön figyelheti és összevetheti azokat együtt egyetlen nézetben. 

Egy alternatív módszer is a telemetria küldhető több szerepkörből ugyanarra az erőforrásra, de [adjon hozzá egy dimenzió tulajdonságot mindegyik telemetriaelemhez](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) , amely azonosítja annak forrásszerepkörét. Ez a megközelítés a mérőszám-diagramok, például a kivételeket, összesített mennyiségeit mutatják, a számát, a különféle szerepkörök, de a diagram szegmentáljon a szerepkör-azonosító, szükség szerint. A keresések ugyanezen dimenziók mentén is végezhet. A szerepkörök között is vezethet egyértelműek, de ez a megoldás könnyebb, aki mindent megtekinthet egy időben lesz.

A böngészőtelemetria általában ugyanabban az erőforrásban jelenik meg, mint a kiszolgálóoldali webes szerepköre.

Az Application Insights-erőforrások, a különböző összetevők helyezzen egy erőforráscsoportban. Ez a megközelítés egyszerűen kezelhetőek majd együtt. 

### <a name="separate-development-test-and-production"></a>A fejlesztési, tesztelési és éles környezetek elkülönítése
Ha már egy következő funkcióhoz fejleszt egyéni eseményeket, miközben a korábbi verzió még éles üzemben működik, érdemes lehet a fejlesztési telemetriát egy külön Application Insights-erőforrásba küldeni. Ellenkező esetben nehézkes lehet a teszt telemetriai adatok az élő webhelyről származó összes forgalom között található.

Ezen helyzet elkerülése érdekében hozzon létre külön erőforrásokat mindegyik buildkonfigurációhoz vagy "időbélyegző" (fejlesztői, teszt, éles és így tovább) a rendszer. Helyezze az egyes buildkonfigurációk erőforrásait külön erőforráscsoportokba. 

A telemetriai adatokat küld a megfelelő erőforrásokon, akkor állítsa be az Application Insights SDK úgy, hogy a különböző kialakítási kulcsokat, a build konfigurációjától függően buildkonfigurációtól. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Application Insights-erőforrás létrehozása mindegyik szerepkörhöz

Ha minden szerepkör egy külön erőforrás létrehozása döntött, és esetleg egy külön készletet az egyes, a legegyszerűbb minden hozni őket az Application Insights portálon. Ha sokat hoz létre erőforrásokat, [automatizálhatják a](../../azure-monitor/app/powershell.md).

1. Az a [az Azure portal][portal]válassza **új** > **fejlesztői szolgáltatások**  >   **Az Application Insights**.  

    ![Application Insights panel](./media/cloudservices/01-new.png)

1. Az a **alkalmazástípus** legördülő listában válassza **ASP.NET-alkalmazás**.  
    Mindegyik erőforrást egy kialakítási kulcs azonosítja. Előfordulhat, hogy ezt a kulcsot később szüksége Ha azt szeretné, manuálisan konfigurálja vagy ellenőrizze az SDK konfigurációját.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Azure Diagnostics beállítása az egyes szerepkörökhöz
Ezzel a beállítással figyelheti az alkalmazást az Application Insightsszal. Webes szerepkörök esetében ez a beállítás alkalmazásteljesítmény-figyelés, riasztások, diagnosztikai és használati elemzés biztosít. Más szerepkörök esetében, keresheti és figyelheti az Azure Diagnostics például újraindítás, a teljesítményszámlálókat és a System.Diagnostics.Trace meghívásait. 

1. A Visual Studio Megoldáskezelőjében alatt  **\<YourCloudService >** > **szerepkörök**, nyissa meg az egyes szerepkörök tulajdonságait.

1. A **konfigurációs**, jelölje be a **Posílat diagnostická data do Application Insights** jelölőnégyzetet, majd válassza ki a korábban létrehozott Application Insights-erőforrást.

Ha úgy döntött, hogy külön Application Insights-erőforrást használ mindegyik buildkonfigurációhoz, előbb válassza ki a konfigurációt.

![Az Application Insights konfigurálása](./media/cloudservices/configure-azure-diagnostics.png)

Ennek a hatása, az Application Insights-kialakítási kulcs beszúrása a fájlok nevű *ServiceConfiguration.\*. cscfg*. Íme a [mintakód](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Ha meg szeretné változtatni a az Application Insightsba küldött diagnosztikai információk szintjét, megteheti [szerkesztésével a *.cscfg* fájlok közvetlen](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="sdk"></a>Az SDK telepítése az egyes projektekben
Ezzel a beállítással bármely szerepkör egyéni üzleti telemetriát adhat hozzá. Lehetőséget biztosít a hogyan alkalmazását és teljesítményének szorosabb elemzése.

A Visual Studióban konfigurálja külön az Application Insights SDK-t az egyes felhőalkalmazás-projektekhez.

1. Konfigurálása **webes szerepkörök**, és kattintson a jobb gombbal a projektre, majd válassza ki **Application Insights konfigurálása** vagy **Hozzáadás > Application Insights telemetria**.

1. Konfigurálása **feldolgozói szerepkörök**: 

    a. Kattintson a jobb gombbal a projektre, és válassza **NuGet-csomagok kezelése**.

    b. Adja hozzá az [Application Insights a Windows-kiszolgálókon](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) modult.

    ![Az „Application Insights” kifejezés keresése](./media/cloudservices/04-ai-nuget.png)

1. Az SDK-val adatokat küldhet az Application Insights-erőforrás konfigurálása:

    a. Egy megfelelő indítási függvényben állítsa be a kialakítási kulcsot a konfigurációs beállításából származó a *.cscfg* fájlt:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Ismételje meg a "lépés a" minden szerepkör az alkalmazásban. Lásd az alábbi példákat:
   
    * [Webes szerepkör](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Feldolgozói szerepkör](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [A súgóablakban](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Állítsa be a *ApplicationInsights.config* fájlt mindig a kimeneti könyvtárba kell másolni.  
    Egy üzenet a *.config* fájlt arra kéri, hogy ott helyezze a kialakítási kulcsot. A felhőalapú alkalmazások esetén célszerű azonban jobb azt a a *.cscfg* fájlt. Ez a megközelítés biztosítja, hogy a szerepkör megfelelően legyen azonosítva a portálon.

#### <a name="run-and-publish-the-app"></a>Az alkalmazás futtatása és közzététele

1. Futtassa az alkalmazást, és jelentkezzen be az Azure-bA. 

1. Nyissa meg a létrehozott Application Insights-erőforrást.  
    Külön adatpontok jelennek meg a [keresési](../../azure-monitor/app/diagnostic-search.md), és összesített adatok megjelennek [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md). 

1. További telemetriai funkciók hozzáadása (lásd a következő szakaszban), majd tegye közzé az alkalmazást, hogy élő diagnosztikai és használati visszajelzéseket kaphat. 

Ha nem szerepel megjeleníthető adat, tegye a következőket:
1. Az egyes események megtekintéséhez nyissa meg a [keresési] [ diagnostic] csempére.
1. Az alkalmazásban nyissa meg különböző oldalakat, hogy létrejöjjön valamennyi telemetria.
1. Várjon néhány másodpercet, és kattintson a **frissítése**.  
    További információkért lásd: [hibaelhárítás][qna].

## <a name="view-azure-diagnostics-events"></a>Az Azure diagnosztikai események megtekintése
Annak a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) információk az Application insights szolgáltatásban a következő helyeken:

* A teljesítményszámlálók egyéni mérőszámokként jelennek meg. 
* A Windows eseménynaplók nyomkövetésekként és egyéni eseményekként jelennek meg.
* Az alkalmazásnaplók, ETW-naplók és egyéb diagnosztikai infrastruktúra-naplók nyomkövetésként jelennek meg.

Teljesítményszámlálók és az eseményszámok megtekintéséhez nyissa meg [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) , és adja hozzá a következő diagramon:

![Az Azure Diagnostics-adatok](./media/cloudservices/23-wad.png)

Az Azure Diagnostics által küldött különféle nyomkövetési naplókban kereshet, használja a [keresési](../../azure-monitor/app/diagnostic-search.md) vagy egy [elemzési lekérdezés](../../azure-monitor/log-query/get-started-portal.md). Tegyük fel például, amely egy szerepkör összeomlását és újrahasznosítását okozta nem kezelt kivétel. Ezek az információk a Windows eseménynaplójában, az Alkalmazás csatornában jelennek meg. Keresés használatával megtekintheti a Windows eseménynaplóban, és a kivétel teljes hívásláncát lekérése. Így segítséget a probléma okának.

![Azure Diagnostics-keresés](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>További telemetria
A következő szakaszok ismertetik, hogy további telemetriai adatokat a az alkalmazás különböző szempontjairól.

## <a name="track-requests-from-worker-roles"></a>Kérések nyomon követése a feldolgozói szerepkörökből
A webes szerepkörökben a kérések modulja automatikusan gyűjti a HTTP-kérésekkel kapcsolatos adatokat. Hogyan felülbírálhatja az alapértelmezett gyűjtési viselkedés példákért tekintse meg a [minta MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

A feldolgozói szerepkörökhöz intézett hívások teljesítményének rögzítése a HTTP-kérésekkel megegyező módon történő nyomkövetéssel hajtható végre. Az Application Insightsban a Kérés telemetriatípus a megnevezett kiszolgálóoldali műveletek egységeit méri, amelyeknek mérhető az idejük, és külön-külön sikerülhetnek vagy meghiúsulhatnak. Bár a HTTP-kéréseket az SDK által automatikusan rögzített, szúrhat be a saját kód kérelmek nyomon követése a feldolgozói szerepkörökhöz.

Tekintse meg a kérések jelentésére két minta feldolgozói szerepkört: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Kivételek
Nem kezelt kivételek gyűjtését különböző típusú webes alkalmazások kapcsolatos információkért lásd: [kivételeket az Application Insights figyelési](../../azure-monitor/app/asp-net-exceptions.md).

A minta webes szerepkör MVC5 és Web API 2 vezérlőkkel rendelkezik. A két vezérlőtől származó nem kezelt kivételeket a rendszer a következő kezelőkkel rögzíti:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) megfelelő az MVC5 tartományvezérlők [ebben a példában látható módon](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) Web API 2 vezérlő beállítva [ebben a példában látható módon](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

A feldolgozói szerepkörök esetében kétféle módon kivételek követheti nyomon:

* TrackException(ex) használja.
* Ha hozzáadta az Application Insights nyomkövetés-figyelő NuGet-csomagot, használhatja a kivételek naplózásához System.Diagnostics.Trace [ebben a példában látható módon](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Teljesítményszámlálók
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

Szerkesztésével megadhat további egyéni vagy más Windows-teljesítményszámlálók *ApplicationInsights.config* [ebben a példában látható módon](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Teljesítményszámlálók](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Feldolgozói szerepkörök korrelált telemetriája
Részletes diagnosztika élményt megtekintheti, mi vezetett vagy késéséhez kérelemre. A webes szerepkörök esetében az SDK automatikusan beállítja a korrelációt a vonatkozó telemetriával. 

Ebben a nézetben, a feldolgozói szerepkörök eléréséhez egy egyéni telemetriainicializáló segítségével állítsa be egy közös Operation.Id környezeti attribútumot az összes telemetriai adat. Ez lehetővé teszi egyetlen pillantással megtekintheti, hogy az a késleltetés vagy hiba problémát egy függőség vagy a kód okozta. 

Ezt a következőképpen teheti meg:

* Állítsa be a correlationId azonosítót egy callContext [ebben a példában látható módon](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Ebben az esetben használjuk a Kérelemazonosító a correlationId azonosítót.
* Adja hozzá a TelemetryInitializer egyéni implementációját az Operation.Id állítsa a korrelációs azonosító, amelyet korábban. Egy vonatkozó példáért lásd: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Adja hozzá az egyéni telemetriainicializálót. Ezért ezt a *ApplicationInsights.config* fájl vagy a kódban [ebben a példában látható módon](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Ügyfél-telemetria
Böngészőalapú telemetriát gyűjthet, például az oldal nézet számát, lapbetöltési idők és szkriptkivételeket, valamint egyéni telemetriát írhat a lapok szkriptjeibe, lásd: [adja hozzá a JavaScript SDK-t a weboldalaihoz][client].

## <a name="availability-tests"></a>Rendelkezésre állási tesztek
Győződjön meg arról, hogy az alkalmazás mindig elérhető és válaszkész legyen, hogy [Webtesztekkel][availability].

## <a name="display-everything-together"></a>Az összes elem együttes megjelenítése
Az átfogó képet, a rendszer, megjelenítheti a figyelési diagramokat összegyűjtheti egy kulcs [irányítópult](../../azure-monitor/app/app-insights-dashboards.md). Például hozzáadhatja az egyes szerepkörök kérés- és hibaszámait. 

Ha a rendszer más Azure-szolgáltatásokkal, például a Stream Analytics, a figyelési diagramjait is tartalmazzák. 

Ha rendelkezik ügyfél-mobilalkalmazással, használja az [App Centert](../../azure-monitor/learn/mobile-center-quickstart.md). [Analytics](../../azure-monitor/app/analytics.md)-lekérdezések létrehozásával megjelenítheti az események számát, és rögzítheti őket az irányítópulton.

## <a name="example"></a>Példa
[Ez a példa](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) egy olyan szolgáltatást figyel, amely egy webes és két feldolgozói szerepkörrel rendelkezik.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>"Metódus nem található" kivétel az Azure cloud servicesben futó
A .NET 4.6-os verziójára készítette el az alkalmazást? .NET 4.6 automatikusan nem támogatott az Azure cloud services-szerepkörök. [Telepítse a .NET 4.6-minden szerepkör](../../cloud-services/cloud-services-dotnet-install-dotnet.md) az alkalmazás futtatása előtt.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>További lépések
* [Azure Diagnostics-diagnosztikák Application Insightsba való küldésének konfigurálása](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Application Insights-erőforrások automatikus létrehozása](../../azure-monitor/app/powershell.md)
* [Az Azure Diagnostics automatizálása](../../azure-monitor/app/powershell-azure-diagnostics.md)
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
