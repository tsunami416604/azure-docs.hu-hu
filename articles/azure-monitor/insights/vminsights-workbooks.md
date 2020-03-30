---
title: Interaktív jelentések létrehozása (Azure Monitor virtuális gépekhez) munkafüzetek használatával
description: Egyszerűsítse az összetett jelentéseket az azure-figyelő előre definiált és egyéni paraméterezett munkafüzeteivel.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a6ab126c3a5b0d2a82b17fac42dcc9e20f6aba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480453"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Interaktív jelentések létrehozása (Azure Monitor virtuális gépekhez) munkafüzetek használatával

A munkafüzetek a szöveget, [a naplólekérdezéseket,](../log-query/query-language.md)a mutatókat és a paramétereket gazdag interaktív jelentésekké egyesítik. A munkafüzeteket bármely más csapattag szerkeszti, akik hozzáférnek ugyanazokhoz az Azure-erőforrásokhoz.

A munkafüzetek olyan esetekben hasznosak, mint például:

* A virtuális gép használatának feltárása, ha nem ismeri előre az érdeklődési mutatókat: CPU-használat, lemezterület, memória, hálózati függőségek stb. A többi használatelemző eszközzel ellentétben a munkafüzetek segítségével többféle vizualizációt és elemzést kombinálhat, így nagyszerűek az ilyen típusú szabad formájú feltáráshoz.
* Elmagyarázza a csapatnak, hogyan teljesít egy nemrég kiépített virtuális gép, a fő számlálók és más naplóesemények metrikáinak megjelenítésével.
* A virtuális gép átméretezési kísérletének eredményei megosztása a csapat többi tagjával. Elmagyarázhatja a kísérlet céljait a szöveggel, majd megjelenítheti a kísérlet kiértékeléséhez használt egyes használati metrikákat és elemzési lekérdezéseket, valamint egyértelmű kijelentéseket arról, hogy az egyes metrikák a cél felett vagy alatt voltak-e.
* Jelentés a szolgáltatáskimaradás hatása a virtuális gép használatára, adatok kombinálásával, szöveges magyarázattal és a jövőbeli kimaradások megelőzésére szolgáló következő lépések megvitatásával.

Az alábbi táblázat összefoglalja az Azure Monitor virtuális gépekhez a kezdéshez tartalmaz munkafüzeteket.

| Munkafüzet | Leírás | Hatókör |
|----------|-------------|-------|
| Teljesítmény | A Top N-lista és a Diagramok nézet testreszabható verzióját biztosítja egyetlen munkafüzetben, amely kihasználja az összes engedélyezett Log Analytics teljesítményszámlálót.| Méretarányban |
| Teljesítményszámlálók | Top N diagramnézet a teljesítményszámlálók széles körében. | Méretarányban |
| Kapcsolatok | A kapcsolatok részletes nézetet biztosít a figyelt virtuális gépek bejövő és kimenő kapcsolatairól. | Méretarányban |
| Aktív portok | Azokat a folyamatokat tartalmazza, amelyek a figyelt virtuális gépek portjaihoz és a kiválasztott időkereten belül végzett tevékenységükhöz kötődnek. | Méretarányban |
| Nyitott portok | A figyelt virtuális gépeken megnyitott portok számát és a nyitott portok részleteit tartalmazza. | Méretarányban |
| Sikertelen kapcsolatok | A sikertelen kapcsolatok száma a figyelt virtuális gépeken, a hibatrend megjelenítése, és ha a hibák százalékos aránya növekszik az idő múlásával. | Méretarányban |
| Biztonság és naplózás | A TCP/IP-forgalom elemzése, amely az általános kapcsolatokról, a rosszindulatú kapcsolatokról számol be, ahol az IP-végpontok globálisan vannak.  Az összes szolgáltatás engedélyezéséhez engedélyeznie kell a biztonsági észlelést. | Méretarányban |
| TCP-forgalom | Rangsorolt jelentés a figyelt virtuális gépekhez és azok elküldött, fogadott és teljes hálózati forgalmához egy rácsban, és trendvonalként jelenik meg. | Méretarányban |
| Adatforgalom-összehasonlítás | Ez a munkafüzet lehetővé teszi a hálózati forgalom trendek összehasonlítását egyetlen gép vagy egy gépcsoport esetében. | Méretarányban |
| Teljesítmény | Teljesítménynézetünk testreszabható verzióját biztosítja, amely kihasználja az összes engedélyezett Log Analytics teljesítményszámlálót. | Egyetlen virtuális gép | 
| Kapcsolatok | A kapcsolatok részletes nézetet biztosít a virtuális gépről érkező és kimenő kapcsolatokról. | Egyetlen virtuális gép |
 
