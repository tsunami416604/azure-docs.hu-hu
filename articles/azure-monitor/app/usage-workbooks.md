---
title: Interaktív jelentések létrehozása az Azure Monitor munkafüzeteivel | Microsoft-dokumentumok
description: Az összetett jelentések egyszerűsítése előre összeállított és egyéni paraméterezett munkafüzetekkel
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671001"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Interaktív jelentések létrehozása az Azure Monitor munkafüzeteivel

A munkafüzetek a szöveget, [az Analytics-lekérdezéseket](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), az Azure Metrics-t és a paramétereket gazdag interaktív jelentésekké egyesítik. A munkafüzeteket bármely más csapattag szerkeszti, akik hozzáférnek ugyanazokhoz az Azure-erőforrásokhoz.

A munkafüzetek olyan esetekben hasznosak, mint például:

* Az alkalmazás használatának feltárása, ha nem ismeri előre az érdeklődési mutatókat: a felhasználók száma, megőrzési arányok, konverziós arányok stb. A többi használatelemző eszközzel ellentétben a munkafüzetek segítségével többféle vizualizációt és elemzést kombinálhat, így nagyszerűek az ilyen típusú szabad formájú feltáráshoz.
* Elmagyarázza a csapatnak, hogyan teljesít egy újonnan kiadott funkció, a legfontosabb interakciók és egyéb mutatók felhasználói számának megjelenítésével.
* Az alkalmazás a/B-kísérlet eredményeinek megosztása a csapat többi tagjával. Elmagyarázhatja a kísérlet céljait a szöveggel, majd megjelenítheti az egyes használati metrikákat és a kísérlet kiértékeléséhez használt Analytics-lekérdezéseket, valamint egyértelmű kijelentéseket arról, hogy az egyes mutatók a cél felett vagy alatt voltak-e.
* A kimaradásnak az alkalmazás használatára gyakorolt hatásának jelentése, az adatok, a szöveges magyarázat és a jövőbeli kimaradások megelőzésére szolgáló következő lépések megvitatása.

## <a name="starting-with-a-template-or-saved-workbook"></a>Kezdés sablonnal vagy mentett munkafüzettel

A munkafüzet ektől függetlenül szerkeszthető diagramokból, táblázatokból, szövegekből és beviteli vezérlőkből álló szakaszokból áll. A munkafüzetek jobb megértéséhez a legjobb, ha megnyit egyet. 

Válassza ki a **munkafüzetek** a bal oldali menüből az Application Insights-élmény az alkalmazáshoz.

![Képernyőkép a munkafüzetekbe való navigálásról](./media/usage-workbooks/001-workbooks.png)

Ezzel elindítja a munkafüzet-gyűjteményt, amely számos előre összeállított munkafüzetet foglal el az első lépések hez.

![Képernyőkép a munkafüzetgyűjteményről](./media/usage-workbooks/002-workbook-gallery.png)

Kezdjük az **Alapértelmezett sablonnal,** amely a **Gyorsindítás**cím alatt található.

![Képernyőkép a munkafüzetgyűjteményről](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Munkafüzet-szakaszok szerkesztése, átrendezése, klónozása és törlése

A munkafüzetek két üzemmódban is rendelkeznek: **szerkesztési módés** **olvasási mód.** Az alapértelmezett munkafüzet első indításakor **szerkesztési módban**nyílik meg. Ez a munkafüzet összes tartalmát megjeleníti, beleértve az egyébként rejtett lépéseket és paramétereket is. **Az olvasási mód** egyszerűsített jelentésstílus-nézetet jelenít meg. Ez lehetővé teszi, hogy absztrakt el a komplexitás, hogy bement létre egy jelentést, miközben továbbra is az alapul szolgáló mechanika csak néhány kattintásnyira van, ha szükséges a módosításhoz.

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/editing-controls-new.png)

1. Ha végzett egy szakasz szerkesztésével, kattintson a szakasz bal alsó sarkában található **Szerkesztés nélkül** elemre.

