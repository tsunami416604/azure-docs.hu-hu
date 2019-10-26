---
title: Interaktív jelentések létrehozása Azure Monitor munkafüzetekkel | Microsoft docs
description: Összetett jelentéskészítés egyszerűsítése előre elkészített és egyéni paraméterekkel rendelkező munkafüzetekből
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bbbf511286c63acf4a939e0a0e7d9c3dc9efa75b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899376"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Interaktív jelentések létrehozása Azure Monitor munkafüzetekkel

A munkafüzetek szöveg-, [elemzési](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)és Azure-metrikákat és-paramétereket egyesítenek gazdag interaktív jelentésekben. A munkafüzetek szerkeszthető más csapattagok számára, akik ugyanahhoz az Azure-erőforrásokhoz férnek hozzá.

A munkafüzetek a következő esetekben hasznosak:

* Az alkalmazás használatának megvizsgálása, ha nem tudja, hogy milyen mérőszámok vannak előre: a felhasználók száma, a megőrzési arány, az átalakítási arány stb. A többi használati elemzési eszköztől eltérően a munkafüzetek több fajta vizualizációt és elemzést is lehetővé tesznek, így nagyszerűen használhatók az ilyen típusú, ingyenes űrlapos feltáráshoz.
* Elmagyarázza a csapatának, hogy hogyan végzi el az újonnan kiadott funkciót, és a felhasználók számát jeleníti meg a kulcsfontosságú interakciók és más metrikák esetében.
* Egy A/B kísérlet eredményeinek megosztása az alkalmazásban a csapat más tagjaival. Megadhatja a kísérlet szöveggel kapcsolatos céljait, majd megjelenítheti a kísérlet kiértékeléséhez használt egyes használati metrikákat és elemzési lekérdezéseket, valamint egyértelmű meghívásokat, hogy az egyes metrikák a fenti vagy a cél alatt voltak-e.
* Az alkalmazás használatának, az adatok összevonásának, a szöveg magyarázatának és a következő lépések megvitatását a jövőben a leállások megelőzése érdekében.

## <a name="starting-with-a-template-or-saved-workbook"></a>Kezdés sablon vagy mentett munkafüzet alapján

A munkafüzetek egymástól függetlenül szerkeszthető diagramokat, táblázatokat, szövegeket és beviteli vezérlőket tartalmazó részből állnak. A munkafüzetek jobb megismeréséhez érdemes megnyitnia egyet. 

Válassza ki a **munkafüzetek** elemet a bal oldali menüben az alkalmazás Application Insights felületén belül.

![Képernyőkép a munkafüzetek közötti navigálásról](./media/usage-workbooks/001-workbooks.png)

Ezzel elindít egy munkafüzet-katalógust számos előre elkészített munkafüzettel, amelyek segítenek az első lépések megtételében.

![Képernyőfelvétel a munkafüzet-gyűjteményről](./media/usage-workbooks/002-workbook-gallery.png)

Kezdjük az **alapértelmezett sablonnal**, amely az első **lépések**fejléc alatt található.

![Képernyőfelvétel a munkafüzet-gyűjteményről](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>A munkafüzet fejezeteinek szerkesztése, átrendezése, klónozása és törlése

A munkafüzeteknek két módja van: **szerkesztési mód**és **olvasási mód**. Az alapértelmezett munkafüzet első indításakor a **szerkesztési módban**nyílik meg. Ez megjeleníti a munkafüzet összes tartalmát, beleértve az egyébként rejtett lépéseket és paramétereket is. Az **olvasási mód** egy egyszerűsített jelentési stílus nézetet jelenít meg. Így elkerülheti a jelentés létrehozásakor felmerülő összetettséget, miközben az alapul szolgáló mechanika csak néhány kattintással elvégezhető a módosításhoz.

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/editing-controls-new.png)

1. Ha elkészült egy szakasz szerkesztésével, kattintson a **kész szerkesztés** gombra a szakasz bal alsó sarkában.

