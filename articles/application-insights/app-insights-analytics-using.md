---
title: Analytics - a hatékony keresési eszköz az Azure Application Insights használatával |} A Microsoft Docs
description: 'Az elemzés, a hatékony diagnosztikai keresés eszköz az Application Insights használatával. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2018
ms.reviewer: danha
ms.author: mbullwin
ms.openlocfilehash: aa86e2f3b1fb147ab167c948475a5207693143c2
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341562"
---
# <a name="using-analytics-in-application-insights"></a>Az Application Insights Analytics használatával
[Analytics](app-insights-analytics.md) az hatékony keresési szolgáltatása [Application Insights](app-insights-overview.md). Ezeket az oldalakat a Log Analytics lekérdezési nyelv leírása.

* **[Tekintse meg a bevezető videót](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Próbálja ki az Analytics szimulált adatokon](https://analytics.applicationinsights.io/demo)**  az alkalmazás nem adatot küldenek, az Application Insightsba még.

## <a name="open-analytics"></a>Nyissa meg Analytics
Az alkalmazás otthoni erőforrásból az Application insights szolgáltatásban kattintson az Analytics.

![Portal.Azure.com címen nyissa meg, nyissa meg az Application Insights-erőforrást, és kattintson az Analytics.](./media/app-insights-analytics-using/001.png)

A beágyazott oktatóanyag néhány ötletet arról, mi mindent biztosít.

Van egy [itt szélesebb körű bemutatót](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>A telemetriai adatok lekérdezése
### <a name="write-a-query"></a>A lekérdezés írása
![Séma megjelenítése](./media/app-insights-analytics-using/150.png)

A bal oldalon felsorolt táblákat nevei kezdődik (vagy a [tartomány](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) vagy [union](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operátorok). Használat `|` hozhat létre egy folyamatot, [operátorok](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

Az IntelliSense kéri az operátorok és a kifejezés elemeket, amelyeket használhat. Információs ikonra (vagy nyomja le a CTRL + szóköz) hosszabb leírását és példákat egyes elemeit használhatja.

Tekintse meg a [Analytics nyelvet bemutató](app-insights-analytics-tour.md) és [nyelvi referencia](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Lekérdezés futtatása
![A lekérdezés futtatása](./media/app-insights-analytics-using/130.png)

1. A lekérdezés egyetlen sortörést is használhatja.
2. Helyezze a kurzort, belül vagy a futtatni kívánt lekérdezés végén.
3. Ellenőrizze az időtartományt a lekérdezés. (Módosíthatja azt, vagy felülbírálását, többek között a saját által [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) záradék szerepel a lekérdezésben.)
3. Kattintson a lekérdezés futtatásához nyissa meg.
4. Nem üres sorok helyezze el a lekérdezést. Egy lekérdezés lapon több elkülönített lekérdezés is megőrizheti az üres sorok elválasztva őket. Csak a lekérdezést, amely rendelkezik a kurzor futtatja.

### <a name="save-a-query"></a>Lekérdezés mentése
![Lekérdezés mentése](./media/app-insights-analytics-using/140.png)

1. Mentse a jelenlegi lekérdezés fájlt.
2. Nyisson meg egy korábban mentett lekérdezés fájlt.
3. Hozzon létre egy új lekérdezés fájlt.

## <a name="see-the-details"></a>Részletek
Bontsa ki az eredményeket a hozzá tartozó tulajdonságok teljes listáját lásd: az összes sort. Bármilyen tulajdonság, amely strukturált érték – például, az egyéni vagy a kivételt az ajánlati verem további bővítheti.

![Bontsa ki a sor](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Az eredmények rendezése
Rendezheti, szűrheti, oldalakra bontása és csoport az a lekérdezés által visszaadott eredményeket.

> [!NOTE]
> Rendezési, csoportosítást és szűrőt a böngészőben nem futtassa újból a lekérdezést. Ezek csak a legutóbbi lekérdezés által visszaadott eredmények átrendezését. 
> 
> A műveletek végrehajtásához a kiszolgálón, mielőtt a rendszer visszairányítja az eredményeket, a lekérdezés írása a [rendezési](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [összefoglalója](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) és [ahol](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operátorok.
> 
> 

Válassza ki az oszlopok megtekintheti, húzza az oszlopfejlécre, és átrendezheti őket, és azok szegélyek húzásával méretezheti át az oszlopokat.

![Oszlopok rendezése](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Elemek rendszerezése és szűrése
Az oszlopok fejlécére kattintva rendezheti az eredményeket. Kattintson ismét a más módon rendezheti, majd kattintson a harmadik visszatér az eredeti sorrendjét a lekérdezés eredményeként kapott időt.

A szűrő ikon használatával szűkíthető.

![Oszlopok rendezése és szűrése](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Elemek csoportosítása
Több oszlop szerinti rendezéshez, a csoportosítás használatához. Először engedélyezni, és húzza a terén a tábla feletti oszlopfejléceket.

![Csoport](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Hiányoznak egyes eredményeket?

Ha úgy véli, hogy nem látja a várt az összes eredmény, van néhány lehetséges okai.

* **Tartomány Időszűrő**. Alapértelmezés szerint az elmúlt 24 órában eredményei csak akkor jelenik meg. Egy automatikus szűrő, amely korlátozza az eredmények, amelyek a forrás-táblák lekérése a tartományban van. 

    Azonban módosíthatja az időtartományt szűrjön rá a legördülő menüből.

    Az automatikus tartomány felülírhatja többek között a saját vagy [ `where  ... timestamp ...` záradék](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) be a lekérdezést. Példa:

    `requests | where timestamp > ago('2d')`

* **Eredmények korlát**. Az eredmények a portál által visszaadott sorain körülbelül 10 k korlátozva van. Figyelmeztetés látható, ha haladnak át a korlátot. Ha ez történik, a táblázatban az eredmények rendezése nem mindig jelennek meg, az összes tényleges első vagy utolsó eredmény. 

    Hasznos lehet, hogy elkerülje a korlátot. Az Időszűrő címtartományt használja, vagy operátorok:

  * [az első 100-tárhelyek időbélyegző szerint](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [100 igénybe](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [Összegzés ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [ahol időbélyeg > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Több mint 10 k sorokat szeretne? Fontolja meg [folyamatos exportálás](app-insights-export-telemetry.md) helyette. Analytics készült elemzés, nem pedig nyers adatok beolvasása során.)

## <a name="diagrams"></a>Diagramok
Válassza ki a diagram szeretné típusát:

![A diagram típusának kiválasztása](./media/app-insights-analytics-using/230.png)

Ha több oszlopot a jobb oldali típusú, kiválaszthatja az x és y tengellyel és dimenziók felosztása által az eredményeket tartalmazó oszlop.

Alapértelmezés szerint eredmény kezdetben látható táblaként, és manuálisan válassza ki a diagramon. Is használhatja, de a [irányelv render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) válassza ki a diagram lekérdezés végén.

### <a name="analytics-diagnostics"></a>Analytics-diagnosztika


A idődiagramját van egy hirtelen megugrás vagy a lépés az adatok jelenhet meg, egy kijelölt pont a sor. Ez azt jelzi, hogy Analytics diagnosztikai azonosította szűrje ki a hirtelen módosítás tulajdonságok kombinációja. Kattintson a pont, a szűrő a részletekért és szűrt verzió megtekintéséhez. Ez segíthet azonosítani, hogy mi okozta, hogy a módosítás. 

[További információ az Analytics-diagnosztika](app-insights-analytics-diagnostics.md)


![Analytics-diagnosztika](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Rögzítés az irányítópulton
Kitűzheti a diagram vagy a táblát egy a [megosztott irányítópultokat](app-insights-dashboards.md) – egyszerűen kattintson a PIN-kódot. 

![Kattintson a PIN-kód](./media/app-insights-analytics-using/pin-01.png)

Ez azt jelenti, hogy helyezett együtt egy irányítópulton megfigyelheti a teljesítmény vagy a webszolgáltatások használatát, megadhatja a más metrikákkal együtt meglehetősen összetett elemzésére. 

Egy táblát az irányítópultra, rögzítheti, így ha négy vagy kevesebb oszlopot. Csak a felső hét sorok jelennek meg.

### <a name="dashboard-refresh"></a>Irányítópult frissítése
A diagram az irányítópulton rögzített futtatásával újból a lekérdezést körülbelül óránként automatikusan frissülnek. A frissítés gombra is kattinthat.

### <a name="automatic-simplifications"></a>Automatikus egyszerűbb

Bizonyos egyszerűbb lépnek érvénybe a diagram rögzítése az irányítópultra.

**Korlátozás ideje:** lekérdezések korlátozódnak automatikusan az elmúlt 30 napban. A hatás megegyezik, ha a lekérdezésben `where timestamp > ago(30d)`.

**Bin száma korlátozás:** diszkrét bins (általában egy sávdiagram) sok rendelkező diagram megjelenítése, ha a kevésbé feltöltött bins automatikusan csoportosítva vannak benne egyetlen "egyéb" bin. Ha például a lekérdezés:

    requests | summarize count_search = count() by client_CountryOrRegion

az elemzések a következőhöz hasonló:

![Hosszú tollal mutató részletes diagram](./media/app-insights-analytics-using/pin-07.png)

de irányítópulthoz rögzít, amikor nézhet ki:

![A korlátozott bins mutató részletes diagram](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportálás Excelbe
A lekérdezés futtatása után letölthet egy .csv-fájlba. Kattintson a **exportálni, az Excel**.

## <a name="export-to-power-bi"></a>Power BI-exportálás
Helyezze a kurzort egy lekérdezést, és válassza a **exportálása a Power BI**.

![Analytics exportálhat a Power bi-bA](./media/app-insights-analytics-using/240.png)

A lekérdezés futtatása a Power bi-ban. Beállíthatja, hogy az ütemezés szerint frissítse.

A Power BI-jal, amelyek egyesítik a adatokat a különféle forrásokból származó irányítópultokat hozhat létre.

[További tudnivalók a Power bi-ban való exportálás](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Áruházra mutató mélyhivatkozás

Szerezzen be egy hivatkozást a **, exportálási és megosztási hivatkozás** , melyet elküldhet egy másik felhasználónak. Ha a felhasználó rendelkezik [hozzáférés az erőforráscsoporthoz](app-insights-resources-roles-access-control.md), a lekérdezés a Analytics felhasználói felület nyílik meg.

(A lekérdezés szövege megjelenik a hivatkozással után "? a q =", tömörítése a gzip és base-64 kódolású. Felhasználók számára biztosított mélyhivatkozások létrehozása kód is írható. Azonban az ajánlott módszer a kódból elemzések futtatására van használatával a [REST API-val](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automation

Használja a [Data Access REST API](https://dev.applicationinsights.io/) elemzési lekérdezések futtatásához. [Például](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (a PowerShell használatával):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Ellentétben a Analytics felhasználói felületén a REST API nem adja hozzá automatikusan bármely időbélyeg korlátozás a lekérdezések. Ne felejtse el hozzáadni a saját where záradék, első hatalmas válaszok elkerülése érdekében.



## <a name="import-data"></a>Adatok importálása

Adatokat importálhat egy CSV-fájl. Egy tipikus használati a rendszer importálja a statikus adatok, amelyek csatlakozhat a telemetriai adatokból származó táblákkal. 

A hitelesített felhasználók a telemetria egy aliast vagy rejtjelezett azonosító alapján azonosítja, ha például sikerült valódi nevét, az aliasokat leképező tábla importálása. A kérelmek telemetriai adatai illesztés elvégzésével felhasználók azonosíthatja az Analytics-jelentések a valódi névvel.

### <a name="define-your-data-schema"></a>Adja meg a sémát

1. Kattintson a **beállítások** (a bal felső sarokban), majd **adatforrások**. 
2. Adjon hozzá egy adatforráshoz, a biztonsági utasítások. A rendszer felkéri az adatokat, ami tartalmaznia kell legalább 10 sort egy mintát adja meg. Majd javítsa ki a sémát.

Ez meghatározza az adatforrás, amely ezután használhatja az egyes táblák importálása.

### <a name="import-a-table"></a>Tábla importálása

1. Nyissa meg az adatforrása definíciója a listából.
2. Kattintson a "Feltöltés" elemre, és kövesse az utasításokat a táblázat feltöltéséhez. Ebbe beletartozik egy REST API-hívást, és így egyszerűen automatizálható. 

A tábla már elérhető az elemzési lekérdezések. Analytics meg fog jelenni 

### <a name="use-the-table"></a>A táblázat

Most tegyük fel, az adatforrása definíciója neve `usermap`, és arról, hogy vannak-e két mezőt `realName` és `user_AuthenticatedId`. A `requests` tábla szintén tartalmaz egy mezőnév `user_AuthenticatedId`, így egyszerűen össze őket:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
Az eredményül kapott tábla kérelmek rendelkezik egy olyan további oszlop `realName`.

### <a name="import-from-logstash"></a>Importálás a Logstashből

Ha [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), Analytics használatával lekérdezheti a naplókat. Használja a [beépülő modul, amely átadja az adatokat analyticsbe](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