2. Szakasz másolatának létrehozásához kattintson a **Szakasz klónozása** ikonra. Az ismétlődő szakaszok létrehozása nagyszerű módja annak, hogy a lekérdezések en a korábbi ismétlések elvesztése nélkül iteráljon.

3. Ha feljebb szeretne lépni egy munkafüzet egy szakaszával, kattintson a **Fel** vagy **a Le** ikonra.

4. Ha véglegesen el szeretne távolítani egy **szakaszt,** kattintson az Eltávolítás ikonra.

## <a name="adding-text-and-markdown-sections"></a>Szöveg és Markdown-szakaszok hozzáadása

Ha címsorokat, magyarázatokat és kommentárokat ad a munkafüzethez, akkor a táblázatok és diagramok készletét narratívaként alakíthatja át. A munkafüzetek szövegszakaszai támogatják a szövegformázás [Markdown szintaxisát,](https://daringfireball.net/projects/markdown/) például a címsorokat, a félkövér, a dőlt és a listajeles listákat.

Ha szövegszakaszt szeretne hozzáadni a munkafüzethez, használja a **Szöveg hozzáadása** gombot a munkafüzet alján vagy bármely szakasz alján.

## <a name="adding-query-sections"></a>Lekérdezési szakaszok hozzáadása

![Lekérdezés szakasz a munkafüzetekben](./media/usage-workbooks/analytics-section-new.png)

Ha lekérdezési szakaszt szeretne hozzáadni a munkafüzethez, használja a **Lekérdezés hozzáadása** gombot a munkafüzet alján vagy bármely szakasz alján.

A lekérdezési szakaszok rendkívül rugalmasak, és olyan kérdések megválaszolására használhatók, mint például:

* Hány kivételt tett a webhely dobott ugyanebben az időszakban, mint a használat csökkenése?
* Mi volt az oldalbetöltési idők eloszlása az egyes oldalakat megtekintő felhasználók számára?
* Hány felhasználó tekintett meg néhány lapot a webhelyén, de más oldalakat nem? Ez hasznos lehet annak megértéséhez, hogy a webhely funkcióinak különböző részhalmazait `join` használó `kind=leftanti` felhasználók csoportjai vannak-e (használja az operátort a [Kusto lekérdezési nyelvének](/azure/kusto/query/)módosítójával).

Nem csak arra korlátozódik, hogy annak az alkalmazásnak a környezetéből kérdezzünk, amelyből elindította a munkafüzetet. Lekérdezheti több Application Insights figyelt alkalmazások, valamint a Log Analytics-munkaterületek, amíg rendelkezik hozzáférési engedéllyel ezeket az erőforrásokat.

További külső Application Insights-erőforrások **app** lekérdezéséhez használja az alkalmazásazonosítót.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Ez a lekérdezés három különböző alkalmazásból származó kérelmeket egyesít. Egy app01 nevű alkalmazás, egy app02 nevű alkalmazás és a helyi Application Insights-erőforrástól érkező kérések.

Ha egy külső Log Analytics-munkaterületről szeretne adatokat beszedni, használja a **munkaterület** azonosítóját.

Ha többet szeretne megtudni az erőforrás-közi lekérdezésekről, olvassa el a [hivatalos útmutatást.](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)

### <a name="advanced-analytic-query-settings"></a>Speciális analitikus lekérdezési beállítások

Minden szakasz saját speciális beállításokkal rendelkezik, ![amelyek a beállítások ikonon Application Insights Munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/005-settings.png) található a jobb oldalon a Paraméterek **hozzáadása** gomb.

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Egyéni szélesség**    | Állítsa be ezt úgy, hogy egy elem tetszőleges méretű legyen, így több elemet is elérhet egyetlen sorban, így jobban rendszerezheti a diagramokat és táblázatokat gazdag interaktív jelentésekbe.  |
   | **Feltételesen látható** | Ezzel elrejtheti a paramétereken alapuló lépéseket olvasási módban. |
   | **Paraméter exportálása**| Ez lehetővé teszi, hogy a rács vagy a diagram kijelölt sora később módosíthatja az értékeket, vagy láthatóvá váljon.  |
   | **Lekérdezés megjelenítése szerkesztés kor** | Ez a lekérdezés a diagram vagy a táblázat felett jelenik meg, még olvasási módban is.
   | **Megnyitás az elemzésben gomb szerkesztés kor** | Ezzel hozzáadja a kék Analytics ikont a diagram jobb sarkához, így egy kattintással elérhető.|

A legtöbb ilyen beállítás meglehetősen intuitív, de a **paraméter exportálásához** jobb megvizsgálni egy munkafüzetet, amely kihasználja ezt a funkciót.

Az egyik előre összeállított munkafüzet az Aktív felhasználókról nyújt tájékoztatást.

A munkafüzet első szakasza analitikus lekérdezési adatokon alapul:

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/003-active-users.png)