2. Egy szakasz másodpéldányának létrehozásához kattintson a **szakasz klónozása** ikonra. Az ismétlődő lépések létrehozása nagyszerű módja annak, hogy a lekérdezéseket az előző iterációk elvesztése nélkül lehessen megismételni.

3. Ha feljebb szeretne helyezni egy szakaszt egy munkafüzetben, kattintson a **fel** vagy **a lejjebb ikonra** .

4. Egy szakasz végleges eltávolításához kattintson az **Eltávolítás** ikonra.

## <a name="adding-text-and-markdown-sections"></a>Szöveg-és Markdown-csoportok hozzáadása

A fejlécek, a magyarázatok és a kommentárok hozzáadása a munkafüzetekhez megkönnyíti a táblázatok és diagramok egy csoportba való bekapcsolását. A munkafüzetek szöveges szakaszai támogatják a [Markdown szintaxisát](https://daringfireball.net/projects/markdown/) , például a fejléceket, a félkövér, a dőlt és a listajeles listákat.

Ha szöveges szakaszt szeretne felvenni a munkafüzetbe, használja a munkafüzet alján található **szöveg hozzáadása** gombot, vagy a szakasz alján.

## <a name="adding-query-sections"></a>Lekérdezési csoportok hozzáadása

![A munkafüzetek lekérdezési szakasza](./media/usage-workbooks/analytics-section-new.png)

A lekérdezési szakasz a munkafüzethez való hozzáadásához használja a munkafüzet alján található **lekérdezés hozzáadása** gombot, vagy a szakasz alján.

A lekérdezési szakaszban nagyon rugalmasak, és a következő kérdésekre lehet válaszolni:

* Hány kivételt hajtottak végre a webhely a használat csökkenésével megegyező időszakban?
* Mi volt az oldal betöltési idejének megoszlása a felhasználók számára egy oldal megtekintésekor?
* Hány felhasználó tekintett meg néhány oldalt a webhelyen, de nem más lapokat? Ez akkor lehet hasznos, ha olyan felhasználói fürtökkel rendelkezik, akik a hely funkcióinak különböző részhalmazait használják (használja a `join` operátort a `kind=leftanti` módosítóval a [Kusto lekérdezési nyelvén](/azure/kusto/query/)).

Emellett nem csak a munkafüzetből indított alkalmazás környezetében való lekérdezésre korlátozódik. Több Application Insights figyelt alkalmazásban, valamint Log Analytics munkaterületeken is lekérdezheti, ha rendelkezik hozzáférési engedéllyel ezen erőforrásokhoz.

További külső Application Insights-erőforrások lekérdezéséhez használja az **alkalmazás** azonosítóját.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Ez a lekérdezés három különböző alkalmazástól származó kérelmeket egyesít. Egy app01 nevű alkalmazás, egy app02 nevű alkalmazás, valamint a helyi Application Insights erőforrástól érkező kérések.

Az adatok külső Log Analytics munkaterületről való lekéréséhez használja a **munkaterület** -azonosítót.

Ha többet szeretne megtudni az erőforrások közötti lekérdezésekről, tekintse meg a [hivatalos útmutatást](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Speciális analitikus lekérdezési beállítások

Az egyes szakaszok saját speciális beállításokkal rendelkeznek, amelyek a beállítások ikonon keresztül érhetők el ![Application Insights munkafüzetek szakaszban, a **Paraméterek hozzáadása** gombtól jobbra található vezérlők szerkesztése](./media/usage-workbooks/005-settings.png).

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Egyéni szélesség**    | Ezt úgy állíthatja be, hogy tetszőleges méretű elemet hozzon létre, így egyetlen sorban sok elem is elfér, így a diagramokat és a táblákat jobban rendszerezheti interaktív jelentésekben.  |
   | **Feltételesen látható** | Ezzel a lépésekkel elrejtheti a paramétereket a paraméterek alapján, ha olvasási módban van. |
   | **Paraméter exportálása**| Ez lehetővé teszi, hogy a rács vagy a diagram kijelölt sora későbbi lépéseket okozzon az értékek módosításához vagy láthatóvá váljon.  |
   | **Lekérdezés megjelenítése nem szerkesztéskor** | Ez megjeleníti a diagram vagy a tábla feletti lekérdezést, még olvasási módban is.
   | **Megnyitás az Analyticsben gomb megjelenítése nem szerkesztéskor** | Ezzel hozzáadja a kék elemzési ikont a diagram jobb oldali sarkához az egykattintásos hozzáférés engedélyezéséhez.|

Ezeknek a beállításoknak a nagy része meglehetősen intuitív, de a **Paraméterek exportálásának** megismeréséhez érdemes megvizsgálni egy olyan munkafüzetet, amely használja ezt a funkciót.

Az előre elkészített munkafüzetek egyike az aktív felhasználókról nyújt információkat.

A munkafüzet első szakasza az analitikus lekérdezési adatmennyiségen alapul:

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/003-active-users.png)

A második szakasz az analitikus lekérdezési adatmennyiségen alapul, de az első táblázatban lévő sorok kiválasztásakor a rendszer interaktív módon frissíti a diagram tartalmát:

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/004-active-users-trend.png)

 Ez az **elem kiválasztásakor lehetséges, ha** olyan speciális beállításokat exportál, amelyek engedélyezve vannak a tábla elemzési lekérdezésében.

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/007-settings-export.png)

