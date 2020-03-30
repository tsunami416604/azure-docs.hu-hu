---
title: Az Azure Application Insights alkalmazásadatainak megtekintése | Microsoft dokumentumok
description: Az Application Insights Connector megoldás segítségével diagnosztizálhatja a teljesítményproblémákat, és megismerheti, hogy a felhasználók mit tesznek az alkalmazással, ha az Application Insights figyel.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665153"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights-összekötő felügyeleti megoldás (elavult)

![Az Application Insights szimbóluma](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Az erőforrások [közötti lekérdezések](../../azure-monitor/log-query/cross-workspace-query.md)támogatásával az Application Insights Összekötő felügyeleti megoldás már nincs szükség. 2019. január 15-én az Azure kereskedelmi felhőjéhez hivatalosan elavult az Azure piactérről, és eltávolították az Azure piactérről. 2019. március 30-án megszűnik az Azure US Government felhője.
>
>A meglévő kapcsolatok 2019. június 30-ig működnek.  Az OMS-portál eprecációjával nincs mód a meglévő kapcsolatok konfigurálására és eltávolítására a portálról. Tekintse meg [az összekötő eltávolítása a PowerShell](#removing-the-connector-with-powershell) alább egy parancsfájlt a PowerShell meglévő kapcsolatok eltávolítása.
>
>Az Application Insights naplóadatainak több alkalmazáshoz történő lekérdezéséhez további útmutatást az [Azure Monitor Application Insights-erőforrások egyesítése.](../log-query/unify-app-resource-data.md) Az OMS-portál eprecation című témakörben további információt az [OMS-portál az Azure-ba való áthelyezés című témakörben talál.](../../azure-monitor/platform/oms-portal-transition.md)
>
> 

Az Applications Insights Connector megoldás segítségével diagnosztizálhatja a teljesítményproblémákat, és megismerheti, hogy a felhasználók mit tesznek az alkalmazással, amikor az [Application Insights](../../azure-monitor/app/app-insights-overview.md)figyeli. A fejlesztők által az Application Insightsban látott azonos alkalmazástelemetriai nézetek érhetők el a Log Analytics szolgáltatásban. Ha azonban integrálja az Application Insights-alkalmazásokat a Log Analytics szolgáltatással, az alkalmazások láthatósága nő azáltal, hogy egy helyen rendelkezik a működési és alkalmazásadatokkal. Az azonos nézetek segít az alkalmazásfejlesztőkkel való együttműködésben. A gyakori nézetek segítségével csökkentheti az alkalmazás- és platformproblémák észlelésére és megoldására szánt időt.

A megoldás használatakor a következőkre van szüksége:

- Az összes Application Insights-alkalmazás megtekintése egy helyen, még akkor is, ha különböző Azure-előfizetésekben vannak
- Infrastruktúraadatok és alkalmazásadatok összefüggése
- Az alkalmazásadatok megjelenítése perspektívákkal a naplókeresésben
- Kimutatás a Log Analytics-adatokból az Application Insights-alkalmazásba az Azure Portalon


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Összekapcsolt források

A legtöbb más Log Analytics-megoldással ellentétben az ügynökök nem gyűjtik az adatokat az Application Insights-összekötőhöz. A megoldás által használt összes adat közvetlenül az Azure-ból származik.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](../../azure-monitor/platform/agent-windows.md) | Nem | A megoldás nem gyűjt adatokat a Windows-ügynököktől. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás nem gyűjt adatokat linuxos ügynököktől. |
| [SCOM felügyeleti csoport](../../azure-monitor/platform/om-agents.md) | Nem | A megoldás nem gyűjt adatokat a csatlakoztatott SCOM felügyeleti csoport ügynökeitől. |
| [Azure tárfiók](collect-azure-metrics-logs.md) | Nem | A megoldás nem gyűjt adatokat az Azure storage-ból. |

## <a name="prerequisites"></a>Előfeltételek

- Az Application Insights-összekötő adatainak eléréséhez Azure-előfizetéssel kell rendelkeznie
- Legalább egy konfigurált Application Insights-erőforrással kell rendelkeznie.
- Az Application Insights erőforrás tulajdonosának vagy közreműködőjének kell lennie.

## <a name="configuration"></a>Konfiguráció

1. Engedélyezze az Azure Web Apps Analytics-megoldást az [Azure piactérről,](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) vagy használja a [Megoldások tárból származó LogAnalytics-megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című részben leírt folyamatot.
2. Keresse fel az [Azure Portalt](https://portal.azure.com). Válassza az **Összes szolgáltatás** lehetőséget az Application Insights megnyitásához. Ezután keresse meg az Application Insights. 
3. Az **Előfizetések**csoportban válasszon ki egy olyan előfizetést, amely Rendelkezik Application Insights-erőforrásokkal, majd a **Név**csoportban válasszon ki egy vagy több alkalmazást.
4. Kattintson a **Mentés** gombra.

Körülbelül 30 perc múlva az adatok elérhetővé válnak, és az Application Insights csempe frissül az adatokkal, például az alábbi képpel:

![Az Application Insights csempéje](./media/app-insights-connector/app-insights-tile.png)

Egyéb szem előtt tartandó pontok:

- Az Application Insights-alkalmazásokat csak egy Log Analytics-munkaterülethez kapcsolhatja.
- Az [Alapszintű vagy az Enterprise Application Insights-erőforrásokat](https://azure.microsoft.com/pricing/details/application-insights) csak a Log Analytics-hez kapcsolhatja. Azonban használhatja a Log Analytics ingyenes szintjét.

## <a name="management-packs"></a>Felügyeleti csomagok

Ez a megoldás nem telepít felügyeleti csomagokat csatlakoztatott felügyeleti csoportokba.

## <a name="use-the-solution"></a>Használja a megoldást

Az alábbi szakaszok ismertetik, hogyan használhatja az Application Insights irányítópultján látható paneleket az alkalmazásokból származó adatok megtekintéséhez és kezeléséhez.

### <a name="view-application-insights-connector-information"></a>Az Application Insights-összekötő adatainak megtekintése

Az **Application Insights** csempére kattintva nyissa meg az **Application Insights** irányítópultját a következő panelek megtekintéséhez.

![Application Insights irányítópult](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights irányítópult](./media/app-insights-connector/app-insights-dash02.png)

Az irányítópult tartalmazza a táblázatban látható paneleket. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. Futtathat olyan naplókeresést, amely az összes rekordot visszaadja, ha a panel alján található **Összes megtekintése** gombra kattint, vagy ha a panelfejlécre kattint.


| **Oszlop** | **Leírás** |
| --- | --- |
| Alkalmazások száma - Pályázatok száma | Az alkalmazások számát jeleníti meg az alkalmazás-erőforrásokban. Az alkalmazásneveket és az egyes alkalmazások számát is felsorolja. Kattintson a naplókeresés futtatásához a számra<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kattintson egy alkalmazás nevére az alkalmazás naplókeresésének futtatásához, amely az alkalmazásrekordokat állomásonként, a telemetriai adatok típusa és az összes adat típus szerint jeleníti meg (az utolsó nap alapján). |
| Adatmennyiség – Adatküldési állomások | Az adatokat küldő számítógép-állomások számát jeleníti meg. Az egyes állomások számítógép-állomásait és rekordszámait is felsorolja. Kattintson a naplókeresés futtatásához a számra<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Kattintson egy számítógépnévre, ha naplókeresést szeretne futtatni az állomásra, amely az alkalmazásrekordokat állomásonként, a telemetriai adatok típusa és az összes adat típus szerint jeleníti meg (az utolsó nap alapján). |
| Elérhetőség – Webtest eredmények | Perecdiagramot jelenít meg a webes teszt eredményekhez, jelezve az áthaladást vagy a sikertelent. Kattintson a diagramra a naplókeresés futtatásához<code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Az eredmények azt mutatják, hogy az összes teszthez hány belépő és sikertelen. Ez azt mutatja, az összes web apps a forgalom az utolsó pillanatban. Kattintson egy alkalmazás nevére a sikertelen webes tesztek részleteit megjelenítő naplókeresés megtekintéséhez. |
| Kiszolgálói kérelmek – óránkénti kérelmek | A különböző alkalmazások kiszolgálói kérelmeinek óránkénti vonaldiagramját jeleníti meg. Mutasson a diagram egy vonalára, és tekintse meg az adott időpontra vonatkozó kérelmeket fogadó első 3 alkalmazást. A kérelmeket fogadó alkalmazások listáját és a kiválasztott időszakra vonatkozó kérelmek számát is megjeleníti. <br><br>Kattintson a diagramra egy <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> naplókeresés futtatásához, amely a különböző alkalmazások kiszolgálói kérelmeinek részletesebb vonaldiagramját jeleníti meg. <br><br> Kattintson a listában egy alkalmazásra <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> a kérelmek listáját, a kérelmek időbeli listáját és a kérelem időtartamát, valamint a kérelemválasz-kódok listáját megjelenítő naplókeresést.   |
| Hibák – Sikertelen kérelmek óránként | A sikertelen alkalmazáskérelmek óránkénti vonaldiagramját jeleníti meg. Mutasson a diagramra, és tekintse meg az első 3 alkalmazást, amelyek egy adott időpontra vonatkozó sikertelen kérelmeket tartalmaznak. Is mutatja az alkalmazások listáját a sikertelen kérelmek száma az egyes. Kattintson a diagramra a <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> sikertelen alkalmazáskérelmek részletesebb sordiagramját megjelenítő naplókeresés futtatásához. <br><br>Kattintson a lista egyik elemére <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> a sikertelen kérelmek, a sikertelen kérelmek időbeli diagramjai és a kérelem időtartamának, valamint a sikertelen kérelemválasz-kódok listáját megjelenítő naplókeresés futtatásához. |
| Kivételek – Kivételek óránként | Az óránkénti kivételek vonaldiagramját jeleníti meg. Mutasson a diagramra, hogy egy adott időpontban kivételekkel láthassa a legjobb 3 alkalmazást. Is mutatja az alkalmazások listáját a kivételek száma az egyes. Kattintson a diagramra a <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> kivételek részletesebb hivatkozásdiagramját megjelenítő naplókeresés futtatásához. <br><br>Kattintson a lista egyik elemére, <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> ha naplókeresést szeretne futtatni, amely a kivételek listáját, a kivételek és a sikertelen kérelmek diagramjait, valamint a kivételtípusok listáját jeleníti meg.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Az Application Insights-perspektíva megtekintése naplókereséssel

Ha az irányítópult bármely elemére kattint, megjelenik egy Application Insights-perspektíva a keresésben. A perspektíva egy kiterjesztett vizualizációt biztosít a kiválasztott telemetriai típus alapján. Így a vizualizációs tartalom különböző telemetriai típusok módosítása.

Ha az Alkalmazások panelen bárhová kattint, megjelenik az alapértelmezett **Alkalmazások** perspektíva.

![Az Application Insights Applications perspektívája](./media/app-insights-connector/applications-blade-drill-search.png)

A perspektíva a kiválasztott alkalmazás áttekintését jeleníti meg.

A **rendelkezésre állási** panel egy másik perspektivikus nézetet jelenít meg, ahol megtekintheti a webes teszt eredmények és a kapcsolódó sikertelen kérelmek.

![Az Application Insights rendelkezésre állási perspektívája](./media/app-insights-connector/availability-blade-drill-search.png)

Ha a **Kiszolgálói kérelmek** vagy **hibák** panelen bárhová kattint, a perspektivikus összetevők megváltoznak, hogy a kérésekhez kapcsolódó vizualizációt kapjanak.

![Az Application Insights-hibák panel](./media/app-insights-connector/server-requests-failures-drill-search.png)

Ha a **Kivételek** panelen bárhová kattint, a kivételekhez igazított vizualizáció jelenik meg.

![Az Application Insights-kivételek panel](./media/app-insights-connector/exceptions-blade-drill-search.png)

Függetlenül attól, hogy rákattint-e valamire az **Application Insights-összekötő** irányítópultján, magában a **Keresési** lapon belül, az Application Insights-adatokat visszaadó lekérdezések az Application Insights perspektíváját jelenítik meg. Ha például az Application Insights-adatokat tekinti meg, egy **&#42;** lekérdezés a perspektíva lapot is megjeleníti, mint például a következő képet:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

A perspektíva összetevői a keresési lekérdezéstől függően frissülnek. Ez azt jelenti, hogy az eredményeket bármely olyan keresési mező használatával szűrheti, amely lehetővé teszi az adatok megtekintését:

- Minden alkalmazás
- Egyetlen kijelölt alkalmazás
- A kérelmek csoportja

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Kimutatás egy alkalmazáshoz az Azure Portalon

Az Application Insights-összekötő paneleket úgy alakították ki, hogy lehetővé tegyék a kiválasztott Application Insights-alkalmazáshoz való elfordulást *az Azure Portal használatakor.* A megoldás magas szintű figyelési platformként használható, amely segít az alkalmazások hibaelhárításának. Ha bármelyik csatlakoztatott alkalmazásban megjelenik egy lehetséges probléma, a Log Analytics-keresésben részletezheti azt, vagy közvetlenül az Application Insights alkalmazáshoz is elvezethet.

A kimutatáshoz kattintson az egyes sorok végén megjelenő három pontra (**...**), és válassza a **Megnyitás az Application Insights programban**lehetőséget.

>[!NOTE]
>**Az Application Insights megnyitása** nem érhető el az Azure Portalon.

![Megnyitás az Application Insights ban](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Mintával korrigált adatok

Az Application Insights *[mintavételi korrekciót](../../azure-monitor/app/sampling.md)* biztosít a telemetriai forgalom csökkentése érdekében. Ha engedélyezi a mintavételt az Application Insights-alkalmazásban, az Application Insightsban és a Log Analytics-ben tárolt bejegyzések száma csökken. Míg az adatok konzisztenciája megmarad az **Application Insights-összekötő** lapon és perspektívák, manuálisan kell kijavítani a mintavételezett adatokat az egyéni lekérdezések.

Íme egy példa a mintavételi korrekcióra egy naplókeresési lekérdezésben:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

A **Mintavételezett számláló** mező minden bejegyzésben jelen van, és a bejegyzés által képviselt adatpontok számát mutatja. Ha bekapcsolja a mintavételezést az Application Insights-alkalmazásban, **a mintavételezett számláló** 1-nél nagyobb. Az alkalmazás által létrehozott bejegyzések tényleges számának megszámlálásához összegezze a **Mintavételezett számláló** mezőket.

Mintavételezési hatással van csak a bejegyzések száma, hogy az alkalmazás által generált. Nem kell kijavítania a metrikus mezők, például **a RequestDuration** vagy az **AvailabilityDuration** mintavételezését, mert ezek a mezők a képviselt bejegyzések átlagát mutatják.

## <a name="input-data"></a>Bemeneti adatok

A megoldás a következő telemetriai típusú adatokat kapja a csatlakoztatott Application Insights-alkalmazásoktól:

- Rendelkezésre állás
- Kivételek
- Kérelmek
- Oldalmegtekintések – Ahhoz, hogy a munkaterület megkapja az oldalmegtekintéseket, konfigurálnia kell az alkalmazásokat, hogy összegyűjtsék ezeket az adatokat. További információ: [Oldalmegtekintések](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Egyéni események – Ahhoz, hogy a munkaterület egyéni eseményekfogadásához konfigurálja az alkalmazásokat az adatok gyűjtésére. További információ: [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Az adatokat a Log Analytics az Application Insights tól kap, amint azok elérhetővé válnak.

## <a name="output-data"></a>Kimeneti adatok

Az *ApplicationInsights* *típusával* rendelkező rekord jön létre az egyes bemeneti adatok típusához. Az ApplicationInsights-rekordok tulajdonságai a következő szakaszokban láthatók:

### <a name="generic-fields"></a>Általános mezők

| Tulajdonság | Leírás |
| --- | --- |
| Típus | ApplicationInsights |
| ÜgyfélIP-címe |   |
| TimeGenerated | A rekord létrehozásának időpontja |
| ApplicationId | Az Application Insights alkalmazás instrumentation kulcsa |
| ApplicationName | Az Application Insights alkalmazás neve |
| RoleInstance | Kiszolgálóállomás azonosítója |
| DeviceType | Ügyféleszköz |
| Képernyőfelbontás |   |
| Kontinens | Az a kontinens, ahonnan a kérelem érkezett |
| Ország | Az az ország/régió, ahonnan a kérelem érkezett |
| Tartomány | Az a tartomány, állam vagy területi beállítás, ahonnan a kérelem származik |
| Város | Az a város vagy város, ahonnan a kérelem érkezett |
| isSzintetikus | Azt jelzi, hogy a kérelmet felhasználó vagy automatikus metódus hozta-e létre. Igaz = automatizált módszer vagy hamis = felhasználó által generált |
| Mintavételi ráta | A portálra küldött SDK által létrehozott telemetriai adatok százalékos aránya. Tartomány 0.0-100.0. |
| Mintavételi szám | 100/(Mintavételi ráta). Például 4&gt; = 25% |
| Hitelesített | Igaz vagy hamis |
| OperationID azonosító | Az azonos műveletazonosítóval rendelkező elemek kapcsolódó elemekként jelennek meg a portálon. Általában a kérelem azonosítója |
| ParentOperationID azonosító | A szülőművelet azonosítója |
| OperationName |   |
| Munkamenet | GUID azonosító a kérés létrehozásának helye szerinti munkamenet egyedi azonosítására |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Rendelkezésre állás-specifikus mezők

| Tulajdonság | Leírás |
| --- | --- |
| TelemettryType típus | Rendelkezésre állás |
| AvailabilityTestname | A webes teszt neve |
| AvailabilityRunLocation | A http-kérelem földrajzi forrása |
| AvailabilityResult (Elérhetőségeredménye) | A webes teszt sikeres eredményét jelzi |
| AvailabilityMessage | A webes teszthez csatolt üzenet |
| Elérhetőségi szám | 100/(Mintavételi arány). Például 4&gt; = 25% |
| DataSizeMetricValue érték | 1.0 vagy 0.0 |
| DataSizeMetricCount | 100/(Mintavételi arány). Például 4&gt; = 25% |
| Elérhetőségidőtartama | A webes teszt időtartamának ideje ezredmásodpercben |
| AvailabilityDurationCount (AvailabilityDurationCount) | 100/(Mintavételi arány). Például 4&gt; = 25% |
| AvailabilityValue érték |   |
| AvailabilityMetricCount (ElérhetőségimetricCount) |   |
| ElérhetőségId azonosító | Egyedi GUID a webes teszthez |
| ElérhetőségIdőbélyeg | A rendelkezésre állási teszt pontos időbélyege |
| AvailabilityDurationMin | A mintavételezett rekordok esetében ez a mező a megjelenített adatpontok minimális webes tesztidőtartamát (ezredmásodperc) mutatja. |
| ElérhetőségIdőtartamMax | A mintavételezett rekordok esetében ez a mező a webes teszt maximális időtartamát (ezredmásodperc) mutatja a képviselt adatpontokhoz. |
| AvailabilityDurationstdDev | A mintavételezett rekordok esetében ez a mező a megjelenített adatpontok összes webes tesztidőtartama (ezredmásodperc) közötti szórást mutatja. |
| ElérhetőségMin |   |
| ElérhetőségMax |   |
| ElérhetőségStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Kivételspecifikus mezők

| Típus | ApplicationInsights |
| --- | --- |
| TelemettryType típus | Kivétel |
| ExceptionType (Kivételtípus) | A kivétel típusa |
| ExceptionMethod (Kivételmódszer) | A kivételt létrehozó módszer |
| ExceptionAssembly | A kódösszeállítás tartalmazza a keretrendszert és a verziót, valamint a nyilvános kulcs tokenjét |
| Kivételcsoport | A kivétel típusa |
| ExceptionHandledAt | Azt a szintet jelzi, amely a kivételt kezelte |
| Kivételszáma | 100/(Mintavételi arány). Például 4&gt; = 25% |
| ExceptionMessage | A kivétel üzenete |
| ExceptionStack | A kivétel teljes kötege |
| ExceptionHasstack | Igaz, ha a kivételnek van halma |



### <a name="request-specific-fields"></a>Kérésspecifikus mezők

| Tulajdonság | Leírás |
| --- | --- |
| Típus | ApplicationInsights |
| TelemettryType típus | Kérés |
| ResponseCode (Válaszkód) | Ügyfélnek küldött HTTP-válasz |
| Kéréssiker | Sikeresvagy sikertelen. TRUE (igaz) vagy FALSE (hamis). |
| RequestID | Azonosító a kérelem egyedi azonosításához |
| Kérelemnév | GET/POST + URL alap |
| RequestDuration (Kérésidőtartama) | A kérelem időtartamának időtartama másodpercben |
| URL-cím | A kérelem URL-címe az állomást nem tartalmazza |
| Gazdagép | Webkiszolgáló-állomás |
| URLBase | A kérelem teljes URL-címe |
| ApplicationProtocol | Az alkalmazás által használt protokoll típusa |
| Kérésszáma | 100/(Mintavételi arány). Például 4&gt; = 25% |
| RequestDurationCount (Kérésidőtartam-szám | 100/(Mintavételi arány). Például 4&gt; = 25% |
| RequestDurationMin | A mintavételezett rekordok esetében ez a mező a megjelenített adatpontok minimális kérelemidőtartamát (ezredmásodperc) jeleníti meg. |
| RequestDurationMax | A mintavételezett rekordok esetében ez a mező a megjelenített adatpontok maximális kérelemidőtartamát (ezredmásodperc) jeleníti meg. |
| RequestDurationStdDev | A mintavételezett rekordok esetében ez a mező a képviselt adatpontok összes kérelemidőtartama (ezredmásodperc) közötti szórást mutatja. |

## <a name="sample-log-searches"></a>Naplókeresési minták

Ez a megoldás nem rendelkezik az irányítópulton megjelenő mintanapló-kereséssel. Azonban a minta napló keresési lekérdezések leírásokkal jelennek meg az [Application Insights-összekötő adatainak megtekintése](#view-application-insights-connector-information) szakaszban.

## <a name="removing-the-connector-with-powershell"></a>Az összekötő eltávolítása a PowerShell használatával
Az OMS-portál eprecációjával nincs mód a meglévő kapcsolatok konfigurálására és eltávolítására a portálról. A meglévő kapcsolatokat a következő PowerShell-parancsfájllal távolíthatja el. A művelet végrehajtásához a munkaterület tulajdonosának vagy közreműködőjének kell lennie, és az Application Insights-erőforrás olvasójának kell lennie.

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

Az alkalmazások listáját a következő PowerShell-parancsfájl használatával, amely meghívja a REST API-hívás. 

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
Ez a parancsfájl egy tulajdonosi hitelesítési jogkivonatot igényel az Azure Active Directory elleni hitelesítéshez. A jogkivonat beolvasásának egyik módja a [REST API dokumentációs webhelyének egy cikke.](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate) Kattintson **a Kipróbálás gombra,** és jelentkezzen be Azure-előfizetésébe. A tulajdonosi jogkivonatot a **kérelem előnézetéből** másolhatja az alábbi képen látható módon.


![Tulajdonosi token](media/app-insights-connector/bearer-token.png)


Az alkalmazások listáját naplólekérdezéssel is lekérheti:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>További lépések

- A [Naplókeresés](../../azure-monitor/log-query/log-query-overview.md) segítségével megtekintheti az Application Insights-alkalmazások részletes adatait.