A második szakasz szintén analitikus lekérdezési adatokon alapul, de az első tábla egy sorának kijelölése interaktívmódon frissíti a diagram tartalmát:

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/004-active-users-trend.png)

 Ez a Ha egy elem kiválasztása kor lehetőséget **választja, exportálja** a tábla Analytics-lekérdezésében engedélyezett speciális paramétereket.

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/007-settings-export.png)

A második elemzési lekérdezés ezután az exportált értékeket használja, ha egy sor van kiválasztva. Ha nincs kijelölt sor, akkor az alapértelmezett érték az általános értékeket jelölő sorra. 

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

## <a name="adding-metrics-sections"></a>Metrikaszakaszok hozzáadása

A Metrikák szakaszok teljes hozzáférést biztosítanak az Azure Monitor metrikaadatainak interaktív jelentésekbe való beépítéséhez. Az előre összeállított munkafüzetek közül sok analitikus lekérdezési adatokat és metrikaadatokat is tartalmaz, amelyek lehetővé teszik, hogy mindkét funkció ból a lehető legjobbat kihasználhassa egy helyen. Azt is lehetővé teszi, hogy lekérése metrika adatokat az erőforrások bármelyik előfizetések hozzáféréssel rendelkezik.

Íme egy példa arra, hogy a virtuális gép adatai bevannak húzva egy munkafüzetbe, hogy a PROCESSZOR teljesítményének rácsos vizualizációját biztosítsák:

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Paraméterszakaszok hozzáadása

A munkafüzet paraméterei lehetővé teszik a munkafüzet értékeinek módosítását anélkül, hogy manuálisan kellene módosítani a lekérdezési vagy szövegszakaszokat.  Ez megszünteti azt a követelményt, hogy meg kell érteni az alapul szolgáló elemzési lekérdezési nyelvet, és jelentősen kibővíti a munkafüzet-alapú jelentések potenciális közönségét.

A paraméterek értékeit a lekérdezési, szöveg- vagy más paraméterszakaszokban lecseréli a ``{parameterName}``paraméter neve kapcsos zárójelbe, például .  A paraméternevek a JavaScript-azonosítókhoz, alapvetően alfabetikus karakterekhez vagy aláhúzásjelekhez hasonló szabályokra korlátozódnak, amelyeket alfanumerikus karakterek vagy aláhúzásjelek követnek. Például **az a1** megengedett, de az **1a** nem megengedett.

A paraméterek lineárisak, a munkafüzet tetejétől kezdve a későbbi lépésekig.  A munkafüzet későbbi részében deklarált paraméterek felülbírálhatják a továbbfelé deklarált paramétereket.  Ez azt is lehetővé teszi, hogy a lekérdezések használatával elérhető értékek et a további feljebb definiált paraméterekből érje el.  Egy paraméter lépésén belül a paraméterek is lineárisak, balról jobbra, ahol a jobb oldali paraméterek függhetnek egy paramétertől, amelyet ugyanebben a lépésben korábban deklaráltak.
 
