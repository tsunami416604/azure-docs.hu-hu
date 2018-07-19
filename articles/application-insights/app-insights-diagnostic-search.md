---
title: Keresés használata az Azure Application Insights |} A Microsoft Docs
description: Keresés és szűrés nyers által küldött telemetriát a webalkalmazás.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: mbullwin
ms.openlocfilehash: 1a343e238662393995404b8e4c705cf799866855
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39137376"
---
# <a name="using-search-in-application-insights"></a>Az Application Insights keresés használata
Keresés az egyik szolgáltatása [Application Insights](app-insights-overview.md) keresse meg és Fedezze fel a telemetriai elem, például a lapmegtekintések, kivételek, vagy webes kérelmek küldéséhez használt. És megtekintheti a nyomkövetési naplók és eseményeket, amelyek van kódolva.

(Az adatok összetettebb lekérdezésekhez használja [Analytics](app-insights-analytics-tour.md).)

## <a name="where-do-you-see-search"></a>Ahol látható keresési?

### <a name="in-the-azure-portal"></a>Az Azure Portalon

Diagnosztikai keresés explicit módon is megnyithatja az alkalmazás az Application Insights áttekintése panelen:

![Nyissa meg a diagnosztikai keresés](./media/app-insights-diagnostic-search/001.png)

![Képernyőkép a diagnosztikai keresés diagramok](./media/app-insights-diagnostic-search/002.png)

Diagnosztikai keresés törzse a lista lapon telemetriai elem - kiszolgálói kérelmek, a nézeteket, egyéni eseményeket, amelyek van kódolva, és így tovább. A lista elején van egy összefoglaló táblázat eseményszámok idővel.

Kattintson a frissítés gombra új események beolvasása.

### <a name="in-visual-studio"></a>Visual Studióban

A Visual Studióban is van egy Application Insights keresés ablakában. Ez leginkább hasznos hibakeresése az alkalmazás által generált telemetriai események megjelenítése. De azt is megjelenítheti az események gyűjtését a közzétett alkalmazást az Azure Portalon.

A Visual Studióban nyissa meg a keresési ablakban:

![A Visual Studio Application Insights keresés megnyitása](./media/app-insights-diagnostic-search/32.png)

A keresési ablak a webes portálhoz hasonló jellemzőkkel rendelkezik:

![A Visual Studio Application Insights keresőablakában](./media/app-insights-diagnostic-search/34.png)

A nyomon követheti a művelet lapon érhető el egy kérelmet, vagy egy oldal nézet megnyitása. Egy "művelet" egyetlen kérelem vagy oldalmegtekintés nézet a kapcsolódó események sorozata. Függőségi hívások, a kivételek, a nyomkövetési naplókat és az egyéni események például lehet az egyetlen művelet részeként. A művelet nyomon követése lap grafikonját ütemét és ezeket az eseményeket a kérelem vagy oldalmegtekintés nézet viszonyítva időtartama. 

## <a name="inspect-individual-items"></a>Vizsgálja meg az egyes elemek

Jelölje ki bármely szereplő telemetriai elem kulcsmezők megtekintéséhez és a kapcsolódó elemek.

![Képernyőkép egy egyéni függőség-kérelem](./media/app-insights-diagnostic-search/003.png)

Ez elindítja a végpontok közötti tranzakció részletei nézet:

![Képernyőfelvétel a végpontok közötti tranzakció részletei nézet.](./media/app-insights-diagnostic-search/004.png)

## <a name="filter-event-types"></a>Eseménytípusok szűrése
A szűrő panel megnyitásához, és válassza ki az eseménytípusok meg szeretné tekinteni. (Ha később szeretné visszaállítani a szűrőket, amellyel megnyitotta a panelt, kattintson az Alaphelyzet.)

![Válasszon szűrőt, és válassza ki a telemetriai adatok típusa](./media/app-insights-diagnostic-search/02-filter-req.png)

Az esemény-típusok a következők:

* **Nyomkövetési** - [diagnosztikai naplók](app-insights-asp-net-trace-logs.md) TrackTrace, log4Net, NLog és System.Diagnostic.Trace hívások többek között.
* **Kérelem** -lapok, parancsfájlok, képek, stílusfájljait és adatok többek között a kiszolgálóalkalmazás által fogadott HTTP-kérelmekre. Ezeket az eseményeket létrehozásához a kérések és válaszok áttekintés diagramokon szolgálnak.
* **Oldal nézet** - [a webes ügyfél által küldött telemetriai adatok](app-insights-javascript.md)oldal nézet jelentések létrehozására szolgál. 
* **Egyéni esemény** – ha annak érdekében, hogy a TrackEvent() hívásainak beszúrt [figyelheti](app-insights-api-custom-events-metrics.md), itt megkeresheti azokat.
* **Kivétel** – nem kezelt [kivételek a kiszolgálón](app-insights-asp-net-exceptions.md), valamint azokat, amelyeket a trackexception() hívásait használatával jelentkezik.
* **Függőségi** - [a kiszolgálóalkalmazás hívásait](app-insights-asp-net-dependencies.md) más szolgáltatásokba, például REST API-k vagy adatbázisok és az AJAX-hívások a a [ügyfélalkalmazás](app-insights-javascript.md).
* **Rendelkezésre állási** -eredményeit [rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>A tulajdonságértékek szűrése
Szűrhet események tulajdonságaik értékeiből. A rendelkezésre álló tulajdonságok a kiválasztott eseménytípusok függ. 

Ha például kérelmek egy adott válaszkóddal választhat. 

![Bontsa ki a tulajdonságot, és válasszon egy értéket](./media/app-insights-diagnostic-search/03-response500.png)

Az azonos hatása, mint minden érték kiválasztása nem egy adott tulajdonság értékét nem. Vált ki a tulajdonságon alapuló szűrőt.

### <a name="narrow-your-search"></a>A keresés szűkítéséhez
Figyelje meg, hogy a számlálás szűrőértékeket jobb megjelenítése hány előfordulások létezik az aktuális szűrt csoportjának szerepelnek. 

Ebben a példában nincs bejelölve, hogy a "Rpt/alkalmazottak" kérelem eredménye a legtöbb, a "500" hibák:

![Bontsa ki a tulajdonságot, és válasszon egy értéket](./media/app-insights-diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Az ugyanahhoz a tulajdonsághoz események
Keresse meg az azonos tulajdonság értéke az összes elemet:

![Kattintson a jobb gombbal egy tulajdonság](./media/app-insights-diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Az adatok keresése

> [!NOTE]
> Összetettebb lekérdezéseket írni, nyissa meg a [ **Analytics** ](app-insights-analytics-tour.md) a keresés panel tetején.
> 

A tulajdonságértékek bármelyikét feltételek kereshet. Ez különösen hasznos adatszolgáltatót [egyéni események](app-insights-api-custom-events-metrics.md) és a tulajdonságértékek. 

Érdemes egy tartományt, mint keresések időbeli rövidebb széles gyorsabbak idő beállítása. 

![Nyissa meg a diagnosztikai keresés](./media/app-insights-diagnostic-search/appinsights-311search.png)

Teljes szavakat, nem a karakterláncrész keresése. Speciális karakterek adni tegye idézőjelek közé.

| sztring | van *nem* által észlelt | Ezek találja |
| --- | --- | --- |
| HomeController.About |kezdőlap<br/>tartományvezérlő<br/>ki | homecontroller<br/>névjegy<br/>"homecontroller.about"|
|Egyesült Államok|UNI<br/>TED|Egyesült<br/>állapotok<br/>Egyesült Államok és<br/>"Egyesült Államok"

Az alábbiakban a keresési kifejezéseket is használhatja:

| Mintalekérdezés | Következmény |
| --- | --- |
| `apple` |Az időtartomány, amelynek mezői tartalmazzák az "apple" szó az összes esemény keresése |
| `apple AND banana` |Mindkét szót tartalmazó események keresése. Használja a tőke "és" nem "és". |
| `apple OR banana`<br/>`apple banana` |Keresse meg vagy szót tartalmazó események. Használja a "Vagy", sem "vagy".<br/>Rövid űrlapot. |
| `apple NOT banana` |Események, amelyek tartalmaznak egy szót, míg a másikon nem található. |

## <a name="sampling"></a>Mintavételezés
Ha az alkalmazása sok telemetriát hoz létre (és használ, akkor a verzió az ASP.NET SDK 2.0.0-beta3 vagy újabb), az adaptív mintavételezési modul automatikusan csökkenti a által csak töredékeket az események elküldése a portálra küldött kötetet. Azonban az azonos kéréshez kapcsolódó eseményeket kiválasztva, vagy a jelölésük, úgy, hogy lehessen mozogni a kapcsolódó események között. 

[Ismerkedés a mintavételezéssel](app-insights-sampling.md).

## <a name="create-work-item"></a>Munkaelem létrehozása
A részleteket az összes telemetriai elem hibát hozhat létre a GitHub vagy a Visual Studio Team Services. 

![Kattintson az új munkaelem, módosítsa a mezőket, és kattintson az OK gombra.](./media/app-insights-diagnostic-search/42.png)

Ebben az esetben először a rendszer felkéri egy hivatkozást a Team Services-fiók és a projekt konfigurálásához.

![Töltse ki az URL-címét a Team Services-kiszolgáló és a projekt nevére, majd kattintson az Engedélyezés parancsra](./media/app-insights-diagnostic-search/41.png)

(Is konfigurálhat a hivatkozás a munkaelemek panelen.)

## <a name="send-more-telemetry-to-application-insights"></a>További telemetriát küldjön az Application Insights
Az Application Insights SDK által küldött-a-beépített telemetriát, valamint a következőket teheti:

* Rögzítheti a naplóbejegyzéseket a a kedvenc naplózási keretrendszeréből [.NET](app-insights-asp-net-trace-logs.md) vagy [Java](app-insights-java-trace-logs.md). Ez azt jelenti, hogy a nyomkövetési naplók kereshet, és összefüggésbe hozva azokat az oldalmegtekintéseket, kivételeket és eseményeket. 
* [Kód írása](app-insights-api-custom-events-metrics.md) küldhet egyéni eseményeket, a lapmegtekintések és a kivételek. 

[Ismerje meg, hogyan naplók és egyéni telemetriai adatokat küldhet az Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>A Q &AMP; A
### <a name="limits"></a>Mennyi adatot megmarad?

Tekintse meg a [korlátozások összegzése](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hogyan tekinthetem meg POST data a kiszolgálói kérelmekre a?
Automatikusan azt ne naplózza a POST data, de használhat [TrackTrace vagy a naplóhoz hívások](app-insights-asp-net-trace-logs.md). Az üzenet-paraméter a POST data helyezze el. Nem lehet szűrni az üzenet tulajdonságait szűrésével ugyanúgy, de a méretkorlátot hosszabb.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Következő lépések
* [Összetett lekérdezéseket írni az Analyticsben](app-insights-analytics-tour.md)
* [Naplók és egyéni telemetriát küldjön az Application Insightsba](app-insights-asp-net-trace-logs.md)
* [Állítsa be a rendelkezésre állás és a válaszképesség tesztek](app-insights-monitor-web-app-availability.md)
* [hibaelhárítással](app-insights-troubleshoot-faq.md)
