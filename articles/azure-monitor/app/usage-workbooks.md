---
title: Interaktív jelentések létrehozása az Azure Monitor-munkafüzetekkel |} A Microsoft docs
description: Egyszerűsítse összetett reporting előre elkészített és az egyéni paraméteres munkafüzetek
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: f7133ed74c9862a7e3d496a9093faaf3959e0d78
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993418"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Interaktív jelentések létrehozása az Azure Monitor-munkafüzetekkel

Munkafüzetek kombinálhatja a szöveg, [elemzési lekérdezések](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), az Azure-mérőszámok és paraméterek látványos interaktív jelentéseket. Munkafüzetek bármely más csapattagokat ugyanazon Azure-erőforrásokhoz való hozzáféréssel rendelkező tagjai szerkeszthetik.

Munkafüzetek például hasznosak:

* Felfedezése az alkalmazás használatát, ha előre nem ismeri az érdekes mérőszám vizsgálatára: számok felhasználók, arányt, átváltási árfolyamok, stb. Ellentétben más használatelemző eszközökkel munkafüzetek lehetővé teszik több bármilyen típusú vizualizációkat és elemzéseket, szabad formátumú feltárása az ilyen nagyszerű minősítené kombinálja.
* A csapata számára elmagyarázza, hogyan teljesít egy újonnan kiadott funkció, amely felhasználó által száma kulcs kapcsolati és más metrikákkal.
* Megosztják az eredményeket, egy A / B kísérletezhet az alkalmazásban, a csapat tagjaival. Ismertetik a kitűzött célokat a kísérlet szövegre, majd minden használati metrika- és elemzési lekérdezés értékeli ki a kísérletet, együtt egyértelmű hívás mintáit mindegyik metrikát volt-e felett vagy alatt-cél az, hogy megjelenítése.
* A leállások hatásának jelentéskészítés a használati adatok, szöveg magyarázata és ismerteti azokat a következő lépéseket a jövőben a leállások elkerülése érdekében az alkalmazás.

## <a name="starting-with-a-template-or-saved-workbook"></a>Egy sablonnal vagy mentett munkafüzet

A munkafüzet függetlenül szerkeszthető diagramokat, táblázatokat, szöveg, amely szakaszok épül fel, és a bemeneti vezérlők. Segít jobban megérteni, munkafüzetek, érdemes egy megnyitásához. 

Válassza ki **munkafüzetek** belül a bal oldali menüből az Application Insights az alkalmazáshoz tapasztalható.

![Navigálás a munkafüzetek képernyőképe](./media/usage-workbooks/001-workbooks.png)

Ezzel elindítja a munkafüzet katalógus előre összeállított munkafüzetek segítséget nyújt az első lépések száma.

![Képernyőkép a munkafüzet gyűjteménye](./media/usage-workbooks/002-workbook-gallery.png)

Kezdjük a **alapértelmezett sablon**, fejléc alatt található **gyors üzembe helyezési**.

![Képernyőkép a munkafüzet gyűjteménye](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Szerkesztés, átrendezése, klónozás és a munkafüzet szakaszok törlése

Munkafüzetek két móddal rendelkeznek: **szerkesztési módban**, és **olvasási módban**. Az alapértelmezett a munkafüzet első megnyitásakor megnyílik a **szerkesztési módban**. Ez jeleníti meg a munkafüzetet, beleértve a lépéseket és egyéb rejtett paraméterek a tartalom. **Olvasási mód** mutat be egy egyszerűsített jelentésnézet style. Ez lehetővé teszi, hogy absztrakt megkezdhető összetettségét, amely adtuk ki a jelentés létrehozása során továbbra is fennáll csak néhány kattintást igényel az alapul szolgáló mechanics, amikor szükséges, a módosítás.

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/editing-controls-new.png)

1. Ha végzett szerkeszt egy szakaszt, kattintson a **Szerkesztés kész** rész bal alsó sarokban található.

2. Duplicitní szakasz létrehozásához kattintson a **szakasz klónozása** ikonra. Ismétlődő szakaszok létrehozása remek lehetőség újrafuttathatja a lekérdezés előző ismétlések elvesztése nélkül.

3. A munkafüzet szakasz áthelyezéséhez kattintson a **feljebb** vagy **mozgatása lefelé** ikonra.

4. Szakasz végleges eltávolításához kattintson a **eltávolítása** ikonra.

## <a name="adding-text-and-markdown-sections"></a>Szöveg- és Markdown-szakaszok hozzáadása