Jelenleg négy különböző típusú paraméter támogatott:

  |         |          |
   | ---------------- |:-----|
   | **Szöveg**    | a felhasználó szerkeszti a szövegdobozt, és tetszés szerint megadhat egy lekérdezést az alapértelmezett érték kitöltéséhez. |
   | **Legördülő lista** | A felhasználó egy értékkészlet közül fog választani. |
   | **Időtartomány-választó**| A felhasználó egy előre meghatározott időtartomány-értékkészlet közül választhat, vagy egyéni időtartományból választ.|
   | **Erőforrás-választó** | A felhasználó a munkafüzethez kiválasztott erőforrások közül fog választani.|

### <a name="using-a-text-parameter"></a>Szövegparaméter használata

A felhasználó által a szövegdobozban beírt értéket a rendszer közvetlenül lecseréli a lekérdezésben, és nem kerül ki, és nem idéz. Ha a szükséges érték karakterlánc, a lekérdezésnek idézőjelekkel kell rendelkeznie a paraméter körül (például **"{parameter}" ).**

Ez lehetővé teszi, hogy a szövegdobozban lévő érték bárhol használható legyen. Ez lehet egy tábla neve, oszlop neve, függvény neve, operátor, stb.

A szövegparaméter típusa rendelkezik egy ** Alapértelmezett érték beszámítása az elemzési lekérdezésből**beállítással, amely lehetővé teszi, hogy a munkafüzet szerzője lekérdezéssel feltöltse az adott szövegdoboz alapértelmezett értékét.

Az elemzési lekérdezés alapértelmezett értékének használatakor a rendszer csak az első sor első értékét használja (0. sor, 0. oszlop) a rendszer alapértelmezett értékként. Ezért ajánlott korlátozni a lekérdezést, hogy csak egy sort és egy oszlopot ad vissza. A lekérdezés által visszaadott egyéb adatokat a rendszer figyelmen kívül hagyja. 

Bármilyen értéket is ad vissza a lekérdezés, közvetlenül lecserélődik, és nem kerül ki, és nem idéz. Ha a lekérdezés nem ad vissza sorokat, a paraméter eredménye vagy egy üres karakterlánc (ha a paraméter nem szükséges) vagy nincs definiálva (ha a paraméter szükséges).

### <a name="using-a-dropdown"></a>Legördülő menü használata

A legördülő paraméter típusa lehetővé teszi egy legördülő vezérlő létrehozását, amely lehetővé teszi egy vagy több érték kiválasztását.

A legördülő menüt egy elemzési lekérdezés tölti fel. Ha a lekérdezés egy oszlopot ad vissza, az oszlopban lévő értékek a legördülő **vezérlőben** szereplő értéket és **feliratot** is tartalmazják. Ha a lekérdezés két oszlopot ad vissza, az első oszlop az **érték**, a második pedig a legördülő menüben látható **címke.**  Ha a lekérdezés három oszlopot ad vissza, a harmadik oszlop jelzi a legördülő lista alapértelmezett kiválasztását.  Ez az oszlop bármilyen típusú lehet, de a legegyszerűbb a bool vagy numerikus típusok használata, ahol a 0 hamis, és az 1 igaz.

 Ha az oszlop karakterlánctípusú, a null/üres karakterlánc hamisnak, és minden más érték igaznak minősül. Az egyszeri kijelölés legördülő menüknél a program az első, valódi értékkel rendelkező értéket használja alapértelmezett ként.  Több kijelöléslegördülő menü esetén a program a valós értékű értékeket használja alapértelmezettként. A legördülő menü elemei a lekérdezés által visszaadott sorok sorrendjében jelennek meg. 

Tekintsük át az Aktív felhasználók jelentésben található paramétereket. Kattintson a **TimeRange**melletti szerkesztési szimbólumra.

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/009-time-range.png)

Ekkor megjelenik a Paraméter szerkesztése menüpont:

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/010-time-range-edit.png)

A lekérdezés az elemzési lekérdezési nyelv egy **datatable** nevű szolgáltatását használja, amely lehetővé teszi egy tetszőleges, tartalommal teli tábla generálását a semmiből! Például a következő elemzési lekérdezés:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Az eredményt hozza létre:

![Az Application Insights-munkafüzetek szakaszszerkesztő vezérlői](./media/usage-workbooks/011-data-table.png)

