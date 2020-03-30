---
title: Útmutató az Azure Monitor Designer csempéihez | Microsoft dokumentumok
description: A View Designer használatával az Azure Monitor, hozhat létre egyéni nézeteket, amelyek megjelennek az Azure Portalon, és a Log Analytics-munkaterület különböző vizualizációkat tartalmaznak. Ez a cikk az egyéni nézetekben elérhető csempék beállításainak útmutatója.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: 0320be3a2cfbb96367799577a6e56bcf5da87dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658506"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Útmutató a Tervezőcsempék megtekintéséhez az Azure Monitorban
Az Azure Monitor Nézettervezőjével számos egyéni nézetet hozhat létre az Azure Portalon, amelyek segítségével vizualizálhatja az adatokat a Log Analytics-munkaterületen. Ez a cikk az egyéni nézetekben elérhető csempék beállításainak útmutatója.

A Tervező megtekintése című témakörben további információt a következő témakörben talál:

* [Tervező megtekintése](view-designer.md): Áttekintést nyújt a Tervező nézetről és az egyéni nézetek létrehozására és szerkesztésére vonatkozó eljárásokról.
* [Képi megjelenítési rész hivatkozása:](view-designer-parts.md)Útmutatót nyújt az egyéni nézetekben elérhető vizualizációs részek beállításaihoz.


Az elérhető Nézettervező csempéket az alábbi táblázat ismerteti:  

