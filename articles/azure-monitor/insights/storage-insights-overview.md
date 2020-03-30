---
title: Az Azure Storage-szolgáltatások figyelése az Azure Monitor for Storage szolgáltatással (előzetes verzió)| Microsoft dokumentumok
description: Ez a cikk ismerteti az Azure Monitor for Storage szolgáltatás, amely a tárolási rendszergazdák számára a teljesítmény és a kihasználtság i. Azure Storage-fiókok gyors megértését.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662519"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>A tárolási szolgáltatás figyelése az Azure Monitor for Storage szolgáltatással (előzetes verzió)

Az Azure Monitor for Storage (előzetes verzió) az Azure Storage-fiókok átfogó figyelését biztosítja az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes nézetével. A tárolókapacitást és a teljesítményt kétféleképpen figyelheti meg, közvetlenül a tárfiókból, vagy az Azure Monitorból tekintheti meg a tárfiókok csoportai közötti megtekintést. 

Ez a cikk segít megérteni az Azure Monitor storage (előzetes verzió) által nyújtott élményt, amely a storage-fiókok állapotára és teljesítményére vonatkozó, nagy méretű, hasznosítható ismereteket biztosít, és képes az elérési pontokra összpontosítani, és diagnosztizálni a késleltetést, a szabályozást, és a rendelkezésre állási problémákat.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Bevezetés az Azure Monitor tárhelyhez szolgáltatásba (előzetes verzió)

Mielőtt belemerülne az élménybe, meg kell értenie, hogyan mutatja be és vizualizálja az információkat. Akár közvetlenül egy tárfiókból választja ki a Storage funkciót, akár az Azure Monitor szolgáltatásból, az Azure Monitor for Storage egységes élményt nyújt. 

Kombinált szállít:

* **A tárolási** szolgáltatás vagy az API-művelet állapota alapján a rendelkezésre állásuk pillanatképnézetét, a storage-szolgáltatás által fogadott kérelmek teljes számát megjelenítő kihasználtság, valamint a tárolási szolgáltatás vagy az API-művelettípus átlagos anamnézise a kérelmek feldolgozásához szükséges átlagos időt mutatja. A kapacitást blob, fájl, tábla és várólista szerint is megtekintheti.

* Egy adott tárfiók **elemzését leáshatja** a problémák diagnosztizálása vagy részletes elemzés elvégzéséhez kategória – rendelkezésre állás, teljesítmény, hibák és kapacitás szerint. A beállítások bármelyikének kiválasztása részletes képet ad a mérőszámokról.  

* **Testre szabható,** ahol módosíthatja, hogy mely mutatókat szeretné látni, módosítani vagy beállítani a korlátokhoz igazodó küszöbértékeket, és mentse saját munkafüzetként. A munkafüzetben lévő diagramok rögzíthetők az Azure irányítópultjára.  

Ez a szolgáltatás nem igényel semmit engedélyezni vagy konfigurálni, a tárfiókok tárolási metrikái alapértelmezés szerint gyűjtik. Ha nem ismeri az Azure Storage-ban elérhető metrikákat, tekintse meg a leírást és a definíciót az Azure Storage-metrikákban az [Azure storage-metrikák](../../storage/common/storage-metrics-in-azure-monitor.md)áttekintésével.

