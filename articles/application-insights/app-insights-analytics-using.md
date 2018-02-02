---
title: "Elemzés - a hatékony keresési eszköz az Azure Application Insights segítségével |} Microsoft Docs"
description: "Az elemzés, az Application Insights a hatékony diagnosztikai keresési eszköz használatával. "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: danha; mbullwin
ms.openlocfilehash: 0ca5c8b19f4699548a8551ec673e4a067d4e5fad
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="using-analytics-in-application-insights"></a>Az Application Insightsban Analytics használatával
[Elemzés](app-insights-analytics.md) a hatékony keresési funkciója [Application Insights](app-insights-overview.md). Ezeken a lapokon a Log Analytics lekérdezési nyelv ismertetik.

* **[A bevezető videó](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Tesztelése szimulált adataink Analytics meghajtóján](https://analytics.applicationinsights.io/demo)**  Ha az alkalmazás nem adatok küldése az Application Insights még.

## <a name="open-analytics"></a>Nyissa meg elemzés
A az alkalmazás otthoni erőforrás az Application Insightsban kattintson az elemzés.

![Nyissa meg portal.azure.com, nyissa meg az Application Insights-erőforrást, majd kattintson a elemzés.](./media/app-insights-analytics-using/001.png)

A beágyazott oktatóanyag lehetővé teszi az elérhető lehetőségekkel ötleteket.

Van egy [itt átfogóbb bemutatása](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>A telemetriai adatok lekérdezése
### <a name="write-a-query"></a>Lekérdezés írása
![Séma megjelenítése](./media/app-insights-analytics-using/150.png)

A bal oldalon felsorolt táblákat nevei kezdődnie (vagy a [tartomány](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) vagy [Unió](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operátorok). Használjon `|` a folyamatokat létrehozni [operátorok](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

IntelliSense kéri az operátorok és a kifejezés elemek közül választhat. Kattintson a ikonra (vagy nyomja meg a CTRL + szóköz) hosszabb leírást és a példákat egyes elemeinek a használatára.

Tekintse meg a [Analytics nyelvi bemutató](app-insights-analytics-tour.md) és [nyelvi referencia](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>A lekérdezés futtatása
![A lekérdezés futtatása](./media/app-insights-analytics-using/130.png)

1. Egyetlen sortörést használható egy lekérdezésben.
2. A kurzor belül, vagy a futtatni kívánt lekérdezés végén elhelyezése.
3. Ellenőrizze a lekérdezés az időtartományt. (Módosíthatja, vagy bírálja felül az által, beleértve a saját [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) záradék a lekérdezés.)
3. A lekérdezés futtatásához az Ugrás gombra.
4. A lekérdezés nem üres sorok be. Beállíthatja, hogy több elkülönített lekérdezések egy lekérdezés lap üres vonallal elválasztva őket. Csak a lekérdezés, amely rendelkezik a kurzor fut.

### <a name="save-a-query"></a>Lekérdezés mentése
![A lekérdezés mentése](./media/app-insights-analytics-using/140.png)

1. Az aktuális lekérdezés fájl mentéséhez.
2. Nyissa meg a korábban mentett lekérdezés fájlt.
3. Hozzon létre egy új lekérdezés fájlt.

## <a name="see-the-details"></a>Részletek
Bontsa ki az összes sort az eredmények között, a tulajdonságok teljes listájának megtekintéséhez. Ennél jobban is kibonthatja, bármely tulajdonsága strukturált érték – például, egyéni dimenziók vagy a veremben egy kivételt listázása.

![Bontsa ki a sor](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Az eredmények rendezése
Rendezési, szűréséhez, megjelenítheti és az a lekérdezés által visszaadott eredmények csoportban.

> [!NOTE]
> Rendezés, a csoportosítás és a szűrés a böngészőben ne futtassa újból a lekérdezést. Ezek csak az eredményeket a legutóbbi lekérdezés által visszaadott eredményobjektumokban tárolt átrendezését. 
> 
> Ezen feladatok végrehajtásával a kiszolgálón, mielőtt a rendszer visszairányítja az eredményeket, a lekérdezés írása a [rendezési](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [összefoglalója](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) és [ahol](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operátorok.
> 
> 

Válasszon az annak megtekintéséhez, húzza az oszlopfejlécek átrendezés, húzza a határok automatikus oszlopok.

![Oszlopok rendezése](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Rendezésére és szűrésére elemek
Egy oszlop vezetője kattintva rendezheti az eredményeket. Kattintson ismét a más módon rendezheti, majd kattintson a harmadik pedig visszatérhet az eredeti rendezést a lekérdezés által visszaadott idő.

A szűrő ikon segítségével szűkítse a keresést.

![Rendezésére és szűrésére oszlopok](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Csoport elemek
Több oszlop szerinti rendezéshez, csoportosítás használata. Először engedélyeznie, és húzza oszlopfejlécek a táblázat felett térbe kerülnek.

![Csoport](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Hiányzik az egyes eredmények?

Ha úgy gondolja, hogy nem minden, a várt eredményt is lát, van néhány lehetséges ok.

* **Tartomány időszűrője**. Alapértelmezés szerint az elmúlt 24 órában származó eredmények csak akkor jelenik meg. Az automatikus szűrő, amely korlátozza az eredmények, amelyek a forrástáblákból lekérése tartományon van. 

    Azonban módosíthatja az időtartományt a legördülő menü használatával szűrő.

    A beállítás felülbírálható az automatikus tartomány beleértve a saját vagy [ `where  ... timestamp ...` záradék](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) be a lekérdezést. Példa:

    `requests | where timestamp > ago('2d')`

* **Eredmények korlát**. A portál által visszaadott eredmények körülbelül 10 KB-os sorok korlátozva van. Figyelmeztetést jeleníti meg, ha meghaladja a lépjen. Ha ez előfordul, a tábla az eredmények rendezéséhez nem mindig, minden a tényleges első vagy utolsó eredmények megjelenítése. 

    Tanácsos elérte-e a korlát elkerülése érdekében. Az idő a tartomány szűrővel, vagy használjon operátorok például:

  * [TOP 100 alapján időbélyeg](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [100 igénybe](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [összefoglalója](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [Ha időbélyeg > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Több mint 10 KB-os sor szeretné? Érdemes lehet [a folyamatos exportálás](app-insights-export-telemetry.md) helyette. Elemzés készült elemzés, nem pedig nyers adatok lekérése során.)

## <a name="diagrams"></a>Diagramok
Válassza ki a kívánt diagram típusát:

![Válassza ki a diagram típusát](./media/app-insights-analytics-using/230.png)

Ha rendelkezik a megfelelő típusú több oszlopot, kiválaszthatja az x és y-tengelyek és dimenziókat osztani az eredményeket tartalmazó oszlop.

Alapértelmezés szerint eredmények kezdetben táblázatként jelenik meg, és manuálisan jelölje ki a diagramot. De használhatja a [irányelv leképezési](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) egy lekérdezést a diagram kiválasztásához végén.

### <a name="analytics-diagnostics"></a>Elemzés diagnosztika


Egy timechart a hirtelen csúcs vagy lépése az adatokat, ha megjelenik a kijelölt pont a sor. Ez azt jelzi, hogy elemzés diagnosztika észlelt a hirtelen módosítás kiszűrhetők tulajdonságok kombinációja. Kattintson a további információkhoz juthat a szűrőre, illetve a jelenik meg a szűrt verzió pontra. Ez lehet, hogy könnyebben azonosíthassa a módosítás okáról. 

[További információ az elemzés diagnosztika](app-insights-analytics-diagnostics.md)


![Elemzés diagnosztika](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Rögzítés az irányítópulton
A diagram vagy a tábla egyik rögzíthető a [irányítópultok megosztott](app-insights-dashboards.md) -kattintson a PIN-kódot. 

![Kattintson a PIN-kód](./media/app-insights-analytics-using/pin-01.png)

Ez azt jelenti, hogy elhelyezett együtt segítenek a teljesítmény vagy a webszolgáltatások használatát az irányítópultot, megadható a más metrikákkal együtt túl összetett elemzést. 

Rögzítheti az irányítópulton, tábla-e azt négy vagy kevesebb oszlopot. Csak az első hét sorok jelennek meg.

### <a name="dashboard-refresh"></a>Irányítópult frissítése
A diagram az irányítópulton rögzítette által futtatja újból a lekérdezést körülbelül óránként automatikusan frissül. A frissítés gombra is kattinthat.

### <a name="automatic-simplifications"></a>Automatikus egyszerűbb

Bizonyos egyszerűbb diagram is vonatkozik, amikor irányítópulton rögzítheti.

**Idő korlátozás:** lekérdezések korlátozódnak automatikusan az elmúlt 14 napban. A hatás megegyezik, ha a lekérdezésben `where timestamp > ago(14d)`.

**Count korlátozás bin:** nagy mennyiségű diszkrét bins (általában a sávdiagram) rendelkező diagram megjelenítése, ha a kisebb ki van töltve bins automatikusan vannak csoportosítva pedig egyetlen "egyéb" bin. Ha például a lekérdezés:

    requests | summarize count_search = count() by client_CountryOrRegion

így néz Analytics:

![A diagramon az hosszú utóhívás](./media/app-insights-analytics-using/pin-07.png)

de ha irányítópulton rögzítheti, így néz:

![A diagramon az korlátozott bins](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportálása az Excelbe
A lekérdezés futtatása után letölthet egy CSV-fájlt. Kattintson a **exportálása Excel**.

## <a name="export-to-power-bi"></a>Power BI-exportálás
A kurzor elhelyezése egy lekérdezésben, és válassza a **exportálásához a Power BI**.

![A Power bi-bA Analytics exportálása](./media/app-insights-analytics-using/240.png)

A lekérdezés futtatása a Power bi-ban. Beállíthatja úgy, hogy az ütemezés szerint frissítse.

A Power BI irányítópultjai összefogására olyan adatokat különböző forrásokból hozhat létre.

[További információ a Power bi-ban való exportáláshoz](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Áruházra mutató mélyhivatkozás

Szerezzen be egy hivatkozást a **exportálás megosztás hivatkozás** , amely egy másik felhasználó is küldhet. Ha a felhasználó rendelkezik [hozzáférés az erőforráscsoportba](app-insights-resources-roles-access-control.md), a lekérdezés megnyílik az elemzés felhasználói felületén.

(A hivatkozás a lekérdezés szövegének után jelenik meg "? q =", gzip tömörített és base-64 kódolású. Kód létrehozásához adja meg a felhasználóknak mélyhivatkozással lehet írni. Azonban az ajánlott módszer kódból Analytics futtatásához segítségével el a [REST API](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automatizálás

Használja a [Data Access REST API](https://dev.applicationinsights.io/) elemzési lekérdezések futtatásához. [Például](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (a PowerShell használatával):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Ellentétben a Analytics felhasználói felületén a REST API nem automatikusan hozzáadják a Timestamp típusú korlátozás a lekérdezések. Ne felejtse el, adja hozzá a saját where záradék, hatalmas válaszok megakadályozása érdekében.



## <a name="import-data"></a>Adatok importálása

Adatokat importálhat egy CSV-fájl. A rendszer egy tipikus használati csatlakozhat a a telemetriai adatokból táblákkal statikus adatok. 

Hitelesített felhasználók a telemetriai adatokat egy aliast vagy rejtjelezett azonosító által azonosítható, ha például sikerült egy táblázat, amely leképezhető aliasok valódi nevét importálni. A – kéréstelemetria illesztés elvégzésével felhasználók azonosíthatja az Analytics-jelentések a valódi névvel.

### <a name="define-your-data-schema"></a>Adja meg a Adatséma

1. Kattintson a **beállítások** (a bal felső), majd **adatforrások**. 
2. Adjon hozzá egy adatforrást, a következő utasításokat. A rendszer felkéri adja meg az adatokat, amelyek tartalmaznia kell legalább 10 sorok mintáját. Javítsa a sémát.

Ez határozza meg az adatforrás, amelyet a későbbiekben is használhat egyes táblák importálásához.

### <a name="import-a-table"></a>A tábla importálása

1. Nyissa meg az adatforrása definíciója a listából.
2. Kattintson a "Feltöltés lehetőségre", és kövesse az utasításokat a táblázat feltöltéséhez. Ebbe beletartozik a REST API hívása, és így könnyen automatizálásához. 

A tábla már elemzési lekérdezések használható. Megjelenik az elemzés 

### <a name="use-the-table"></a>A táblázat

Tegyük fel, az adatforrása definíciója nevezik `usermap`, és arról, hogy rendelkezik-e két mező `realName` és `user_AuthenticatedId`. A `requests` is táblához nevű mező `user_AuthenticatedId`, így egyszerűen csatlakoztassa azokat:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
Az eredményül kapott tábla kérelmek rendelkezik egy olyan további oszlop `realName`.

### <a name="import-from-logstash"></a>Importálás a LogStash

Ha [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), Analytics segítségével lekérdezni a naplókat. Használja a [beépülő modul, amely adatok csővezeték elemzés be](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