| Csempe | Leírás |
|:--- |:--- |
| [Szám](#number-tile) |A lekérdezésből származó rekordok száma. |
| [Két szám](#two-numbers-tile) |Két különböző lekérdezés rekordjainak száma. |
| [Fánk](#donut-tile) | Lekérdezésen alapuló diagram, középen összegző értékkel. |
| Vonaldiagram és ábra | Lekérdezésen alapuló vonaldiagram és összesítő értékkel rendelkező ábra. |
| [Vonaldiagram](#line-chart-tile) |Lekérdezésen alapuló vonaldiagram. |
| [Két idővonal](#two-timelines-tile) | Két adatsort tartalmazó oszlopdiagram, amelyek mindegyike külön lekérdezésen alapul. |

A következő szakaszok részletesen ismertetik a csempetípusokat és azok tulajdonságait.

> [!NOTE]
> A nézetek ben lévő csempék a Log Analytics-munkaterületen lévő [naplólekérdezéseken](../log-query/log-query-overview.md) alapulnak. Jelenleg nem támogatják [az erőforrások közötti lekérdezések](../log-query/cross-workspace-query.md) az Application Insights adatok lekéréséhez.

## <a name="number-tile"></a>Szám csempe
A **Szám** csempe a naplólekérdezésből és a címkerekordjainak számát is megjeleníti.

![Szám csempe](media/view-designer-tiles/tile-number.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Csempe** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg. |
| Lekérdezés |A futtatott lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| **Speciális** |**> Adatáramlás ellenőrzése** |
| Engedélyezve |Válassza ezt a hivatkozást, ha engedélyezni szeretné az adatfolyam-ellenőrzést a csempéhez. Ez a megközelítés alternatív üzenetet biztosít, ha az adatok nem érhetők el. Általában a nézet telepítése és az adatok elérhetővé válnak ideiglenes időszakban a megközelítés használatával jelenik meg. |
| Lekérdezés |A lekérdezés, amely fut annak megállapítására, hogy az adatok rendelkezésre állnak-e a nézethez. Ha a lekérdezés nem ad vissza eredményt, a fő lekérdezés értéke helyett egy üzenet jelenik meg. |
| Üzenet |Az adatfolyam-ellenőrzési lekérdezés esetén megjelenő üzenet nem ad vissza adatokat. Ha nem ad meg üzenetet, megjelenik egy *Előadói értékelés* állapotjelző üzenet. |


## <a name="two-numbers-tile"></a>Két szám csempe
Ez a csempe két különböző naplólekérdezés rekordjainak számát és mindegyikcímkéjét jeleníti meg.

![Két szám csempe](media/view-designer-tiles/tile-two-numbers.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Első mozaik** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg. |
| Lekérdezés |A futtatott lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| **Második mozaik** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg. |
| Lekérdezés |A futtatott lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| **Speciális** |**> Adatáramlás ellenőrzése** |
| Engedélyezve |Válassza ezt a hivatkozást, ha engedélyezni szeretné az adatfolyam-ellenőrzést a csempéhez. Ez a megközelítés alternatív üzenetet biztosít, ha az adatok nem érhetők el. Általában a nézet telepítése és az adatok elérhetővé válnak ideiglenes időszakban a megközelítés használatával jelenik meg. |
| Lekérdezés |A lekérdezés, amely fut annak megállapítására, hogy az adatok rendelkezésre állnak-e a nézethez. Ha a lekérdezés nem ad vissza eredményt, a fő lekérdezés értéke helyett egy üzenet jelenik meg. |
| Üzenet |Az adatfolyam-ellenőrzési lekérdezés esetén megjelenő üzenet nem ad vissza adatokat. Ha nem ad meg üzenetet, megjelenik egy *Előadói értékelés* állapotjelző üzenet. |


## <a name="donut-tile"></a>Fánk csempe
A **Fánk** csempe egyetlen számot jelenít meg, amely egy naplólekérdezés ben egy értékoszlopot összegez. A fánk grafikusan jeleníti meg az első három rekord eredményeit.

![Fánk csempe](media/view-designer-tiles/tile-donut.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Fánk** | |
| Lekérdezés |A fánkhoz futtatott lekérdezés. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. Ez a lekérdezés általában a *mértékkulcsszót* használja az eredmények összegzésére. |
| **Fánk** |**> Központ** |
| Szöveg |A fánkon belüli érték alatt megjelenő szöveg. |
| Művelet |Az értéktulajdonságon végrehajtott művelet, amely egyetlen értékként összegzi.<ul><li>Összeg: Adja hozzá a tulajdonságértékkel rendelkező összes rekord értékét.</li><li>Százalék: A tulajdonságértékkel rendelkező rekordok összesített értékeinek százaléka az összes rekord összesített értékéhez képest.</li></ul> |
| A középső műveletben használt eredményértékek |Ha azt is meg szeretné tetszés szerint, válassza a pluszjelet (+) egy vagy több érték hozzáadásához. A lekérdezés eredménye a megadott tulajdonságértékekkel rendelkező rekordokra korlátozódik. Ha nem ad hozzá értékeket, a lekérdezés minden rekordja szerepel. |
| **Fánk** |**> További lehetőségek** |
| Színek |A három legnépszerűbb tulajdonság mindegyikéhez megjelenített szín. Ha alternatív színeket szeretne megadni adott tulajdonságértékekhez, használja a *Speciális színleképezés parancsot.* |
| Speciális színleképezés |Adott tulajdonságértékeket jelölő színt jelenít meg. Ha a megadott érték az első háromban van, az alternatív szín jelenik meg a normál szín helyett. Ha a tulajdonság nem szerepel az első háromban, a szín nem jelenik meg. |
| **Speciális** |**> Adatáramlás ellenőrzése** |
| Engedélyezve |Válassza ezt a hivatkozást, ha engedélyezni szeretné az adatfolyam-ellenőrzést a csempéhez. Ez a megközelítés alternatív üzenetet biztosít, ha az adatok nem érhetők el. Általában a nézet telepítése és az adatok elérhetővé válnak ideiglenes időszakban a megközelítés használatával jelenik meg. |
| Lekérdezés |A lekérdezés, amely fut annak megállapítására, hogy az adatok rendelkezésre állnak-e a nézethez. Ha a lekérdezés nem ad vissza eredményt, a fő lekérdezés értéke helyett egy üzenet jelenik meg. |
| Üzenet |Az adatfolyam-ellenőrzési lekérdezés esetén megjelenő üzenet nem ad vissza adatokat. Ha nem ad meg üzenetet, megjelenik egy *Előadói értékelés* állapotjelző üzenet. |


## <a name="line-chart-tile"></a>Vonaldiagram csempe
Ez a csempe egy vonaldiagram, amely több adatsort jelenít meg egy naplólekérdezésből az idő múlásával. 

![Vonaldiagram és ábraábra és ábrakivágási csempe](media/view-designer-tiles/tile-line-chart.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A vonaldiagramhoz futtatott lekérdezés. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. Ez a lekérdezés általában a *mértékkulcsszót* használja az eredmények összegzésére. Ha a lekérdezés az *intervallum* kulcsszót használja, akkor az x tengely ezt az időintervallumot használja. Ha a lekérdezés nem használja az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Ezt a hivatkozást választva az y tengelyhez logaritmikus skálát szeretne használni. |
| Egység |Adja meg a lekérdezés által visszaadott értékek mértékegységeit. Ez az információ arra szolgál, hogy címkéket jelenítsen meg a diagramon, jelezve az értéktípusokat, és tetszés szerint az értékek konvertálásához. Az **Egységtípus** meghatározza az egység kategóriáját, és meghatározza a rendelkezésre álló **aktuális egységtípus** értékeket. Ha a **Konvertálás** elemre lehetőséget választ, akkor a numerikus értékek az **Aktuális egység** típusból a **Konvertálás** típussá alakulnak át. |
| Egyéni címke |Az *egységtípus* címkéje mellett az y tengelyhez megjelenített szöveg. Ha nincs megadva címke, csak az *Egység* típus jelenik meg. |
| **Speciális** |**> Adatáramlás ellenőrzése** |
| Engedélyezve |Válassza ezt a hivatkozást, ha engedélyezni szeretné az adatfolyam-ellenőrzést a csempéhez. Ez a megközelítés alternatív üzenetet biztosít, ha az adatok nem érhetők el. Általában a nézet telepítése és az adatok elérhetővé válnak ideiglenes időszakban a megközelítés használatával jelenik meg. |
| Lekérdezés |A lekérdezés, amely fut annak megállapítására, hogy az adatok rendelkezésre állnak-e a nézethez. Ha a lekérdezés nem ad vissza eredményt, a fő lekérdezés értéke helyett egy üzenet jelenik meg. |
| Üzenet |Az adatfolyam-ellenőrzési lekérdezés esetén megjelenő üzenet nem ad vissza adatokat. Ha nem ad meg üzenetet, megjelenik egy *Előadói értékelés* állapotjelző üzenet. |


## <a name="line-chart-and-callout-tile"></a>Vonaldiagram és ábraábra és ábrakivágási csempe
Ez a csempe egy sordiagramot is mutat, amely idővel több adatsort jelenít meg egy naplólekérdezésből, és egy kiírt vonalat összegzett értékkel. 

![Vonaldiagram és ábraábra és ábrakivágási csempe](media/view-designer-tiles/tile-line-chart-callout.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A vonaldiagramhoz futtatott lekérdezés. Az első tulajdonság egy szöveges érték, a második pedig numerikus érték. Ez a lekérdezés általában a *mértékkulcsszót* használja az eredmények összegzésére. Ha a lekérdezés az *intervallum* kulcsszót használja, akkor az x tengely ezt az időintervallumot használja. Ha a lekérdezés nem használja az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| **Vonaldiagram** |**> hívás** |
| Kihívó cím | A felirat értéke felett megjelenő szöveg. |
| Adatsor neve |A kitekintési értékként használandó adatsor tulajdonság értéke. Ha nincs adatsor, a rendszer a lekérdezés összes rekordját használja. |
| Művelet |Az értéktulajdonságon végrehajtott művelet, amely egyetlen értékként összegzi a kitekintést.<ul><li>Átlag: Az összes rekord értékeinek átlaga.</li><li>Darabszám: A lekérdezés által visszaadott összes rekord száma.</li><li>Utolsó minta: A diagramban szereplő utolsó intervallum értéke.</li><li>Max: A diagramban szereplő intervallumok maximális értéke.</li><li>Min: A diagramban szereplő intervallumok minimális értéke.</li><li>Összeg: Az összes rekord értékeinek összege.</li></ul> |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Ezt a hivatkozást választva az y tengelyhez logaritmikus skálát szeretne használni. |
| Egység |Adja meg a lekérdezés által visszaadandó értékek mértékegységeit. Ez az információ az értéktípusokat jelző diagramfeliratok megjelenítésére, és adott esetben az értékek konvertálására szolgál. Az *Egység* típus határozza meg az egység kategóriáját, és a rendelkezésre álló aktuális egységtípus-értékeket. *Current Unit* Ha a Konvertálás lehetőséget *választja,* a numerikus értékek az *Aktuális egység* típusból a *Konvertálás* szöveggé alakulnak át. |
| Egyéni címke |Az *egységtípus* címkéje mellett az y tengelyhez megjelenített szöveg. Ha nincs megadva címke, csak az *Egység* típus jelenik meg. |
| **Speciális** |**> Adatáramlás ellenőrzése** |
| Engedélyezve |Válassza ezt a hivatkozást, ha engedélyezni szeretné az adatfolyam-ellenőrzést a csempéhez. Ez a megközelítés alternatív üzenetet biztosít, ha az adatok nem érhetők el. Általában a nézet telepítése és az adatok elérhetővé válnak ideiglenes időszakban a megközelítés használatával jelenik meg. |
| Lekérdezés |A lekérdezés, amely fut annak megállapítására, hogy az adatok rendelkezésre állnak-e a nézethez. Ha a lekérdezés nem ad vissza eredményt, a fő lekérdezés értéke helyett egy üzenet jelenik meg. |
| Üzenet |Az adatfolyam-ellenőrzési lekérdezés esetén megjelenő üzenet nem ad vissza adatokat. Ha nem ad meg üzenetet, megjelenik egy *Előadói értékelés* állapotjelző üzenet. |


## <a name="two-timelines-tile"></a>Két idővonal csempe
A **Két idősor** csempe két naplólekérdezés eredményét jeleníti meg az idő múlásával oszlopdiagramként. Minden sorozathoz felirat jelenik meg. 

![Két idővonal csempe](media/view-designer-tiles/tile-two-timelines.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| Első diagram | |
| Jelmagyarázat |Az első adatsor felirata alatt megjelenő szöveg. |
| Color |Az első sorozat oszlopaihoz használt szín. |
| Diagramlekérdezés |Az első adatsorhoz futó lekérdezés. A rekordok számát az egyes időintervallumokban a diagram oszlopai jelölik. |
| Művelet |Az értéktulajdonságon végrehajtott művelet, amely egyetlen értékként összegzi a kitekintést.<ul><li>Átlag: Az összes rekord értékeinek átlaga.</li><li>Darabszám: A lekérdezés által visszaadott összes rekord száma.</li><li>Utolsó minta: A diagramban szereplő utolsó intervallum értéke.</li><li>Max: A diagramban szereplő intervallumok maximális értéke.</li></ul> |
| **Második diagram** | |
| Jelmagyarázat |A második adatsor felirata alatt megjelenő szöveg. |
| Color |A második sorozat oszlopaihoz használt szín. |
| Diagramlekérdezés |A második adatsorhoz futó lekérdezés. A rekordok számát az egyes időintervallumokban a diagram oszlopai jelölik. |
| Művelet |Az értéktulajdonságon végrehajtott művelet, amely egyetlen értékként összegzi a kitekintést.<ul><li>Átlag: Az összes rekord értékeinek átlaga.</li><li>Darabszám: A lekérdezés által visszaadott összes rekord száma.</li><li>Utolsó minta: A diagramban szereplő utolsó intervallum értéke.</li><li>Max: A diagramban szereplő intervallumok maximális értéke. |
| **Speciális** |**> Adatáramlás ellenőrzése** |
| Engedélyezve |Válassza ezt a hivatkozást, ha engedélyezni szeretné az adatfolyam-ellenőrzést a csempéhez. Ez a megközelítés alternatív üzenetet biztosít, ha az adatok nem érhetők el. Általában a nézet telepítése és az adatok elérhetővé válnak ideiglenes időszakban a megközelítés használatával jelenik meg. |
| Lekérdezés |A lekérdezés, amely fut annak megállapítására, hogy az adatok rendelkezésre állnak-e a nézethez. Ha a lekérdezés nem ad vissza eredményt, a fő lekérdezés értéke helyett egy üzenet jelenik meg. |
| Üzenet |Az adatfolyam-ellenőrzési lekérdezés esetén megjelenő üzenet nem ad vissza adatokat. Ha nem ad meg üzenetet, megjelenik egy *Előadói értékelés* állapotjelző üzenet. |


## <a name="next-steps"></a>További lépések
* További információ a csempékben lévő lekérdezéseket támogató [naplólekérdezésekről.](../log-query/log-query-overview.md)
* Képi [megjelenítési részeket](view-designer-parts.md) adhat az egyéni nézethez.
