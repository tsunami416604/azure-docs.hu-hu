---
title: Az Azure Application Insights keresztül Analytics bemutatása |} Microsoft Docs
description: A fő lekérdezések Analytics, a hatékony keresési eszköz az Application Insights rövid minta.
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
ms.openlocfilehash: 01a34eed3ea073deaaf449d28d7f8462f12b5a33
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294309"
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Az Application Insightsban Analytics bemutatása
[Elemzés](app-insights-analytics.md) a hatékony keresési funkciója [Application Insights](app-insights-overview.md). Ezeken a lapokon a Log Analytics lekérdezési nyelv ismertetik.

* **[A bevezető videó](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Tesztelése szimulált adataink Analytics meghajtóján](https://analytics.applicationinsights.io/demo)**  Ha az alkalmazás nem adatok küldése az Application Insights még.
* **[SQL-felhasználók lap cheat](https://aka.ms/sql-analytics)**  fordítja le a leggyakrabban használt idioms.

Vegyünk egy lépésein végighaladva az első lépésekhez néhány alapvető lekérdezések.

## <a name="connect-to-your-application-insights-data"></a>Csatlakozás az Application Insights adatokhoz
Nyissa meg az alkalmazás Analytics [áttekintése panel](app-insights-dashboards.md) az Application Insightsban:

![Nyissa meg portal.azure.com, nyissa meg az Application Insights-erőforrást, majd kattintson a elemzés.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsiodocslanguage-referencetabular-operators-show-me-n-rows"></a>[Igénybe](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators): n sorok megjelenítése
Felhasználói műveletek (általában HTTP-kérelmekre a webes alkalmazás által fogadott) jelentkezzen adatpontok tárolódnak a következő táblába `requests`. Minden egyes sorára az egy telemetriai adat az Application Insights SDK az alkalmazás kapott.

Először néhány minta sorokat a tábla vizsgálata:

![eredmények](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Helyezze el a kurzor valahol az utasítás előtt Ugrás gombra. Egy utasítás is átnyúlik egynél több sort, de nem üres sorok be egy utasítást. Üres sorai egy kényelmes módszert arra több külön lekérdezésekké megtartja az ablak.
>
>

Oszlopok kiválasztása, egérrel húzza őket, oszlop, csoport, és szűréséhez:

![Kattintson a felső jobb eredmények oszlop kiválasztása](./media/app-insights-analytics-tour/030.png)

Bontsa ki bármelyik elemre, hogy a részleteket lásd:

![Kattintson a táblázat, és használja az oszlopok konfigurálása](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Kattintson egy sorrendjének az eredményeket a webböngészőben elérhető oszlop fejlécében. De vegye figyelembe, hogy a nagy eredményhalmazt, a böngésző letöltött sorok száma korlátozott. Egyszerűen így rendezési rendezi a visszaadott eredményhalmaz és nem mindig meg a tényleges legnagyobb vagy legkisebb elemek megjelenítése. Elemek megbízhatóan rendezéséhez használja a `top` vagy `sort` operátor.
>
>

## <a name="query-across-applications"></a>Alkalmazások átfogó lekérdezése
Az Application Insights több alkalmazás adatainak kombinálását, használja a **app** kulcsszó adhatja meg a táblázat neve mellett az alkalmazást.  Ez a lekérdezés egyesíti a két különböző alkalmazás érkező kéréseket a **Unió** parancsot.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsiodocslanguage-referencetabular-operatorstop-operator-and-sorthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssort-operator"></a>[Felső](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) és [rendezés](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
`take` hasznos eredményeként gyors mintát eléréséhez, de nem meghatározott sorrendben jeleníti meg a tábla azon sorait. Amelyet egy rendezett nézet `top` (a minta) vagy `sort` (keresztül az egész tábla).

Az első n sorok, adott oszlop szerint rendezve jelenjenek meg:

```AIQL

    requests | top 10 by timestamp desc
```

* *Szintaxis:* legtöbb operátorok rendelkeznek kulcsszó paraméterek például `by`.
* `desc` csökkenő sorrendben = `asc` = növekvő.

![](./media/app-insights-analytics-tour/260.png)

`top...` További performant módszer megkapta a `sort ... | take...`. A Microsoft sikerült írt:

```AIQL

    requests | sort by timestamp desc | take 10
```

Az eredmény változatlan marad, de egy kicsit lassabban fog futni. (Is létrehozható `order`, ez az alias az `sort`.)

Az oszlopfejlécek nézetében is használható a képernyőn az eredmények rendezéséhez. De ha már használta természetesen `take` vagy `top` csak lekérdezni egy tábla, kattintson az oszlopfejlécre a része lesz csak sorrendjének a korábban beolvasott rekordok.

## <a name="wherehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorswhere-operator-filtering-on-a-condition"></a>[Ha](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator): szűrési feltétel

Nézzük meg, csak kérelmeket, amelyek egy adott eredmény, hibakód:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

A `where` operátor egy logikai kifejezés vesz igénybe. Az alábbiakban néhány kulcsfontosságú pont róluk:

* `and`, `or`: Logikai operátorok
* `==`, `<>`, `!=` : egyenlő, és nem egyenlő
* `=~`, `!~` : egyenlő és nem egyenlő azonban nem karakterlánc. Számos további karakterlánc-összehasonlítási operátorok.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Sikertelen kérelmek keresése

Karakterlánc-érték átalakítása használandó nagyobb egész-összehasonlítás mint:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Time

Alapértelmezés szerint a lekérdezések csak az utolsó 24 óra. Azonban módosíthatja ezt a tartományt:

![](./media/app-insights-analytics-tour/change-time-range.png)

Bírálja felül az időtartományt, amely akkor említi lekérdezés írásával `timestamp` a where záradékban. Példa:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

Az idő a tartomány szolgáltatás megegyezik a "where" záradék után minden hashtagként a forrástáblákból közül az egyik beilleszteni.

`ago(3d)` azt jelenti, hogy a "három nappal ezelőtt". Más időegységekkel óra közé tartozik (`2h`, `2.5h`), a perc (`25m`), és a másodperc (`10s`).

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

[Dátumokat és időpontokat hivatkozás](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime).


## <a name="projecthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorsproject-operator-select-rename-and-compute-columns"></a>[Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator): válassza ki, nevezze át és számítási oszlopok
Használjon [ `project` ](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) kívánt oszlopok kiválasztására:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Nevezze át az oszlopok is, és újakat megadása:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![eredménye](./media/app-insights-analytics-tour/270.png)

* Oszlopnevek szóközöket is tartalmazhatnak, vagy azok vannak zárójeles Ha szimbólumokat, ez például: `['...']` vagy `["..."]`
* `%` van a szokásos moduló operátor.
* `1d` (Ez egy számjegy, majd egy kellett ") literális timespan tehát az egy nap. Az alábbiakban néhány további timespan-szövegkonstans: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) kerekít egy értéket, az alapérték megadta a legközelebbi többszörösére le. Ezért `floor(aTime, 1s)` kerekítése a legközelebbi második le egyszerre.

Kifejezések lehetnek a szokásos operátorok (`+`, `-`,...), és számos különböző hasznos funkciók.

## <a name="extend"></a>Bővíthető
Ha csak szeretné oszlopok hozzáadása a már meglévőket, [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Használatával [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator) kevésbé részletes, mint [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) Ha meg szeretné tartani a meglévő oszlopokat.

### <a name="convert-to-local-time"></a>Helyi idő átalakítása

Időbélyeg helyi időre mindig UTC formátumban vannak. Így ha Ön a US csendes-óceáni part és téli, akkor célszerű ezt:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```

## <a name="summarizehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssummarize-operator-aggregate-groups-of-rows"></a>[Összefoglalója](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator): sorcsoportra összesítése
`Summarize` alkalmazza a megadott *aggregátumfüggvény* keresztül sorcsoportra.

Például a webalkalmazás válaszol a kérelemre idejét a mezőben jelentett `duration`. Nézzük meg, az átlagos válaszidő kérelmekre:

![](./media/app-insights-analytics-tour/410.png)

Vagy az eredmény azt sikerült külön kéréseket a különböző nevek:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` gyűjti az adatfolyamban az adatpontok csoportokba, amelynek a `by` záradék egyaránt értékelődik ki. Minden érték a `by` kifejezés - minden egyedi művelet neve a fenti példa - eredménytáblájában egy sort eredményez.

Vagy azt sikerült eredmények csoportosítás időpontja:

![](./media/app-insights-analytics-tour/430.png)

Figyelje meg, hogyan használunk a `bin` funkciót (más néven `floor`). Ha éppen most használt `by timestamp`, mindegyik bemeneti sorában végül volna a saját kis csoport. A minden alkalommal például folyamatos skaláris vagy számok, frissítenünk kell a folyamatos tartomány felosztása kezelhető számos diszkrét értéket. `bin` -Ez csak a jól ismert kerekítési lefelé `floor` működik – a a legegyszerűbb módja.

Ugyanaz a technika használatával is csökkentheti a karakterláncok tartományait:

![](./media/app-insights-analytics-tour/440.png)

Figyelje meg, hogy használható `name=` olyan eredményoszlopot, az összesítő kifejezések vagy a-záradék nevének megadása.

## <a name="counting-sampled-data"></a>Leltár adatminta
`sum(itemCount)` az ajánlott összevonása események száma a rendszer. Sok esetben az elemek száma 1, ==, a függvény egyszerűen megjeleníti a csoportban található sorok számát. De ha [mintavételi](app-insights-sampling.md) van műveletben, csak töredéke alatt az eredeti események kerülnek, az Application Insights adatpontok úgy, hogy az egyes adatpontokban látja, `itemCount` események.

Például, ha a mintavételi elveti az eredeti események, akkor az elemek száma 75 % == 4 megtartott rekordokban - Ez azt jelenti, hogy megtartott rekordot, volt négy eredeti rögzíti.

Adaptív mintavételi az elemek száma a magasabb lehet, ha az alkalmazás fokozottan használatos időszakokban okoz.

Ezért az elemek száma összegzése ad események számát az eredeti jó becslése.

![](./media/app-insights-analytics-tour/510.png)

Szerepel továbbá egy `count()` összesítési (és a count művelet), olyan esetekben, ahol valóban szeretné, hogy a csoportban található sorok számát.

Nincs számos [aggregátumfüggvényeket](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions).

## <a name="charting-the-results"></a>Az eredmények diagramkészítési
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Alapértelmezés szerint eredmények táblázatos megjelenítéséhez:

![](./media/app-insights-analytics-tour/225.png)

Tudnánk mint a tábla nézet. Vizsgáljuk meg az eredményeket a diagram nézetben a függőleges a sáv lehetőséget:

![Diagram, kattintson, majd válassza ki a függőleges sávdiagram, és rendelje hozzá x és y tengely](./media/app-insights-analytics-tour/230.png)

Figyelje meg, hogy bár azt nem az eredmények rendezéséhez idő (ahogy látja, jelennek meg a táblázat), a diagram megjelenítési mindig látható időpontok megfelelő sorrendben.


## <a name="timecharts"></a>Timecharts
Események nincs mutatja óránként:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Válassza ki a diagram megjelenítési lehetőséget:

![timechart](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Több sorozat
Több kifejezést a `summarize` záradékban több oszlop hoz létre.

Több kifejezést a `by` záradékban több sort, egy a minden értékkombinációja hoz létre.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tábla kérelmek óra és hely szerint](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>A diagram szegmentálja dimenziók
Ha a diagram karakterlánc oszlopot és egy numerikus oszlopot tartalmazó táblát, a karakterlánc segítségével a numerikus adatok felosztása pontok külön sorozata. Ha egynél több oszlophoz, dönthet úgy, hogy melyik oszlop kívánja használni, mint a Diszkriminátor ki.

![Egy diagramra szegmens](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Golflabda arány

Olyan logikai érték átalakítása egy karakterláncot megfeleltessen egy Diszkriminátor használni:

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

### <a name="display-multiple-metrics"></a>Több metrikák megjelenítése
Ha Ön diagram mellett a Timestamp értéket, több mint egy numerikus oszlopot tartalmazó táblát jelenítheti meg azok bármilyen kombinációját.

![Egy diagramra szegmens](./media/app-insights-analytics-tour/110.png)

Ki kell választania **nem osztott** több numerikus oszlop kiválasztása előtt. Az azonos időpontban csak egy numerikus oszlopot megjelenítése nem osztható fel karakterlánc oszlop szerint.

## <a name="daily-average-cycle"></a>Napi átlagos ciklus
Hogyan változik a használat átlagos napon keresztül?

Count kérések moduló egy nap idő munkaidőre binned:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Az órát egy átlagos napon belül vonaldiagram](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Figyelje meg, hogy jelenleg kell csak datetimes értékek ahhoz, hogy megjelenjen egy vonaldiagramot idő időtartamok átalakítása.
>
>

## <a name="compare-multiple-daily-series"></a>Több napi sorozat összehasonlítása
Hogyan használati változik a időpontot keresztül a különböző országokban?

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

![Vegyes által client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Egy terjesztési ábrázolása
Hány munkamenetek vannak-e különböző hosszúságú?

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

Az utolsó sort kell az átalakítás datetime értékre. Jelenleg a diagram x tengelyéhez jelenik meg skaláris csak akkor, ha egy DateTime típusú érték.

A `where` záradék nem tartalmazza a végeredménye munkamenetek (sessionDuration == 0), és beállítja az x tengely hosszát.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsiodocslanguage-referenceaggregation-functionspercentiles"></a>[Százalékos érték](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/percentiles())
Milyen tartományok időtartamok fedik le a munkamenetek különböző százalékának?

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

Is a felső határ eltávolítása WHERE záradék, beleértve több kérelemmel minden munkamenet megfelelő adatok lekérése érdekében:

![eredménye](./media/app-insights-analytics-tour/180.png)

Ahol láthatja, hogy:

* a munkamenetek 5 % rendelkezik a kevesebb mint 3 perc alatt 34s; időtartama
* 50 %-a munkamenetek utolsó 36 percnél kevesebb;
* az utolsó 7 napnál 5 %-a munkamenetek

A client_CountryOrRegion oszlop külön-külön is keresztül külön részletes információkat lekérni az egyes országok, csak azt kell léptetniük összesítse a kezelők:

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
A Microsoft több táblázatokat, beleértve a kérelmek és kivételek hozzáférése.

A kérelem által visszaadott hibaválaszt kapcsolatos kivételeket megkereséséhez azt kapcsolhatja össze a táblák `session_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Jó gyakorlat használandó `project` csak a végrehajtása az illesztés előtt kell oszlopok kiválasztásához.
Az azonos záradékban azt a Timestamp típusú oszlop átnevezése.

## <a name="lethttpsdocsloganalyticsiodocslanguage-referencequery-statementslet-statement-assign-a-result-to-a-variable"></a>[Így](https://docs.loganalytics.io/docs/Language-Reference/Query-statements/Let-statement): eredményt hozzárendelése egy változó

Használjon `let` válassza ki az előző kifejezés részei. Az eredmények ugyanazok maradtak, mint:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> Az elemzés ügyfél put nem üres sorok a lekérdezés részei között. Ügyeljen arra, hogy az összes hajtható végre.
>

Használjon `toscalar` egyetlen cellában átalakítani egy értéket:

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

Használjon *segítségével* függvény meghatározásához:

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
A beágyazott objektumok könnyen elérhetők. Például a kivételek adatfolyam láthatja a strukturált objektumok:

![eredménye](./media/app-insights-analytics-tour/520.png)

Válassza ki a tulajdonságai továbbra is érdekli konvertálhatók:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Ne feledje, hogy az eredmény a megfelelő típusúra konvertálni.


## <a name="custom-properties-and-measurements"></a>Egyéni tulajdonságok és mérések
Ha az alkalmazás csatol [egyéni dimenzió (Tulajdonságok) és egyéni mérték](app-insights-api-custom-events-metrics.md#properties) eseményekre, majd megtekintheti azokat a a `customDimensions` és `customMeasurements` objektumok.

Ha például az alkalmazás tartalmazza:

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

Annak ellenőrzéséhez, hogy egy egyéni dimenzió egy adott típusú:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

### <a name="special-characters"></a>Speciális karakterek

A különleges karaktereket vagy a név nyelvi kulcsszavak azonosítókhoz elérhet keresztül kell `['` és `']` vagy `["` és `"]`.

```AIQL

    customEvents
    | extend p2d2 = customDimensions.['p2.d2'], ...
```

[Azonosító elnevezési szabályok referencia](https://docs.loganalytics.io/docs/Learn/References/Naming-principles)

## <a name="dashboards"></a>Irányítópultok
Ahhoz, hogy minden a legfontosabb diagramok és táblák összefogni a irányítópult eredmény rögzíthető.

* [Az Azure megosztott irányítópult](app-insights-dashboards.md#share-dashboards): a rögzítés ikonjára. Mielőtt ezt megtehetné, egy megosztott irányítópult kell rendelkeznie. Az Azure portálon nyissa meg, vagy hozzon létre egy irányítópultot, és kattintson a megosztást.
* [A Power BI-irányítópulton](app-insights-export-power-bi.md): kattintson az exportálni, a Power BI-lekérdezést. Ez a megoldás előnye, hogy a lekérdezés más a mellett számos információforrás jelenítheti meg.

## <a name="combine-with-imported-data"></a>Az importált adatok kombinálása

Elemzési jelentések remekül az irányítópulton, de az adatok több emészthető űrlapra fordítása előfordulhat. Tegyük fel, hogy a hitelesített felhasználók azonosítják a telemetriai adatok alias. Szeretné a valós nevek megjelenítése az eredmények között. Ehhez szüksége van egy CSV-fájl, amely leképezhető a aliasok a valódi nevét.

Importálhatja egy adatfájlt és csakúgy, mint szabványos táblákat (kérelmeket, kivételek, és így tovább) használja. Vagy lekérdezni az önállóan, vagy más táblák csatlakozzon hozzá. Például, ha a usermap nevű tábla, és nem rendelkezik-e oszlopok `realName` és `userId`, majd lefordítani használja a `user_AuthenticatedId` mező mellett a – kéréstelemetria:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Importálni tudjon egy táblát, a séma panelen a **egyéb adatforrások**, kövesse az utasításokat egy minta az adatok feltöltése egy új adatforrás hozzáadása. Ez a definíció segítségével majd töltse fel a táblázatokat.

Az importálás funkció jelenleg előzetes, ezért kezdetben megjelenik az "Egyéb adatforrások." ", lépjen velünk kapcsolatba" hivatkozás Ennek segítségével jelentkezzen be a programot, és a hivatkozás majd váltja fel az "Új adatforrás hozzáadása" gombra.


## <a name="tables"></a>Táblák
Az alkalmazástól fogadott telemetriai adatfolyama több táblázatot keresztül érhető el. A bal oldali ablak tulajdonságok érhetők el minden tábla sémája akkor jelenik meg.

### <a name="requests-table"></a>Kérelmek tábla
Count HTTP-kéréseket a web app és a szegmens lapnév szerint:

![Count kérelmek szegmentált név szerint](./media/app-insights-analytics-tour/analytics-count-requests.png)

A legtöbb sikertelen alatt álló kérelmek keresése:

![Count kérelmek szegmentált név szerint](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Egyéni események tábla
Ha [trackevent() függvény](app-insights-api-custom-events-metrics.md#trackevent) saját események küldésére is el ebből a táblázatból.

Vegyünk egy példát, amelyben az alkalmazás kódját tartalmazza a sorokat:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Ezek az események gyakorisága megjelenítése:

![Egyéni események megjelenítése aránya](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Mérések és dimenziókat kinyerése a események:

![Egyéni események megjelenítése aránya](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Egyéni metrikák tábla
Ha használ [trackmetric() függvény](app-insights-api-custom-events-metrics.md#trackmetric) küldése saját metrika értékeket, az eredményeket a találhat a **customMetrics** adatfolyam. Példa:  

![Egyéni metrikákat az Application Insights elemzés](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> A [Metrikaböngésző](app-insights-metrics-explorer.md), telemetriai bármilyen típusú csatolt összes egyéni mértékek együtt jelennek meg a metrikák panelről, és metrikák használatával küldött `TrackMetric()`. De Analytics, egyéni mértékek továbbra is csatolt bármelyik telemetriai azok zajlott - események vagy kéréseket, és így tovább -, amíg TrackMetric által küldött metrikák jelennek meg a saját adatfolyam típusú.
>
>

### <a name="performance-counters-table"></a>Teljesítmény számlálók tábla
[Teljesítményszámlálók](app-insights-performance-counters.md) láthat rendszer alapvető metrikákat az alkalmazás, például a Processzor, memória, és a hálózathasználat. Az SDK számlálókat, beleértve a saját egyéni számlálók küldendő konfigurálhatja.

A **performanceCounters** séma elérhetővé teszi a `category`, `counter` nevét, és `instance` minden teljesítményszámláló nevét. Teljesítményszámláló-példányok nevei csak egyes teljesítményszámlálók érvényes, és általában azt jelzik, amelyre a count vonatkozik a folyamat nevét. A telemetriai minden alkalmazáshoz csak a számláló az alkalmazás látható. Például hogy milyen számlálók érhetők el:

![Az Application Insights analytics teljesítményszámlálók](./media/app-insights-analytics-tour/analytics-performance-counters.png)

A diagram a rendelkezésre álló memória megszerezni a kiválasztott időszakban:

![Az Application Insights Analytics memória timechart](./media/app-insights-analytics-tour/analytics-available-memory.png)

Egyéb telemetriai adatokat, például **performanceCounters** egy olyan oszlop is van `cloud_RoleInstance` azt jelzi, hogy az állomáson, amelyen fut az alkalmazás identitását. Ha például a az alkalmazás teljesítményével kapcsolatos különböző gépeken összehasonlítandó:

![Az Application Insights Analytics szerepkör példánya szegmentált teljesítmény](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Kivételek tábla
[Az alkalmazás által jelentett kivételek](app-insights-asp-net-exceptions.md) érhetők el ebben a táblában.

A HTTP-kérelem, az alkalmazás lett kezelése, ha a kivételt észlelt megkereséséhez csatlakoztatás operation_Id:

![Csatlakoztatás operation_Id kivételek kérések](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Időzítés tábla Browser
`browserTimings` a felhasználók böngészőjének gyűjtött lapbetöltési adatainak megjelenítése.

[Állítsa be az alkalmazás ügyféloldali telemetriai](app-insights-javascript.md) metrikákat láthatók.

A séma tartalmaz [a lap betöltési folyamat különböző szakaszaiban a hosszának jelző metrikák](app-insights-javascript.md#page-load-performance). (A felhasználók, olvassa el a lap idő hosszúsága nem utal.)  

A különböző oldalakhoz popularities megjelenítése, és az összes lapon alkalommal betöltése:

![Lapbetöltési idők Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Rendelkezésre állási eredmények táblázatában
`availabilityResults` eredményeit jeleníti meg a [webalkalmazás-tesztek](app-insights-monitor-web-app-availability.md). Minden egyes teszt helyen a teszt futtatásakor a külön-külön jelenti.

![Lapbetöltési idők Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Függőségek tábla
Eredményeit, hogy az alkalmazás adatbázisok és a REST API-kat, és az egyéb TrackDependency() a hívások tartalmazza. A böngészőből AJAX-hívások is tartalmaz.

AJAX-hívások a böngészőből:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

A kiszolgáló függőségi hívások esetében:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Kiszolgálóoldali függőségi mindig eredményben `success==False` Ha az Application Insights-ügynök nincs telepítve. Azonban az egyéb adatok megfelelőek-e.

### <a name="traces-table"></a>Nyomkövetések tábla
Tartalmazza az TrackTrace(), használó alkalmazás által küldött telemetriai adatok vagy [más naplózási keretrendszer](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Videó 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Speciális lekérdezéseket:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>További lépések
* [Elemzés nyelvi referencia](app-insights-analytics-reference.md)
* [SQL-felhasználók lap cheat](https://aka.ms/sql-analytics) fordítja le a leggyakrabban használt idioms.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