Fejlécek magyarázatok és mellékmondatokkal ad hozzá a munkafüzetek segít információkká táblákat és diagramokat egy narratíva. Munkafüzetek támogatási szöveg szakaszai a [Markdown-szintaxisa](https://daringfireball.net/projects/markdown/) szöveg formázását, például fejlécek félkövér, dőlt és listajeles listák készítéséhez.

A munkafüzet egy szöveges szakasz hozzáadásához használja a **szöveg hozzáadása** gomb alsó részén a munkafüzetet, vagy minden szakasz alján.

## <a name="adding-query-sections"></a>Adja hozzá lekérdezési szakaszokat

![A munkafüzetekben lekérdezés szakaszban](./media/usage-workbooks/analytics-section-new.png)

A munkafüzet lekérdezésszakaszt hozzáadásához használja a **lekérdezés hozzáadása** gomb alsó részén a munkafüzetet, vagy minden szakasz alján.

Lekérdezési szakaszokat rendkívül rugalmas, és hasonló kérdések megválaszolása céljából használható:

* Hány kivételek fejeződött a hely throw idő alatt, csökken a használat során?
* Mi volt a lapbetöltési idők néhány lap megtekintésének felhasználók elosztását?
* Hány felhasználó helyen tekinthetők meg néhány azon lapok készlete, de nem más lapok készlete? Ez akkor lehet hasznos megértéséhez, ha a felhasználók, akik használni a webhely funkciók részhalmazokat fürttel rendelkezik (használja a `join` az operátor a `kind=leftanti` módosító adatkezelő lekérdezési nyelven).

Akkor is nem csak legfeljebb lekérdezése az alkalmazás a környezetből, elindul a munkafüzet. Több Application Insights figyelt alkalmazások, valamint a Log Analytics-munkaterületek között lekérdezés, amennyiben ezek az erőforrások hozzáférési jogosultsággal rendelkezik.

A lekérdezés további külső Application Insights erőforrások használatát a **alkalmazás** azonosítója.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Ez a lekérdezés van kombinálva három különböző alkalmazás érkező kérelmeket. Egy alkalmazás nevű app01, az alkalmazás app02, és a kérelmek az Application Insights-erőforrás neve.

Használatával egy külső Log Analytics-munkaterület használata az adatok kérhetők le a **munkaterület** azonosítója.

Erőforrások közötti lekérdezések bővebben lásd: a [hivatalos útmutatást](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Speciális elemzési lekérdezés beállításai

Minden szakasz rendelkezik a saját speciális beállítások, amelyek elérhetők a beállítások ikonra ![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/005-settings.png) jobb oldalán található a **paraméterek hozzáadása** gombra.

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Egyéni szélessége**    | Ezt a elem egy tetszőleges méretű, győződjön meg, így sok elem kiférjen egy sorba, így jobban a diagramok és táblázatok szervezheti látványos interaktív jelentéseket állítja be.  |
   | **Feltételesen láthatóvá** | Ezzel az olvasási módban egy paraméter alapján rejthet el lépéseket. |
   | **Paraméter exportálása**| Ez lehetővé teszi a kijelölt sor a rács vagy diagram okozhat a későbbi lépésekben értékek módosításához, és láthatóvá válhat.  |
   | **Lekérdezés megjelenítése nem szerkesztési módban** | Ez a lekérdezés felett a diagramra vagy a táblázatra, még akkor is, ha az olvasási módban a jeleníti meg.
   | **Megnyitás az elemzés gomb megjelenítése nem szerkesztési módban** | Ez hozzáadja a kék Analytics ikonra az egykattintásos hozzáférést a diagram jobb sarkában.|

Ezen beállítások többsége viszonylag könnyen használható, de megértéséhez **paraméter exportálása** vizsgálja meg a munkafüzet jobb használó ezt a funkciót.

Aktív felhasználók egy előre létrehozott munkafüzetre nyújt tájékoztatást.

A munkafüzet első szakasza elemzési lekérdezés adatai alapján:

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/003-active-users.png)

A második szakasz is elemzési adatok alapján, de az első tábla egy sor kijelölése interaktív módon frissíti a diagram tartalmát:

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/004-active-users-trend.png)

 Ez akkor lehetséges, az használatával a **egy elem kijelölésekor paraméter exportálása** speciális beállítások, amelyek engedélyezve vannak a táblázat elemzési lekérdezés.

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/007-settings-export.png)

A második elemzési lekérdezés majd az exportált értékeket használja, egy sor kiválasztásakor. Ha egyetlen sor van kijelölve, a rendszer alapértelmezés szerint az általános értékeket képviselő sor. 

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

## <a name="adding-metrics-sections"></a>Metrikák szakaszok hozzáadása

Metrikák szakaszok a Azure Monitor-metrikák adatokat építhet be az interaktív jelentések teljes hozzáférést biztosít. Elemzési adatok és a metrikaadatokat, így kihasználhatja a legjobb mindkét szolgáltatást egy helyen számos, előre létrehozott munkafüzeteket tartalmazza. Akkor is lehetővé teszi bármely hozzáfér az előfizetések erőforrásokból metrikaadatok bekérésére.

