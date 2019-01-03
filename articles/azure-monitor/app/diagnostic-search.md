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
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mbullwin
ms.openlocfilehash: 9b7f49571aa78a262a464f9626b7b98a85f8187f
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973332"
---
# <a name="using-search-in-application-insights"></a>Az Application Insights keresés használata
Keresés az egyik szolgáltatása [Application Insights](../../application-insights/app-insights-overview.md) keresse meg és Fedezze fel a telemetriai elem, például a lapmegtekintések, kivételek, vagy webes kérelmek küldéséhez használt. És megtekintheti a nyomkövetési naplók és eseményeket, amelyek van kódolva.

(Az adatok összetettebb lekérdezésekhez használja [Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Ahol látható keresési?

### <a name="in-the-azure-portal"></a>Az Azure Portalon

Diagnosztikai keresés explicit módon is megnyithatja az alkalmazás az Application Insights áttekintése panelen:

![Nyissa meg a diagnosztikai keresés](./media/diagnostic-search/001.png)

![Képernyőkép a diagnosztikai keresés diagramok](./media/diagnostic-search/002.png)

Diagnosztikai keresés törzse a lista lapon telemetriai elem - kiszolgálói kérelmek, a nézeteket, egyéni eseményeket, amelyek van kódolva, és így tovább. A lista elején van egy összefoglaló táblázat eseményszámok idővel.

Kattintson a frissítés gombra új események beolvasása.

### <a name="in-visual-studio"></a>Visual Studióban

A Visual Studióban is van egy Application Insights keresés ablakában. Ez leginkább hasznos hibakeresése az alkalmazás által generált telemetriai események megjelenítése. De azt is megjelenítheti az események gyűjtését a közzétett alkalmazást az Azure Portalon.

A Visual Studióban nyissa meg a keresési ablakban:

![A Visual Studio Application Insights keresés megnyitása](./media/diagnostic-search/32.png)

A keresési ablak a webes portálhoz hasonló jellemzőkkel rendelkezik:

![A Visual Studio Application Insights keresőablakában](./media/diagnostic-search/34.png)

A nyomon követheti a művelet lapon érhető el egy kérelmet, vagy egy oldal nézet megnyitása. Egy "művelet" egyetlen kérelem vagy oldalmegtekintés nézet a kapcsolódó események sorozata. Függőségi hívások, a kivételek, a nyomkövetési naplókat és az egyéni események például lehet az egyetlen művelet részeként. A művelet nyomon követése lap grafikonját ütemét és ezeket az eseményeket a kérelem vagy oldalmegtekintés nézet viszonyítva időtartama. 

## <a name="inspect-individual-items"></a>Vizsgálja meg az egyes elemek

Jelölje ki bármely szereplő telemetriai elem kulcsmezők megtekintéséhez és a kapcsolódó elemek.

![Képernyőkép egy egyéni függőség-kérelem](./media/diagnostic-search/003.png)

Ez elindítja a végpontok közötti tranzakció részletei nézet:

![Képernyőfelvétel a végpontok közötti tranzakció részletei nézet.](./media/diagnostic-search/004.png)

## <a name="filter-event-types"></a>Eseménytípusok szűrése
A szűrő panel megnyitásához, és válassza ki az eseménytípusok meg szeretné tekinteni. (Ha később szeretné visszaállítani a szűrőket, amellyel megnyitotta a panelt, kattintson az Alaphelyzet.)

![Válasszon szűrőt, és válassza ki a telemetriai adatok típusa](./media/diagnostic-search/02-filter-req.png)

Az esemény-típusok a következők:

* **Nyomkövetési** - [diagnosztikai naplók](../../azure-monitor/app/asp-net-trace-logs.md) TrackTrace, log4Net, NLog és System.Diagnostic.Trace hívások többek között.
* **Kérelem** -lapok, parancsfájlok, képek, stílusfájljait és adatok többek között a kiszolgálóalkalmazás által fogadott HTTP-kérelmekre. Ezeket az eseményeket létrehozásához a kérések és válaszok áttekintés diagramokon szolgálnak.
* **Oldal nézet** - [a webes ügyfél által küldött telemetriai adatok](../../azure-monitor/app/javascript.md)oldal nézet jelentések létrehozására szolgál. 
* **Egyéni esemény** – ha annak érdekében, hogy a TrackEvent() hívásainak beszúrt [figyelheti](../../azure-monitor/app/api-custom-events-metrics.md), itt megkeresheti azokat.
* **Kivétel** – nem kezelt [kivételek a kiszolgálón](../../azure-monitor/app/asp-net-exceptions.md), valamint azokat, amelyeket a trackexception() hívásait használatával jelentkezik.
* **Függőségi** - [a kiszolgálóalkalmazás hívásait](../../azure-monitor/app/asp-net-dependencies.md) más szolgáltatásokba, például REST API-k vagy adatbázisok és az AJAX-hívások a a [ügyfélalkalmazás](../../azure-monitor/app/javascript.md).
* **Rendelkezésre állási** -eredményeit [rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>A tulajdonságértékek szűrése
Szűrhet események tulajdonságaik értékeiből. A rendelkezésre álló tulajdonságok a kiválasztott eseménytípusok függ. 

Ha például kérelmek egy adott válaszkóddal választhat. 

![Bontsa ki a tulajdonságot, és válasszon egy értéket](./media/diagnostic-search/03-response500.png)

Az azonos hatása, mint minden érték kiválasztása nem egy adott tulajdonság értékét nem. Vált ki a tulajdonságon alapuló szűrőt.

### <a name="narrow-your-search"></a>A keresés szűkítéséhez
Figyelje meg, hogy a számlálás szűrőértékeket jobb megjelenítése hány előfordulások létezik az aktuális szűrt csoportjának szerepelnek. 

Ebben a példában nincs bejelölve, hogy a "Rpt/alkalmazottak" kérelem eredménye a legtöbb, a "500" hibák:

![Bontsa ki a tulajdonságot, és válasszon egy értéket](./media/diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Az ugyanahhoz a tulajdonsághoz események
Keresse meg az azonos tulajdonság értéke az összes elemet:

![Kattintson a jobb gombbal egy tulajdonság](./media/diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Az adatok keresése

> [!NOTE]
> Összetettebb lekérdezéseket írni, nyissa meg a [ **Analytics** ](../../azure-monitor/log-query/get-started-portal.md) a keresés panel tetején.
> 

A tulajdonságértékek bármelyikét feltételek kereshet. Ez különösen hasznos adatszolgáltatót [egyéni események](../../azure-monitor/app/api-custom-events-metrics.md) és a tulajdonságértékek. 

Érdemes egy tartományt, mint keresések időbeli rövidebb széles gyorsabbak idő beállítása. 

![Nyissa meg a diagnosztikai keresés](./media/diagnostic-search/appinsights-311search.png)

Teljes szavakat, nem a karakterláncrész keresése. Speciális karakterek adni tegye idézőjelek közé.

| sztring | van *nem* által észlelt | Ezek találja |
| --- | --- | --- |
| HomeController.About |kezdőlap<br/>tartományvezérlő<br/>ki | homecontroller<br/>névjegy<br/>"homecontroller.about"|
|Egyesült Államok|UNI<br/>TED|Egyesült<br/>állapotok<br/>Egyesült Államok és<br/>"Egyesült Államok"

Az alábbiakban a keresési kifejezéseket is használhatja:

| Mintalekérdezés | Következmény |
| --- | --- |
| `apple` |Az időtartomány, amelynek mezői tartalmazzák az "apple" szó az összes esemény keresése |
| `apple AND banana` <br/>`apple banana` |Mindkét szót tartalmazó események keresése. Használja a tőke "és" nem "és". <br/>Rövid űrlapot. |
| `apple OR banana` |Keresse meg vagy szót tartalmazó események. Használja a "Vagy", sem "vagy". |
| `apple NOT banana` |Események, amelyek tartalmaznak egy szót, míg a másikon nem található. |

## <a name="sampling"></a>Mintavételezés
Ha az alkalmazása sok telemetriát hoz létre (és használ, akkor a verzió az ASP.NET SDK 2.0.0-beta3 vagy újabb), az adaptív mintavételezési modul automatikusan csökkenti a által csak töredékeket az események elküldése a portálra küldött kötetet. Azonban az azonos kéréshez kapcsolódó eseményeket kiválasztva, vagy a jelölésük, úgy, hogy lehessen mozogni a kapcsolódó események között. 

[Ismerkedés a mintavételezéssel](../../application-insights/app-insights-sampling.md).

## <a name="create-work-item"></a>Munkaelem létrehozása
Létrehozhat egy hiba a GitHub vagy az Azure DevOps bármilyen szereplő telemetriai elem adatait. 

![Kattintson az új munkaelem, módosítsa a mezőket, és kattintson az OK gombra.](./media/diagnostic-search/42.png)

Ebben az esetben először a rendszer felkéri egy hivatkozást az Azure DevOps-szervezetnek és a projekt konfigurálásához.

![Töltse ki az URL-címét az Azure DevOps-szolgáltatásokkal és a projekt nevére, majd kattintson az Engedélyezés parancsra](./media/diagnostic-search/41.png)

(Is konfigurálhat a hivatkozás a munkaelemek panelen.)

## <a name="send-more-telemetry-to-application-insights"></a>További telemetriát küldjön az Application Insights
Az Application Insights SDK által küldött-a-beépített telemetriát, valamint a következőket teheti:

* Rögzítheti a naplóbejegyzéseket a a kedvenc naplózási keretrendszeréből [.NET](../../azure-monitor/app/asp-net-trace-logs.md) vagy [Java](../../azure-monitor/app/java-trace-logs.md). Ez azt jelenti, hogy a nyomkövetési naplók kereshet, és összefüggésbe hozva azokat az oldalmegtekintéseket, kivételeket és eseményeket. 
* [Kód írása](../../azure-monitor/app/api-custom-events-metrics.md) küldhet egyéni eseményeket, a lapmegtekintések és a kivételek. 

[Ismerje meg, hogyan naplók és egyéni telemetriai adatokat küldhet az Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>A Q &AMP; A
### <a name="limits"></a>Mennyi adatot megmarad?

Tekintse meg a [korlátozások összegzése](../../application-insights/app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hogyan tekinthetem meg POST data a kiszolgálói kérelmekre a?
Automatikusan azt ne naplózza a POST data, de használhat [TrackTrace vagy a naplóhoz hívások](../../azure-monitor/app/asp-net-trace-logs.md). Az üzenet-paraméter a POST data helyezze el. Nem lehet szűrni az üzenet tulajdonságait szűrésével ugyanúgy, de a méretkorlátot hosszabb.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Következő lépések
* [Összetett lekérdezéseket írni az Analyticsben](../../azure-monitor/log-query/get-started-portal.md)
* [Naplók és egyéni telemetriát küldjön az Application Insightsba](../../azure-monitor/app/asp-net-trace-logs.md)
* [Állítsa be a rendelkezésre állás és a válaszképesség tesztek](../../azure-monitor/app/monitor-web-app-availability.md)
* [hibaelhárítással](../../application-insights/app-insights-troubleshoot-faq.md)