A második elemzési lekérdezés ezt követően kijelöli az exportált értékeket egy sor kijelölésekor. Ha nincs kiválasztva sor, a rendszer alapértelmezés szerint a teljes értékeket jelképező sort jelöli. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Metrikák fejezeteinek hozzáadása

A metrikák részben teljes hozzáférést biztosítanak a Azure Monitor metrikák adatait az interaktív jelentésekhez. Az előre elkészített munkafüzetek közül számos analitikus lekérdezési adatokat és metrikus adatokat is tartalmaz, így teljes mértékben kihasználhatja mindkét funkció előnyeit egy helyen. Lehetősége van arra is, hogy az erőforrásokból származó metrikus adatokat bármely olyan előfizetésből lehívhatja, amelyhez hozzáfér.

Itt látható egy példa arra, hogy a virtuális gép adatai egy munkafüzetbe kerüljenek, és a CPU-teljesítmény rácsos megjelenítését biztosítják:

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Paraméterek hozzáadása

A munkafüzet-paraméterek lehetővé teszik az értékek módosítását a munkafüzetben anélkül, hogy manuálisan kellene szerkeszteni a lekérdezési vagy szöveges szakaszt.  Ez megszünteti az alapul szolgáló analitikai lekérdezési nyelv megismerésének követelményét, és nagy mértékben kiterjeszti a munkafüzet-alapú jelentéskészítés lehetséges célközönségét.

A paraméterek értékei a lekérdezés, a szöveg vagy más paraméterek részekben vannak lecserélve, ha a paraméter nevét kapcsos zárójelbe helyezi, például ``{parameterName}``.  A paraméterek nevei a hasonló szabályokra korlátozódnak JavaScript-azonosítóként, alapvetően alfabetikus karakterként vagy aláhúzásként, amelyet alfanumerikus karakterek vagy aláhúzások követnek. Az **a1** például engedélyezve van, de az **1a** nem engedélyezett.

A paraméterek lineárisak, a munkafüzetek elejétől kezdve, és a későbbi lépésekig áramlanak.  A munkafüzet későbbi részében deklarált paraméterek felülbírálják azokat, amelyeket a rendszer még nem jelentett.  Ez azt is lehetővé teszi, hogy a lekérdezéseket használó paraméterek hozzáférjenek a további definiált paraméterek értékeihez.  A paraméter lépésein belül a paraméterek is lineárisak, balról jobbra, ahol a jobb oldali paraméterek az adott lépésben korábban bejelentett paraméterektől függően változhatnak.
 