## <a name="creating-a-new-workbook"></a>Új munkafüzet létrehozása

A munkafüzet ektől függetlenül szerkeszthető diagramokból, táblázatokból, szövegekből és beviteli vezérlőkből álló szakaszokból áll. A munkafüzetek jobb megértéséhez kezdjük egy sablon megnyitásával, és mutassuk be az egyéni munkafüzet létrehozását. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Válassza a **Virtuális gépek**lehetőséget.

3. Válasszon ki egy virtuális gépet a listából.

4. A virtuális gép lapján a **Figyelés** csoportban válassza **az Insights**lehetőséget.

5. A Virtuálisgép-elemzési lapon válassza a **Teljesítmény** vagy a **Térképek** lapot, majd a lapon található hivatkozásról válassza a **Munkafüzetek megtekintése** lehetőséget. A legördülő listában válassza az Ugrás a **galériába**lehetőséget.

    ![Képernyőkép a munkafüzet legördülő listájáról](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Ezzel elindítja a munkafüzetgyűjteményt számos előre elkészített munkafüzettel, amelyek segítséget nyújtanak az első lépésekhez.

7. Hozzon létre egy új munkafüzetet az **Új**lehetőség kiválasztásával.

    ![Képernyőkép a munkafüzetgyűjteményről](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Munkafüzetszakaszok szerkesztése

A munkafüzetek két üzemmódban is rendelkeznek: **szerkesztési módés** **olvasási mód.** Amikor először indít el egy új munkafüzetet, **az szerkesztési módban**nyílik meg. A munkafüzet összes tartalmát megjeleníti, beleértve az egyébként rejtett lépéseket és paramétereket is. **Az olvasási mód** egyszerűsített jelentésstílus-nézetet jelenít meg. Olvasási mód lehetővé teszi, hogy absztrakt el a komplexitás, hogy bement létre egy jelentést, miközben még mindig az alapul szolgáló mechanika csak néhány kattintásnyira van, ha szükséges a módosításhoz.

![Virtuális gépek Azure-figyelője munkafüzetek szakaszszerkesztő vezérlők](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Ha végzett egy szakasz szerkesztésével, kattintson a szakasz bal alsó sarkában található **Szerkesztés nélkül** elemre.

2. Szakasz másolatának létrehozásához kattintson a **Szakasz klónozása** ikonra. Az ismétlődő szakaszok létrehozása nagyszerű módja annak, hogy a lekérdezések en a korábbi ismétlések elvesztése nélkül iteráljon.

3. Ha feljebb szeretne lépni egy munkafüzet egy szakaszával, kattintson a **Fel** vagy **a Le** ikonra.

4. Ha véglegesen el szeretne távolítani egy **szakaszt,** kattintson az Eltávolítás ikonra.

## <a name="adding-text-and-markdown-sections"></a>Szöveg és Markdown-szakaszok hozzáadása

Ha címsorokat, magyarázatokat és kommentárokat ad a munkafüzethez, akkor a táblázatok és diagramok készletét narratívaként alakíthatja át. A munkafüzetek szövegszakaszai támogatják a szövegformázás [Markdown szintaxisát,](https://daringfireball.net/projects/markdown/) például a címsorokat, a félkövér, a dőlt és a listajeles listákat.

Ha szövegszakaszt szeretne hozzáadni a munkafüzethez, használja a **Szöveg hozzáadása** gombot a munkafüzet alján vagy bármely szakasz alján.

## <a name="adding-query-sections"></a>Lekérdezési szakaszok hozzáadása

![Lekérdezés szakasz a munkafüzetekben](media/vminsights-workbooks/005-workbook-query-section.png)

Ha lekérdezési szakaszt szeretne hozzáadni a munkafüzethez, használja a **Lekérdezés hozzáadása** gombot a munkafüzet alján vagy bármely szakasz alján.

A lekérdezési szakaszok rendkívül rugalmasak, és olyan kérdések megválaszolására használhatók, mint például:

* Hogyan volt a CPU-használat ugyanebben az időszakban, mint a hálózati forgalom növekedése?
* Mi volt a tendencia a rendelkezésre álló lemezterület az elmúlt hónapban?
* Hány hálózati kapcsolat hibák nem a virtuális gép tapasztalata az elmúlt két hétben? 

Nem csak arra korlátozódik, hogy lekérdezze azt a virtuális gépet, amelyből elindította a munkafüzetet. Több virtuális gép, valamint a Log Analytics-munkaterületek között is lekérdezheti a lekérdezést, feltéve, hogy rendelkezik hozzáférési engedéllyel ezekhez az erőforrásokhoz.

Más Log Analytics-munkaterületekből vagy egy adott Application Insights-alkalmazásból származó adatok felvétele a **munkaterület-azonosító** használatával. Ha többet szeretne megtudni az erőforrás-közi lekérdezésekről, olvassa el a [hivatalos útmutatást.](../log-query/cross-workspace-query.md)

### <a name="advanced-analytic-query-settings"></a>Speciális analitikus lekérdezési beállítások

Minden szakasz saját speciális beállításokkal rendelkezik, ![amelyek a](media/vminsights-workbooks/006-settings.png) Beállítások Munkafüzetek szakasz szerkesztési vezérlőiikonon keresztül érhetők el, amelyek a **Paraméterek hozzáadása** gomb tól jobbra találhatók.

![Virtuális gépek Azure-figyelője munkafüzetek szakaszszerkesztő vezérlők](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Egyéni szélesség**    | Tetszőleges méretűvé teszi az elemeket, így egyetlen sorban több elemet is elilleszthet, így a diagramokat és a táblázatokat gazdag interaktív jelentésekbe rendezheti.  |
| **Feltételesen látható** | Adja meg, ha olvasási módban szeretné elrejteni a paramétereken alapuló lépéseket. |
| **Paraméter exportálása**| Hagyja, hogy a rács vagy a diagram kijelölt sora később módosíthatja az értékeket, vagy láthatóvá váljon.  |
| **Lekérdezés megjelenítése szerkesztés kor** | A lekérdezést a diagram vagy a táblázat felett jeleníti meg, még olvasási módban is.
| **Megnyitás az elemzésben gomb szerkesztés kor** | Hozzáadja a kék Analytics ikont a diagram jobb sarkához, hogy egy kattintással hozzáférhessen.|

A legtöbb ilyen beállítás meglehetősen intuitív, de a **paraméter exportálásához** jobb megvizsgálni egy munkafüzetet, amely kihasználja ezt a funkciót.

Az egyik előre összeállított munkafüzetek - **TCP Traffic**, a virtuális gép csatlakozási metrikák információkat tartalmaz.

A munkafüzet első szakasza a naplólekérdezési adatokon alapul. A második szakasz a naplólekérdezési adatokon is alapul, de az első tábla egy sorának kijelölése interaktívmódon frissíti a diagramok tartalmát:

![Virtuális gépek Azure-figyelője munkafüzetek szakaszszerkesztő vezérlők](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

A viselkedés az Elem **kiválasztásakor** lehetőség szerint exportálhat egy speciális paraméterbeállításokat, amelyek engedélyezve vannak a tábla naplólekérdezésében.

![Virtuális gépek Azure-figyelője munkafüzetek szakaszszerkesztő vezérlők](media/vminsights-workbooks/009-settings-export.png)

A második naplólekérdezés ezután az exportált értékeket használja, amikor egy sor van kiválasztva, hogy olyan értékeket hozzon létre, amelyeket aztán a szakaszfejléc ek és a diagramok használnak. Ha nincs kijelölt sor, elrejti a szakaszfejlécet és a diagramokat. 

A második szakaszban lévő rejtett paraméter például a következő hivatkozást használja a rácsban kijelölt sorból:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Metrikaszakaszok hozzáadása

A Metrikák szakaszok teljes hozzáférést biztosítanak az Azure Monitor metrikaadatainak interaktív jelentésekbe való beépítéséhez. Az Azure Monitor virtuális gépek, az előre összeállított munkafüzetek általában tartalmaznak analitikus lekérdezési adatok at metrikaadatok helyett.  Dönthet úgy, hogy mérőszámadatokat tartalmazó munkafüzeteket hoz létre, amelyek lehetővé teszik, hogy mindkét funkció ból kihasználhassa a legjobbat egy helyen. Azt is lehetővé teszi, hogy lekérése metrika adatokat az erőforrások bármelyik előfizetések hozzáféréssel rendelkezik.

Íme egy példa arra, hogy a virtuális gép adatai bevannak húzva egy munkafüzetbe, hogy a PROCESSZOR teljesítményének rácsos vizualizációját biztosítsák:

![Virtuális gépek Azure-figyelője munkafüzetek szakaszszerkesztő vezérlők](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Paraméterszakaszok hozzáadása

A munkafüzet paraméterei lehetővé teszik a munkafüzet értékeinek módosítását anélkül, hogy manuálisan kellene módosítani a lekérdezési vagy szövegszakaszokat. Ez megszünteti azt a követelményt, hogy meg kell érteni az alapul szolgáló elemzési lekérdezési nyelvet, és jelentősen kibővíti a munkafüzet-alapú jelentések potenciális közönségét.

A paraméterek értékeit a lekérdezési, szöveg- vagy más paraméterszakaszokban lecseréli a ``{parameterName}``paraméter neve kapcsos zárójelbe, például . A paraméternevek a JavaScript-azonosítókhoz, alfabetikus karakterekhez vagy aláhúzásjelekhez hasonló szabályokra korlátozódnak, amelyeket alfanumerikus karakterek vagy aláhúzásjelek követnek. Például **az a1** megengedett, de az **1a** nem megengedett.

A paraméterek lineárisak, a munkafüzet tetejétől kezdve a későbbi lépésekig.  A munkafüzet ben később deklarált paraméterek felülbírálhatják a korábban deklarált paramétereket. Ez azt is lehetővé teszi, hogy a paraméterek et használó paraméterek a korábban definiált paraméterek értékeinek eléréséhez. Egy paraméter lépésén belül a paraméterek is lineárisak, balról jobbra, ahol a jobb oldali paraméterek függhetnek egy paramétertől, amelyet ugyanebben a lépésben korábban deklaráltak.
 
Jelenleg négy különböző típusú paraméter létezik, amelyek jelenleg támogatottak:

|                  |      |
| ---------------- |:-----|
| **Szöveg**    | Lehetővé teszi a felhasználó számára a szövegdoboz szerkesztését, és tetszés szerint megadhat egy lekérdezést az alapértelmezett érték kitöltéséhez. |
| **Legördülő lista** | Lehetővé teszi, hogy a felhasználó egy értékkészlet közül válasszon. |
| **Időtartomány-választó**| Lehetővé teszi, hogy a felhasználó előre meghatározott időtartomány-értékek közül válasszon, vagy egyéni időtartományból válasszon.|
| **Erőforrás-választó** | Lehetővé teszi a felhasználó számára, hogy a munkafüzethez kiválasztott erőforrások közül válasszon.|

### <a name="using-a-text-parameter"></a>Szövegparaméter használata

A felhasználó által a szövegmezőben beírt érték közvetlenül a lekérdezésben kerül lecserélésre, kiváltás vagy idézés nélkül. Ha a szükséges érték karakterlánc, a lekérdezésnek idézőjelekkel kell rendelkeznie a paraméter körül (például **"{parameter}" ).**

A szövegparaméter lehetővé teszi, hogy a szövegdobozban lévő érték bárhol használható legyen. Ez lehet egy tábla neve, oszlop neve, függvény neve, operátor, stb.  A szövegparaméter típusa rendelkezik egy ** Alapértelmezett érték beszámítása az elemzési lekérdezésből**beállítással, amely lehetővé teszi, hogy a munkafüzet szerzője lekérdezéssel feltöltse a szövegmező alapértelmezett értékét.

Naplólekérdezés alapértelmezett értékének használatakor a program csak az első sor első értékét használja (0. sor, 0. oszlop). Ezért ajánlott korlátozni a lekérdezést, hogy csak egy sort és egy oszlopot ad vissza. A lekérdezés által visszaadott egyéb adatokat a rendszer figyelmen kívül hagyja. 

Bármilyen értéket is ad vissza a lekérdezés, közvetlenül lecserélődik, és nem kerül ki, és nem idéz. Ha a lekérdezés nem ad vissza sorokat, a paraméter eredménye vagy egy üres karakterlánc (ha a paraméter nem szükséges) vagy nincs definiálva (ha a paraméter szükséges).

### <a name="using-a-drop-down"></a>Legördülő menü használata

A legördülő paraméter típusa lehetővé teszi egy legördülő vezérlő létrehozását, amely lehetővé teszi egy vagy több érték kiválasztását.

A legördülő menüt naplólekérdezés vagy JSON tölti fel. Ha a lekérdezés egy oszlopot ad vissza, az oszlopban lévő értékek a legördülő vezérlőelem ben szereplő értéket és feliratot is tartalmazják. Ha a lekérdezés két oszlopot ad vissza, az első oszlop az érték, a második pedig a legördülő menüben látható címke. Ha a lekérdezés három oszlopot ad vissza, a harmadik oszlop jelzi az alapértelmezett kijelölést a legördülő menüben. Ez az oszlop bármilyen típusú lehet, de a legegyszerűbb a bool vagy numerikus típusok használata, ahol a 0 hamis, és az 1 igaz.

Ha az oszlop karakterlánctípusú, a null/üres karakterlánc hamisnak, és minden más érték igaznak minősül. Egyetlen kijelölés legördülő lista esetén a program az első, valódi értékkel rendelkező értéket használja alapértelmezett ként.  Több kijelöléslegördülő menü esetén a program minden valós értékű értéket használ alapértelmezettként. A legördülő menü elemei a lekérdezés által visszaadott sorok sorrendjében jelennek meg. 

Tekintsük át a Kapcsolatok áttekintése jelentésben található paramétereket. Kattintson az **Irány**gomb melletti szerkesztési szimbólumra.

![Virtuális gépek Azure-figyelője munkafüzetek szakaszszerkesztő vezérlők](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Ezzel elindítja a **Paraméter szerkesztése** menüelemet.

![Virtuális gépek Azure-figyelője munkafüzetek szakaszszerkesztő vezérlők](media/vminsights-workbooks/012-workbook-edit-parameter.png)

A JSON lehetővé teszi, hogy tetszőleges, tartalommal feltöltött táblát hozzon létre. A következő JSON például két értéket hoz létre a legördülő menüben:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Egy alkalmazhatóbb példa egy legördülő lista használata a teljesítményszámlálók név szerint történő kiválasztásához:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A lekérdezés az eredményeket a következőképpen jeleníti meg:

![Perf számláló legördülő](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

A legördülő menük hihetetlenül hatékony eszközök az interaktív jelentések testreszabásához és létrehozásához.

### <a name="time-range-parameters"></a>Időtartomány paraméterei

Bár a legördülő paraméter típusán keresztül saját egyéni időtartomány-paramétert hozhat létre, használhatja a beépített időtartomány paramétertípusát is, ha nincs szüksége ugyanolyan fokú rugalmasságra. 

Az időtartomány-paramétertípusok 15 alapértelmezett tartományt használnak, amelyek öt perctől az utolsó 90 napig terjednek. Lehetőség van egyéni időtartomány-kiválasztásengedélyezésére is, amely lehetővé teszi, hogy a jelentés kezelője explicit kezdő és leállítási értékeket válasszon az időtartományhoz.

### <a name="resource-picker"></a>Erőforrás-választó

Az erőforrás-választó paraméter típusa lehetővé teszi a jelentés hatókörét bizonyos típusú erőforrásokra. Az erőforrás-választó típusát használó előre összeállított munkafüzet a **Teljesítmény** munkafüzet.

![Munkaterületek legördülő menü](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Munkafüzetek mentése és megosztása a csapattal

A munkafüzetek a Naplóelemzési munkaterületen vagy a virtuálisgép-erőforrásban kerülnek mentésre, attól függően, hogy hogyan éri el a munkafüzetgyűjteményt. A munkafüzet et a **Saját jelentések** szakaszba lehet menteni, amely magánjellegű, vagy a **Megosztott jelentések** szakaszba, amely mindenki számára elérhető, aki hozzáfér az erőforráshoz. Az erőforrás összes munkafüzetének megtekintéséhez kattintson a műveletsáv **Megnyitás** gombjára.

A **Saját jelentések**ben lévő munkafüzet megosztása:

1. Kattintson a **műveletsáv Megnyitás** gombjára
2. Kattintson a "..." gombot a megosztani kívánt munkafüzet mellett
3. Kattintson **az Áthelyezés megosztott jelentésekre gombra.**

Ha egy munkafüzetet hivatkozással vagy e-mailben szeretne megosztani, kattintson a műveletsáv **Megosztás gombjára.** Ne feledje, hogy a hivatkozás címzettjeinek hozzáférésre van szükségük ehhez az erőforráshoz az Azure Portalon a munkafüzet megtekintéséhez. A szerkesztéshez a címzetteknek legalább közreműködői engedélyekkel kell rendelkezniük az erőforráshoz.

Munkafüzetre mutató hivatkozás rögzítése Egy Azure-irányítópultra:

1. Kattintson a **műveletsáv Megnyitás** gombjára
2. Kattintson a "..." gombot a rögzíteni kívánt munkafüzet mellett
3. Kattintson **a Rögzítés az irányítópultra gombra.**

## <a name="next-steps"></a>További lépések

- A korlátozások és a virtuális gép általános teljesítményének azonosításáról az [Azure virtuális gép teljesítményének megtekintése](vminsights-performance.md)című témakörben van.

- A felderített alkalmazásfüggőségekről az [Azure-figyelő szolgáltatás virtuális gépek leképezésének megtekintése.](vminsights-maps.md)
