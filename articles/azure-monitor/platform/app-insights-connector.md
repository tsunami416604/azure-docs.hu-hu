---
title: Az Azure Application Insights-alkalmazások adatainak megtekintése |} A Microsoft Docs
description: Az Application Insights-összekötő megoldás segítségével diagnosztizálhatja a teljesítménybeli problémákat, és megismerheti, mit a felhasználók az alkalmazását, amikor az Application insights segítségével figyeli.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: magoedte
ms.openlocfilehash: 4e91e193b3980901e7778a8826989e729517a29a
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481756"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights-összekötő felügyeleti megoldás (elavult)

![Application Insights szimbólum](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> A támogatási [erőforrások közötti lekérdezések](../../azure-monitor/log-query/cross-workspace-query.md), az Application Insights-összekötő-felügyeleti megoldás már nem szükséges. Már elavult, és törli az Azure piactéren, az OMS-portálon, hogy 2019. január 15. az Azure kereskedelmi felhőben hivatalosan elavulttá együtt. Ez jén kivonjuk a forgalomból 2019. március 30 Azure US Government-felhőben.
>
>A meglévő kapcsolatok továbbra is működnek, amíg 2019. június 30.  Az OMS-portál elavulásának nincs lehetőség a konfigurálását, és távolítsa el a meglévő kapcsolatok a portálról. Lásd: [eltávolításával a PowerShell-lel az összekötő](#removing-the-connector-with-powershell) alább talál egy parancsfájlt a PowerShell használatával távolítsa el a meglévő kapcsolatok.
>
>Útmutató az Application Insights lekérdezési napló az adatok több alkalmazáshoz, lásd: [használja őket egységes Előtérrendszerként több Azure Monitor az Application Insights-erőforrást](../log-query/unify-app-resource-data.md). Az OMS-portál elavulással kapcsolatos további információkért lásd: [Azure-bA az OMS-portálon](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

A Applications Insights Connector megoldás segítségével diagnosztizálhatja a teljesítménnyel kapcsolatos problémák, és megismerheti, mit a felhasználók az alkalmazását, amikor a figyelhető [Application Insights](../../azure-monitor/app/app-insights-overview.md). Az ugyanazon alkalmazás telemetriát az Application Insightsban a fejlesztőknek nézet áll rendelkezésre a Log Analyticsben. Azonban az Application Insights-alkalmazások integrálása a Log Analytics-szel, az alkalmazások láthatóságát emelkedett üzemeltetési és alkalmazásadatokat létesíteni egy helyen. Az azonos nézetek kellene megismerheti, hogyan működhet együtt az alkalmazásfejlesztőknek. Az Általános nézetek segítségével csökkentheti az időt, és oldja meg az alkalmazás és a platformmal.

A megoldás használata esetén is:

- Megtekintése az Application Insights-alkalmazások egy helyen tudhatja, akkor is, ha azokat az Azure-előfizetések
- Az alkalmazásadatok infrastruktúra adatait
- A naplókeresésben szolgáltatva az alkalmazások adatainak megjelenítéséhez
- Forgáspont a Log Analytics-adatok az Application Insights alkalmazáshoz az Azure Portalon

## <a name="connected-sources"></a>Összekapcsolt források

Ellentétben a legtöbb más Log Analytics-megoldások adatok nem lesznek gyűjtve az Application Insights-összekötő az ügynökök által. A megoldás által használt összes adat közvetlenül az Azure-ból származik.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](../../azure-monitor/platform/agent-windows.md) | Nem | Windows-ügynököktől a megoldás nem gyűjt adatokat. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás a Linux-ügynökök nem gyűjt adatokat. |
| [Az SCOM felügyeleti csoport](../../azure-monitor/platform/om-agents.md) | Nem | A megoldás az ügynökök a csatlakoztatott SCOM felügyeleti csoport nem gyűjt adatokat. |
| [Azure Storage-fiók](collect-azure-metrics-logs.md) | Nem | A megoldás nem az Azure storage-ból nem gyűjteményadatokat. |

## <a name="prerequisites"></a>Előfeltételek

- Application Insights-összekötő adatainak eléréséhez Azure-előfizetéssel kell rendelkeznie
- Rendelkeznie kell legalább egy beállított Application Insights-erőforrást.
- A tulajdonos vagy közreműködő szerepkörrel az Application Insights-erőforrást kell lennie.

## <a name="configuration"></a>Konfiguráció

1. Engedélyezze az Azure Web Apps Analytics megoldás a a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) vagy leírt folyamatot követve [adja hozzá a Log Analytics solutions kövesse a megoldástárban](../../azure-monitor/insights/solutions.md).
2. Keresse fel az [Azure Portalt](https://portal.azure.com). Válassza ki **minden szolgáltatás** Application Insights megnyitásához. Ezután keresse meg az Application Insights. 
3. A **előfizetések**, válasszon ki egy előfizetést, amely rendelkezik az Application Insights-erőforrást, majd a **neve**, válassza ki egy vagy több alkalmazás.
4. Kattintson a **Save** (Mentés) gombra.

Körülbelül 30 percet, az adatok elérhetővé válnak, és az Application Insights csempe frissül, az adatok, például a következő képen:

![Az Application Insights-csempe](./media/app-insights-connector/app-insights-tile.png)

Egyéb szempontok figyelembe kell venni:

- Application Insights-alkalmazások csak kapcsolat egy Log Analytics-munkaterülethez.
- Csak kapcsolat [alapszintű vagy vállalati Application Insights-erőforrások](https://azure.microsoft.com/pricing/details/application-insights) a Log Analytics szolgáltatásba. A Log Analytics ingyenes szintjét is használhatja.

## <a name="management-packs"></a>Felügyeleti csomagok

Ez a megoldás nem telepíti a csatlakoztatott felügyeleti csoportok minden olyan felügyeleti csomagot.

## <a name="use-the-solution"></a>A megoldás használatához

A következő szakaszok ismertetik, hogyan használhatja a paneleket, látható az Application Insights irányítópult megtekintéséhez és az alkalmazásokból származó adatok kezeléséhez.

### <a name="view-application-insights-connector-information"></a>Application Insights-összekötő adatainak megtekintése

Kattintson a **Application Insights** csempére kattintva nyissa meg a **Application Insights** irányítópultján megtekintheti a következő paneleket.

![Application Insights irányítópult](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights irányítópult](./media/app-insights-connector/app-insights-dash02.png)

Az irányítópult tartalmaz a paneleket a táblázatban látható. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. Naplókeresést, amely az összes olyan rekord visszaadása kattintva futtathatja **összes** alján a panel, vagy ha a panel fejlécére kattint.


| **Oszlop** | **Leírás** |
| --- | --- |
| Alkalmazások – alkalmazások száma | Alkalmazás-erőforrásokat az alkalmazások számát jeleníti meg. Felsorolja az alkalmazásnevek és az egyes alkalmazás-rekordok számát. Kattintson a számra a Naplókeresés futtatásához <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kattintson az alkalmazás nevét, amely megjeleníti a gazdagép, valamint a rekordok a telemetriai adatok típusa szerint és minden adat (az elmúlt nap alapján) típus szerint alkalmazás bejegyzéseket Naplókeresés futtatásához. |
| Adatmennyiség – adatokat továbbító gazdagépek | Adatokat küldő számítógép-állomások számát jeleníti meg. Számítógép-gazdagépek és -rekordok száma minden állomás számára is megjeleníti. Kattintson a számra a Naplókeresés futtatásához <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Kattintson a számítógép nevét, a gazdagép, amely megjeleníti a gazdagép, valamint a rekordok a telemetriai adatok típusa szerint és minden adat (az elmúlt nap alapján) típus szerint alkalmazásrekordok Naplókeresés futtatásához. |
| Rendelkezésre állás – Webtesztek eredményei | A webes teszt eredményei, pass vagy sikertelen jelző perecdiagram mutatja. Kattintson a diagramra a Naplókeresés futtatásához <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Eredmények megjelenítése a műveletek és az összes teszt fellépő hibák száma. Azt mutatja be minden Web Apps, a forgalom az elmúlt percben. Kattintson az alkalmazás nevét, egy naplóbeli keresés sikertelen webes tesztek részleteit megjelenítő megtekintéséhez. |
| Kiszolgálói kérelmek – kérelmek száma óránként | A különböző alkalmazások az óránként kiszolgálókérelmek egy vonaldiagram látható. A 3 leggyakoribb alkalmazások pont kérések fogadása szerinti megtekintéséhez a diagramban a sor fölé. A fogadása a kérelmek és a kérések száma a kijelölt időszak alkalmazások listáját is tartalmazza. <br><br>Kattintson a diagramra a Naplókeresés futtatásához <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> azt mutatjuk be, a kiszolgálói kérelmek óránkénti különféle alkalmazások részletesebb vonaldiagram. <br><br> Kattintson a listában egy alkalmazást egy Naplókeresés futtatásához a <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , amelyek listáját jeleníti meg a kérelmek, a kérések teljes ideje és kérelem időtartama a diagramokban és a kérés listáját válaszkódot.   |
| Hiba – a sikertelen kérelmek száma óránként | Alkalmazás sikertelen kérelmek száma óránként egy vonaldiagram látható. A kurzort a diagram a sikertelen kérelmek pont 3 legnépszerűbb alkalmazása időben. Sikertelen kérelmek száma az egyes alkalmazások listáját is tartalmazza. Kattintson a diagramra a Naplókeresés futtatásához <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> azt mutatjuk be, a sikertelen alkalmazáskérések egy részletesebb vonaldiagram. <br><br>A Naplókeresés futtatásához a lista egy elemére kattintva <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , hogy mutat be sikertelen kérések, diagramokon a sikertelen kérelmek ideje és kérelem időtartama, valamint a sikertelen kérelmek válaszkódot listáját. |
| Kivételek – a kivételek száma óránként | Kivételek száma óránként egy vonaldiagram látható. A kurzort a diagram pont kivételekkel 3 legnépszerűbb alkalmazása időben. Az egyes kivételek számát az alkalmazások listáját is tartalmazza. Kattintson a diagramra a Naplókeresés futtatásához <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , amely látható a részletesebb hivatkozás kivételek. <br><br>A Naplókeresés futtatásához a lista egy elemére kattintva <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> , kivételek, a kivételek az idő és a sikertelen kérelmek függvényében diagramok és kivételtípusok listáját listája látható.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Az Application Insights-perspektíva naplókeresési megtekintése

Ha az irányítópult valamely elemére kattint, egy keresési látható az Application Insights-perspektíva láthatja. A perspektíva biztosít egy kiterjesztett képi megjelenítés, a kiválasztott telemetriai adatok típusa alapján. Igen, képi megjelenítés a tartalmi változások különféle telemetriai esetében.

Amikor az alkalmazások panelen bárhol kattint, láthatja, az alapértelmezett **alkalmazások** szempontjából.

![Application Insights-alkalmazások perspektíva](./media/app-insights-connector/applications-blade-drill-search.png)

A perspektíva a kiválasztott alkalmazás áttekintése látható.

A **rendelkezésre állási** panelen jelenik meg egy másik szempont nézet, ahol megtekintheti webes teszt eredményeit és a kapcsolódó sikertelen kérelmek.

![Application Insights rendelkezésre állási szempontból](./media/app-insights-connector/availability-blade-drill-search.png)

Kattintva bárhol a **kiszolgálói kérelmek** vagy **hibák** paneleken a perspektíva összetevők módosítása, hogy egy vizualizációt, amely a kapcsolódó kéréseket.

![Application Insights-hibák panelen](./media/app-insights-connector/server-requests-failures-drill-search.png)

Kattintva bárhol a **kivételek** kivételek igényeinek megfelelő Vizualizációk panelen láthatók.

![Application Insights-kivételek panel](./media/app-insights-connector/exceptions-blade-drill-search.png)

Függetlenül attól, hogy valami valamelyik gombra a **Application Insights-összekötő** irányítópulton belül, a **keresési** lapon, Application Insights-adatok az alkalmazás látható visszaadó lekérdezés Insights-perspektívát. Ha az Application Insights-adatokat, például egy **&#42;** lekérdezés is megjeleníti a perspektíva lapon a következő képhez hasonlóan:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspektíva összetevők frissülnek, attól függően, a keresési lekérdezésben. Ez azt jelenti, hogy az eredményeket szűrheti bármely, amely felkínálja az adatokat megtekinthetik keresési mező használatával:

- Az alkalmazások
- Egyetlen kiválasztott alkalmazás
- Alkalmazások egy csoportja

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Kimutatás az alkalmazás az Azure Portalon

Application Insights-összekötő panelek úgy tervezték, hogy lehetővé teszi a kiválasztott Application Insights alkalmazáshoz forgáspont *használhatja az Azure Portalon*. A megoldás magas szintű felügyeleti platform, amely segít egy alkalmazás hibaelhárításával foglalkozó szakaszt is használhatja. Amikor megjelenik a csatlakoztatott alkalmazások valamelyikében egy potenciális problémát, vagy részletes elemzéseket készíthet, a Log Analytics-keresést is, vagy Ön is kimutatás, közvetlenül az Application Insights alkalmazást az.

Forgáspont, kattintson a három pontra (**...** ), amely akkor jelenik meg az egyes sorok végén található, és válassza ki **nyissa meg az Application Insights**.

>[!NOTE]
>**Nyissa meg az Application Insights** nem érhető el az Azure Portalon.

![Megnyitás az Application Insightsban](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Minta – korrigált adatok

Az Application Insights nyújt *[javítás mintavételi](../../azure-monitor/app/sampling.md)* telemetriai forgalom csökkentése érdekében. Mintavételi engedélyezi az Application Insights alkalmazást, kap egy Application Insights és a Log Analytics szolgáltatásban tárolt bejegyzések csökkentett száma. Amíg megőrzi az adatkonzisztencia magyarázata a **Application Insights-összekötő** oldal és a perspektívák kézzel korrigálja mintavételezett adatokat az egyéni lekérdezések.

Íme egy példa, egy naplóbeli keresési lekérdezés a mintavételi korrekciós:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

A **mintavételezés száma** mező szerepel az összes bejegyzést, és a bejegyzést képviselő adatpontok számát jeleníti meg. Ha az Application Insights-alkalmazások esetén mintavételezés bekapcsolása **mintavételezés száma** 1-nél nagyobb. Az alkalmazás által létrehozott bejegyzéseket tényleges számát, a sum a **mintavételezés száma** mezőket.

Mintavételezés csak a bejegyzések száma összesen az alkalmazás által létrehozott van hatással. Javítsa ki a mintavételt a metrikaalapú mezők, például nem kell **RequestDuration** vagy **AvailabilityDuration** mivel ezek a mezők megjelenítése az átlagos képviselt bejegyzések.

## <a name="input-data"></a>Bemeneti adatok

A megoldás a következő telemetriai típusú adatokat fogad a csatlakoztatott Application Insights-alkalmazások:

- Rendelkezésre állás
- Kivételek
- Kérelmek
- Lapmegtekintések – a munkaterület fogadni a lapmegtekintések, konfigurálnia kell az alkalmazásokat, adatokat gyűjthet. További információkat lásd: [Oldalmegtekintések](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Egyéni események – az egyéni eseményeket szeretne fogadni. a munkaterület konfigurálnia kell az alkalmazások, adatok gyűjtéséhez. További információkat lásd: [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Érkezik adat az Application Insights szolgáltatásból a Log Analytics által elérhetővé váló.

## <a name="output-data"></a>Kimeneti adatok

Egy rekord egy *típus* , *ApplicationInsights* jön létre az egyes bemeneti adatokat. Applicationinsights – rekordokat az alábbiakban látható jellemzőkkel rendelkeznek:

### <a name="generic-fields"></a>Általános mezők

| Tulajdonság | Leírás |
| --- | --- |
| Típus | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | A rekord létrehozásának időpontja |
| Alkalmazásazonosító | Az Application Insights-alkalmazás kialakítási kulcs |
| Alkalmazásnév | Neve az Application Insights alkalmazást |
| RoleInstance | Kiszolgáló állomás azonosítója |
| DeviceType | Ügyféleszközök |
| ScreenResolution |   |
| Kontinens | Kontinens, amelyben adja meg a kérelem |
| Ország | Ország, ahonnan a kérés származik |
| Megye | Tartomány, állapot vagy a területi beállítás, amelyben adja meg a kérelem |
| Város | Város vagy a város, amelyben adja meg a kérelem |
| isSynthetic | Azt jelzi, hogy a kérelem egy felhasználó által vagy automatikus módon hozták-e. = Igaz a felhasználó által vagy a false = az automatikus módszer |
| Érvénytelen a SamplingRate | A telemetriát a portálra küldött SDK által generált százalékos értéke. Tartomány 0,0-100.0. |
| SampledCount | 100/(SamplingRate). Ha például 4 =&gt; 25 %-kal |
| IsAuthenticated | Igaz vagy hamis |
| Műveletazonosító: | Olyan elemek, amelyek ugyanazt a műveletet azonosító kapcsolódó elemek jelennek meg a portálon. Általában a kérelem azonosítója |
| ParentOperationID | A szülőművelet azonosítója |
| OperationName |   |
| munkamenet-azonosító | GUID egyedi azonosítására szolgál a munkamenet, ahol a kérelmet létrehozták. |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Rendelkezésre állási-specifikus mezők

| Tulajdonság | Leírás |
| --- | --- |
| TelemetryType | Rendelkezésre állás |
| AvailabilityTestName | A webes teszt neve |
| AvailabilityRunLocation | Http-kérelem földrajzi forrása |
| AvailabilityResult | Azt jelzi, hogy a webes teszt a sikeres művelet |
| AvailabilityMessage | Az üzenet csatlakozik a webes teszt |
| AvailabilityCount | 100 /(Sampling Rate). Ha például 4 =&gt; 25 %-kal |
| DataSizeMetricValue | 1.0-s vagy 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Ha például 4 =&gt; 25 %-kal |
| AvailabilityDuration | Idő, a webes teszt futtatásának időtartama ezredmásodpercben |
| AvailabilityDurationCount | 100 /(Sampling Rate). Ha például 4 =&gt; 25 %-kal |
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
| ExceptionCount | 100 /(Sampling Rate). Ha például 4 =&gt; 25 %-kal |
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
| Kérelemazonosító: | Egyedi azonosítására szolgál a kérelem azonosítója |
| RequestName | GET/POST + alap URL-je |
| RequestDuration | A kérelem időtartama másodpercben |
| URL-cím | A kérelem nem többek között a gazdagép URL-címe |
| Gazdagép | Web server-gazdagép |
| URLBase | A kérelem teljes URL-cím |
| ApplicationProtocol | Az alkalmazás által használt protokoll típusát |
| RequestCount | 100 /(Sampling Rate). Ha például 4 =&gt; 25 %-kal |
| RequestDurationCount | 100 /(Sampling Rate). Ha például 4 =&gt; 25 %-kal |
| RequestDurationMin | A mintavételezett rekordok ebben a mezőben a képviselt adatpontokhoz a minimális kérés időtartama (ezredmásodpercben) látható. |
| RequestDurationMax | A mintavételezett rekordok Ez a mező mutatja be, a kérelem maximális időtartama (ezredmásodpercben) a képviselt adatpontokhoz |
| RequestDurationStdDev | A mintavételezett rekordok ebben a mezőben a szórást az összes kérelem időtartama (ezredmásodpercben) között látható a képviselt adatpontokhoz |

## <a name="sample-log-searches"></a>Naplókeresési minták

Ez a megoldás nem rendelkezik az irányítópulton látható naplókeresési mintákat egy készletét. Minta naplóbeli keresési lekérdezések leírásokat tartalmazó látható azonban a [nézet Application Insights-összekötő információk](#view-application-insights-connector-information) szakaszban.

## <a name="removing-the-connector-with-powershell"></a>A PowerShell-lel az összekötő eltávolítása
Az OMS-portál elavulásának nincs lehetőség a konfigurálását, és távolítsa el a meglévő kapcsolatok a portálról. Meglévő kapcsolatok a következő PowerShell-parancsfájllal távolíthatja el. A tulajdonosi vagy közreműködői a munkaterület és Application Insights-erőforrást az olvasó végrehajtani a műveletet kell lennie.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId $Subscription_app
$AIApp = Get-AzureRmApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzureRmContext -SubscriptionId $Subscription_workspace
Remove-AzureRmOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Kérheti le a következő PowerShell-parancsfájlt, amely REST API-hívás hív meg használó alkalmazások listáját. 

```powershell
Connect-AzureRmAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzureRmContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzureRmOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Ehhez a szkripthez hitelesítési tulajdonosi jogkivonattal az Azure Active Directoryval történő hitelesítésre. Egyik módja a jogkivonat lekéréséhez használja a cikk a [REST API-dokumentációs oldalának](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Kattintson a **Kipróbálom** , és jelentkezzen be az Azure-előfizetésében. A tulajdonosi jogkivonatot is másolja a **előzetes kérése** az alábbi képen látható módon.


![Tulajdonosi jogkivonat](media/app-insights-connector/bearer-token.png)


Alkalmazások használata listáját egy naplólekérdezés is lekérhet:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>További lépések

- Használat [naplóbeli keresés](../../azure-monitor/log-query/log-query-overview.md) részletes információk az Application Insights-alkalmazások megtekintéséhez.