A jelenleg négy különböző típusú paraméter támogatott:

  |         |          |
   | ---------------- |:-----|
   | **Szöveg**    | a felhasználó szerkesztheti a szövegmezőt, és igény szerint megadhat egy lekérdezést az alapértelmezett érték kitöltéséhez. |
   | **Legördülő lista** | A felhasználó az értékek egy halmazát fogja választani. |
   | **Időtartomány-választó**| A felhasználó egy előre meghatározott időtartomány-érték közül választhat, vagy egyéni időtartományból válogathat.|
   | **Erőforrás-választó** | A felhasználó a munkafüzethez kiválasztott erőforrások közül választhat.|

### <a name="using-a-text-parameter"></a>Szöveges paraméter használata

A szövegmezőben a felhasználói típusok értékét a rendszer közvetlenül a lekérdezésben cseréli le, és nem jár el. Ha a szükséges érték egy karakterlánc, a lekérdezésnek idézőjelekkel kell rendelkeznie a paraméter körül (például **"{paraméter}"** ).

Ez lehetővé teszi, hogy a szövegmezőben lévő értéket bárhol lehessen használni. Ez lehet egy tábla neve, egy oszlop neve, egy függvény neve, operátor stb.

A Text paraméter típusa beállítás az **alapértelmezett érték beolvasása az elemzési lekérdezésből**, amely lehetővé teszi, hogy a munkafüzet szerzője lekérdezés használatával töltse fel az adott szövegmezőhöz tartozó alapértelmezett értéket.

Egy elemzési lekérdezés alapértelmezett értékének használatakor a rendszer csak az első sor első értékét (0. sor, 0. oszlop) használja alapértelmezett értékként. Ezért javasolt a lekérdezés korlátozása, hogy csak egy sort és egy oszlopot adjanak vissza. A rendszer figyelmen kívül hagyja a lekérdezés által visszaadott összes egyéb adatkérést. 

A lekérdezés által visszaadott értéket a rendszer közvetlenül a szökés vagy az idézés nélkül váltja fel. Ha a lekérdezés nem ad vissza sorokat, a paraméter eredménye vagy üres karakterlánc (ha a paraméter nem szükséges) vagy nincs megadva (ha a paraméter megadása kötelező).

### <a name="using-a-dropdown"></a>Legördülő lista használata

A legördülő paraméter típusa lehetővé teszi egy legördülő lista létrehozását, amely lehetővé teszi egy vagy több érték kijelölését.

A legördülő lista egy elemzési lekérdezéssel van feltöltve. Ha a lekérdezés egy oszlopot ad vissza, az oszlopban szereplő értékek mind az **érték** , mind a legördülő lista **címkéje** . Ha a lekérdezés két oszlopot ad vissza, akkor az első oszlop az **érték**, a második oszlop pedig a legördülő menüben látható **felirat** .  Ha a lekérdezés három oszlopot ad vissza, a harmadik oszlop a legördülő lista alapértelmezett kijelölését jelzi.  Ez az oszlop bármilyen típusú lehet, de a legegyszerűbb a logikai vagy numerikus típusok használata, ahol a 0 hamis, és 1 igaz.

 Ha az oszlop karakterlánc típusú, a null/üres karakterlánc hamisnak minősül, és minden más érték igaznak minősül. Az egyszeres kiválasztási legördülő listában az alapértelmezett érték a true értékkel lesz kiválasztva.  Több kijelölési legördülő lista esetén az összes igaz értékkel rendelkező érték lesz az alapértelmezett kijelölt készlet. A legördülő listában szereplő elemek a lekérdezés által visszaadott sorrendben jelennek meg. 

Nézzük meg az aktív felhasználók jelentésben szereplő paramétereket. Kattintson a **TimeRange**melletti Szerkesztés szimbólumra.

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/009-time-range.png)

Ekkor elindul a paraméter szerkesztése menüelem:

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/010-time-range-edit.png)

A lekérdezés a **DataTable** nevű elemzési lekérdezési nyelv egy funkcióját használja, amely lehetővé teszi egy tetszőleges tábla, teljes tartalom, valamint a vékony levegőben történő létrehozását. Például a következő elemzési lekérdezés:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Az eredmény generálása:

![Application Insights munkafüzetek szakasz szerkesztési vezérlői](./media/usage-workbooks/011-data-table.png)

