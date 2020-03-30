---
title: Útmutató az Azure Monitor Designer-részeinek megtekintése céljához | Microsoft dokumentumok
description: A View Designer használatával az Azure Monitor, hozhat létre egyéni nézeteket, amelyek megjelennek az Azure Portalon, és a Log Analytics-munkaterület különböző vizualizációkat tartalmaznak. Ez a cikk az egyéni nézetekben elérhető vizualizációs részek beállításainak útmutatója.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 7dc4164cac1998a02ae62d7682f9630aa4faa619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658557"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Útmutató a Tervezői megjelenítési elemek megtekintéséhez az Azure Monitorban
Az Azure Monitor Nézettervezőjével számos egyéni nézetet hozhat létre az Azure Portalon, amelyek segítségével vizualizálhatja az adatokat a Log Analytics-munkaterületen. Ez a cikk az egyéni nézetekben elérhető vizualizációs részek beállításainak útmutatója.

A Tervező megtekintése című témakörben további információt a következő témakörben talál:

* [Tervező megtekintése](view-designer.md): Áttekintést nyújt a Tervező nézetről és az egyéni nézetek létrehozására és szerkesztésére vonatkozó eljárásokról.
* [Csempehivatkozás](view-designer-tiles.md): Hivatkozást tartalmaz az egyéni nézetekben elérhető csempék beállításaira.


Az elérhető Nézettervező csempetípusokat az alábbi táblázat ismerteti:

| Nézet típusa | Leírás |
|:--- |:--- |
| [Lekérdezések listája](#list-of-queries-part) |Megjeleníti a naplólekérdezések listáját. Az egyes lekérdezések közül kiválaszthatja az eredmények megjelenítését. |
| [Szám és lista](#number-and-list-part) |A fejléc egyetlen számot jelenít meg, amely a naplólekérdezés rekordjainak számát jeleníti meg. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Két szám és lista](#two-numbers-and-list-part) |A fejléc két számot jelenít meg, amelyek a különböző naplólekérdezések rekordjainak számát jelenítik meg. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Fánk és lista](#donut-and-list-part) |A fejléc egyetlen számot jelenít meg, amely egy naplólekérdezés egyik értékoszlopát összegzi. A fánk grafikusan jeleníti meg az első három rekord eredményeit. |
| [Két ütemterv és lista](#two-timelines-and-list-part) |A fejléc két naplólekérdezés eredményét jeleníti meg az idő múlásával oszlopdiagramként, egy olyan kitekintéssel, amely egyetlen számot jelenít meg, amely egy értékoszlopot összegez egy naplólekérdezésben. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Információ](#information-part) |A fejléc statikus szöveget és egy választható hivatkozást jelenít meg. A lista egy vagy több statikus címmel és szöveggel rendelkező elemet jelenít meg. |
| [Vonaldiagram, ábravonal és lista](#line-chart-callout-and-list-part) |A fejléc egy sordiagramot jelenít meg, amely idővel több adatsort jelenít meg egy naplólekérdezésből, és egy összesített értéket tartalmazó ábra. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Vonaldiagram és -lista](#line-chart-and-list-part) |A fejléc egy sordiagramot jelenít meg, amely idővel több adatsort tartalmaz egy naplólekérdezésből. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Sordiagramok kötegei](#stack-of-line-charts-part) |Három különálló vonaldiagramot jelenít meg, amelyek ből több adatsor is adatsor származik egy naplólekérdezésből. |

A következő szakaszok részletesen ismertetik a csempetípusokat és azok tulajdonságait.

> [!NOTE]
> A nézetek részei a Log Analytics-munkaterület [naplólekérdezésein](../log-query/log-query-overview.md) alapulnak. Jelenleg nem támogatják [az erőforrások közötti lekérdezések](../log-query/cross-workspace-query.md) az Application Insights adatok lekéréséhez.

## <a name="list-of-queries-part"></a>A lekérdezések része
A lekérdezések listája rész a naplólekérdezések listáját jeleníti meg. Az egyes lekérdezések közül kiválaszthatja az eredmények megjelenítését. A nézet alapértelmezés szerint egyetlen lekérdezést tartalmaz, és további lekérdezések hozzáadásához a **+ Lekérdezés** lehetőséget is választhatja.

![Lekérdezések listája nézet](media/view-designer-parts/view-list-queries.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Cím |A nézet tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Előre kiválasztott szűrők |A lekérdezés kiválasztásakor a bal oldali szűrőablakban szerepelendő tulajdonságok vesszővel tagolt listája. |
| Leképezési mód |A lekérdezés kiválasztásakor megjelenő kezdeti nézet. A lekérdezés megnyitása után bármelyik elérhető nézetet kiválaszthatja. |
| **Lekérdezések** | |
| Keresési lekérdezés |A futtatandó lekérdezés. |
| Felhasználóbarát név | A megjelenő leíró név. |

## <a name="number-and-list-part"></a>Szám és listarész
A fejléc egyetlen számot jelenít meg, amely a naplólekérdezés rekordjainak számát jeleníti meg. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Lekérdezések listája nézet](media/view-designer-parts/view-number-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A nézet tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Ikon |A fejlécben lévő eredmény mellett megjelenő képfájl. |
| A használat ikonja |Az ikon megjelenítéséhez jelölje ki ezt a hivatkozást. |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg. |
| Lekérdezés |A fejléchez futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Átkattintásos navigáció | A fejlécre való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Az eredmények első tíz rekordjának első két tulajdonsága jelenik meg. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. A program automatikusan létrehozza azokat a sávokat, amelyek a numerikus oszlop relatív értékén alapulnak.<br><br>A `Sort` lekérdezésben lévő paranccsal rendezheti a listában szereplő rekordokat. A lekérdezés futtatásához és az összes rekord visszaadásához jelölje be **az Összes megtekintése**jelölőnégyzetet. |
| Diagram elrejtése |Ezzel a hivatkozással letilthatja a numerikus oszlop tól jobbra lévő grafikont. |
| Értékgörbék engedélyezése |Ezzel a hivatkozással vízszintes sáv helyett értékgörbét jeleníthet meg. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Color |A sávok vagy az értékgörbék színe. |
| Név és értékelválasztó |A szövegtulajdonság több értékre történő elemzéséhez használt egykarakteres határolójel. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Átkattintásos navigáció | A lista egy elemére való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Lista** |**> oszlop címei** |
| Név |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információt a Gyakori beállítások című témakörben [talál.](#thresholds) |

## <a name="two-numbers-and-list-part"></a>Két szám és listarész
A fejléc két számmal rendelkezik, amelyek a különböző naplólekérdezések rekordjainak számát jelenítik meg. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Két szám & listanézet](media/view-designer-parts/view-two-numbers-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A nézet tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Ikon |A fejlécben lévő eredmény mellett megjelenő képfájl. |
| A használat ikonja |Az ikon megjelenítéséhez jelölje ki ezt a hivatkozást. |
| **Cím navigáció** | |
| Átkattintásos navigáció | A fejlécre való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg. |
| Lekérdezés |A fejléchez futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Az eredmények első tíz rekordjának első két tulajdonsága jelenik meg. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. A sávok automatikusan létrejönnek a numerikus oszlop relatív értéke alapján.<br><br>A `Sort` lekérdezésben lévő paranccsal rendezheti a listában szereplő rekordokat. A lekérdezés futtatásához és az összes rekord visszaadásához jelölje be **az Összes megtekintése**jelölőnégyzetet. |
| Diagram elrejtése |Ezzel a hivatkozással letilthatja a numerikus oszlop tól jobbra lévő grafikont. |
| Értékgörbék engedélyezése |Ezzel a hivatkozással vízszintes sáv helyett értékgörbét jeleníthet meg. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Color |A sávok vagy az értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtásához végrehajtandó művelet. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Név és értékelválasztó |A szövegtulajdonság több értékre történő elemzéséhez használt egykarakteres határolójel. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Átkattintásos navigáció | A lista egy elemére való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Lista** |**> oszlop címei** |
| Név |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információt a Gyakori beállítások című témakörben [talál.](#thresholds) |

## <a name="donut-and-list-part"></a>Fánk és lista rész
A fejléc egyetlen számot jelenít meg, amely egy naplólekérdezés egyik értékoszlopát összegzi. A fánk grafikusan jeleníti meg az első három rekord eredményeit.

![Fánk és lista nézet](media/view-designer-parts/view-donut-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Ikon |A fejlécben lévő eredmény mellett megjelenő képfájl. |
| A használat ikonja |Az ikon megjelenítéséhez jelölje ki ezt a hivatkozást. |
| **Fejléc** | |
| Cím |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején lévő cím alatt megjelenő szöveg. |
| **Fánk** | |
| Lekérdezés |A fánkhoz futtatandó lekérdezés. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. |
| Átkattintásos navigáció | A fejlécre való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Fánk** |**> Központ** |
| Szöveg |A fánkon belüli érték alatt megjelenő szöveg. |
| Művelet |Az értéktulajdonságon végrehajtandó művelet, amely egyetlen értékként összegzi azt.<ul><li>Összeg: Az összes rekord értékét összeadja.</li><li>Százalék: A **középérték-értékek** ben használt eredményértékek által visszaadott rekordok és a lekérdezés összes rekordjának aránya.</li></ul> |
| A középső műveletben használt eredményértékek |Ha azt is meg szeretné tetszés szerint, válassza a pluszjelet (+) egy vagy több érték hozzáadásához. A lekérdezés eredménye a megadott tulajdonságértékekkel rendelkező rekordokra korlátozódik. Ha nem ad hozzá értékeket, a lekérdezés minden rekordja szerepel. |
| **További lehetőségek** |**> színek** |
| 1. szín<br>2. szín<br>3. szín |Válassza ki a fánkban megjelenő értékek színét. |
| **További lehetőségek** |**> Speciális színleképezés** |
| Mező értéke |Írja be egy mező nevét, hogy más színként jelenjen meg, ha szerepel a fánkban. |
| Color |Válassza ki az egyedi mező színét. |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Diagram elrejtése |Ezzel a hivatkozással letilthatja a numerikus oszlop tól jobbra lévő grafikont. |
| Értékgörbék engedélyezése |Ezzel a hivatkozással vízszintes sáv helyett értékgörbét jeleníthet meg. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Color |A sávok vagy az értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtásához végrehajtandó művelet. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Név és értékelválasztó |A szövegtulajdonság több értékre történő elemzéséhez használt egykarakteres határolójel. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Átkattintásos navigáció | A lista egy elemére való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Lista** |**> oszlop címei** |
| Név |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információt a Gyakori beállítások című témakörben [talál.](#thresholds) |

## <a name="two-timelines-and-list-part"></a>Két ütemterv és listarész
A fejléc két naplólekérdezés eredményét jeleníti meg az idő múlásával oszlopdiagramként, egy olyan kitekintéssel, amely egyetlen számot jelenít meg, amely egy értékoszlopot összegez egy naplólekérdezésben. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Két ütemterv és listanézet](media/view-designer-parts/view-two-timelines-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Ikon |A fejlécben lévő eredmény mellett megjelenő képfájl. |
| A használat ikonja |Az ikon megjelenítéséhez jelölje ki ezt a hivatkozást. |
| **Cím navigáció** | |
| Átkattintásos navigáció | A fejlécre való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Első<br>diagram második diagram** | |
| Jelmagyarázat |Az első adatsor felirata alatt megjelenő szöveg. |
| Color |A sorozat oszlopaihoz használandó szín. |
| Lekérdezés |Az első adatsorhoz futtatandó lekérdezés. A rekordok számát az egyes időintervallumokban a diagram oszlopai jelölik. |
| Művelet |Az értéktulajdonságon végrehajtandó művelet, amely egyetlen értékként összegzi a kitekintést.<ul><li>Összeg: Az összes rekord értékeinek összege.</li><li>Átlag: Az összes rekord értékeinek átlaga.</li><li>Utolsó minta: A diagramban szereplő utolsó intervallum értéke.</li><li>Első minta: A diagramban szereplő első intervallum értéke.</li><li>Darabszám: A lekérdezés által visszaadott összes rekord száma.</li></ul> |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Diagram elrejtése |Ezzel a hivatkozással letilthatja a numerikus oszlop tól jobbra lévő grafikont. |
| Értékgörbék engedélyezése |Ezzel a hivatkozással vízszintes sáv helyett értékgörbét jeleníthet meg. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Color |A sávok vagy az értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtásához végrehajtandó művelet. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Átkattintásos navigáció | A lista egy elemére való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Lista** |**> oszlop címei** |
| Név |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információt a Gyakori beállítások című témakörben [talál.](#thresholds) |

## <a name="information-part"></a>Információs rész
A fejléc statikus szöveget és egy választható hivatkozást jelenít meg. A lista egy vagy több statikus címmel és szöveggel rendelkező elemet jelenít meg.

![Információs nézet](media/view-designer-parts/view-information.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Color |A fejléc háttérszíne. |
| **Fejléc** | |
| Kép |A fejlécben megjelenő képfájl. |
| Címke |A fejlécben megjelenő szöveg. |
| **Fejléc** |**> hivatkozás** |
| Címke |A hivatkozás szövege. |
| URL-cím |A hivatkozás URL-címe. |
| **Információs elemek** | |
| Cím |Az egyes elemek címéhez megjelenített szöveg. |
| Tartalom |Az egyes elemekhez megjelenített szöveg. |

## <a name="line-chart-callout-and-list-part"></a>Vonaldiagram, ábravonal és listarész
A fejléc egy több adatsort tartalmazó sordiagramot jelenít meg egy naplólekérdezésből az idő múlásával, és egy összesített értéket tartalmazó kijelentéssel. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Vonaldiagram, ábravonal és listanézet](media/view-designer-parts/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Ikon |A fejlécben lévő eredmény mellett megjelenő képfájl. |
| A használat ikonja |Az ikon megjelenítéséhez jelölje ki ezt a hivatkozást. |
| **Fejléc** | |
| Cím |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején lévő cím alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A vonaldiagramhoz futtatandó lekérdezés. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. Ez a lekérdezés általában a *mértékkulcsszót* használja az eredmények összegzésére. Ha a lekérdezés az *intervallum* kulcsszót használja, akkor a diagram x tengelye ezt az időintervallumot használja. Ha a lekérdezés nem tartalmazza az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| Átkattintásos navigáció | A fejlécre való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Vonaldiagram** |**> hívás** |
| Kihívó cím |A felirat értéke felett megjelenő szöveg. |
| Adatsor neve |A kiszólított értékhez használandó adatsor tulajdonságértéke. Ha nincs adatsor, a rendszer a lekérdezés összes rekordját használja. |
| Művelet |Az értéktulajdonságon végrehajtandó művelet, amely egyetlen értékként összegzi a kitekintést.<ul><li>Átlag: Az összes rekord értékeinek átlaga.</li><li>Darabszám: A lekérdezés által visszaadott összes rekord száma.</li><li>Utolsó minta: A diagramban szereplő utolsó intervallum értéke.</li><li>Max: A diagramban szereplő intervallumok maximális értéke.</li><li>Min: A diagramban szereplő intervallumok minimális értéke.</li><li>Összeg: Az összes rekord értékeinek összege.</li></ul> |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Ezt a hivatkozást választva az y tengelyhez logaritmikus skálát szeretne használni. |
| Egység |Adja meg a lekérdezés által visszaadandó értékek mértékegységeit. Ez az információ az értéktípusokat jelző diagramfeliratok megjelenítésére, és adott esetben az értékek konvertálására szolgál. Az *Egység* típus határozza meg az egység kategóriáját, és a rendelkezésre álló aktuális egységtípus-értékeket. *Current Unit* Ha a Konvertálás lehetőséget *választja,* a numerikus értékek az *Aktuális egység* típusból a *Konvertálás* szöveggé alakulnak át. |
| Egyéni címke |Az *egységtípus* címkéje mellett az y tengelyhez megjelenített szöveg. Ha nincs megadva címke, csak az *Egység* típus jelenik meg. |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Diagram elrejtése |Ezzel a hivatkozással letilthatja a numerikus oszlop tól jobbra lévő grafikont. |
| Értékgörbék engedélyezése |Ezzel a hivatkozással vízszintes sáv helyett értékgörbét jeleníthet meg. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Color |A sávok vagy az értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtásához végrehajtandó művelet. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Név és értékelválasztó |A szövegtulajdonság több értékre történő elemzéséhez használt egykarakteres határolójel. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Átkattintásos navigáció | A lista egy elemére való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Lista** |**> oszlop címei** |
| Név |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információt a Gyakori beállítások című témakörben [talál.](#thresholds) |

## <a name="line-chart-and-list-part"></a>Sordiagram és listarész
A fejléc egy több adatsort tartalmazó sordiagramot jelenít meg egy naplólekérdezésből az idő múlásával. A lista egy lekérdezés első tíz találatát jeleníti meg, egy grafikont, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Vonaldiagram és listanézet](media/view-designer-parts/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Ikon |A fejlécben lévő eredmény mellett megjelenő képfájl. |
| A használat ikonja |Az ikon megjelenítéséhez jelölje ki ezt a hivatkozást. |
| **Fejléc** | |
| Cím |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején lévő cím alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A vonaldiagramhoz futtatandó lekérdezés. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. Ez a lekérdezés általában a *mértékkulcsszót* használja az eredmények összegzésére. Ha a lekérdezés az *intervallum* kulcsszót használja, akkor a diagram x tengelye ezt az időintervallumot használja. Ha a lekérdezés nem tartalmazza az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| Átkattintásos navigáció | A fejlécre való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Ezt a hivatkozást választva az y tengelyhez logaritmikus skálát szeretne használni. |
| Egység |Adja meg a lekérdezés által visszaadandó értékek mértékegységeit. Ez az információ az értéktípusokat jelző diagramfeliratok megjelenítésére, és adott esetben az értékek konvertálására szolgál. Az *Egység* típus határozza meg az egység kategóriáját, és a rendelkezésre álló aktuális egységtípus-értékeket. *Current Unit* Ha a Konvertálás lehetőséget *választja,* a numerikus értékek az *Aktuális egység* típusból a *Konvertálás* szöveggé alakulnak át. |
| Egyéni címke |Az *egységtípus* címkéje mellett az y tengelyhez megjelenített szöveg. Ha nincs megadva címke, csak az *Egység* típus jelenik meg. |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Diagram elrejtése |Ezzel a hivatkozással letilthatja a numerikus oszlop tól jobbra lévő grafikont. |
| Értékgörbék engedélyezése |Ezzel a hivatkozással vízszintes sáv helyett értékgörbét jeleníthet meg. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Color |A sávok vagy az értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtásához végrehajtandó művelet. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Név és értékelválasztó |A szövegtulajdonság több értékre történő elemzéséhez használt egykarakteres határolójel. További információt a Gyakori beállítások című témakörben [talál.](#sparklines) |
| Átkattintásos navigáció | A lista egy elemére való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Lista** |**> oszlop címei** |
| Név |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információt a Gyakori beállítások című témakörben [talál.](#thresholds) |

## <a name="stack-of-line-charts-part"></a>Sordiagramok kötegei
A vonaldiagram-verem három különálló vonaldiagramot jelenít meg, amelyek idővel több adatsort is bemutatnak egy naplólekérdezésből, ahogy az itt látható:

![Vonaldiagramok halmozása](media/view-designer-parts/view-stack-line-charts.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Ezzel a hivatkozással új csoportot hozhat létre a nézetben, az aktuális nézettől kezdve. |
| Ikon |A fejlécben lévő eredmény mellett megjelenő képfájl. |
| **1.<br>ábra<br>2.** |**> fejléc** |
| Cím |A diagram tetején megjelenő szöveg. |
| Alcím |A diagram tetején a cím alatt megjelenő szöveg. |
| **1.<br>ábra<br>2.** |**Vonaldiagram** |
| Lekérdezés |A vonaldiagramhoz futtatandó lekérdezés. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. Ez a lekérdezés általában a *mértékkulcsszót* használja az eredmények összegzésére. Ha a lekérdezés az *intervallum* kulcsszót használja, akkor a diagram x tengelye ezt az időintervallumot használja. Ha a lekérdezés nem tartalmazza az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| Átkattintásos navigáció | A fejlécre való kattintáskor végrehajtott művelet.  További információt a Gyakori beállítások című témakörben [talál.](#click-through-navigation) |
| **Diagram** |**> Y tengely** |
| Logaritmikus skála használata |Ezt a hivatkozást választva az y tengelyhez logaritmikus skálát szeretne használni. |
| Egység |Adja meg a lekérdezés által visszaadandó értékek mértékegységeit. Ez az információ az értéktípusokat jelző diagramfeliratok megjelenítésére, és adott esetben az értékek konvertálására szolgál. Az *Egység* típus határozza meg az egység kategóriáját, és a rendelkezésre álló aktuális egységtípus-értékeket. *Current Unit* Ha a Konvertálás lehetőséget *választja,* a numerikus értékek az *Aktuális egység* típusból a *Konvertálás* szöveggé alakulnak át. |
| Egyéni címke |Az *egységtípus* címkéje mellett az y tengelyhez megjelenített szöveg. Ha nincs megadva címke, csak az *Egység* típus jelenik meg. |

## <a name="common-settings"></a>Gyakori beállítások
A következő szakaszok olyan beállításokat írnak le, amelyek több vizualizációs résznél is közösek.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Név és értékelválasztó
A név és az értékelválasztó az egykarakteres határolójel, amely a listalekérdezés szövegtulajdonságának több értékre való elemzéséhez használható. Ha ha elhatárolót ad meg, az egyes mezőknevét a Név mezőben ugyanazzal a határolójelrel elválasztva **adhatja** meg.

Vegyünk például egy *Hely* nevű tulajdonságot, amely olyan értékeket tartalmazott, mint a *Redmond-Building 41* és a *Bellevue-Building 12*. Megadhat egy kötőjelet (-) a névhez és az értékelválasztóhoz, *a Város-építés* értéket pedig a névhez. Ez a megközelítés az egyes értékeket két tulajdonságba *elemzi,* a Város és *a Building .*

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Átkattintásos navigáció
Az átkattintási navigáció határozza meg, hogy milyen műveletet hajtson végre a program, amikor egy nézetfejlécére vagy listaelemére kattint.  Ez vagy megnyit egy lekérdezést a [Log Analytics](../../azure-monitor/log-query/portals.md) szolgáltatásban, vagy egy másik nézetet indít el.

Az alábbi táblázat az átkattintásos navigáció beállításait ismerteti.

| Beállítás           | Leírás |
|:--|:--|
| Naplókeresés (automatikus) | Fejlécelem kijelölésétekkor futtatandó naplólekérdezés.  Ez ugyanaz a naplólekérdezés, amelyen az elem alapul.
| Naplók keresése        | Naplólekérdezés futtatása, amikor kijelöl egy elemet egy listában.  Írja be a lekérdezést a **Navigációs lekérdezés** mezőbe.   *A(z) {selected item}* segítségével adja meg a felhasználó által kijelölt elem szintaxisát.  Ha például a lekérdezésnek van egy Számítógép nevű *oszlopa,* és a navigációs lekérdezés *{selected item}*, a számítógép kiválasztásakor egy olyan lekérdezés fut, mint *a Computer="MyComputer".* Ha a navigációs lekérdezés *Type=Event {selected item}*, a *Type=Event Computer="MyComputer"* lekérdezés fut. |
| Nézet              | A Nézet megnyitása, amikor fejlécelemet vagy egy lista elemét jelöl ki.  A Név **megtekintése** mezőben jelölje ki egy nézet nevét a munkaterületen. |



### <a name="sparklines"></a><a name="sparklines"></a>Értékgörbék
Az értékgörbe egy kis vonaldiagram, amely a listabejegyzések idővel történő értékét mutatja be. Listaelemek megjelenítéséhez megadhatja, hogy vízszintes sávjelenjen-e meg, amely egy numerikus oszlop relatív értékét, vagy egy értékgörbét jelez, amely az értékét jelzi az idő múlásával.

Az alábbi táblázat az értékgörbék beállításait ismerteti:

| Beállítás | Leírás |
|:--- |:--- |
| Értékgörbék engedélyezése |Ezzel a hivatkozással vízszintes sáv helyett értékgörbét jeleníthet meg. |
| Művelet |Ha az értékgörbék engedélyezve vannak, ez az a művelet, amelyet a lista minden tulajdonságán végre kell hajtani az értékgörbe értékértékeinek kiszámításához.<ul><li>Utolsó minta: Az adatsor utolsó értéke az időintervallumban.</li><li>Max: A sorozat maximális értéke az időintervallumban.</li><li>Min: A sorozat minimális értéke az időintervallumban.</li><li>Összeg: Az adatsor értékeinek összege az időintervallumban.</li><li>Összegzés: Ugyanazt `measure` a parancsot használja, mint a fejlécben lévő lekérdezés.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Küszöbértékek
A küszöbértékek használatával egy színes ikont jeleníthet meg a lista minden eleme mellett. A küszöbértékek gyors vizuális jelzést adnak azon elemekről, amelyek meghaladják az adott értéket, vagy egy adott tartományba esnek. Megjeleníthetünk például egy zöld ikont az elfogadható értékű elemekhez, sárga, ha az érték egy figyelmeztetésre utaló tartományon belül van, és pirosat, ha az meghaladja a hibaértéket.

Ha egy alkatrészhez küszöbértékeket engedélyez, meg kell adnia egy vagy több küszöbértéket. Ha egy cikk értéke nagyobb, mint egy küszöbérték, és kisebb, mint a következő küszöbérték, akkor a program az adott érték színét használja. Ha az elem nagyobb, mint a legmagasabb küszöbérték, a program egy másik színt használ. 

Minden küszöbérték-készletnek van egy alapértelmezett értékkel rendelkező **küszöbértéke.** Ez az a szín, amely et beállít, ha más értéket nem lép túl. A küszöbértékek hozzáadásához vagy eltávolításához válassza a **Hozzáadás** (+) vagy a **Törlés** (x) gombot.

Az alábbi táblázat a küszöbértékek beállításait ismerteti:

| Beállítás | Leírás |
|:--- |:--- |
| Küszöbértékek engedélyezése |Ezzel a hivatkozással minden értékbal balra megjeleníthet egy színikont. Az ikon jelzi az érték állapotát a megadott küszöbértékekhez képest. |
| Név |A küszöbérték neve. |
| Küszöbérték |A küszöbérték értéke. Az egyes listaelemek állapotszíne az elem által túllépt legmagasabb küszöbérték színére van beállítva. Ha a rendszer nem lépi túl a küszöbértékeket, a program alapértelmezett színt használ. |
| Color |A küszöbértéket jelző szín. |

## <a name="next-steps"></a>További lépések
* Információ a vizualizációs részek lekérdezéseinek támogatásához használt [naplólekérdezésekről.](../log-query/log-query-overview.md)
