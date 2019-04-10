---
title: Interaktív jelentések létrehozása az Azure Monitor-munkafüzetekkel |} A Microsoft Docs
description: Egyszerűsítse összetett jelentéskészítést az előre meghatározott és egyéni paraméteres munkafüzetekkel Azure Monitor-beli virtuális gépek.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288711"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Interaktív jelentések létrehozása az Azure Monitor-munkafüzetekkel

Munkafüzetek kombinálhatja a szöveg, [lekérdezések naplózását](../log-query/query-language.md), metrikákat és paraméterek látványos interaktív jelentéseket. Munkafüzetek bármely más csapattagokat ugyanazon Azure-erőforrásokhoz való hozzáféréssel rendelkező tagjai szerkeszthetik.

Munkafüzetek is hasznos forgatókönyvek például:

* Felfedezése a virtuális gép használatát, ha előre nem ismeri az érdekes mérőszám vizsgálatára: CPU-kihasználtság, lemezterület, memória, hálózati függőségeket, stb. Ellentétben más használatelemző eszközökkel munkafüzetek lehetővé teszik több bármilyen típusú vizualizációkat és elemzéseket, szabad formátumú feltárása az ilyen nagyszerű minősítené kombinálja.
* A csapata számára elmagyarázza, hogyan nemrég üzembe helyezett virtuális gép működik-e, kulcsfontosságú teljesítményszámlálók és más alkalmazásnapló-események metrikáinak megjelenítésével.
* A virtuális gép egy átméretezési kísérlet eredményeit osztanak meg a csoport más tagjai. Ismertetik a kitűzött célokat a kísérlet szövegre, majd minden egyes használati metrika- és elemzési lekérdezések megjelenítése a kísérletet, és egyértelmű hívás mintáit tartozó mindegyik metrikát volt-e felett vagy alatt-célzott kiértékeléséhez használta.
* Jelentéskészítés a leállások hatásának a virtuális gép adatait, szöveg magyarázata és ismerteti azokat a következő lépéseket a jövőben a leállások elkerülése érdekében a használatát.

A virtuális gépek az Azure Monitor tartalmaz a kezdéshez több munkafüzetek, és a következő táblázat összefoglalja azokat.

| Munkafüzet | Leírás | Hatókör |
|----------|-------------|-------|
| Teljesítmény | Biztosít egy testre szabható verzióját a felső N listázása és a diagramok megtekintése egyetlen munkafüzetben, amely minden, a Log Analytics-teljesítményszámlálók, amelyeken engedélyezve.| Ipari méretekben |
| Teljesítményszámlálók | A felső N diagram nézet több széles teljesítményszámlálók. | Ipari méretekben |
| Kapcsolatok | Kapcsolatok egy a bejövő és kimenő kapcsolatok részletes áttekintést nyújt a figyelt virtuális gépekről. | Ipari méretekben |
| Aktív portok | A folyamatok, amelyek van kötve, a portok a figyelt virtuális gépek és a választott időkereten tevékenységével listáját tartalmazza. | Ipari méretekben |
| Nyitott portok | A portok száma nyissa meg a figyelt virtuális gépeken, majd a részletek a portok biztosít. | Ipari méretekben |
| Sikertelen kapcsolatok | Sikertelen kapcsolatok száma megjelenjenek a figyelt virtuális gépek, a hibák trendjét, és ha a hibák aránya az növekszik az idő múlásával. | Ipari méretekben |
| Biztonság és naplózás | A TCP/IP-forgalom, amely átfogó kapcsolatok, rosszindulatú kapcsolatokat, ahol az IP-végpontok találhatók globálisan jelentést elemzése.  Ahhoz, hogy az összes funkciót, szüksége lesz biztonsági észleléséhez. | Ipari méretekben |
| TCP-forgalom | Rangsorolt a jelentést a figyelt virtuális gépek és azok elküldött, a fogadott és a teljes hálózati forgalmat a rács, és egy trendvonal jelenik meg. | Ipari méretekben |
| Adatforgalom-összehasonlítás | A munkafüzetek lehetővé teszi a hálózati forgalom trendeket egyetlen gép vagy gépek csoport összehasonlítása. | Ipari méretekben |
| Teljesítmény | Biztosít egy testre szabható a teljesítmény nézet, amely a Log Analytics teljesítményszámlálók, hogy engedélyezte az összes verzióját. | Egyetlen virtuális gép | 
| Kapcsolatok | Kapcsolatok egy a bejövő és kimenő kapcsolatok részletes áttekintést nyújt a virtuális gépről. | Egyetlen virtuális gép |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Egy sablonnal vagy mentett munkafüzet