Egy részletesebb példa egy olyan legördülő listát használ, amely az országok/régiók csoportból való kiválasztását használja név szerint:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

A lekérdezés a következőképpen jeleníti meg az eredményeket:

![Ország legördülő lista](./media/usage-workbooks/012-country-dropdown.png)

A legördülő lista hihetetlenül hatékony eszközöket biztosít az interaktív jelentések testreszabásához és létrehozásához.

### <a name="time-range-parameters"></a>Időintervallum-paraméterek

Míg a legördülő menüből a saját egyéni időtartomány-paramétert is létrehozhatja, a beépített időtartomány-paraméter típusát is használhatja, ha nem kell ugyanolyan fokú rugalmasságot biztosítania. 

Az időtartomány-paraméterek típusának 15 alapértelmezett tartománya van, amelyek öt perctől az utolsó 90 napra mutatnak. Lehetőség van az egyéni időtartomány kiválasztására is, amely lehetővé teszi, hogy a jelentés kezelője explicit indítási és leállítási értékeket válasszon az időtartományhoz.

### <a name="resource-picker"></a>Erőforrás-választó

Az erőforrás-választó paraméterének típusa lehetővé teszi, hogy a jelentést bizonyos típusú erőforrásokra szűkítse. Példa az erőforrás-választót használó előre létrehozott munkafüzetre, amely a **sikertelen** elemzések munkafüzetét használja.

![Ország legördülő lista](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Munkafüzetek mentése és megosztása a csapattal

A munkafüzetek egy Application Insights erőforráson belül lesznek mentve, vagy a **saját jelentések** szakaszban, amely az Ön számára elérhető, vagy a **megosztott jelentések** szakaszban, amelyet mindenki elérhet a Application Insights erőforráshoz való hozzáféréssel. Az erőforrás összes munkafüzetének megtekintéséhez kattintson a művelet sávban a **Megnyitás** gombra.

A jelenleg **saját jelentésekban**lévő munkafüzetek megosztása:

1. Kattintson a **Megnyitás** gombra a műveleti sávon
2. Kattintson a "..." a megosztani kívánt munkafüzet melletti gomb
3. Kattintson **a áthelyezés megosztott jelentésekre**elemre.

Egy hivatkozással vagy e-mailen keresztüli munkafüzet megosztásához kattintson a művelet sávban a **megosztás** elemre. Ne feledje, hogy a hivatkozás címzettjeinek hozzáféréssel kell rendelkezniük ehhez az erőforráshoz a Azure Portal a munkafüzet megtekintéséhez. A módosítások elvégzéséhez a címzetteknek legalább közreműködői engedélyekkel kell rendelkezniük az erőforráshoz.

Egy munkafüzetre mutató hivatkozás rögzítése egy Azure-irányítópulton:

1. Kattintson a **Megnyitás** gombra a műveleti sávon
2. Kattintson a "..." a rögzíteni kívánt munkafüzet melletti gomb
3. Kattintson **a rögzítés az irányítópulton**elemre.

## <a name="contributing-workbook-templates"></a>Közreműködő munkafüzet-sablonok

Létrehozott egy fantasztikus munkafüzet-sablont, és szeretné megosztani a Közösséggel? További információért látogasson el a [GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)-tárházba.

## <a name="next-steps"></a>Következő lépések
- A használati tapasztalatok engedélyezéséhez kezdjen el [Egyéni eseményeket](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [oldalletöltések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)küldését.
- Ha már elküldte az egyéni eseményeket vagy a lapok nézeteit, tekintse meg a használati eszközöket, amelyekkel megismerheti, hogy a felhasználók miként használják a szolgáltatást.
    - [Felhasználók, munkamenetek, események](../../azure-monitor/app/usage-segmentation.md)
    - [Tölcsérek](../../azure-monitor/app/usage-funnels.md)
    - [Megőrzés](../../azure-monitor/app/usage-retention.md)
    - [Felhasználói folyamatok](../../azure-monitor/app/usage-flows.md)
    - [Felhasználói környezet hozzáadása](../../azure-monitor/app/usage-send-user-context.md)