Íme egy példa alatt álló rács képi megjelenítés, CPU-teljesítményt biztosít a munkafüzetbe lekért virtuális gépek adatainak:

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adja hozzá a paraméter szakaszokat

A munkafüzet paraméterek lehetővé teszik a munkafüzetben szereplő értékeket módosítsa a lekérdezésekben vagy szakaszok manuális szerkesztése nélkül.  Ez a feleslegessé kellene a mögöttes analytics lekérdezési nyelv ismertetése, és jelentősen kibővíti a munkafüzet-alapú reporting lehetséges célközönségét.

A paraméterek helyébe a lekérdezés, szöveges vagy más paraméter szakaszok üzembe zárójelek között, a paraméter neve például ``{parameterName}``.  Paraméternevek korlátozva, hasonló szabályokat JavaScript azonosítók, alapvetően alfabetikus karaktereket és aláhúzásjeleket tartalmazhat, alfanumerikus karakter követ. Például **a1** engedélyezett, de **1a** nem engedélyezett.

Paraméterek a következők lineáris, kezdve a felső részén egy munkafüzetet és a későbbi lépésekben lefelé halad.  Később a munkafüzet deklarált paraméterek felül lehet bírálni, amelyek további be lett deklarálva.  Ez lehetővé teszi továbbá, hogy az értékek hozzá a definiált paraméterek használata lekérdezések további beállítása paramétereket.  Belül magát egy paraméter lépésben paraméterei is lineáris, balról jobbra, ahol paraméterek jobb ugyanazt a lépés a korábbi deklarált paramétert is függenek.
 
A jelenleg támogatott paraméterek négy különböző típusa van:

  |         |          |
   | ---------------- |:-----|
   | **Szöveg**    | a felhasználó szerkeszteni egy szövegmezőt, és igény szerint megadhat egy lekérdezést az alapértelmezett értéket adja meg. |
   | **Legördülő lista** | A felhasználó az értékek közül fog választani. |
   | **Időtartomány-választó**| A felhasználó választhat az előre definiált tartományértékeknek idő, vagy egy egyéni időtartományt válasszon.|
   | **Erőforrás-választó** | A felhasználó a a munkafüzetet a kijelölt erőforrások közül fog választani.|

### <a name="using-a-text-parameter"></a>Egy szöveges paraméter használatával

Az érték a felhasználó begépeli a szövegmezőben közvetlenül a lekérdezésben az escape-karaktersorozat vagy idézése váltja fel. A szükséges érték karakterlánc, ha a lekérdezés kell rendelkeznie a paraméter idézőjeleket (például **'{parameter}'**).

Ez lehetővé teszi, hogy egy szövegmező, bárhol használható az értéket. Lehet, hogy a táblázat neve, oszlopnév, függvénynév, operátor, stb.

A szöveg paraméter típusa van egy beállítás **alapértelmezett érték beolvasása elemzési lekérdezésből**, amely lehetővé teszi, hogy a lekérdezés használatával töltse fel az alapértelmezett érték a szövegmezőben, hogy a munkafüzet szerzője.

Az alapértelmezett érték az analytics-lekérdezések használata esetén az első sort (sort 0, 0 oszlop) csak az első értéket az alapértelmezett érték lesz. Ezért ajánlott a lekérdezést korlátozni szeretné csak egy sort, és a egy oszlopot ad vissza. A lekérdezés által visszaadott minden egyéb adatot figyelmen kívül hagyja. 

Akármilyen területen is a lekérdezés által visszaadott érték helyettesítődik közvetlenül escape-karaktersorozat vagy megkísérelte idézőjelek közé rakni. Ha a lekérdezés a sor nem ad vissza, az eredmény a paraméter üres karakterlánc (Ha a paraméter nem kötelező) vagy (Ha a paraméter megadása kötelező) nincs definiálva.

### <a name="using-a-dropdown"></a>Legördülő lista használatával

A legördülő listából paraméter típusa létrehozását teszi lehetővé egy legördülő vezérlőt, amely lehetővé teszi a kijelölt egy vagy több értéket.

A legördülő listában analytics-lekérdezések alapján van feltöltve. A lekérdezés egy oszlopot ad vissza, ha az oszlopban szereplő értékeket is a **érték** és a **címke** a legördülő lista vezérlőelem. A lekérdezés két oszlopot ad vissza, ha az első oszlop van-e a **érték**, és a második oszlop az **címke** jelenik meg a legördülő listában.  A lekérdezés a három oszlopot ad vissza, ha a 3. oszlop jelzi, hogy a legördülő listában az alapértelmezett beállítás szolgál.  Ez az oszlop bármilyen típusú is lehet, de a legegyszerűbb az, hogy logikai vagy numerikus típusok, ahol 0 a False (hamis), és 1 igaz.

 Ha az oszlop egy karakterlánc típusú, NULL értékű vagy üres karakterlánc számít false (hamis), és semmilyen más érték igaz akkor tekinthető. Az egyetlen kijelölésre legördülő menük az első érték a true érték szolgál az alapértelmezetten kiválasztottat.  Több kiválasztása legördülő menük minden érték a true érték az alapértelmezett beállítás szerint történik. A legördülő menüben található elemek jelennek meg bármilyen sorrendben a lekérdezésben visszaadott sorok. 

