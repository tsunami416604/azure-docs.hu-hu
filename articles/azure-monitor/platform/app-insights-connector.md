---
title: Az Azure Application Insights-alkalmazások adatainak megtekintése |} A Microsoft Docs
description: Az Application Insights-összekötő megoldás segítségével diagnosztizálhatja a teljesítménybeli problémákat, és megismerheti, mit a felhasználók az alkalmazását, amikor az Application insights segítségével figyeli.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665153"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights Connector felügyeleti megoldás (elavult)

![Application Insights szimbólum](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Az [erőforrások közötti lekérdezések](../../azure-monitor/log-query/cross-workspace-query.md)támogatásával a Application Insights Connector felügyeleti megoldás már nem szükséges. Az Azure piactéren elavult és eltávolított, valamint a OMS-portálon, amely az Azure-beli kereskedelmi felhő esetében hivatalosan elavult, 2019. 2019. március 30-án megszűnik az Azure US government Cloud.
>
>A meglévő kapcsolatok 2019 június 30-ig továbbra is működni fognak.  A OMS-portál elavult használata esetén nincs lehetőség a meglévő kapcsolatok konfigurálására és eltávolítására a portálon. A meglévő kapcsolatok eltávolításához lásd: [az összekötő eltávolítása](#removing-the-connector-with-powershell) az alábbi PowerShell-lel a PowerShell használatával.
>
>Több alkalmazás naplózási információinak lekérdezéséhez Application Insights tekintse meg a [több Azure Monitor Application Insights erőforrás egységesítését](../log-query/unify-app-resource-data.md)ismertető témakört. További információ a OMS-portál érvénytelenítéséről: a [OMS-portál áthelyezése az Azure](../../azure-monitor/platform/oms-portal-transition.md)-ba.
>
> 

Az Applications-alapú adatelemzési összekötő megoldásával diagnosztizálhatja a teljesítménnyel kapcsolatos problémákat, és megismerheti, hogy a felhasználók milyen műveleteket végeznek az alkalmazással, ha [Application Insights](../../azure-monitor/app/app-insights-overview.md)figyelik. Az ugyanazon alkalmazás telemetriát az Application Insightsban a fejlesztőknek nézet áll rendelkezésre a Log Analyticsben. Azonban az Application Insights-alkalmazások integrálása a Log Analytics-szel, az alkalmazások láthatóságát emelkedett üzemeltetési és alkalmazásadatokat létesíteni egy helyen. Az azonos nézetek kellene megismerheti, hogyan működhet együtt az alkalmazásfejlesztőknek. Az Általános nézetek segítségével csökkentheti az időt, és oldja meg az alkalmazás és a platformmal.

A megoldás használata esetén is:

- Megtekintése az Application Insights-alkalmazások egy helyen tudhatja, akkor is, ha azokat az Azure-előfizetések
- Az alkalmazásadatok infrastruktúra adatait
- A naplókeresésben szolgáltatva az alkalmazások adatainak megjelenítéséhez
- Forgáspont a Log Analytics-adatok az Application Insights alkalmazáshoz az Azure Portalon


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Összekapcsolt források

Ellentétben a legtöbb más Log Analytics-megoldások adatok nem lesznek gyűjtve az Application Insights-összekötő az ügynökök által. A megoldás által használt összes adat közvetlenül az Azure-ból származik.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](../../azure-monitor/platform/agent-windows.md) | Nem | Windows-ügynököktől a megoldás nem gyűjt adatokat. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás a Linux-ügynökök nem gyűjt adatokat. |
| [SCOM felügyeleti csoport](../../azure-monitor/platform/om-agents.md) | Nem | A megoldás az ügynökök a csatlakoztatott SCOM felügyeleti csoport nem gyűjt adatokat. |
| [Azure Storage-fiók](collect-azure-metrics-logs.md) | Nem | A megoldás nem az Azure storage-ból nem gyűjteményadatokat. |

## <a name="prerequisites"></a>Előfeltételek

- Application Insights-összekötő adatainak eléréséhez Azure-előfizetéssel kell rendelkeznie
- Rendelkeznie kell legalább egy beállított Application Insights-erőforrást.
- A tulajdonos vagy közreműködő szerepkörrel az Application Insights-erőforrást kell lennie.

## <a name="configuration"></a>Konfiguráció

1. Engedélyezze a Azure Web Apps Analytics megoldást az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) -en, vagy használja az [Solutions Gallery log Analytics-megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című témakörben leírt eljárást.
2. Keresse fel az [Azure Portalt](https://portal.azure.com). Application Insights megnyitásához válassza **az összes szolgáltatás** elemet. Ezután keresse meg az Application Insights. 
3. Az **előfizetések**területen válasszon ki egy Application Insights erőforrásokkal rendelkező előfizetést, majd a **név**területen válasszon ki egy vagy több alkalmazást.
4. Kattintson a **Save** (Mentés) gombra.

Körülbelül 30 percet, az adatok elérhetővé válnak, és az Application Insights csempe frissül, az adatok, például a következő képen:

![Az Application Insights-csempe](./media/app-insights-connector/app-insights-tile.png)

Egyéb szempontok figyelembe kell venni:

- Application Insights-alkalmazások csak kapcsolat egy Log Analytics-munkaterülethez.
- Log Analyticshoz csak az [alapszintű vagy a Nagyvállalati Application Insights erőforrásokat](https://azure.microsoft.com/pricing/details/application-insights) lehet összekapcsolni. A Log Analytics ingyenes szintjét is használhatja.

## <a name="management-packs"></a>Felügyeleti csomagok

Ez a megoldás nem telepíti a csatlakoztatott felügyeleti csoportok minden olyan felügyeleti csomagot.

## <a name="use-the-solution"></a>A megoldás használatához

A következő szakaszok ismertetik, hogyan használhatja a paneleket, látható az Application Insights irányítópult megtekintéséhez és az alkalmazásokból származó adatok kezeléséhez.

### <a name="view-application-insights-connector-information"></a>Application Insights-összekötő adatainak megtekintése

A **Application Insights** csempére kattintva nyissa meg a **Application Insights** irányítópultot, és tekintse meg a következő pengéket.

![Application Insights irányítópult](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights irányítópult](./media/app-insights-connector/app-insights-dash02.png)

Az irányítópult tartalmaz a paneleket a táblázatban látható. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. Futtathat egy olyan naplóbeli keresést, amely az összes rekordot visszaadja, amikor az **összes** megjelenítése gombra kattint a panel alján, vagy amikor rákattint a panel fejlécére.


| **Oszlop** | **Leírás** |
| --- | --- |
| Alkalmazások – alkalmazások száma | Alkalmazás-erőforrásokat az alkalmazások számát jeleníti meg. Felsorolja az alkalmazásnevek és az egyes alkalmazás-rekordok számát. Kattintson a számra a <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> naplózásának futtatásához <br><br>  Kattintson az alkalmazás nevét, amely megjeleníti a gazdagép, valamint a rekordok a telemetriai adatok típusa szerint és minden adat (az elmúlt nap alapján) típus szerint alkalmazás bejegyzéseket Naplókeresés futtatásához. |
| Adatmennyiség – adatokat továbbító gazdagépek | Adatokat küldő számítógép-állomások számát jeleníti meg. Számítógép-gazdagépek és -rekordok száma minden állomás számára is megjeleníti. Kattintson a számra a <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> naplózásának futtatásához <br><br> Kattintson a számítógép nevét, a gazdagép, amely megjeleníti a gazdagép, valamint a rekordok a telemetriai adatok típusa szerint és minden adat (az elmúlt nap alapján) típus szerint alkalmazásrekordok Naplókeresés futtatásához. |
| Rendelkezésre állás – Webtesztek eredményei | A webes teszt eredményei, pass vagy sikertelen jelző perecdiagram mutatja. Kattintson a diagramra, és futtassa a naplóbeli keresést <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Eredmények megjelenítése a műveletek és az összes teszt fellépő hibák száma. Azt mutatja be minden Web Apps, a forgalom az elmúlt percben. Kattintson az alkalmazás nevét, egy naplóbeli keresés sikertelen webes tesztek részleteit megjelenítő megtekintéséhez. |
| Kiszolgálói kérelmek – kérelmek száma óránként | A különböző alkalmazások az óránként kiszolgálókérelmek egy vonaldiagram látható. A 3 leggyakoribb alkalmazások pont kérések fogadása szerinti megtekintéséhez a diagramban a sor fölé. A fogadása a kérelmek és a kérések száma a kijelölt időszak alkalmazások listáját is tartalmazza. <br><br>Kattintson a gráfra egy olyan <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> naplójának kereséséhez, amely a különböző alkalmazások óránkénti kiszolgálói kéréseinek részletesebb táblázatát jeleníti meg. <br><br> Kattintson egy alkalmazásra a listában egy olyan <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> naplójának kereséséhez, amely megjeleníti a kérelmek listáját, a kérelmekre vonatkozó diagramokat, valamint a kérelmek időtartamát és a kérelmekre adott válaszok listáját.   |
| Hiba – a sikertelen kérelmek száma óránként | Alkalmazás sikertelen kérelmek száma óránként egy vonaldiagram látható. A kurzort a diagram a sikertelen kérelmek pont 3 legnépszerűbb alkalmazása időben. Sikertelen kérelmek száma az egyes alkalmazások listáját is tartalmazza. Kattintson a diagramra egy olyan <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> naplójának kereséséhez, amely a sikertelen kérelmek részletesebb táblázatát jeleníti meg. <br><br>Kattintson a lista egyik elemére a sikertelen kérelmeket, a sikertelen kérelmekre vonatkozó diagramokat és a kérelmek időtartamát, valamint a sikertelen kérelmekre vonatkozó hibakódok listáját futtató <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> naplójának kereséséhez. |
| Kivételek – a kivételek száma óránként | Kivételek száma óránként egy vonaldiagram látható. A kurzort a diagram pont kivételekkel 3 legnépszerűbb alkalmazása időben. Az egyes kivételek számát az alkalmazások listáját is tartalmazza. Kattintson a diagramra egy olyan <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> naplójának kereséséhez, amely részletesebb diagramot jelenít meg a kivételekről. <br><br>Kattintson a lista egyik elemére egy olyan <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> naplójának kereséséhez, amely megjeleníti a kivételek listáját, a kivételek diagramját az idő és a sikertelen kérelmek esetében, valamint a kivételek listáját.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Az Application Insights-perspektíva naplókeresési megtekintése

Ha az irányítópult valamely elemére kattint, egy keresési látható az Application Insights-perspektíva láthatja. A perspektíva biztosít egy kiterjesztett képi megjelenítés, a kiválasztott telemetriai adatok típusa alapján. Igen, képi megjelenítés a tartalmi változások különféle telemetriai esetében.

Ha az alkalmazások panelen bárhová kattint, megjelenik az alapértelmezett **alkalmazások** perspektívája.

![Application Insights-alkalmazások perspektíva](./media/app-insights-connector/applications-blade-drill-search.png)

A perspektíva a kiválasztott alkalmazás áttekintése látható.

A **rendelkezésre állás** panel egy másik nézőpont-nézetet mutat be, ahol megtekintheti a webes tesztek eredményeit és a kapcsolódó sikertelen kérelmeket.

![Application Insights rendelkezésre állási szempontból](./media/app-insights-connector/availability-blade-drill-search.png)

Ha a **kiszolgálói kérelmek** vagy **hibák** paneleken bárhová kattint, a perspektíva összetevői változnak, hogy a kérésekhez kapcsolódó vizualizációt adjanak.

![Application Insights-hibák panelen](./media/app-insights-connector/server-requests-failures-drill-search.png)

Ha a **kivételek** panelen bárhová kattint, a rendszer a kivételekhez igazított vizualizációt jeleníti meg.

![Application Insights-kivételek panel](./media/app-insights-connector/exceptions-blade-drill-search.png)

Függetlenül attól, hogy rákattint-e a **Application Insights Connector** -irányítópultra, a **keresési** oldalon belül minden olyan lekérdezés, amelyik Application Insights az adatmennyiséget adja vissza, megjeleníti a Application Insights perspektívát. Ha például Application Insights-adatok megtekintését szeretné megtekinteni **&#42;** , egy lekérdezés a perspektíva fület is megjeleníti a következő képhez hasonlóan:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspektíva összetevők frissülnek, attól függően, a keresési lekérdezésben. Ez azt jelenti, hogy az eredményeket szűrheti bármely, amely felkínálja az adatokat megtekinthetik keresési mező használatával:

- Az alkalmazások
- Egyetlen kiválasztott alkalmazás
- Alkalmazások egy csoportja

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Kimutatás az alkalmazás az Azure Portalon

A Application Insights Connector-lapok úgy lettek kialakítva, hogy a *Azure Portal használatakor*a kiválasztott Application Insights alkalmazáshoz lehessen eldönteni. A megoldás magas szintű felügyeleti platform, amely segít egy alkalmazás hibaelhárításával foglalkozó szakaszt is használhatja. Amikor megjelenik a csatlakoztatott alkalmazások valamelyikében egy potenciális problémát, vagy részletes elemzéseket készíthet, a Log Analytics-keresést is, vagy Ön is kimutatás, közvetlenül az Application Insights alkalmazást az.

A kimutatáshoz kattintson az egyes sorok végén megjelenő három pontra ( **...** ), majd válassza a **Megnyitás Application Insightsban**lehetőséget.

>[!NOTE]
>A **megnyitás Application Insightsban** nem érhető el a Azure Portal.

![Megnyitás az Application Insightsban](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Minta – korrigált adatok

A Application Insights *[mintavételi korrekciót](../../azure-monitor/app/sampling.md)* biztosít a telemetria-forgalom csökkentése érdekében. Mintavételi engedélyezi az Application Insights alkalmazást, kap egy Application Insights és a Log Analytics szolgáltatásban tárolt bejegyzések csökkentett száma. Míg az adatkonzisztencia megmarad a **Application Insights Connector** oldalon és perspektívákban, manuálisan kell megadnia az egyéni lekérdezésekhez tartozó mintavételes adatok helyességét.

Íme egy példa, egy naplóbeli keresési lekérdezés a mintavételi korrekciós:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

A **mintavételes darabszám** mező minden bejegyzésben megtalálható, és a bejegyzés által reprezentált adatpontok számát jeleníti meg. Ha bekapcsolja a mintavételezést a Application Insights alkalmazáshoz, a mintavételi **szám** nagyobb, mint 1. Az alkalmazás által generált bejegyzések tényleges számának megszámlálásához összesítse a **mintavételes darabszám** mezőket.

Mintavételezés csak a bejegyzések száma összesen az alkalmazás által létrehozott van hatással. Nem kell kijavítania a mintavételezést a metrika mezőihez (például **RequestDuration** vagy **AvailabilityDuration** ), mert ezek a mezők a megjelenített bejegyzések átlagát mutatják.

## <a name="input-data"></a>Bemeneti adatok

A megoldás a következő telemetriai típusú adatokat fogad a csatlakoztatott Application Insights-alkalmazások:

- Rendelkezésre állás
- Kivételek
- Kérelmek
- Lapmegtekintések – a munkaterület fogadni a lapmegtekintések, konfigurálnia kell az alkalmazásokat, adatokat gyűjthet. További információ: [oldalmegtekintések](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Egyéni események – az egyéni eseményeket szeretne fogadni. a munkaterület konfigurálnia kell az alkalmazások, adatok gyűjtéséhez. További információ: [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Érkezik adat az Application Insights szolgáltatásból a Log Analytics által elérhetővé váló.

## <a name="output-data"></a>Kimeneti adatok

Minden típusú bemeneti adathoz létrejön egy *ApplicationInsights* *típusú* rekord. Applicationinsights – rekordokat az alábbiakban látható jellemzőkkel rendelkeznek:

### <a name="generic-fields"></a>Általános mezők

| Tulajdonság | Leírás |
| --- | --- |
| Típus | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | A rekord létrehozásának időpontja |
| ApplicationId | Az Application Insights-alkalmazás kialakítási kulcs |
| Alkalmazásnév | Neve az Application Insights alkalmazást |
| RoleInstance | Kiszolgáló állomás azonosítója |
| DeviceType | Ügyféleszközök |
| ScreenResolution |   |
| Kontinens | Kontinens, amelyben adja meg a kérelem |
| Ország | Az ország/régió, ahol a kérés származik |
| Megye | Tartomány, állapot vagy a területi beállítás, amelyben adja meg a kérelem |
| Város | Város vagy a város, amelyben adja meg a kérelem |
| isSynthetic | Azt jelzi, hogy a kérelem egy felhasználó által vagy automatikus módon hozták-e. True = automatizált metódus vagy hamis = felhasználó által generált |
| Érvénytelen a SamplingRate | A telemetriát a portálra küldött SDK által generált százalékos értéke. Tartomány 0,0-100.0. |
| SampledCount | 100/(SamplingRate). Például: 4 =&gt; 25% |
| IsAuthenticated | Igaz vagy hamis |
| OperationID | Olyan elemek, amelyek ugyanazt a műveletet azonosító kapcsolódó elemek jelennek meg a portálon. Általában a kérelem azonosítója |
| ParentOperationID | A szülőművelet azonosítója |
| OperationName |   |
| SessionId | GUID egyedi azonosítására szolgál a munkamenet, ahol a kérelmet létrehozták. |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Rendelkezésre állási-specifikus mezők

| Tulajdonság | Leírás |
| --- | --- |
| TelemetryType | Rendelkezésre állás |
| AvailabilityTestName | A webes teszt neve |
| AvailabilityRunLocation | Http-kérelem földrajzi forrása |
| AvailabilityResult | Azt jelzi, hogy a webes teszt a sikeres művelet |
| AvailabilityMessage | Az üzenet csatlakozik a webes teszt |
| AvailabilityCount | 100 /(Sampling Rate). Például: 4 =&gt; 25% |
| DataSizeMetricValue | 1.0-s vagy 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Például: 4 =&gt; 25% |
| AvailabilityDuration | Idő, a webes teszt futtatásának időtartama ezredmásodpercben |
| AvailabilityDurationCount | 100 /(Sampling Rate). Például: 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | A webteszt egyedi GUID azonosítója |
| AvailabilityTimestamp | A rendelkezésre állási teszt pontos időbélyegét |
| AvailabilityDurationMin | A mintavételezett rögzíti ezt a mezőt a minimális webes teszt futtatásának időtartama (ezredmásodpercben) a képviselt adatpontokhoz mutatja be |
| AvailabilityDurationMax | A mintavételezett rögzíti ezt a mezőt a maximális webes teszt futtatásának időtartama (ezredmásodpercben) a képviselt adatpontokhoz mutatja be |
| AvailabilityDurationStdDev | A mintavételezett rögzíti ezt a mezőt a szórás között minden webes teszt időtartamát (ezredmásodperc) a képviselt adatpontokhoz mutatja be |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Kivétel-specifikus mezők

| Típus | ApplicationInsights |
| --- | --- |
| TelemetryType | Kivétel |
| ExceptionType | A kivétel típusa |
| ExceptionMethod | A metódus a kivételt létrehozó |
| ExceptionAssembly | Szerelvény tartalmazza a keretrendszert és verziója, valamint a nyilvános kulcs jogkivonata |
| ExceptionGroup | A kivétel típusa |
| ExceptionHandledAt | Azt jelzi, hogy a szintet, amelyet a kivétel kezelve |
| ExceptionCount | 100 /(Sampling Rate). Például: 4 =&gt; 25% |
| ExceptionMessage | A kivétel üzenet |
| ExceptionStack | A kivétel teljes verem |
| ExceptionHasStack | IGAZ, ha kivétel a verem |



### <a name="request-specific-fields"></a>Kérelem-specifikus mezők

| Tulajdonság | Leírás |
| --- | --- |
| Típus | ApplicationInsights |
| TelemetryType | Kérés |
| ResponseCode | Az ügyfélnek küldött HTTP-válasz |
| RequestSuccess | Azt jelzi, hogy sikeres vagy sikertelen. IGAZ vagy hamis. |
| RequestID | Egyedi azonosítására szolgál a kérelem azonosítója |
| RequestName | GET/POST + alap URL-je |
| RequestDuration | A kérelem időtartama másodpercben |
| URL-cím | A kérelem nem többek között a gazdagép URL-címe |
| Gazdagép | Web server-gazdagép |
| URLBase | A kérelem teljes URL-cím |
| ApplicationProtocol | Az alkalmazás által használt protokoll típusát |
| RequestCount | 100 /(Sampling Rate). Például: 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Például: 4 =&gt; 25% |
| RequestDurationMin | A mintavételezett rekordok ebben a mezőben a képviselt adatpontokhoz a minimális kérés időtartama (ezredmásodpercben) látható. |
| RequestDurationMax | A mintavételezett rekordok Ez a mező mutatja be, a kérelem maximális időtartama (ezredmásodpercben) a képviselt adatpontokhoz |
| RequestDurationStdDev | A mintavételezett rekordok ebben a mezőben a szórást az összes kérelem időtartama (ezredmásodpercben) között látható a képviselt adatpontokhoz |

## <a name="sample-log-searches"></a>Naplókeresési minták

Ez a megoldás nem rendelkezik az irányítópulton látható naplókeresési mintákat egy készletét. A példákat tartalmazó naplóbeli keresési lekérdezések azonban a [Application Insights Connector információk megtekintése](#view-application-insights-connector-information) szakaszban láthatók.

## <a name="removing-the-connector-with-powershell"></a>Az összekötő eltávolítása a PowerShell-lel
A OMS-portál elavult használata esetén nincs lehetőség a meglévő kapcsolatok konfigurálására és eltávolítására a portálon. A meglévő kapcsolatokat a következő PowerShell-parancsfájl használatával távolíthatja el. A művelet végrehajtásához a munkaterület tulajdonosának vagy közreműködője, Application Insights erőforrás-olvasójának kell lennie.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Az alkalmazások listáját az alábbi PowerShell-parancsfájl segítségével kérheti le, amely egy REST API hívást hív meg. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
A szkriptnek rendelkeznie kell egy tulajdonosi hitelesítési jogkivonattal a Azure Active Directory való hitelesítéshez. A token lekérésének egyik módja a [REST API dokumentációs webhelyén](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate)található cikk használata. Kattintson a **kipróbálás** gombra, és jelentkezzen be az Azure-előfizetésbe. A tulajdonosi jogkivonatot a **kérelem előzetes** verziójából másolhatja, ahogy az alábbi képen is látható.


![Tulajdonosi jogkivonat](media/app-insights-connector/bearer-token.png)


Lekérheti az alkalmazások listáját is, ha egy napló lekérdezést használ:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Következő lépések

- A Application Insights-alkalmazások részletes adatainak megtekintéséhez használja a [log Search kifejezést](../../azure-monitor/log-query/log-query-overview.md) .
