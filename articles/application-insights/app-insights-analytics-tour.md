---
title: Az Azure Application Insights-analitika segítségével bemutatása |} A Microsoft Docs
description: A fő lekérdezések Analytics, az Application insights hatékony keresési eszköz rövid mintáit.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: mbullwin
ms.openlocfilehash: 470779f80e998c3908cf28328cfb415d98c5e06c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579255"
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Az Application Insights az elemzés bemutatása
[Analytics](app-insights-analytics.md) az hatékony keresési szolgáltatása [Application Insights](app-insights-overview.md). Ezeket az oldalakat a Log Analytics lekérdezési nyelv leírása.

* **[Tekintse meg a bevezető videót](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Próbálja ki az Analytics szimulált adatokon](https://analytics.applicationinsights.io/demo)**  az alkalmazás nem adatot küldenek, az Application Insightsba még.
* **[SQL-felhasználók – adatlap](https://aka.ms/sql-analytics)**  fordítja le a leggyakrabban használt nyelv szintaxisát.

Vessünk egy az útmutató néhány alapszintű lekérdezéseket a kezdéshez.

## <a name="connect-to-your-application-insights-data"></a>Az Application Insights-adatait
Nyissa meg az alkalmazás Analytics [áttekintő paneljén](app-insights-dashboards.md) az Application insights szolgáltatásban:

![Portal.Azure.com címen nyissa meg, nyissa meg az Application Insights-erőforrást, és kattintson az Analytics.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsiodocslanguage-referencetabular-operators-show-me-n-rows"></a>[Igénybe](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators): n sorok megjelenítése
(Általában a HTTP által fogadott kérelmek a webes alkalmazás) felhasználói műveletek naplózására adatpontok vannak tárolva a következő táblába `requests`. Minden egyes sor az egy telemetriai adat az Application Insights SDK az alkalmazás kapott.

Kezdjük a tábla sorát néhány minta megvizsgálásával:

![eredmények](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Helyezze a kurzort valahol az utasítás előtt kattintson az Indítás gombra. Egy utasítást is átnyúlik egynél több sort, de ne helyezzen üres sorok utasításban. Üres sorok kényelmes módon, hogy több külön lekérdezések az ablakban.
>
>

Oszlopok kiválasztása, húzza át őket, csoport oszlop szerint, és szűréssel keresse:

![Kattintson a felső jobb eredmények oszlop kiválasztása](./media/app-insights-analytics-tour/030.png)

Bontsa ki a bármely elemet a részletek megtekintéséhez:

![Válassza ki a tábla, és használjon oszlopokat konfigurálása](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Kattintson a fejléc, az oszlopok sorrendjének az eredményeket a webböngészőben elérhető. De vegye figyelembe, hogy egy nagy eredményhalmazt, a böngésző le sorok száma korlátozva. Egyszerűen csak ezzel a módszerrel rendezési rendezi a visszaadott eredménykészlet és nem mindig jelenik meg, a tényleges legmagasabb vagy legalacsonyabb elemeket. Megbízhatóan rendezheti az elemeket, használja a `top` vagy `sort` operátor.
>
>

## <a name="query-across-applications"></a>Lekérdezés több alkalmazások
Ha több Application Insights-alkalmazások származó adatokat kombinálni szeretne, használja a **alkalmazás** kulcsszó használatával adja meg a táblázat neve mellett az alkalmazást.  Ez a lekérdezés egyesít két különböző alkalmazás kérelmeit a **union** parancsot.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsiodocslanguage-referencetabular-operatorstop-operator-and-sorthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssort-operator"></a>[Felső](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) és [rendezés](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
`take` hasznos. Ennek eredményeként a gyors minta letöltése, de nem meghatározott sorrendben jeleníti meg a tábla sorait. Egy rendezett nézet használja `top` (a minta) vagy `sort` (a teljes tábla) protokollon keresztül.

Az első n sorok, adott oszlop szerint rendezett megjelenítése:

```AIQL

    requests | top 10 by timestamp desc
```

* *Szintaxis:* legtöbb operátorok rendelkezik kulcsszó-paraméterek például `by`.
* `desc` Csökkenő sorrend = `asc` = növekvő.

![](./media/app-insights-analytics-tour/260.png)

`top...` egy üzenettel további lehetőség a nagy teljesítményű `sort ... | take...`. Sikerült azt kell írni:

```AIQL

    requests | sort by timestamp desc | take 10
```

Az eredmény ugyanaz lesz, de szeretné egy kicsit lassabban futnak. (Is is írható `order`, azaz egy aliast `sort`.)

A táblázatos nézetre az oszlopfejléceket is használható a képernyőn az eredmények rendezéséhez. De ha használt már természetesen `take` vagy `top` csak lekérdezni egy táblát az oszlop fejlécére kattintva része lesz csak sorrendjének a rekordjával.

## <a name="wherehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorswhere-operator-filtering-on-a-condition"></a>[Ahol](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator): szűrési feltétel

Lássuk csak kérelmek egy adott eredmény, hibakód:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

A `where` operátor egy logikai kifejezés vesz igénybe. Az alábbiakban a velük kapcsolatos néhány alapvető szempontokat:

* `and`, `or`: Logikai operátorok
* `==`, `<>`, `!=` : egyenlő, és nem egyenlő
* `=~`, `!~` : kis-és karakterlánc nem lehet, és nem egyenlő. Számos további karakterlánc-összehasonlítási operátor.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Keresse meg a sikertelen kérelmek

Karakterlánc-érték konvertálása egy egész szám, amely nagyobb – összehasonlítása, mint:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Time

Alapértelmezés szerint a lekérdezések korlátozva az utolsó 24 óra. Azonban módosíthatja ezt a tartományt:

![](./media/app-insights-analytics-tour/change-time-range.png)

Bírálja felül az időtartományt, ha a lekérdezés, amely említi `timestamp` a where záradékban. Példa:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

Az idő a tartomány funkció megegyezik a "where" záradék után minden Dicséretben részesítettünk a forrástáblákhoz egyik egészül ki.

`ago(3d)` azt jelenti, hogy "három nappal ezelőtt". Egyéb időegységben az üzemidő (`2h`, `2.5h`), perc (`25m`), és a másodperc (`10s`).

További példák:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

    // Between specific day/time range
    requests
    | where timestamp > datetime(2018-05-17T17:06:19.892Z) and timestamp <= datetime(2018-05-18T17:06:19.892Z)
    | where duration > 0

```

[Dátum és időpont referencia](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime).


## <a name="projecthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorsproject-operator-select-rename-and-compute-columns"></a>[Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator): kiválasztása, átnevezése és oszlopok compute
Használat [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) használatával mindössze a kívánt oszlopokat választhat:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Oszlopok átnevezése és definiálása újakat is:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![Eredmény](./media/app-insights-analytics-tour/270.png)

* Oszlopnevek szóközöket is tartalmazhatnak, vagy ha azok vannak zárójeles szimbólumok, mint ez: `['...']` vagy `["..."]`
* `%` van a szokásos módon Maradékos osztás operátort.
* `1d` (egy számjegyet, ez akkor volt ") literál timespan van tehát egy nap. Az alábbiakban néhány további timespan literálok: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) kerekít egy érték csak akkor adja meg az alapérték a legközelebbi többszörösére. Ezért `floor(aTime, 1s)` egy időt a legközelebbi második lefelé kerekít.

Kifejezések tartalmazhatnak a szokásos operátorok (`+`, `-`,...), és számos hasznos funkció.

## <a name="extend"></a>Bővíthető
Ha csak át szeretné oszlopok hozzáadása és a meglévő, [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Használatával [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator) kevésbé részletes, mint [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) Ha meg szeretné tartani a meglévő oszlopok.

### <a name="convert-to-local-time"></a>Helyi idő átalakítása

Időbélyegeket mindig UTC formátumban vannak. Így ha Ön az Amerikai csendes-óceáni partján és téli olimpiai, majd a helyi idő UTC-ről-8 óra, akkor ehhez hasonló lehet:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```

## <a name="summarizehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssummarize-operator-aggregate-groups-of-rows"></a>[Összegzés](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator): sorcsoportra összesítése
`Summarize` vonatkozik egy meghatározott *aggregátumfüggvény* keresztül sorcsoportra.

Például a mezőben a webalkalmazás egy kérelem mennyi idő van jelentett `duration`. Nézzük meg, az átlagos válaszidő az összes kérelem:

![](./media/app-insights-analytics-tour/410.png)

Vagy azt sikerült az eredmény szét különböző neveket kérelmeket:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` gyűjti a Stream az adatpontok csoportokba, amelynek a `by` záradék egyaránt kiértékeli. Minden érték a `by` kifejezés - minden művelet egyedi neve a fenti példában - eredménytáblájában egy sort eredményez.

Vagy hogy sikerült eredmények csoportosítás napon:

![](./media/app-insights-analytics-tour/430.png)

Figyelje meg, hogyan használjuk a `bin` funkció (más néven `floor`). Ha éppen most használt `by timestamp`, minden bemeneti sor végül lenne a saját kis csoport. A minden olyan alkalommal például folyamatos skaláris vagy számokat kell, hogy a folyamatos tartomány felosztása egy kezelhető diszkrét értékek száma. `bin` – Ez csak a jól ismert kerekítés lefelé `floor` függvényt – ennek a legegyszerűbb módja.

Ugyanezen technika használatával csökkentheti a karakterláncok tartományok:

![](./media/app-insights-analytics-tour/440.png)

Figyelje meg, hogy használhatja `name=` beállítani egy eredményoszlop összesítő kifejezések vagy a-záradék nevét.

## <a name="counting-sampled-data"></a>Leltár változásához
`sum(itemCount)` az események száma az ajánlott összesítést. Sok esetben az elemek száma 1, ==, így a függvény csak megszámlálja a csoportban található sorok számát. De ha [mintavételi](app-insights-sampling.md) van a művelet csak az eredeti események tört megmaradnak adatpontot az Application insights szolgáltatásban, mint úgy, hogy minden egyes adatponthoz látja, `itemCount` események.

Például, ha a mintavételi elveti 75 %-át az eredeti eseményeket, majd az elemek száma == 4 a megőrzött rekordok -, minden megőrzött rekord voltak négy eredeti rekordokat.

Az adaptív mintavételezési hatására az elemek száma magasabb lehet, ha az alkalmazás erősen használatos időszakokban.

Ezért az elemek száma összegzésének események számát az eredeti jó becslése biztosít.

![](./media/app-insights-analytics-tour/510.png)

Emellett van egy `count()` összesítés (és a egy count művelet) olyan esetekben, ahol valóban szeretné, hogy a csoportban található sorok számát.

Számos van [összesítő függvényektől](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions).

## <a name="charting-the-results"></a>Az eredmények diagramkészítési
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Alapértelmezés szerint eredmények táblázatos megjelenítéséhez:

![](./media/app-insights-analytics-tour/225.png)

Javíthatunk, mint a táblázatos nézetre. Nézzük meg a diagram nézetben az eredményeket a függőleges a sáv lehetőséget:

![Kattintson a diagramra, majd válassza ki a függőleges sávdiagram és hozzárendelését x és y tengely](./media/app-insights-analytics-tour/230.png)

Figyelje meg, hogy bár azt nem az eredmények rendezéséhez idő (ahogy az a tábla megjelenített látható), a diagram megjelenítési mindig látható időpontok megfelelő sorrendben.


## <a name="timecharts"></a>Timecharts
Nincs események számát mutatja óránként:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Válassza ki a diagram megjelenítési beállítás:

![idődiagramját](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Több sorozat
Több kifejezésének a `summarize` záradékban több oszlopot hoz létre.

Több kifejezésének a `by` záradékban több sort, egy az egyes értékek kombinációjának hoz létre.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tábla kérelmek óra és hely szerint](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>A diagram szegmentáljon dimenziók
Ha Ön a diagram egy karakterláncokat tartalmazó oszlopot, és a egy numerikus oszlopot tartalmazó táblát, a karakterlánc ossza fel a numerikus adatokat külön adatsorozat pontjainak használható. Ha egynél több oszlophoz, kiválaszthatja a Diszkriminátor használandó oszlop.

![Egy diagramra szegmens](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Visszadobási arány

Logikai érték konvertálása egy karakterláncot megfeleltessen egy Diszkriminátor szeretné használni:

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Több metrika megjelenítése
Ha a diagram mellett az időbélyeget, numerikus egynél több oszlopot tartalmazó táblát –, megjelenítheti, hogy azok bármilyen kombinációját.

![Egy diagramra szegmens](./media/app-insights-analytics-tour/110.png)

Ki kell választania **nem osztott** több numerikus oszlopok kiválasztása előtt. Egyszerre több numerikus oszlop megjelenítése, nem felosztás egy karakterláncokat tartalmazó oszlopot.

## <a name="daily-average-cycle"></a>Napi átlagos ciklus
Hogyan változik a használati az átlagos nap során?

Száma kérések a modulus egy nap, ideje munkaidőre binned:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Vonaldiagram, az óra egy átlagos nap](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Figyelje meg, hogy jelenleg van ideje időtartamok átalakítása időpontok annak érdekében, hogy a vonaldiagram megjelenítéséhez.
>
>

## <a name="compare-multiple-daily-series"></a>Hasonlítsa össze a napi több sorozat
Hogyan használati változik a napi időpontot át a különböző országokban?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![A client_CountryOrRegion split szerint](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Egy terjesztési ábrázolása
Hány munkamenetek vannak-e, egymástól eltérő hosszúságú?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

Utolsó sora van szükség az átalakítás DateTime értékre. Jelenleg a diagram x tengely megjelent skaláris csak akkor, ha egy dátum és idő.

A `where` záradék nem tartalmazza a végeredménye munkamenetek (sessionDuration == 0), és beállítja az x tengely hosszát.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsiodocslanguage-referenceaggregation-functionspercentiles"></a>[Percentilisei](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/percentiles())
Milyen tartományok időtartamok terjed ki a munkamenetek különböző százalékos?

A fenti lekérdezéssel, de az utolsó sort cserélje le:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Emellett eltávolítottuk a felső korlátja a WHERE záradékban, többek között az egynél több kérelem minden munkamenet megfelelő adatok érdekében:

![Eredmény](./media/app-insights-analytics-tour/180.png)

Amelyből látható, amely:

* 5 %-a munkamenetek rendelkezik a kevesebb mint 3 perc alatt 34s; időtartama
* 50 %-a munkamenetek az elmúlt kevesebb mint 36 perc
* a munkamenetek 5 % az elmúlt több mint 7 nap

Minden ország, előbb rendelkezik ahhoz, hogy külön információkat beolvasni a client_CountryOrRegion oszlop külön-külön is keresztül operátorok összefoglalásképpen:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Csatlakozás
Több táblát, beleértve a kérések és kivételek hozzáféréssel rendelkezünk.

Keresse meg a egy kérelmet, amely egy adott vissza kapcsolódó kivételeket, azt is csatlakozhat a táblák `operation_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Célszerű használni `project` csak ellenőriznünk kell a join végrehajtása előtt oszlopok kiválasztásához.
Az azonos záradékban nem átnevezni az időbélyegző-oszlopa.

## <a name="lethttpsdocsloganalyticsiodocslanguage-referencequery-statementslet-statement-assign-a-result-to-a-variable"></a>[Lehetővé teszik](https://docs.loganalytics.io/docs/Language-Reference/Query-statements/Let-statement): eredményt hozzárendelése egy változóhoz

Használat `let` válassza ki az előző kifejezés részei. Az eredmények nem változik:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> Az Analytics-ügyfelet, az üres sorok, a lekérdezés részei között ne helyezzen. Ne felejtse el minden adatot.
>

Használat `toscalar` egyetlen cellában konvertál egy értéket:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Functions

Használat *teszik* függvény meghatározásához:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Beágyazott objektumok elérése
Beágyazott objektumok könnyen elérhetők. Például a kivételeket a stream látható strukturált objektumok ehhez hasonló:

![Eredmény](./media/app-insights-analytics-tour/520.png)

Válassza ki az Önt érdeklő tulajdonságok konvertálhatók:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Vegye figyelembe, hogy az eredmény a megfelelő típus között szüksége.


## <a name="custom-properties-and-measurements"></a>Egyéni tulajdonságok és a mértékek
Ha az alkalmazás csatolja [egyéni méretek (Tulajdonságok) és az egyéni mértékek](app-insights-api-custom-events-metrics.md#properties) eseményeket, majd megtekintheti azokat a a `customDimensions` és `customMeasurements` objektumokat.

Például ha az alkalmazás tartalmaz:

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2.d2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Ezek az értékek Analytics kibontásához:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type
```

Annak ellenőrzése, hogy egy egyéni dimenzió-e egy adott típusú:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

### <a name="special-characters"></a>Speciális karakterek

A speciális karakterek vagy nyelvi kulcsszavak nevükben azonosítók, hozzá kell férnie őket keresztül `['` és `']` vagy `["` és `"]`.

```AIQL

    customEvents
    | extend p2d2 = customDimensions.['p2.d2'], ...
```

[Hivatkozási azonosító elnevezési szabályok](https://docs.loganalytics.io/docs/Learn/References/Naming-principles)

## <a name="dashboards"></a>Irányítópultok
Annak érdekében, hogy minden a legfontosabb diagramok és táblázatok egyesítik az eredményeket az irányítópulton is rögzíthet.

* [Az Azure megosztott irányítópult](app-insights-dashboards.md#share-dashboards): kattintson a rajzszög ikonra. Mielőtt ezt megtehetné, rendelkeznie kell egy megosztott irányítópulton. Az Azure Portalon nyissa meg, vagy hozzon létre egy irányítópultot, és kattintson a megosztást.
* [Power BI-irányítópult](app-insights-export-power-bi.md): kattintson az exportálás, a Power BI lekérdezés. Ez a megoldás az előnye, hogy a lekérdezést más a mellett számos forrásból megjelenítheti.

## <a name="combine-with-imported-data"></a>Az importált adatok kombinálása

Analytics-jelentések alapbeállítást az irányítópulton jelenik meg, de néha szeretné az adatokat több emészthető űrlap fordítása. Tegyük fel, hogy a hitelesített felhasználók alias azonosítja a telemetriát. Meg szeretne mutatni a valódi neve az eredmények között. Ehhez szüksége van egy CSV-fájlt, amely leképezi az aliasokat a valódi nevét.

Importálja egy adatfájlt, és használja, mint bármelyik a hagyományos táblázatokban (kérelmeket, kivételeket és így tovább). Saját hajthat végre lekérdezést, vagy csatlakoztassa más táblázatokkal. Például, ha rendelkezik usermap nevű táblát, és rendelkezik oszlopok `realName` és `userId`, használhatja az átalakításra a `user_AuthenticatedId` mezőbe a kérelmek telemetriai adatai:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Egy tábla sémája panelen található importálása **egyéb adatforrásokból**, kövesse az utasításokat, fel kell töltenie az adatokat egy minta egy új adatforrás hozzáadásához. Ez a definíció használatával majd töltse fel a táblákat.

Az importálás funkció jelenleg előzetes verzióban, így először megjelenik "Lépjen kapcsolatba velünk" hivatkozást a "Más adatforrások." Használja ezt az előzetes programhoz való regisztrációhoz, és a hivatkozás ezután lesz lecserélve az "Új adatforrás hozzáadása" gombra.


## <a name="tables"></a>Táblák
A stream az alkalmazástól fogadott telemetriát több táblát keresztül érhető el. Minden tábla esetében elérhető tulajdonságok sémája látható az ablak bal oldalán.

### <a name="requests-table"></a>Requests táblához
A webalkalmazás és szegmens szerint száma HTTP-kéréseket:

![Név szerint szegmentált száma kérelmek](./media/app-insights-analytics-tour/analytics-count-requests.png)

Keresse meg a kéréseket, amelyek a legtöbb sikertelen:

![Név szerint szegmentált száma kérelmek](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Egyéni események tábla
Ha [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) szeretne egyéni eseményeket küldeni, akkor olvashatja őket ebből a táblázatból.

Vegyünk egy példát, ahol az alkalmazás kódját tartalmazza a sorokat:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Ezek az események gyakorisága megjelenítése:

![Egyéni események megjelenített aránya](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Bontsa ki a mértékek és dimenziókat leválasztása az eseményekről:

![Egyéni események megjelenített aránya](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Egyéni metrikák tábla
Ha használ [a trackmetric() függvény](app-insights-api-custom-events-metrics.md#trackmetric) az eredményeket a megtalálhatja a saját metrikaértékek küldeni, a **customMetrics** stream. Példa:  

![Egyéni metrikákat az Application Insights Analytics szolgáltatásban](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> A [Metrikaböngésző](app-insights-metrics-explorer.md), bármilyen típusú telemetriát csatolt összes egyéni mérések együtt jelennek meg a metrikák paneljén, és használatával küldött mérőszámokat `TrackMetric()`. De Analytics, az egyéni mértékek is továbbra is csatlakozik, amíg TrackMetric által küldött metrikák jelennek meg a saját stream végezték - események vagy a kéréseket, és így tovább – a telemetriai adatok bármelyik típusú.
>
>

### <a name="performance-counters-table"></a>Teljesítmény-számlálói tábla
[Teljesítményszámlálók](app-insights-performance-counters.md) bemutatják, rendszer alapvető metrikákat az alkalmazáshoz, például a Processzor, memória, és a hálózati kihasználtság. Beállíthatja, hogy az SDK-t küldeni a számlálókat, beleértve a saját egyéni számlálókat.

A **performanceCounters** sémát tesz elérhetővé a `category`, `counter` nevét, és `instance` minden teljesítményszámláló neve. Teljesítményszámláló-példány neveket csak néhány teljesítményszámlálók vonatkoznak, és általában azt jelzik, amelyre a count vonatkozik a folyamat nevét. A telemetria minden alkalmazáshoz csak az adott alkalmazáshoz számlálókat megjelenik. Például hogy milyen számlálók érhetők el:

![Az Application Insights Analytics teljesítményszámlálók](./media/app-insights-analytics-tour/analytics-performance-counters.png)

A rendelkezésre álló memória diagram lekérése a megadott időszakban:

![Az Application Insights Analytics memória idődiagramját](./media/app-insights-analytics-tour/analytics-available-memory.png)

Egyéb telemetriai adatokat, például **performanceCounters** is rendelkezik egy olyan oszlop `cloud_RoleInstance` , amely azt jelzi, hogy a gazdagépen, amelyen fut az alkalmazás identitását. Ha például a különböző gépeken az alkalmazás teljesítményének összehasonlítására:

![Az Application Insights Analytics szerepkörpéldány alapján szegmentált teljesítmény](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Kivételek tábla
[Az alkalmazás által jelentett kivételek](app-insights-asp-net-exceptions.md) érhetők el ebben a táblában.

Az alkalmazás lett kezelése, ha a kivételt a HTTP-kéréssel megkereséséhez műveletazonosítója csatlakozzon:

![Csatlakozzon a kérelmek kivételek műveletazonosítója](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Böngésző időzítésüket tábla
`browserTimings` gyűjti a felhasználói böngészők lapbetöltési adatainak megjelenítése.

[Állítsa be az alkalmazás ügyféloldali telemetria](app-insights-javascript.md) annak érdekében, hogy ezeket a metrikákat.

A séma tartalmazza [az oldal betöltési folyamat különböző szakaszaiban hosszának jelző mérőszámok](app-insights-javascript.md#page-load-performance). (Azt nem jelzik, hogy mennyi ideig a felhasználók számára, olvassa el a lapot.)  

A különböző oldalakat popularities megjelenítése, és minden olyan lap alkalommal betöltése:

![Lapbetöltési idők Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Rendelkezésre állási eredmények táblázatában
`availabilityResults` eredményeit jeleníti meg a [webes teszteket](app-insights-monitor-web-app-availability.md). A teszt mindegyik teszthelyen minden egyes futtatásához külön kell jelenteni.

![Lapbetöltési idők Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Függőségek tábla
Hívások, hogy az alkalmazás lehetővé teszi az adatbázisok és a REST API-k és más TrackDependency()-hívások eredményeinek tartalmazza. A böngésző weblapjáról végzett AJAX hívások is tartalmaz.

AJAX-hívások a böngészőben:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Függőségi hívások a kiszolgálóról:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Kiszolgálóoldali függőségi eredményekben mindig `success==False` Ha nincs telepítve az Application Insights-ügynökkel. Azonban az egyéb adatok megfelelőek-e.

### <a name="traces-table"></a>Nyomkövetési táblát
Tartalmazza a TrackTrace(), használja az alkalmazás által küldött telemetriát vagy [más naplózási keretrendszerekből](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Videó 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Összetett lekérdezéseket:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>További lépések
* [Analytics nyelvi referencia](app-insights-analytics-reference.md)
* [SQL-felhasználók – adatlap](https://aka.ms/sql-analytics) fordítja le a leggyakrabban használt nyelv szintaxisát.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