>[!NOTE]
>A szolgáltatás eléréséért nem számítunk fel díjat, és csak az Azure Monitor által konfigurált vagy engedélyezett alapvető funkciókért kell fizetnie, az [Azure Monitor díjszabási részletei](https://azure.microsoft.com/pricing/details/monitor/) lapján leírtak szerint.

>[!NOTE]
>Az Azure Monitor for Storage nem támogatja [az általános célú v1-fiókokat.](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)
>

## <a name="view-from-azure-monitor"></a>Megtekintés az Azure Monitorból

Az Azure Monitorban megtekintheti a tranzakciós, késési és kapacitásadatokat az előfizetéstöbb tárfiókjából, és segíthet a teljesítmény, a kapacitásproblémák és a hibák azonosításában.

A tárfiókok kihasználtságának és rendelkezésre állásának megtekintéséhez az összes előfizetésben hajtsa végre a következő lépéseket.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Válassza a **Figyelő lehetőséget** az Azure Portal bal oldali ablaktáblájában, és az **Insights** szakaszban válassza a **Tárfiókok (előzetes verzió)** lehetőséget.

    ![Több tárfiók nézet](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Áttekintő munkafüzet

A kijelölt előfizetés **áttekintő** munkafüzetében a tábla interaktív tármetrikákat és szolgáltatás rendelkezésre állási állapotát jeleníti meg legfeljebb 10, az előfizetésen belül csoportosított tárfiókok számára. Az eredményeket az alábbi legördülő listákból kiválasztott beállítások alapján szűrheti:

* **Előfizetések** – csak a tárfiókkal rendelkező előfizetések vannak felsorolva.  

* **Tárfiókok** – alapértelmezés szerint 10 tárfiók van előre kiválasztva. Ha az összes vagy több tárfiókok a hatókör-választó, legfeljebb 200 tárfiókok lesz visszaadva. Ha például összesen 573 tárfiókkal rendelkezett három kiválasztott előfizetésben, csak 200 fiók jelenik meg. 

* **Időtartomány** – alapértelmezés szerint az utolsó 4 óra információt jeleníti meg a megfelelő beállítások alapján.

A legördülő lista pultcsempéje összegzi az előfizetésben lévő tárfiókok teljes számát, és tükrözi, hogy az összes összeg ből hány van kiválasztva. Feltételes színkódolás vagy hőtérkép van a munkafüzet azon oszlopaihoz, amelyek tranzakciós mutatókat vagy hibákat jelentenek. A legmélyebb szín értéke a legmagasabb, a világosabb szín pedig a legalacsonyabb értékeken alapul. A hibaalapú oszlopok esetében az érték piros, a metrikaalapú oszlopok esetében pedig kék.

Válasszon ki egy értéket az **Elérhetőség**, **E2E Késés**, **Kiszolgálói késés**és **tranzakcióhiba típusa/hibák** oszlopokban egy olyan jelentéshez irányítja, amely az adott tárfiókhoz kiválasztott oszlopnak megfelelő tárolási metrikák adott típusára van szabva. Az egyes kategóriákmunkafüzeteiről az alábbi [Részletes tárolási munkafüzetek](#detailed-storage-workbooks) című szakaszban olvashat bővebben. 

>[!NOTE]
>A jelentésben megjelenhető hibákról a [Választípusa séma](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) című témakörben talál olyan választípusokat, mint **a ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. A kiválasztott tárfiókoktól függően, ha háromnál több típusú hibát jelentettek, az összes többi hiba az **Egyéb**kategóriában jelenik meg.

Az alapértelmezett **rendelkezésre állási** küszöbérték:

* Figyelmeztetés - 99%
* Kritikus - 90%

Ha a megfigyelés vagy a követelmények eredményei alapján szeretne beállítani egy [rendelkezésre állási küszöbértéket, tekintse át a rendelkezésre állási küszöbérték módosítását.](#modify-the-availability-threshold) 

### <a name="capacity-workbook"></a>Kapacitásmunkafüzet

Válassza **a Kapacitás** lehetőséget a lap tetején, és megnyílik a **Kapacitás** munkafüzet. Ez megmutatja a fiókban használt teljes tárterület mennyiségét és a fiók egyes adatszolgáltatásai által használt kapacitást, hogy segítsen azonosítani a túl- és alul használt tárhelyet.

![Több tárfiók Kapacitás munkafüzet](./media/storage-insights-overview/storage-account-capacity-02.png) 

Feltételes színkódolás vagy hőtérkép van a munkafüzet azon oszlopaihoz, amelyek kék értékű kapacitásmutatókat jelentenek. A legmélyebb szín értéke a legmagasabb, a világosabb szín pedig a legalacsonyabb értékeken alapul.

Ha a munkafüzet bármelyik oszlopa alatt kijelöl egy értéket, leáshat a tárfiók **Kapacitás** munkafüzetére. A részletezési jelentéssel kapcsolatos további részleteket az alábbi [Részletes tárolási munkafüzetek](#detailed-storage-workbooks) szakasz ismerteti. 

## <a name="view-from-a-storage-account"></a>Megtekintés tárfiókból

Az Azure Monitor virtuális gépekhez való hozzáférése közvetlenül egy tárfiókból:

1. Az Azure Portalon válassza a Storage-fiókok.

2. A listából válasszon egy tárfiókot. A Figyelés csoportban válassza az Insights (előzetes verzió) lehetőséget.

    ![Kijelölt tárfiók – áttekintés lap](./media/storage-insights-overview/storage-account-direct-overview-01.png)

A tárfiók **áttekintő** munkafüzetében számos tárolási teljesítménymutató látható, amelyek segítségével gyorsan felmérheti a következőket:

* A Storage szolgáltatás állapota azonnal annak ellenőrzéséhez, hogy egy, a felügyeletén kívül álló probléma hatással van-e a Storage szolgáltatásra abban a régióban, ahol telepítve van, amely az **Összegzés** oszlopban található.

* Interaktív teljesítménydiagramok, amelyek a tárolókapacitással, a rendelkezésre állással, a tranzakciókkal és a késéssel kapcsolatos legfontosabb részleteket mutatják be.  

* Metrika- és állapotcsempék, amelyek kiemelik a szolgáltatás elérhetőségét, a tárolási szolgáltatás tranzakcióinak teljes számát, az E2E késést és a kiszolgáló késését.

A **hibák,** **a teljesítmény,** a **rendelkezésre állás**és a **kapacitás** gombjai nak bármelyikének kiválasztása megnyitja a megfelelő munkafüzetet. 

![Kijelölt tárfiók – áttekintés lap](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Részletes tárolási munkafüzetek

Akár kiválasztott egy értéket az **Elérhetőség**, **E2E Késés**, **Kiszolgálói késés**és **tranzakcióhiba típusa/hibák** oszlopban a többtárfiók **áttekintése** munkafüzetből, vagy kiválasztotta **a** **hibák,** **teljesítmény,** **rendelkezésre állás**és **kapacitás** bármelyik gombját egy adott tárfiókból, mindegyik az adott kategóriára szabott interaktív tárral kapcsolatos információkat jelenít meg.  

* **Az elérhetőség** megnyitja az **elérhetőségi** munkafüzetet. Ez azt mutatja, az Azure Storage szolgáltatás aktuális állapotát, egy táblázatot, amely bemutatja a rendelkezésre álló állapot az egyes objektumok által meghatározott adatszolgáltatás a tárfiókban egy trendsort képviselő időtartomány kiválasztott, és egy rendelkezésre állási trend diagram a fiók minden egyes adatszolgáltatása.  

    ![Példa a rendelkezésre állási jelentésre](./media/storage-insights-overview/storage-account-availability-01.png)

* **Az E2E késés** e2- és **kiszolgálói késés** e-késleltetése megnyitja a **Teljesítmény** munkafüzetet. Ez magában foglalja az E2E késést és a kiszolgáló késését megjelenítő összesítő állapotcsempét, az E2E és a kiszolgáló késésének teljesítménydiagramját, valamint a tárfiókban definiált adatszolgáltatás által definiált, API-késésre vonatkozó sikeres hívások késését lebontó táblázatot.

    ![Példa teljesítményjelentésre](./media/storage-insights-overview/storage-account-performance-01.png)

* A rácsban felsorolt hibakategóriák bármelyikének kijelölésével nyissa meg a **Hiba** munkafüzetet. A jelentés az összes többi ügyféloldali hiba metrikus csempéit jeleníti meg, kivéve a leírt és sikeres kérelmeket, az ügyfélszabályozási hibákat, a Transaction **Response Type** dimenzió metrikus metrikus mutatóit a ClientOtherError attribútumra jellemző, és két táblát – **Tranzakciók API-név** és **tranzakció konválasztípus szerint.**

   ![Példa hibajelentésre](./media/storage-insights-overview/storage-account-failures-01.png)

* **A kapacitás** megnyitja a **Kapacitás** munkafüzetet. Azt mutatja, hogy a csempékben és a diagramon a fiókban lévő egyes táradat-objektumok hoz használt tárterület teljes mennyisége, valamint a fiókban tárolt adatobjektumok teljes mennyisége.  

    ![Kijelölt tárfiók kapacitása lap](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Rögzítés és exportálás

A metrikaszakaszok bármelyikét rögzítheti egy Azure-irányítópulton a szakasz jobb felső részén lévő pushpin ikon kiválasztásával.

![Példa az irányítópult metrikus szakaszpinje](./media/storage-insights-overview/workbook-pin-example.png)

A több előfizetéses és tárfiók **áttekintése** vagy **a Kapacitás** munkafüzetek támogatják az eredmények Excel formátumban történő exportálását a pushpin ikontól jobbra lévő lefelé mutató nyílikon kiválasztásával.

![Példa munkafüzetrács-eredmények exportálására](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Az Azure Monitor tárhelyhez való testreszabása (előzetes verzió)

Ez a szakasz a munkafüzet szerkesztésének gyakori forgatókönyveit emeli ki, hogy az adatelemzési igények et támogatva személyre szabhassa:

* A munkafüzet hatóköre, hogy mindig kiválasszon egy adott előfizetést vagy tárfiókot
* Mérőszámok módosítása a rácsban
* A rendelkezésre állási küszöbérték módosítása
* A színvisszaadás módosítása

A testreszabások egy egyéni munkafüzetbe kerülnek, hogy megakadályozzák az alapértelmezett konfiguráció felülírását a közzétett munkafüzetben. A munkafüzetek egy erőforráscsoporton belül kerülnek mentésre, vagy a **Saját jelentések** szakaszban, amely magánjellegű, vagy a **Megosztott jelentések** szakaszban, amely mindenki számára elérhető, aki hozzáfér az erőforráscsoporthoz. Az egyéni munkafüzet mentése után a bemutató elindításához a munkafüzettárba kell lépnie.

![Munkafüzettár indítása a parancssávról](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Előfizetés vagy tárfiók megadása

Beállíthatja, hogy a több-előfizetés és a tárfiók **áttekintése** vagy **kapacitás** munkafüzetek hatóköre egy adott előfizetés(ek) vagy tárfiók (ok) minden távon, hajtsa végre a következő lépéseket.

1. Válassza a **Figyelő** lehetőséget a portálon, majd válassza **a Tárfiókok (előzetes verzió)** lehetőséget a bal oldali ablaktáblában.

2. Az **Áttekintés** munkafüzet parancssávján válassza a **Szerkesztés**lehetőséget.

3. Válasszon az **Előfizetések** legördülő listából egy vagy több előfizetést, amelyet alapértelmezettként szeretne látni. Ne feledje, hogy a munkafüzet legfeljebb 10 előfizetés kiválasztását támogatja.  

4. Válasszon a **Tárfiókok** legördülő listából egy vagy több olyan fiókot, amelyet alapértelmezettként szeretne látni. Ne feledje, hogy a munkafüzet legfeljebb 200 tárfiók kiválasztását támogatja. 

5. Válassza a **Mentés másként** lehetőséget a parancssávon, ha menteni szeretné a munkafüzet egy példányát a testreszabásokkal, majd kattintson a **Kész szerkesztés gombra** az olvasási módba való visszatéréshez.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Mérőszámok és színek módosítása a munkafüzetben

Az előre összeállított munkafüzetek metrikus adatokat tartalmaznak, és módosíthatja vagy eltávolíthatja a vizualizációk bármelyikét, és testre szabhatja a csapat egyedi igényeinek megfelelően.

Példánkban a többelőfizetéses és tárfiók kapacitásának munkafüzetével dolgozunk, hogy bemutassuk, hogyan:

* Mérőszám eltávolítása
* Színrenderelés módosítása

Ugyanazokat a módosításokat az előre összeállított hibák, **a teljesítmény,** a **rendelkezésre állás**és a **kapacitás** **munkafüzetek**bármelyikén végrehajthatja.

1. Válassza a **Figyelő** lehetőséget a portálon, majd válassza **a Tárfiókok (előzetes verzió)** lehetőséget a bal oldali ablaktáblában.

2. Válassza **a Kapacitás lehetőséget** a kapacitásmunkafüzetre való váltáshoz, majd a parancssávon válassza a **Szerkesztés** lehetőséget a parancssávról.

    ![A Szerkesztés kiválasztása munkafüzet módosításához](./media/storage-insights-overview/workbook-edit-workbook.png)

3. A metrikák szakasz mellett válassza a **Szerkesztés**lehetőséget.

    ![Válassza a Szerkesztés lehetőséget a kapacitásmunkafüzet-mérőszámok módosításához](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Eltávolítjuk a **Fiók használt kapacitás ütemtervoszlopát,** ezért válassza az **Oszlopbeállítások lehetőséget** a metrikarácsban.

    ![Oszlopbeállítások szerkesztése](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. Az **Oszlopbeállítások szerkesztése** ablaktáblán jelölje be **a** **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$| A fiók kapacitása Timeline$**, és az **Oszlopmegjelenítő legördülő** listában válassza a **Rejtett**lehetőséget.

6. Válassza a **Mentés és a bezárás** lehetőséget a módosítás véglegesítéséhez.

Most változtassuk meg a jelentés kapacitási mutatóinak színtémát, hogy a kék helyett zöldet használjunk.

1. Válassza **az Oszlopbeállítások lehetőséget** a metrikarácsban.

2. Az **Oszlopbeállítások szerkesztése** ablaktáblában jelölje be **a** **microsoft.storage/storageaccounts-Capacity-UsedCapacity$|microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storage/storageaccounts/fileservices-capacity-filecapacity$|microsoft.storage/storageaccounts/queueservices-capacity-queuecapacity$|microsoft.storage/storageaccounts/blobservices-capacity-capacity-tablecapacity$** szakaszban. A Legördülő lista **Színpaletta területén**válassza a **Zöld**lehetőséget.

3. Válassza a **Mentés és a bezárás** lehetőséget a módosítás véglegesítéséhez.

4. Válassza a **Mentés másként** lehetőséget a parancssávon, ha menteni szeretné a munkafüzet egy példányát a testreszabásokkal, majd kattintson a **Kész szerkesztés gombra** az olvasási módba való visszatéréshez.  

### <a name="modify-the-availability-threshold"></a>A rendelkezésre állási küszöbérték módosítása

Ebben a példában dolgozunk a tárfiók kapacitása munkafüzet, és bemutatja, hogyan módosíthatja a rendelkezésre állási küszöbértéket. Alapértelmezés szerint a csempe és a rácsjelentési százalékos rendelkezésre állás 90-es minimális küszöbértékkel és 99-es maximális küszöbértékkel van konfigurálva. Az **API-névrács rendelkezésre állási** **%ának** minimális küszöbértékét 85%-ra fogjuk módosítani, ami azt jelenti, hogy az állapot kritikusra változik, ha a küszöbérték 85 százaléknál kisebb. 

1. Válassza **ki a storage-fiókok** a portálon, majd válasszon ki egy tárfiókot a listából.

2. Válassza az **Elemzési adatok (előnézet)** lehetőséget a bal oldali ablaktáblából.

3. A munkafüzetben válassza az **Elérhetőség** lehetőséget az elérhetőségi munkafüzetre való váltáshoz, majd **válassza** a szerkesztés parancsot a parancssávról. 

4. Görgessen le a lap aljára, és a bal oldalon az **Elérhető API-rács** mellett, válassza a **Szerkesztés**lehetőséget.

    ![Az elérhetőség szerkesztése API-névrács beállításai szerint](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Válassza **az Oszlopbeállítások lehetőséget,** majd az **Oszlopbeállítások szerkesztése** ablaktáblán az **Oszlopok** csoportban válassza az **Elérhetőség (%) lehetőséget. (Küszöbértékek + Formázott)**.

6. Módosítsa a **kritikus** állapot értékét **90-ről** **85-re,** majd kattintson a **Mentés és bezárás**gombra.

    ![A kritikus állapot rendelkezésre állási küszöbértékének módosítása](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Válassza a **Mentés másként** lehetőséget a parancssávon, ha menteni szeretné a munkafüzet egy példányát a testreszabásokkal, majd kattintson a **Kész szerkesztés gombra** az olvasási módba való visszatéréshez.

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz segítséget nyújt az Azure Monitor for Storage használata (előzetes verzió) használata során felmerülő gyakori problémák diagnosztizálásában és hibaelhárításában. Az alábbi lista segítségével megkeresheti az adott problémával kapcsolatos információkat.

### <a name="resolving-performance-capacity-or-availability-issues"></a>A teljesítménnyel, a kapacitással és a rendelkezésre állással kapcsolatos problémák megoldása

Az Azure Monitor for Storage (előzetes verzió) segítségével az Azure Storage [hibaelhárítási útmutatójában](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)segítséget nyújt.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Miért csak 200 tárfiókot látok?

A kiválasztott tárfiókok száma legfeljebb 200, függetlenül a kiválasztott előfizetések száma.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Mi történik, ha egy nemrég rögzített csempére kattintok az irányítópulton?

* Ha a csempe bármely pontjára kattint, az arra a lapra vezet, ahonnan a csempe rögzítésre került. Ha például egy grafikont rögzít a "Tárfiók áttekintése" lapon, majd amikor az irányítópulton rákattint a csempére, megnyílik az alapértelmezett nézet, azonban ha a saját mentett példányából rögzít egy grafikont, akkor megnyitja a mentett példány nézetét.
* A cím bal felső részén található szűrőikon megnyílik a "Csempebeállítások konfigurálása" fülre.
* A jobb felső sarokban található ellipszis ikon a "Címadatok testreszabása", a "testreszabás", a "frissítés" és az "eltávolítás az irányítópultról" lehetőséggel kínál.

### <a name="what-happens-when-i-save-a-workbook"></a>Mi történik a munkafüzet mentésekor?

* Munkafüzet mentésekor létrehozhatja a munkafüzet új példányát a szerkesztésekkel együtt, és módosíthatja a címet. A mentés nem írja felül a munkafüzetet, az aktuális munkafüzet mindig az alapértelmezett nézet.
* A **nem mentett** munkafüzet csak az alapértelmezett nézet.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Miért nem látom az összes előfizetésemet a portálon?

A portál csak a kiválasztott előfizetések adatait jeleníti meg a portál indításakor. A kijelölt előfizetések módosításához lépjen a jobb felső sarokban, és kattintson a szűrőikonnal ellátott jegyzetfüzetre. Ez a Könyvtár + előfizetések fülön jelenik meg.

![Címtár és előfizetés](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Hogyan változtassuk meg a színezést és a rendelkezésre állási küszöbértéket?

Tekintse meg [a rendelkezésre állási küszöbérték módosítása](storage-insights-overview.md#modify-the-availability-threshold) szakaszban a színezés és a rendelkezésre állási küszöbértékek módosításának részletes lépéseit.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Az Azure Monitor for Storage-ban megjelenített adatok elemzése és hibaelhárítása?

 Tekintse meg a [Monitor, diagnosztizálása és hibaelhárítása a Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) cikket, hogyan elemezheti és háríthatja el az Azure Storage-adatok az Azure Monitor for Storage.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Miért nem jelenik meg az összes típusú hiba a metrikákban?

Jelenleg legfeljebb három különböző típusú hiba jelenik meg, és a többi hiba egyetlen gyűjtőbe van csoportosítva. A splitByLimit segítségével vezérelhető, és módosítható. A tulajdonság módosításához:

1. Kattintson a munkafüzet szerkesztése gombra.
2. Lépjen a metrikákra, kattintson a szerkesztésre, majd válassza **a Tranzakciók, Összeg** vagy bármilyen kívánt mérőszámok lehetőséget.

    ![Ugrás a mutatókat, és kattintson a szerkesztés, majd a "Tranzakciók, összegek"](./media/storage-insights-overview/fqa7.png)

1. Ezután módosítsa a Felosztások számát.

    ![Metrikus paraméterek kiválasztása"](./media/storage-insights-overview/fqa7-2.png)

Ha azt szeretnénk, hogy n különböző típusú hiba, mint adja splitByLimit n + 1, 1 extra a többi hiba.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Mentettem a munkafüzetet egy tárfiókban. Miért nem találom meg most?

Minden munkafüzet abban a tárfiókban kerül mentésre, amelybe mentette. Próbálja meg keresni azt a tárfiókot, amelyben a felhasználó mentette a munkafüzetet. Ellenkező esetben nincs mód egy adott munkafüzet megkeresésére az erőforrás (tárfiók) ismerete nélkül.

### <a name="what-is-time-range"></a>Mi az időtartomány?

Az időtartomány egy adott időkeretből származó adatokat jelenít meg. Ha például az időtartomány 24 óra, akkor az elmúlt 24 óra adatait jeleníti meg.

### <a name="what-is-time-granularity-time-grain"></a>Mi az idő granularitása (időgabona)?

Az idő részletessége két adatpont közötti időkülönbség. Ha például az idősécséma 1 másodpercre van állítva, az azt jelenti, hogy a mérőszámok másodpercenként gyűjtik össze.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Mennyi az idő, amikor a munkafüzetek bármely részét egy irányítópultra rögzítjük?

Az alapértelmezett idő részletességautomatikus, jelenleg nem módosítható.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Hogyan módosíthatom a munkafüzetlépés időtartományát/ időtartományát az irányítópulton?

Alapértelmezés szerint az irányítópult csempéjének időtartománya 24 órára van állítva, hogy a jobb felső sarokban lévő három pontra való kattintás takarása megváltozzon, válassza a **Csempeadatok testreszabása**lehetőséget, jelölje be a "felülbírálja az irányítópult időbeállításait a címszinten" jelölőnégyzetet, majd válasszon egy időtartományt a legördülő menü segítségével.  

![Jelölje ki a mozaik jobb sarkában lévő három pontot, és válassza az Adatok testreszabása parancsot.](./media/storage-insights-overview/fqa-data-settings.png)

![A Csempe beállításainak konfigurálása területen válassza ki az időtartomány legördülő legördülő listát az időtartomány/időtartomány módosításához](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Hogyan módosíthatom az irányítópultra rögzített munkafüzet vagy munkafüzet-lépés címét?

Az irányítópultra rögzített munkafüzet vagy munkafüzetlépés címe megtartja a munkafüzetben lévő névvel megegyező nevet. A cím módosításához el kell mentenie a munkafüzet saját példányát. Ezután a mentés megnyomása előtt elnevezheti a munkafüzetet.

![A munkafüzet másolatának mentéséhez és nevének módosításához válassza a mentést felül](./media/storage-insights-overview/fqa-change-workbook-name.png)

A mentett munkafüzet egy lépésének nevének módosításához válassza a szerkesztés lehetőséget a lépés alatt, majd a beállítások legalján válassza ki a fogaskereket.

![Válassza a szerkesztés lehetőséget a munkafüzet alján a](./media/storage-insights-overview/fqa-edit.png)
![beállítások megnyitásához A beállításokban válassza ki az alsó fokozatot, hogy meg tudja változtatni a lépés nevét](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>További lépések

* [Metrikariasztások](../platform/alerts-metric.md) és [szolgáltatásállapot-értesítések](../../service-health/alerts-activity-log-service-notifications.md) konfigurálásával automatikus riasztást állíthat be a problémák észleléséhez.

* Ismerje meg a munkafüzetek támogatását, új és testre szabható forgatókönyveket, valamint az Interaktív jelentések létrehozása az Azure Monitor munkafüzetekkel című [áttekintést.](../app/usage-workbooks.md)

* A Storage Analytics és más eszközök használatával az Azure Storage szolgáltatással kapcsolatos problémák azonosítására, diagnosztizálására és elhárítására vonatkozó részletes útmutatót a [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)című témakörben talál.
