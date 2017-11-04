---
title: "Keresés használata az Azure Application Insightsban |} Microsoft Docs"
description: "Keresés és szűrés nyers telemetriaadatok küldött a webes alkalmazást."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: ce2fd9ed1ce796762cc15622cb1c59a316c1909d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="using-search-in-application-insights"></a>Az Application Insightsban keresés használata
Keresés csak a [Application Insights](app-insights-overview.md) található és egyéni telemetriai elemek, például Lapmegtekintések, kivételek, így megismerkedhet vagy kérelmek használható. És naplókivonatokat és eseményeket, amelyek rendelkeznek a kódolt megtekintéséhez.

(Az adatok a összetettebb lekérdezések, használjon [Analytics](app-insights-analytics-tour.md).)

## <a name="where-do-you-see-search"></a>Ha látja keresési?
### <a name="in-the-azure-portal"></a>Az Azure-portálon
Az Application Insights – áttekintés panel az alkalmazás a explicit módon nyithatja diagnosztikai keresés:

![Diagnosztikai keresés megnyitása](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Néhány diagramok és a rács elemek kattintva is megnyílik. Ebben az esetben a szűrők vannak előre beállított összpontosítani a kijelölt elem típusát. 

Például az Áttekintés panel nincs kérelmek válaszideje által besorolt sávdiagram. Kattintson a teljesítmény széles egyedi adott válasz időtartományba esik tartalmazó lista megtekintéséhez:

![Kattintson a kérelem teljesítmény](./media/app-insights-diagnostic-search/07-open-from-filters.png)

A fő diagnosztikai keresési szövegtörzse listáját telemetriai elemek - kiszolgálói kérelmek lapon a nézetek, egyéni események, amelyek rendelkeznek a kódolt és így tovább. A lista tetején van egy összefoglaló táblázat időbeli események száma.

Kattintson a frissítés gombra új esemény lekérdezésekor.

### <a name="in-visual-studio"></a>Visual Studióban

A Visual Studio esetében is az Application Insights keresési ablak. Legtöbb célszerű akkor hibakeresése alkalmazás által generált telemetriai események megjelenítése. Azonban akkor is megjelenhet a közzétett alkalmazást az Azure-portálon az összegyűjtött események.

Nyissa meg a keresési ablak a Visual Studio:

![A Visual Studio Application Insights keresés megnyitása](./media/app-insights-diagnostic-search/32.png)

A keresési ablak a webes portálhoz hasonló jellemzőkkel rendelkezik:

![Visual Studio Application Insights – keresési ablak](./media/app-insights-diagnostic-search/34.png)

A Track művelet lapon érhető el egy kérelem vagy egy nézet megnyitásakor. Egy "művelet" egyetlen kérelemmel vagy a lap nézetre kapcsolódó események sorozata. Például függőségi hívások esetében, kivételek, nyomkövetési naplókat és egyéni események része lehet egyetlen művelettel. A művelet nyomon követése lap grafikonját időzítése és ezeket az eseményeket a kérelem vagy a lap nézet viszonyítva időtartama. 

## <a name="inspect-individual-items"></a>Vizsgálja meg az egyes elemek
Válassza ki bármelyik telemetriai elemre kulcsmezők megtekintéséhez és a kapcsolódó elemek. Ha meg szeretné tekinteni a mezők teljes készletét, kattintson a "...". 

![Kattintson az új munkaelemre vonatkozóan, módosítsa a mezőket, és kattintson az OK gombra.](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>Szűrő eseménytípusok
Nyissa meg a szűrő panelre, és válassza ki a megjeleníteni kívánt típusait. (Ha később szeretné visszaállítani a szűrőket, amellyel megnyitni a panelt, kattintson a visszaállítás elemre.)

![Kattintson a szűrő és telemetriai kijelölve](./media/app-insights-diagnostic-search/02-filter-req.png)

Az esemény típusok a következők:

* **Nyomkövetési** - [diagnosztikai naplók](app-insights-asp-net-trace-logs.md) TrackTrace, log4Net, NLog és System.Diagnostic.Trace hívások beleértve.
* **Kérelem** -HTTP-kérések a kiszolgálóalkalmazás, beleértve a lapok, parancsfájlok, képek, stílus fájlokat és adatokat fogadja. Ezek az események létrehozásához használt a kérelem-válasz áttekintő diagramok.
* **Lapmegtekintés** - [a webes ügyfél által küldött Telemetriai](app-insights-javascript.md), felhasznált lap megtekintése jelentések létrehozásához. 
* **Egyéni esemény** – Ha a trackevent() függvény annak érdekében, hogy hívásainak beszúrt [megfigyeléséhez](app-insights-api-custom-events-metrics.md), Itt kereshet bennük.
* **Kivétel** – fellépő nem kezelt [kivételek a kiszolgálón](app-insights-asp-net-exceptions.md), valamint azokat, amelyeket a TrackException() használatával jelentkezik.
* **A függőségi** - [a kiszolgálóalkalmazás hívásait](app-insights-asp-net-dependencies.md) más szolgáltatások, például REST API-k vagy adatbázisok és AJAX hívásait a [Ügyfélkód](app-insights-javascript.md).
* **Rendelkezésre állási** -eredményeinek [rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>A tulajdonságértékek szűrése
A tulajdonságok értékeit események jeleníthetők meg. A rendelkezésre álló tulajdonságok a kijelölt esemény típusoktól függnek. 

Válasszon például adott válaszkód kéréseket. 

![Bontsa ki a tulajdonságot, és adjon meg értéket](./media/app-insights-diagnostic-search/03-response500.png)

Ugyanaz a hatása, mint minden érték kiválasztása nem egy adott tulajdonság értékének rendelkezik. Vált ki a a tulajdonságon alapuló szűrőt.

### <a name="narrow-your-search"></a>A keresés szűkítéséhez
Figyelje meg, hogy a számlálás szűrőértékek jobb mutatja hány előfordulások nincs aktuális szűrt készletében. 

Az ebben a példában is egyértelmű, hogy a "Rpt/alkalmazottak" kérése az "500" hibák többségét eredményezi:

![Bontsa ki a tulajdonságot, és adjon meg értéket](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>Az ugyanahhoz a tulajdonsághoz olyan esemény megkeresése
Keresse meg elemeinek tulajdonság ugyanarra az értékre:

![Kattintson a jobb gombbal egy tulajdonság](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>Az adatok keresése

> [!NOTE]
> Összetett lekérdezéseket írhat, nyissa meg a [ **Analytics** ](app-insights-analytics-tour.md) a keresési panel tetején.
> 

A feltételek bármelyike tulajdonságértéket kereshet. Ez különösen akkor hasznos, ha írt az [egyéni események](app-insights-api-custom-events-metrics.md) tulajdonság értékekkel. 

Előfordulhat, hogy be szeretné állítani a tartományon, mint rövidebb tartományban keresések gyorsabbak időpontot. 

![Diagnosztikai keresés megnyitása](./media/app-insights-diagnostic-search/appinsights-311search.png)

Teljes szavak, nem karakterláncrész keresése. Tegye idézőjelek közé tegye a speciális karaktereket.

| Karakterlánc | van *nem* által talált | Ezek találja |
| --- | --- | --- |
| HomeController.About |otthoni<br/>Tartományvezérlő<br/>Kimenő | homecontroller<br/>tudnivalók<br/>"homecontroller.about"|
|Egyesült Államok|UNI<br/>TED|Egyesült<br/>állapotok<br/>Egyesült Államok és<br/>"az Amerikai Egyesült Államok"

Az alábbiakban a keresési kifejezésre:

| Mintalekérdezés | Következmény |
| --- | --- |
| `apple` |Összes esemény található egyéb mezőjének tartalmaznia a word "apple" időtartomány |
| `apple AND banana` |Megkeresése, amelynek mindkét szavakat tartalmaznak. A tőkéhez "és", nem használható "és". |
| `apple OR banana`<br/>`apple banana` |Megkeresése, amelynek vagy szót tartalmaz. "Vagy", nem használható "vagy".<br/>Rövid alak. |
| `apple NOT banana` |Esemény megkeresése, amelyek tartalmaznak egy szót, de a másik nem. |



## <a name="sampling"></a>Mintavételezés
Ha az alkalmazás nagy mennyiségű telemetriai adatokat hoz létre (és az ASP.NET SDK verzió 2.0.0-beta3 használ vagy újabb), a adaptív mintavételi modul automatikusan csökkenti a Portal reprezentatív része események küldése által küldött. Azonban a kérésben kapcsolódó események kiválasztva, vagy nincs kijelölve csoportosan, hogy a kapcsolódó események közti léphet. 

[Ismerkedés a mintavételezéssel](app-insights-sampling.md).



## <a name="create-work-item"></a>Munkaelem létrehozása
A Githubból vagy a Visual Studio Team Services programhiba bármely telemetriai eleme az adatokkal hozhat létre. 

![Kattintson az új munkaelemre vonatkozóan, módosítsa a mezőket, és kattintson az OK gombra.](./media/app-insights-diagnostic-search/42.png)

Ebben az esetben először a rendszer felkéri a Team Services-fiók és a projekt kapcsolat konfigurálásához.

![Töltse ki az URL-CÍMÉT a Team Services-kiszolgáló és a projekt nevét, majd kattintson az Engedélyezés parancsra](./media/app-insights-diagnostic-search/41.png)

(Beállíthatja úgy is a hivatkozás a munkaelemek panelen.)

## <a name="save-your-search"></a>A Keresés mentése
Ha azt szeretné, az összes szűrő beállítása, a Kedvencek közé mentheti a keresést. Ha a szervezeti fiók dolgozunk, is eldöntheti, hogy az azt megosztása más csoport tagjai.

![Kattintson a kedvenc, állítson be a nevet és kattintson a Mentés gombra](./media/app-insights-diagnostic-search/08-favorite-save.png)

A Keresés újra, hogy **keresse fel a áttekintése panel** , és nyissa meg a Kedvencek:

![Kedvencek csempe](./media/app-insights-diagnostic-search/09-favorite-get.png)

Ha mentett relatív időtartomány, újra megnyitni a panelt a legfrissebb adatokat tartalmaz. Ha mentett abszolút időtartomány, látni ugyanazokhoz az adatokhoz minden alkalommal. ("Relatív" nem használható kedvenc menteni szeretné, ha az időtartományt kattintson a fejléc, és a beállítása egy időtartományt, amely nem egy egyéni tartományt.)

## <a name="send-more-telemetry-to-application-insights"></a>További telemetriai adatokat küldhet az Application Insights részére
Az Application Insights SDK által küldött out-of-az-box telemetriai, mellett a következő műveletek végezhetők el:

* A kedvenc naplózási keretrendszer a naplózási nyomkövetés rögzítése a [.NET](app-insights-asp-net-trace-logs.md) vagy [Java](app-insights-java-trace-logs.md). Ez azt jelenti, hogy a naplókivonatokat közötti keresésre, és a kivizsgált Lapmegtekintések, kivételeket és eseményeket. 
* [Kód írása](app-insights-api-custom-events-metrics.md) egyéni események, a lapmegtekintések és a kivételeket. 

[Útmutató a naplók és egyéni telemetriai adatokat küldhet az Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>A KÉRDÉSEK ÉS VÁLASZOK
### <a name="limits"></a>Mennyi adatot megmarad?

Tekintse meg a [korlátok összegzés](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Honnan látom POST-adatokat a saját kiszolgáló kérések?
Automatikusan azt ne naplózza a POST-adatokat, de használhat [TrackTrace vagy a napló hívások](app-insights-asp-net-trace-logs.md). Az üzenet paraméter helyezze el a POST-adatokat. Nem lehet szűrést végezni az üzenet tulajdonságai alapján szűrhet ugyanúgy, de a méretkorlátot hosszabb.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Következő lépések
* [Összetett lekérdezések írás Analytics](app-insights-analytics-tour.md)
* [Naplók és egyéni telemetriai adatokat küldhet az Application Insights](app-insights-asp-net-trace-logs.md)
* [Rendelkezésre állási és reakcióidőt tesztek beállítása](app-insights-monitor-web-app-availability.md)
* [hibaelhárítással](app-insights-troubleshoot-faq.md)
