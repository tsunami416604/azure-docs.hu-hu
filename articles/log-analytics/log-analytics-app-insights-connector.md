---
title: "Az alkalmazásadatok Azure Application Insights megtekintése |} Microsoft Docs"
description: "Az Application Insights-összekötő megoldás segítségével teljesítménnyel kapcsolatos problémák diagnosztizálásához és megérteni a felhasználók mit az alkalmazással, ha a figyelt az Application insights szolgáltatással."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: magoedte
ms.openlocfilehash: 1556e91710990351d6723325789201afa99b1943
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="application-insights-connector-management-solution-preview"></a>Application Insights-összekötő felügyeleti megoldás (előzetes verzió)

![Application Insights szimbólum](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

Az alkalmazások Insights-összekötő a megoldással teljesítménnyel kapcsolatos problémák diagnosztizálásához és megérteni a felhasználók mit az alkalmazással, ha a számítógép megfigyelés alatt áll a [Application Insights](../application-insights/app-insights-overview.md). Az Application Insights fejlesztők látható azonos telemetriai nézeteinek Naplóelemzési érhetők el. Azonban ha Log Analytics integrálja az Application Insights-alkalmazásokat, látható-e az alkalmazások jobb lesz, azzal, hogy a művelet és az alkalmazások adatainak egy helyen. Az azonos nézetek rendelkező segítségével az alkalmazásfejlesztők együttműködni. A közös nézetek csökkentheti a idő észlelésére és az alkalmazás és a platform-problémák megoldása.

A megoldás használata esetén is:

- Az Application Insights alkalmazások megtekintése az egyik helyen, akkor is, ha a különböző Azure-előfizetések
- Az alkalmazásadatok infrastruktúra adatainak
- Alkalmazás-adatok ábrázolása a naplófájl-keresési nézetből
- Forgáspont Naplóelemzési adatokból az Application Insights-alkalmazásban, az Azure-portálon

## <a name="connected-sources"></a>Összekapcsolt források

Legtöbb egyéb Naplóelemzési megoldásoktól eltérően nem összegyűjtött adatok az Application Insights-összekötő az ügynök. A megoldás által használt összes adatok származnak, közvetlenül az Azure-ból.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](log-analytics-windows-agent.md) | Nem | A megoldás a Windows-ügynökök nem gyűjt adatokat. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem | A megoldás a Linux-ügynökök nem gyűjt adatokat. |
| [SCOM felügyeleti csoport](log-analytics-om-agents.md) | Nem | A megoldás nem gyűjt adatokat az ügynökök a csatlakoztatott SCOM felügyeleti csoport. |
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | A megoldás nem az Azure storage nem gyűjtemény adatait. |

## <a name="prerequisites"></a>Előfeltételek

- Application Insights-összekötő információk eléréséhez Azure-előfizetéssel kell rendelkeznie
- Rendelkeznie kell legalább egy konfigurált Application Insights-erőforrást.
- A tulajdonos vagy közreműködő az Application Insights-erőforrás kell lennie.

## <a name="configuration"></a>Konfiguráció

1. Engedélyezze az Azure Web Apps Analytics megoldást a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
2. Az OMS-portálon kattintson **beállítások** &gt; **adatok** &gt; **Application Insights**.
3. A **válasszon egy előfizetést**, válasszon egy előfizetést, amely rendelkezik az Application Insights-erőforrások majd a **alkalmazásnév**, válassza ki egy vagy több alkalmazást.
4. Kattintson a **Save** (Mentés) gombra.

Körülbelül 30 percet, az adatok elérhetővé válik, és az Application Insights csempe frissül az adatok, például az alábbi képen:

![Az Application Insights csempéje](./media/log-analytics-app-insights-connector/app-insights-tile.png)

Ne feledje egyéb szempontok:

- Csak az Application Insights alkalmazások hozzákapcsolhatja egy Naplóelemzési munkaterület.
- Csak társíthatja [Basic vagy vállalati Application Insights-erőforrások](https://azure.microsoft.com/pricing/details/application-insights) szolgáltatáshoz. A Naplóelemzési ingyenes szint is használhatja.

## <a name="management-packs"></a>Felügyeleti csomagok

Ez a megoldás a csatlakoztatott felügyeleti csoportok nem telepíti a felügyeleti csomagok.

## <a name="use-the-solution"></a>A megoldás használja

A következő szakaszok ismertetik, hogyan használhatja a paneleket az Application Insights-irányítópulton látható megtekintéséhez és alkalmazásokból származó adatok kezeléséhez.

### <a name="view-application-insights-connector-information"></a>Application Insights-összekötő adatainak megtekintése

Kattintson a **Application Insights** csempére kattintva nyissa meg a **Application Insights** irányítópult a következő paneleken megjelenítéséhez.

![Alkalmazás irányítópult](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Alkalmazás irányítópult](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

Az irányítópult tartalmaz a paneleken a táblázatban látható. Minden egyes panel adott panelhez feltételeknek, a megadott hatókör és időtartomány legfeljebb 10 elemeket sorolja fel. A napló keresési, amely visszaadja az összes rekord kattintva futtathatja **láthatja az összes** alján a panelről, vagy a panel fejléc kattint.

[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| **Oszlop** | **Leírás** |
| --- | --- |
| Alkalmazások – alkalmazások száma | Az alkalmazások számát mutatja az alkalmazás-erőforrásokat. Is megjeleníti az alkalmazás nevét és minden, az alkalmazás rekordok száma. Kattintson a napló keresése futtatásához <code>Type=ApplicationInsights &#124; measure sum(SampledCount) by ApplicationName</code> <br><br>  Kattintson az alkalmazás nevét, az alkalmazás, amely tartalmazza az alkalmazás rekordok száma állomás, telemetriai típus szerint rekordok és minden adat (az elmúlt nap során alapján) típus szerint a napló keresés futtatásához. |
| Adatmennyiség – küldő adatokat tároló | A számítógépen, amely adatokat küldenek a számát mutatja. A számítógépen és minden állomás számára rekordok száma is tartalmazza. Kattintson a napló keresése futtatásához <code>Type=ApplicationInsights &#124; measure sum(SampledCount) by Host</code> <br><br> Kattintson a napló keressen rá a fogadó alkalmazás rekordok száma állomás, telemetriai típus szerint rekordok és minden adat (az elmúlt nap során alapján) típus szerint megjelenítő futtatásához a számítógép neve. |
| Rendelkezésre állás – eredmények webtesztben. | A perecdiagram webteszteredmények, sikeres, vagy sikertelen jelző jeleníti meg. Kattintson a diagram futtatásához napló keresése <code>Type=ApplicationInsights TelemetryType=Availability &#124; measure sum(SampledCount) by AvailabilityResult</code> <br><br> Az eredményben műveletek és az összes tesztre vonatkozóan hibák száma. Azt az elmúlt percben forgalom minden webalkalmazások jelennek meg. Kattintson a napló keresés sikertelen webteszt részletes adatait megjelenítő megtekintéséhez egy alkalmazásnevet. |
| Kiszolgálói kérelmek – kérések száma óránként | A kiszolgáló kérelem a különböző alkalmazások óránként sor diagramot ábrázol. Egy sort a diagramon az első 3 alkalmazások pontnál kérelmek fogadásának ideje megtekintéséhez mutasson. A fogadó kérések és a kérelmek száma a kijelölt időszak alkalmazások listáját is tartalmazza. <br><br>Kattintson a diagramot úgy, hogy a naplóban keresse meg futtatni <code>Type=ApplicationInsights TelemetryType=Request &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> , amely a kiszolgálói kérelmek óránként, a különböző alkalmazások részletesebb vonaldiagram jeleníti meg. <br><br> Egy alkalmazás futtatásához a naplóban keresse meg a listában kattintson <code>Type=ApplicationInsights  ApplicationName=yourapplicationname  TelemetryType=Request</code> , amely listáját jeleníti meg a kéréseket, a diagramok kérelmek ideje és kérelem időtartama alatt és a kérelem listája válaszkódot.   |
| Hiba – sikertelen kérések száma óránként | A hibás alkalmazást kérelmek óránként sor diagramot ábrázol. A diagram a sikertelen kérelmek egy pont a legfontosabb 3 alkalmazást időben mutasson. A sikertelen kérelmek száma az egyes alkalmazások listáját is tartalmazza. Kattintson a napló keresése futtatásához a diagram <code>Type=ApplicationInsights TelemetryType=Request  RequestSuccess = false &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> , amely bemutatja, hogy a hibás alkalmazást kérelmek részletesebb vonaldiagram. <br><br>Futtassa a naplóban keresse meg a lista elemeire <code>Type=ApplicationInsights ApplicationName=yourapplicationname TelemetryType=Request  RequestSuccess=false</code> , hogy megjelenítése sikertelen kérelmek, a diagramok sikertelen kérelmek ideje és kérelem időtartam és a sikertelen kérelmek válaszkódot listáját. |
| Kivételek – a kivételek száma óránként | Kivételek száma óránként sor diagramot ábrázol. A diagram a kivételeket a ponton a legfontosabb 3 alkalmazást időben mutasson. Az egyes kivételek száma az alkalmazások listáját is tartalmazza. Kattintson a napló keresése futtatásához a diagram <code>Type=ApplicationInsights TelemetryType=Exception &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> kivételek részletesebb hivatkozás diagramot ábrázol, amely. <br><br>Futtassa a naplóban keresse meg a lista elemeire <code>Type=ApplicationInsights  ApplicationName=yourapplicationname TelemetryType=Exception</code> , amelyek kivételek, időt és a sikertelen kérelmek keresztül kivételekhez diagramok és a kivétel típusainak listáját listáját tartalmazza.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Az Application Insights perspektíva a keresési napló megtekintése

Az irányítópult valamelyik elemre kattintva, tekintse meg az Application Insights perspektíva keresési látható. A perspektíva biztosít egy kiterjesztett képi megjelenítés, a kiválasztott telemetriai adatok típusa alapján. Igen, a képi megjelenítés a tartalmi változások különféle telemetriai esetében.

Ha kattint bárhol az alkalmazások panelen, megjelenik az alapértelmezett **alkalmazások** szempontjából.

![Application Insights alkalmazások perspektíva](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

A terv a kiválasztott alkalmazás áttekintését mutatja.

A **rendelkezésre állási** panelt jeleníti meg, különböző perspektíva ahol webteszteredmények és a kapcsolódó sikertelen kérelmek láthatók.

![Application Insights rendelkezésre állási perspektíva](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

Kattintva bárhol a **kiszolgálói kérelmek** vagy **hibák** paneleken, perspektíva összetevőit módosítja, amely kérelmekkel kapcsolatos képi megjelenítés Önnek.

![Application Insights hibák panel](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

Ha kattint bárhol a **kivételek** panelen láthatja kivételek igényeinek megfelelő képi megjelenítés.

![Application Insights kivételek panel](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

Függetlenül attól, hogy valami valamelyik gombra a **Application Insights-összekötő** irányítópult, melyhez a **keresési** lapon, az Application Insights az adat tartalmazza az alkalmazás visszaadó lekérdezés Elemzések szempontjából. Ha az Application Insights adatokat, például egy  **&#42;**  lekérdezés is a perspektíva fülre, például az alábbi képen látható:

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

Perspektíva összetevők attól függően, hogy a keresési lekérdezés frissítése. Ez azt jelenti, hogy az eredményeket szűrheti bármely keresés mezője, amely lehetővé teszi a a adatai használatával:

- Az alkalmazások
- Egyetlen kiválasztott alkalmazás
- Az alkalmazások azon csoportját

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Forgáspont alkalmazásokhoz az Azure-portálon

Application Insights-összekötő paneleken úgy tervezték, hogy lehetővé teszik a kijelölt Application Insights alkalmazásba forgáspont *használatakor az OMS-portálon*. A megoldás egy magas szintű felügyeleti platform, amely segít az alkalmazás hibaelhárítása is használhatja. Amikor megjelenik egy potenciális problémát sem az egymáshoz kapcsolódó alkalmazások, vagy részletezési bele a Naplóelemzési keresési is, vagy akkor is forgáspont közvetlenül az Application Insights alkalmazás számára.

Forgáspont, kattintson a három pontot (**...** ), amely minden sor végén jelenik meg, és válassza ki **nyissa meg az Application Insightsban**.

>[!NOTE]
>**Nyissa meg az Application Insightsban** nem érhető el az Azure portálon.

![Nyissa meg az Application Insightsban](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>A minta-javítani adatok

Az Application Insights biztosít  *[javítási mintavételi](../application-insights/app-insights-sampling.md)*  telemetriai forgalom csökkentése érdekében. Az Application Insights-alkalmazás mintavételi engedélyezi, az Application Insights és a Naplóelemzési tárolt bejegyzések csökkentett számos nyílik meg. Amíg az adatok konzisztenciájának megőrződik a **Application Insights-összekötő** perspektívák, és a lap a egyéni lekérdezések általában elhárítja mintaadatokat manuálisan.

Itt látható egy példa a naplófájl-keresési lekérdezés mintavételi javítása:

```
Type=ApplicationInsights | measure sum(SampledCount) by TelemetryType
```

A **mintát száma** mező található összes bejegyzést, és a bejegyzést képviselő adatpontok számát jeleníti meg. Ha bekapcsolja a mintavétel az Application Insights alkalmazás **mintát száma** nagyobb, mint 1. Sum bejegyzéseit, amelyek állít elő, az alkalmazás tényleges száma, hogy a **mintát száma** mezőket.

Mintavételi hatással van a bejegyzéseket, amelyek állít elő, az alkalmazás csak a teljes száma. Javítsa ki a mintavételi vonatkozó Átjárómetrika mezők, például nem kell **RequestDuration** vagy **AvailabilityDuration** mivel ezek a mezők megjelenítése képviselt bejegyzések átlagos.

## <a name="input-data"></a>A bemeneti adatok

A megoldás a következő telemetriai típusú adatokat fogad az Application Insights csatlakoztatott alkalmazásba:

- Rendelkezésre állás
- Kivételek
- Kérelmek
- Lapmegtekintések – munkaterület fogadásához Lapmegtekintések, konfigurálnia kell összegyűjteni ezeket az információkat az alkalmazások. További információkat lásd: [PageViews](../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Egyéni események – egyéni események fogadásához munkaterület konfigurálnia kell az alkalmazások összegyűjteni ezeket az információkat. További információkat lásd: [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Amint az elérhetővé válik adatok az Application Insights szolgáltatáshoz érkezik.

## <a name="output-data"></a>Kimeneti adatok

Egy rekordot egy *típus* a *ApplicationInsights* jön létre az egyes bemeneti adatokat. ApplicationInsights rögzíti az alábbiakban megtekintheti jellemzőkkel rendelkezik:

### <a name="generic-fields"></a>Általános mezők

| Tulajdonság | Leírás |
| --- | --- |
| Típus | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | A rekord idő |
| ApplicationId | Az Application Insights alkalmazás Instrumentation kulcs |
| ApplicationName | Az Application Insights neve alkalmazás |
| RoleInstance | Kiszolgáló állomás azonosítója |
| DeviceType | Ügyféleszközök |
| ScreenResolution |   |
| Kontinens | A kérelem származási helyének kontinensen |
| Ország | A kérelem származási helyének ország |
| Tartomány | Tartomány, állapot vagy a kérelem származási helyének területi beállítás |
| Város | Település a kérelem származási helyének |
| isSynthetic | Azt jelzi, hogy a felhasználó által vagy automatikus módon a kérelmet létrehozták. = Igaz a felhasználók által létrehozott vagy false = automatikus módszer |
| Érvénytelen a SamplingRate | A portál küldött SDK által generált telemetriai százalékát. Tartomány 0,0-100.0. |
| SampledCount | 100/(SamplingRate). Például, 4 =&gt; 25 %-át |
| IsAuthenticated | Igaz vagy hamis |
| OperationID | Ugyanazt a műveletet azonosító sablonobjektumhoz kapcsolódó elemként láthatók a portálon rendelkező elemek. Általában a kérelem azonosítója |
| ParentOperationID | A szülő művelet azonosítója |
| OperationName |   |
| SessionId | A munkamenet, amelyen a kérelmet létrehozták egyedi azonosításához GUID |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Rendelkezésre állási-specifikus mezők

| Tulajdonság | Leírás |
| --- | --- |
| TelemetryType | Rendelkezésre állás |
| AvailabilityTestName | A webalkalmazás-teszt neve |
| AvailabilityRunLocation | A http-kérelem földrajzi forrás |
| AvailabilityResult | A webalkalmazás-teszt sikeres eredményét jelzi. |
| AvailabilityMessage | Az üzenet csatolva a webtesztet |
| AvailabilityCount | 100 /(Sampling Rate). Például, 4 =&gt; 25 %-át |
| DataSizeMetricValue | 1.0 vagy 0,0-nál |
| DataSizeMetricCount | 100 /(Sampling Rate). Például, 4 =&gt; 25 %-át |
| AvailabilityDuration | Idő, a webes vizsgálat időtartama ezredmásodpercben |
| AvailabilityDurationCount | 100 /(Sampling Rate). Például, 4 =&gt; 25 %-át |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | A webteszt egyedi GUID azonosítója |
| AvailabilityTimestamp | Az elérhetőségi teszt pontos időbélyegzője |
| AvailabilityDurationMin | A mintában szereplő rekordok ebben a mezőben látható a képviselt adatpontok minimális web-vizsgálat időtartama (ezredmásodpercben) |
| AvailabilityDurationMax | A mintában szereplő rekordok ebben a mezőben látható a képviselt adatpontok maximális web-vizsgálat időtartama (ezredmásodpercben) |
| AvailabilityDurationStdDev | A mintában szereplő rekordok ebben a mezőben látható a szórás minden webes teszt időtartamát (ezredmásodperc) képviselt adatok pontok között |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Kivétel-specifikus mezők

| Típus | ApplicationInsights |
| --- | --- |
| TelemetryType | Kivétel |
| ExceptionType | A kivétel típusa |
| ExceptionMethod | A módszert, amelyet a kivételt hoz létre |
| ExceptionAssembly | A keretrendszer verzióját, valamint a nyilvánoskulcs-kivonatnak a szerelvény tartalmazza |
| ExceptionGroup | A kivétel típusa |
| ExceptionHandledAt | A kivétel kezelve szintjét jelzi |
| ExceptionCount | 100 /(Sampling Rate). Például, 4 =&gt; 25 %-át |
| ExceptionMessage | A kivétel üzenetét |
| ExceptionStack | A kivétel teljes verem |
| ExceptionHasStack | IGAZ, ha kivétel a verem |



### <a name="request-specific-fields"></a>Kérelem-specifikus mezők

| Tulajdonság | Leírás |
| --- | --- |
| Típus | ApplicationInsights |
| TelemetryType | Kérés |
| ResponseCode | Az ügyfélnek küldött HTTP-válasz |
| RequestSuccess | Azt jelzi, sikeres vagy sikertelen volt. IGAZ vagy hamis értéket. |
| RequestID | A kérelem egyedi azonosításához azonosítója |
| RequestName | GET/POST + alap URL-je |
| RequestDuration | A kérelem időtartam másodpercben |
| URL-cím | A kérelem nem többek között a gazdagépre URL-címe |
| Gazdagép | Web server állomás |
| URLBase | A kérelem teljes URL-címe |
| ApplicationProtocol | Az alkalmazás által használt protokoll típusát |
| RequestCount | 100 /(Sampling Rate). Például, 4 =&gt; 25 %-át |
| RequestDurationCount | 100 /(Sampling Rate). Például, 4 =&gt; 25 %-át |
| RequestDurationMin | A mintában szereplő rekordok ebben a mezőben látható a minimális kérelem időtartama (ezredmásodpercben) a képviselt adatpontok számára. |
| RequestDurationMax | A mintában szereplő rekordok ebben a mezőben a kérelmek maximális időtartama (ezredmásodpercben) látható a képviselt adatpontok |
| RequestDurationStdDev | A mintában szereplő rekordok ebben a mezőben a szórás közötti összes kérelem időtartama (ezredmásodpercben) látható a képviselt adatpontok |

## <a name="sample-log-searches"></a>Naplókeresési minták

Ez a megoldás nem rendelkeznek egy minta napló keresések az irányítópulton látható. Azonban leírását a napló keresési lekérdezés megjelennek-e a [nézet Application Insights-összekötő információk](#view-application-insights-connector-information) szakasz.

## <a name="next-steps"></a>További lépések

- Használjon [naplófájl-keresési](log-analytics-log-searches.md) az Application Insights-alkalmazásokhoz részletes információk megtekintéséhez.