Tekintsük át a paramétereket, az aktív felhasználók jelentés szerepel. Kattintson a Tovább gombra a Szerkesztés szimbólum **TimeRange**.

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/009-time-range.png)

Ez a paraméter szerkesztése menüelem fog megjelenni:

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/010-time-range-edit.png)

A lekérdezés az analytics lekérdezési nyelvre nevű egy olyan szolgáltatását használja egy **datatable** , amely lehetővé teszi egy tetszőleges táblát a teljes tartalom, vékony légi kívül! Ha például a következő elemzési lekérdezés:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Állít elő, az eredmény:

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/usage-workbooks/011-data-table.png)

Egy több megfelelő példa használja legördülő lista bizonyos országokban név alapján választja ki:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

A lekérdezés a következő eredményeket jeleníti meg:

![Ország legördülő menü](./media/usage-workbooks/012-country-dropdown.png)

Legördülő menük olyan nagyon hatékony eszközök testreszabása és interaktív jelentések létrehozásához.

### <a name="time-range-parameters"></a>Idő tartomány paraméterei

A saját egyéni tartomány paraméter a legördülő listából paramétertípus keresztül teheti meg, amíg is használhatja az out-of-box időtartomány paraméter típusa Ha már nincs szüksége az azonos mértékű rugalmasságot. 

Idő tartomány paramétertípusok 15-öt perc alatt az Ugrás az utolsó 90 napban alapértelmezett címtartományokat kell. Egyéni időtartomány kiválasztásának, amely lehetővé teszi az üzemeltető explicit kezdő választható, és állítsa le az értékeket az időtartományt a jelentés engedélyezze a lehetőség is van.

### <a name="resource-picker"></a>Erőforrás-választó

Az erőforrás-választó paraméter típusa lehetővé teszi, a hatókör bizonyos típusú erőforrások a jelentést. Előre összeállított munkafüzet, amely az erőforrás-választó típusú például a **hiba Insights** munkafüzetet.

![Ország legördülő menü](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>És munkafüzetek megosztása munkatársaival

Application Insights-erőforrás, akár a mentett munkafüzetek a **jelentések mappa** magánjellegű, vagy a szakasz a **megosztott jelentések** hozzáférést mindenki számára elérhető, a szakasz a Application Insights-erőforrás. Az erőforrás minden a munkafüzetek megtekintéséhez kattintson a **nyílt** gombra a művelet sávon.

A munkafüzetet, amely a jelenleg **jelentések mappa**:

1. Kattintson a **nyílt** a művelet sávon
2. Kattintson a "..." gombot a megosztani kívánt munkafüzet
3. Kattintson a **helyezze át a megosztott jelentések**.

A munkafüzet egy hivatkozást, vagy e-mailen keresztül megosztásához kattintson **megosztása** a művelet sávon. Ne feledje, hogy a címzettek a hivatkozásra kell a munkafüzet megtekintéséhez az Azure Portalon az erőforráshoz való hozzáférés. Ahhoz, hogy a módosításokat, címzettek van szükség, legalább közreműködői engedélye az erőforrás.

A PIN-kódot egy munkafüzetet, egy Azure-irányítópultra mutató hivatkozást:

1. Kattintson a **nyílt** a művelet sávon
2. Kattintson a "..." gombot szeretne rögzíteni a munkafüzet
3. Kattintson a **rögzítés az irányítópulton**.

## <a name="contributing-workbook-templates"></a>A munkafüzet sablonok közreműködő

Létrehozott egy nagyszerű munkafüzet-sablon és a Közösség többi tagjával is megoszthat szeretné? További tudnivalókért látogasson el a [GitHub-adattárat](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>További lépések
- Használati bővítsék küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [Lapmegtekintések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha már küldhet egyéni eseményeket vagy lapmegtekintéseket, Fedezze fel az megtudhatja, hogy a felhasználók használni a szolgáltatást a Használatelemzési eszközben.
    - [Felhasználók, munkamenetek, események](../../azure-monitor/app/usage-segmentation.md)
    - [Tölcsérek](../../azure-monitor/app/usage-funnels.md)
    - [Megőrzés](../../azure-monitor/app/usage-retention.md)
    - [Felhasználói folyamatok](../../azure-monitor/app/usage-flows.md)
    - [Adja hozzá a felhasználói környezet](../../azure-monitor/app/usage-send-user-context.md)