A munkafüzet függetlenül szerkeszthető diagramokat, táblázatokat, szöveg, amely szakaszok épül fel, és a bemeneti vezérlők. Most segít jobban megérteni, munkafüzetek, egy sablon megnyitásával indítsa el, és végig egy egyéni munkafüzet létrehozása. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki **virtuális gépek**.

3. Válasszon ki egy virtuális gépet a listából.

4. A virtuális gép lapon található a **figyelés** szakaszban jelölje be **Insights (előzetes verzió)**.

5. Válassza ki a virtuális gép insights oldalon **teljesítmény** vagy **Maps** lapot, majd **nézet munkafüzetek** a hivatkozásból az oldalon. 

    ![Navigálás a munkafüzetek képernyőképe](media/vminsights-workbooks/workbook-option-01.png)

6. A legördülő listából válassza ki a **katalógus megnyitása** a lista alján.

    ![Képernyőkép a munkafüzet legördülő lista](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Elindul a munkafüzet-katalógus előre összeállított munkafüzetek segítséget nyújt az első lépések száma.

7. Kezdjük a **alapértelmezett sablon**, fejléc alatt található **gyors üzembe helyezési**.

    ![Képernyőkép a munkafüzet gyűjteménye](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>A munkafüzet szakaszok szerkesztése

Munkafüzetek két móddal rendelkeznek: **szerkesztési módban**, és **olvasási módban**. Az alapértelmezett sablon a munkafüzet első megnyitásakor megnyílik a **szerkesztési módban**. A munkafüzetet, beleértve a lépéseket és egyéb rejtett paraméterek összes tartalmát jeleníti meg. **Olvasási mód** mutat be egy egyszerűsített jelentésnézet style. Olvasási mód absztrakt megkezdhető összetettségét, amely adtuk ki a jelentés létrehozása során továbbra is fennáll csak néhány kattintást igényel az alapul szolgáló mechanics, amikor szükséges, a módosítás lehetővé teszi.

![Az Azure Monitor virtuális gépek munkafüzetek szakasz vezérlők szerkesztése](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Ha befejezte egy szakasz szerkesztését, kattintson a **Szerkesztés kész** a szakaszban a bal alsó sarkában található.

2. Duplicitní szakasz létrehozásához kattintson a **szakasz klónozása** ikonra. Ismétlődő szakaszok létrehozása remek lehetőség újrafuttathatja a lekérdezés előző ismétlések elvesztése nélkül.

3. A munkafüzet szakasz áthelyezéséhez kattintson a **feljebb** vagy **mozgatása lefelé** ikonra.

4. Szakasz végleges eltávolításához kattintson a **eltávolítása** ikonra.

## <a name="adding-text-and-markdown-sections"></a>Szöveg- és Markdown-szakaszok hozzáadása

Fejlécek magyarázatok és mellékmondatokkal ad hozzá a munkafüzetek segít információkká táblákat és diagramokat egy narratíva. Munkafüzetek támogatási szöveg szakaszai a [Markdown-szintaxisa](https://daringfireball.net/projects/markdown/) szöveg formázását, például fejlécek félkövér, dőlt és listajeles listák készítéséhez.

A munkafüzet egy szöveges szakasz hozzáadásához használja a **szöveg hozzáadása** gomb alsó részén a munkafüzetet, vagy minden szakasz alján.

## <a name="adding-query-sections"></a>Adja hozzá lekérdezési szakaszokat

![A munkafüzetekben lekérdezés szakaszban](media/vminsights-workbooks/005-workbook-query-section.png)

A munkafüzet lekérdezésszakaszt hozzáadásához használja a **lekérdezés hozzáadása** gomb alsó részén a munkafüzetet, vagy minden szakasz alján.

Lekérdezési szakaszokat rendkívül rugalmas, és hasonló kérdések megválaszolása céljából használható:

* Milyen volt a CPU-kihasználtság alatt idő alatt megnövekedett hálózati forgalom?
* Mi volt a rendelkezésre álló lemezterület változása az elmúlt hónapban?
* Hány hálózati kapcsolati hibák találkoztak a virtuális gépem az elmúlt két hétben? 

Akkor is nem csak korlátozott való lekérdezésekor a virtuális gép a környezetből, elindul a munkafüzet. Több virtuális gépeket, valamint Log Analytics-munkaterületek között lekérdezés, amennyiben ezek az erőforrások hozzáférési jogosultsággal rendelkezik.

Adatok más Log Analytics-munkaterületek, illetve egy adott Application Insights segítségével a **munkaterület** azonosítója. Erőforrások közötti lekérdezésekkel kapcsolatos további tudnivalókért tekintse meg a [hivatalos útmutatást](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Speciális elemzési lekérdezés beállításai

Minden szakasz rendelkezik a saját speciális beállítások, amelyek elérhetők a beállítások ![munkafüzetek szakasz szerkesztési vezérlők](media/vminsights-workbooks/006-settings.png) jobb oldalán található ikon a **paraméterek hozzáadása** gombra.

![Az Azure Monitor virtuális gépek munkafüzetek szakasz vezérlők szerkesztése](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Egyéni szélessége**    | Lehetővé teszi egy elemet egy tetszőleges méretű, így sok elem kiférjen egy sorba, így jobban a diagramok és táblázatok szervezheti gazdag, interaktív jelentésekkel.  |
| **Feltételesen láthatóvá** | Adja meg, amely olvasási módban egy paraméter alapján rejthet el lépéseket. |
| **Paraméter exportálása**| Lehetővé teszi a rács vagy diagram okozhat a későbbi lépésekben értékek módosításához, és láthatóvá válnak a kijelölt sorhoz.  |
| **Lekérdezés megjelenítése nem szerkesztési módban** | Megjeleníti a lekérdezés felett a diagramra vagy a táblázatra, még akkor is, ha olvasási módban.
| **Az elemzésben való megnyitásra szolgáló gomb megjelenítése nem szerkesztési üzemmódban** | Az egykattintásos hozzáférést a diagram jobb sarkában ad hozzá a kék Analytics ikonra.|

Ezen beállítások többsége viszonylag könnyen használható, de megértéséhez **paraméter exportálása** vizsgálja meg a munkafüzet jobb használó ezt a funkciót.

Egy előre létrehozott munkafüzetre - **TCP-forgalom**, kapcsolati metrika információt nyújt a virtuális gépről.

A munkafüzet első szakasza a naplóadatok lekérdezés alapul. A második szakasz is log lekérdezés adatokon alapulnak, de egy sor kijelölése az első tábla interaktív módon frissíti a diagramok tartalmát:

![Az Azure Monitor virtuális gépek munkafüzetek szakasz vezérlők szerkesztése](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

A viselkedés az használatával lehetőség a **egy elem kijelölésekor paraméter exportálása** speciális beállítások, amelyek engedélyezve vannak a tábla lekérdezésének.

![Az Azure Monitor virtuális gépek munkafüzetek szakasz vezérlők szerkesztése](media/vminsights-workbooks/009-settings-export.png)

A második naplólekérdezés majd használja az exportált értékeket, létrehozhat egy csoportot, majd a szakaszfejléc és diagramok által használt értékek sor kiválasztásakor. Ha egyetlen sor van kijelölve, azt elrejti a szakaszfejléc és diagramokat. 

Például a rejtett paraméter, a második szakaszban használja a következő hivatkozás a rácsban kijelölt sor:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Metrikák szakaszok hozzáadása

Metrikák szakaszok a Azure Monitor-metrikák adatokat építhet be az interaktív jelentések teljes hozzáférést biztosít. Az Azure Monitor-beli virtuális gépek az előre összeállított munkafüzetek általában tartalmaz metrikaadatok helyett elemzési adatokat.  Munkafüzetek létrehozása a metrikaadatokat, választhatja, így kihasználhatja a legjobb mindkét szolgáltatást egy helyen. Akkor is lehetővé teszi bármely hozzáfér az előfizetések erőforrásokból metrikaadatok bekérésére.

Íme egy példa alatt álló rács képi megjelenítés, CPU-teljesítményt biztosít a munkafüzetbe lekért virtuális gépek adatainak:

![Az Azure Monitor virtuális gépek munkafüzetek szakasz vezérlők szerkesztése](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adja hozzá a paraméter szakaszokat

A munkafüzet paraméterek lehetővé teszik a munkafüzetben szereplő értékeket módosítsa a lekérdezésekben vagy szakaszok manuális szerkesztése nélkül. Ez a feleslegessé kellene a mögöttes analytics lekérdezési nyelv ismertetése, és jelentősen kibővíti a munkafüzet-alapú reporting lehetséges célközönségét.

A paraméterek helyébe a lekérdezés, szöveges vagy más paraméter szakaszok üzembe zárójelek között, a paraméter neve például ``{parameterName}``. Paraméternevek korlátozva, hasonló szabályokat JavaScript azonosítók, alfabetikus karaktereket és aláhúzásjeleket tartalmazhat, alfanumerikus karakter követ. Például **a1** engedélyezett, de **1a** nem engedélyezett.

Paraméterek a következők lineáris, kezdve a felső részén egy munkafüzetet és a későbbi lépésekben lefelé halad.  Később a munkafüzet deklarált paraméterek felül lehet bírálni paraméterek, amelyeket korábban lett deklarálva. Ez lehetővé teszi továbbá lekérdezések használata az értékek hozzá a korábban meghatározott paraméterek paramétereket. Belül magát egy paraméter lépésben paraméterei is lineáris, balról jobbra, ahol paraméterek jobb ugyanazt a lépés a korábbi deklarált paramétert is függenek.
 
Paraméterek, amelyek jelenleg támogatott négy különböző típusa van:

|                  |      |
| ---------------- |:-----|
| **Szöveg**    | Lehetővé teszi, hogy a felhasználó szerkesztheti egy szövegmezőt, és igény szerint megadhat egy lekérdezést az alapértelmezett értéket adja meg. |
| **Legördülő lista** | Lehetővé teszi a felhasználói értékekből álló közül választhat. |
| **Időtartomány-választó**| Lehetővé teszi, hogy a felhasználó választhat az előre definiált tartományértékeknek idő, vagy egy egyéni időtartományt válasszon.|
| **Erőforrás-választó** | A felhasználó a munkafüzetet a kijelölt erőforrások közül választhat.|

### <a name="using-a-text-parameter"></a>Egy szöveges paraméter használatával

Az érték, közvetlenül a lekérdezésben az escape-karaktersorozat vagy idézése váltja fel a felhasználó begépeli a szövegmezőben. A szükséges érték karakterlánc, ha a lekérdezés kell rendelkeznie a paraméter idézőjeleket (például **'{parameter}'**).

A szöveg paraméter lehetővé teszi, hogy egy szövegmező, bárhol használható az értéket. Lehet, hogy a táblázat neve, oszlopnév, függvénynév, operátor, stb.  A szöveg paraméter típusa van egy beállítás **alapértelmezett érték beolvasása elemzési lekérdezésből**, amely lehetővé teszi, hogy a munkafüzet szerzője-t használni az alapértelmezett érték az adott szöveg mezőben feltöltéséhez.

Az alapértelmezett érték a napló-lekérdezés használata esetén az első sort (sort 0, 0 oszlop) csak az első értéket az alapértelmezett érték lesz. Ezért ajánlott a lekérdezést korlátozni szeretné csak egy sort, és a egy oszlopot ad vissza. A lekérdezés által visszaadott minden egyéb adatot figyelmen kívül hagyja. 

Akármilyen területen is a lekérdezés által visszaadott érték helyettesítődik közvetlenül escape-karaktersorozat vagy megkísérelte idézőjelek közé rakni. Ha a lekérdezés a sor nem ad vissza, az eredmény a paraméter üres karakterlánc (Ha a paraméter nem kötelező) vagy (Ha a paraméter megadása kötelező) nincs definiálva.

### <a name="using-a-drop-down"></a>Egy legördülő menü használatával

A legördülő listából paraméter típusa lehetővé teszi a létrehozni egy legördülő vezérlőt, lehetővé téve a kijelölt egy vagy több értéket.

A legördülő menüben megjelenik a naplólekérdezés vagy JSON. A lekérdezés egy oszlopot ad vissza, ha az adott oszlop értékei az érték és a legördülő vezérlőt címkéje is. A lekérdezés két oszlopot ad vissza, ha az első oszlop a értékét, és a második oszlop a listában látható a címkét. Ha a lekérdezés a három oszlopot ad vissza, a harmadik oszlop jelzi az alapértelmezésként beállított elemet a legördülő szolgál. Ez az oszlop bármilyen típusú is lehet, de a legegyszerűbb az, hogy logikai vagy numerikus típusok, ahol 0 a False (hamis), és 1 igaz.

Ha az oszlop egy karakterlánc típusú, NULL értékű vagy üres karakterlánc számít false (hamis), és semmilyen más érték igaz akkor tekinthető. Az egyetlen kijelölésre listákból az első érték a true érték lesz az alapértelmezetten kiválasztottat.  Minden érték a true érték több kijelölési listákból, az alapértelmezett beállítás szerint történik. A listában szereplő elemeket bármilyen sorrendben a lekérdezésben visszaadott sorok jelennek meg. 

Tekintsük át a paramétereket a kapcsolatok áttekintése jelentésben szerepelnek. Kattintson a Tovább gombra a Szerkesztés szimbólum **iránya**.

![Az Azure Monitor virtuális gépek munkafüzetek szakasz vezérlők szerkesztése](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Ekkor elindul a **paraméter szerkesztése** menüpontot.

![Az Azure Monitor virtuális gépek munkafüzetek szakasz vezérlők szerkesztése](media/vminsights-workbooks/012-workbook-edit-parameter.png)

A JSON-ban hozzon létre egy tetszőleges táblát tartalom használatával teszi lehetővé. Például a következő JSON hoz létre két értéket a listából:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Egy több megfelelő példa használ egy legördülő választhatok a teljesítményszámlálók készletét név alapján:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A lekérdezés a következő eredményeket jeleníti meg:

![Teljesítményoptimalizált számláló legördülő menü](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Legördülő listákból olyan nagyon hatékony eszközök testreszabása és interaktív jelentések létrehozásához.

### <a name="time-range-parameters"></a>Idő tartomány paraméterei

A saját egyéni tartomány paraméter a legördülő listából paramétertípus keresztül teheti meg, amíg is használhatja az out-of-box időtartomány paraméter típusa Ha már nincs szüksége az azonos mértékű rugalmasságot. 

Idő tartomány paramétertípusok 15-öt perc alatt az Ugrás az utolsó 90 napban alapértelmezett címtartományokat kell. Egyéni időtartomány kiválasztásának, amely lehetővé teszi az üzemeltető explicit kezdő választható, és állítsa le az értékeket az időtartományt a jelentés engedélyezze a lehetőség is van.

### <a name="resource-picker"></a>Erőforrás-választó

Az erőforrás-választó paraméter típusa lehetővé teszi, a hatókör bizonyos típusú erőforrások a jelentést. Előre összeállított olyat, amelyben az erőforrás-választó típusú használja például a **teljesítmény** munkafüzetet.

![Munkaterületek legördülő menü](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>És munkafüzetek megosztása munkatársaival

Munkafüzetek Log Analytics-munkaterület vagy egy virtuális gép típusú erőforrást, attól függően, hogyan elérni a munkafüzetek katalógus belül lesznek elmentve. A munkafüzetet, melyekbe menthetők a **jelentések mappa** magánjellegű, vagy a szakasz a **megosztott jelentések** szakaszt, amely az erőforrás-hozzáféréssel rendelkező mindenki számára elérhető. Az erőforrás minden a munkafüzetek megtekintéséhez kattintson a **nyílt** gombra a művelet sávon.

A munkafüzetet, amely a jelenleg **jelentések mappa**:

1. Kattintson a **nyílt** a művelet sávon
2. Kattintson a "..." gombot a megosztani kívánt munkafüzet
3. Kattintson a **helyezze át a megosztott jelentések**.

A munkafüzet egy hivatkozást, vagy e-mailen keresztül megosztásához kattintson **megosztása** a művelet sávon. Ne feledje, hogy a címzettek a hivatkozásra kell a munkafüzet megtekintéséhez az Azure Portalon az erőforráshoz való hozzáférés. Ahhoz, hogy a módosításokat, címzettek van szükség, legalább közreműködői engedélye az erőforrás.

A PIN-kódot egy munkafüzetet, egy Azure-irányítópultra mutató hivatkozást:

1. Kattintson a **nyílt** a művelet sávon
2. Kattintson a "..." gombot szeretne rögzíteni a munkafüzet
3. Kattintson a **rögzítés az irányítópulton**.

## <a name="next-steps"></a>További lépések
Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [megtekintése az Azure virtuális gép állapota](vminsights-health.md), vagy a felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). 