Egy alkalmazhatóbb példa egy legördülő lista segítségével választ egy sor ország / régiók név szerint:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

A lekérdezés az eredményeket a következőképpen jeleníti meg:

![Ország legördülő menü](./media/usage-workbooks/012-country-dropdown.png)

A legördülő menük hihetetlenül hatékony eszközök az interaktív jelentések testreszabásához és létrehozásához.

### <a name="time-range-parameters"></a>Időtartomány paraméterei

Bár a legördülő paraméter típusán keresztül saját egyéni időtartomány-paramétert hozhat létre, használhatja a beépített időtartomány paramétertípusát is, ha nincs szüksége ugyanolyan fokú rugalmasságra. 

Az időtartomány-paramétertípusok 15 alapértelmezett tartományt használnak, amelyek öt perctől az utolsó 90 napig terjednek. Lehetőség van egyéni időtartomány-kiválasztásengedélyezésére is, amely lehetővé teszi, hogy a jelentés kezelője explicit kezdő és leállítási értékeket válasszon az időtartományhoz.

### <a name="resource-picker"></a>Erőforrás-választó

Az erőforrás-választó paraméter típusa lehetővé teszi a jelentés hatókörét bizonyos típusú erőforrásokra. Egy példa az előre összeállított munkafüzet, amely kihasználja az erőforrás-választó típus a **Failure Insights** munkafüzet.

![Ország legördülő menü](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Munkafüzetek mentése és megosztása a csapattal

A munkafüzetek egy Application Insights-erőforráson belül kerülnek mentésre, vagy az Ön számára privát ként rendelkező **Saját jelentések** szakaszban, vagy a **Megosztott jelentések** szakaszban, amely mindenki számára elérhető, aki hozzáfér az Application Insights-erőforráshoz. Az erőforrás összes munkafüzetének megtekintéséhez kattintson a műveletsáv **Megnyitás** gombjára.

A **Saját jelentések**ben lévő munkafüzet megosztása:

1. Kattintson a **műveletsáv Megnyitás** gombjára
2. Kattintson a "..." gombot a megosztani kívánt munkafüzet mellett
3. Kattintson **az Áthelyezés megosztott jelentésekre gombra.**

Ha egy munkafüzetet hivatkozással vagy e-mailben szeretne megosztani, kattintson a műveletsáv **Megosztás gombjára.** Ne feledje, hogy a hivatkozás címzettjeinek hozzáférésre van szükségük ehhez az erőforráshoz az Azure Portalon a munkafüzet megtekintéséhez. A szerkesztéshez a címzetteknek legalább közreműködői engedélyekkel kell rendelkezniük az erőforráshoz.

Munkafüzetre mutató hivatkozás rögzítése Egy Azure-irányítópultra:

1. Kattintson a **műveletsáv Megnyitás** gombjára
2. Kattintson a "..." gombot a rögzíteni kívánt munkafüzet mellett
3. Kattintson **a Rögzítés az irányítópultra gombra.**

## <a name="contributing-workbook-templates"></a>Közreműködő munkafüzetsablonok

Létrehozott egy fantasztikus munkafüzetsablont, és szeretné megosztani a közösséggel? Ha többet szeretne megtudni, látogasson el a [GitHub repo](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>További lépések
- A használati élmény engedélyezéséhez kezdje el elküldeni [az egyéni eseményeket](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [oldalnézeteket.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Ha már küld egyéni eseményeket vagy oldalnézeteket, fedezze fel a Használati eszközöket, hogy megtudja, hogyan használják a felhasználók a szolgáltatást.
    - [Felhasználók, munkamenetek, események](../../azure-monitor/app/usage-segmentation.md)
    - [Tölcsérek](../../azure-monitor/app/usage-funnels.md)
    - [Megőrzés](../../azure-monitor/app/usage-retention.md)
    - [Felhasználói folyamatok](../../azure-monitor/app/usage-flows.md)
    - [Felhasználói környezet hozzáadása](../../azure-monitor/app/usage-send-user-context.md)
