---
title: "A referencia-útmutató az Azure Naplóelemzés Az adatforrásnézet-tervezőből mozaikok |} Microsoft Docs"
description: "Naplóelemzési adatforrásnézet-tervezőből használatával egyéni nézeteket hozhat létre az Azure portálon, amelyeknél a Naplóelemzési munkaterület különböző adatmegjelenítési. Ez a cikk egy referencia-útmutató a csempéket, az egyéni nézetek elérhető beállításai."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: a14a6bf196c165bdffa0a9d5d343c0430cff7f29
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="reference-guide-to-view-designer-tiles-in-log-analytics"></a>Az adatforrásnézet-tervezőből csempéket a Naplóelemzési használati útmutató
Azure Naplóelemzés adatforrásnézet-tervezőből használatával egyéni nézeteket hozhat létre az Azure portálon, hogy az adatmegjelenítéseket a Naplóelemzési munkaterület különböző. Ez a cikk egy referencia-útmutató a csempéket, az egyéni nézetek elérhető beállításai.

Az adatforrásnézet-tervezőből kapcsolatos további információkért lásd:

* [Megtekintheti a tervező](log-analytics-view-designer.md): egy áttekintést nyújt az adatforrásnézet-tervezőből és eljárások létrehozásának és szerkesztésének egyéni nézetek.
* [A képi megjelenítés része hivatkozás](log-analytics-view-designer-parts.md): egy referencia-útmutató az egyéni nézetek elérhető képi megjelenítés összetevőkre vonatkozó beállításokat tartalmaz.

> [!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), a lekérdezések minden nézetben úgy kell megírni, a [új lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856078). A munkaterület frissítés előtt létrehozott nézetekkel automatikusan lesznek átalakítva.

A rendelkezésre álló adatforrásnézet-tervezőből csempéket a következő táblázat ismerteti:  

| Mozaik elrendezés | Leírás |
|:--- |:--- |
| [Szám](#number-tile) |Egy lekérdezés által rekordok száma. |
| [Két szám](#two-numbers-tile) |Két különböző lekérdezéseket rekordok számát. |
| [Fánk](#donut-tile) | Ez a diagram lekérdezés, a központ összefoglaló érték alapján. |
| [Vonaldiagram és kihívás](#line-chart-amp-callout-tile) | Egy vonaldiagramot, amely egy lekérdezést, és egy kihívás értéket összesítő alapul. |
| [Vonaldiagram](#line-chart-tile) |Egy vonaldiagramot, amely a lekérdezés alapján. |
| [Két ütemtervek](#two-timelines-tile) | A két sorozat oszlopdiagram, minden egyes külön lekérdezés alapján. |

A következő szakaszok ismertetik a mozaik típusát és azok tulajdonságait részletesen.

## <a name="number-tile"></a>Szám csempe
A **szám** csempe mindkét napló lekérdezés és a címkék rekordok számát jeleníti meg.

![Szám csempe](media/log-analytics-view-designer/tile-number.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Mozaik elrendezés** | |
| Jelmagyarázat |A szöveg, az érték jelenik meg. |
| Lekérdezés |A lekérdezés futtatásakor. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha az adatfolyam ellenőrzési csempe engedélyezni kell. Ezt a módszert használja egy másik üzenetet biztosít, ha az adatok nem érhető el. Szokásos használja a megközelítés a üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és az adatok elérhetővé válik. |
| Lekérdezés |A lekérdezés futtatása annak megállapításához, hogy adatok a nézet érhető el. Ha a lekérdezés eredménytelen, egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet akkor jelenik meg, ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem. Ha nincs üzenet ad meg egy *végrehajtása Assessment* állapotüzenet jelenik meg. |


## <a name="two-numbers-tile"></a>Két szám csempéje
Ez a csempe érkező két különböző naplófájl-lekérdezések és címkék az egyes számát jeleníti meg.

![Két szám csempéje](media/log-analytics-view-designer/tile-two-numbers.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Első csempe** | |
| Jelmagyarázat |A szöveg, az érték jelenik meg. |
| Lekérdezés |A lekérdezés futtatásakor. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| **Második csempe** | |
| Jelmagyarázat |A szöveg, az érték jelenik meg. |
| Lekérdezés |A lekérdezés futtatásakor. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha az adatfolyam ellenőrzési csempe engedélyezni kell. Ezt a módszert használja egy másik üzenetet biztosít, ha az adatok nem érhető el. Szokásos használja a megközelítés a üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és az adatok elérhetővé válik. |
| Lekérdezés |A lekérdezés futtatása annak megállapításához, hogy adatok a nézet érhető el. Ha a lekérdezés eredménytelen, egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet akkor jelenik meg, ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem. Ha nincs üzenet ad meg egy *végrehajtása Assessment* állapotüzenet jelenik meg. |


## <a name="donut-tile"></a>Fánk csempe
A **fánk** csempe egy számot, amely összefoglalja a napló lekérdezésben érték oszlop jeleníti meg. A fánk grafikusan felső három rekord eredményeit jeleníti meg.

![Fánk csempe](media/log-analytics-view-designer/tile-donut.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Fánk** | |
| Lekérdezés |A lekérdezés a fánk a futtatni kívánt. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. Ez a lekérdezés szokásos használja a *mérték* , hogy összesítse a eredmények kulcsszó. |
| **Fánk** |**> Center** |
| Szöveg |A szöveg, amely alatt a fánk értéket jelenik meg. |
| Művelet |A művelet, amely a value tulajdonság egy értékként legyen megadva összefoglalásképpen hajtja végre.<ul><li>Sum: Adja meg a értéket az összes rekord tulajdonság értékét.</li><li>Százalékos aránya: A tulajdonság értékének összehasonlítva az összes rekord összegzett értékekkel rekordokból az összegzett értékek százalékos aránya</li></ul> |
| Center művelet során használt eredményt értékek |Ha kijelöl egy vagy több érték hozzáadása a plusz jelre (+). A lekérdezés eredményeit rögzíti a megadott tulajdonságértéket korlátozódnak. Ha nincs érték ad hozzá, a lekérdezés szereplő összes rekordok. |
| **Fánk** |**> További beállítások** |
| Színek |Az egyes a három legfontosabb tulajdonságai megjelenő szín. Adja meg az adott tulajdonságértékek másodlagos színt, *szín leképezési speciális*. |
| Speciális szín leképezése |Megjeleníti egy adott tulajdonságra értéket jelölő színét. Ha a megadott érték a három legfontosabb, a másodlagos színt helyett a szabványos szín jelenik meg. Ha a tulajdonság nem szerepel a három legfontosabb, a szín nem jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha az adatfolyam ellenőrzési csempe engedélyezni kell. Ezt a módszert használja egy másik üzenetet biztosít, ha az adatok nem érhető el. Szokásos használja a megközelítés a üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és az adatok elérhetővé válik. |
| Lekérdezés |A lekérdezés futtatása annak megállapításához, hogy adatok a nézet érhető el. Ha a lekérdezés eredménytelen, egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet akkor jelenik meg, ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem. Ha nincs üzenet ad meg egy *végrehajtása Assessment* állapotüzenet jelenik meg. |


## <a name="line-chart-tile"></a>Sor diagram csempe
Ez a csempe vonaldiagramról szó, amely megjeleníti a napló lekérdezésből több sorozat adott idő alatt. 

![Sor diagram és kihívás csempe](media/log-analytics-view-designer/tile-line-chart.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés a vonaldiagram futtatott. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. Ez a lekérdezés szokásos használja a *mérték* , hogy összesítse a eredmények kulcsszó. Ha a lekérdezés használ a *időköz* kulcsszóval, az x tengely használja az adott időszakban. Ha a lekérdezés nem használta a *időköz* kulcsszót, az x tengely használ óránként történik. |
| **Vonaldiagram** |**> Y-axis** |
| A Logaritmikus skála használata |Válassza ki a tengely logaritmikus skála használata ezen a hivatkozáson. |
| egység |Adja meg a lekérdezés által visszaadott értékek használt mértékegységet. Ezt az információt a diagram, amely jelzi, az értéktípusokat és opcionálisan az értékek alakításának feliratok megjelenítéséhez használatos. A **egységtípus** egység kategória határozza meg, és határozza meg a **aktuális egységtípus** elérhető értékek. Ha válasszon ki egy értéket **átalakítása** konvertálja a numerikus értéket, majd a **aktuális egység** típus a **átalakítása** típusa. |
| Egyéni felirat |A felirat melletti tengely megjelenő szöveg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha az adatfolyam ellenőrzési csempe engedélyezni kell. Ezt a módszert használja egy másik üzenetet biztosít, ha az adatok nem érhető el. Szokásos használja a megközelítés a üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és az adatok elérhetővé válik. |
| Lekérdezés |A lekérdezés futtatása annak megállapításához, hogy adatok a nézet érhető el. Ha a lekérdezés eredménytelen, egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet akkor jelenik meg, ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem. Ha nincs üzenet ad meg egy *végrehajtása Assessment* állapotüzenet jelenik meg. |


## <a name="line-chart-and-callout-tile"></a>Sor diagram és kihívás csempe
Ez a csempe diagram azt mutatja, hogy a naplóból több sorozat lekérdezési idő és az összesített érték egy kihívás mindkét egy sor van. 

![Sor diagram és kihívás csempe](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés a vonaldiagram futtatott. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. Ez a lekérdezés szokásos használja a *mérték* , hogy összesítse a eredmények kulcsszó. Ha a lekérdezés használ a *időköz* kulcsszóval, az x tengely használja az adott időszakban. Ha a lekérdezés nem használta a *időköz* kulcsszót, az x tengely használ óránként történik. |
| **Vonaldiagram** |**> Kihívás** |
| Kihívás cím | A képfelirat érték felett megjelenő szöveg. |
| Az adatsorozat neve |A sorozat tulajdonság értéke, a kihívás érték használható. Adatsorozatok valósul meg, ha a lekérdezés összes rekordot szolgálnak. |
| Művelet |A művelet, amely a value tulajdonság, a kihívás az érték összefoglalásképpen hajtja végre.<ul><li>Átlagos: Minden rekordokban levő értékek átlaga.</li><li>Számláló: A lekérdezés által visszaadott összes rekord száma.</li><li>Legutóbbi minta: az utolsó időköze, amely megtalálható a diagram értékét.</li><li>Maximális: A diagramban szereplő időszakok maximális értékét.</li><li>Minimum: Minimális értékének a diagramban szereplő időszakok.</li><li>Sum: Minden rekordokban levő értékek összegét.</li></ul> |
| **Vonaldiagram** |**> Y-axis** |
| A Logaritmikus skála használata |Válassza ki a tengely logaritmikus skála használata ezen a hivatkozáson. |
| egység |A lekérdezés által visszaadott értékek a egységeket adjon meg. Az adatok megjelenítési adatfelirat, amelyek jelzik az értéktípusokat és opcionálisan az az értékeket átalakítani. A *egység* típusa határozza meg az egység a kategóriát, és határozza meg a rendelkezésre álló *aktuális egység* írja be az értékeket. Ha válasszon ki egy értéket *átalakítása*, számértékek konvertálja a *aktuális egység* típus a *átalakítása* típusa. |
| Egyéni felirat |A felirat melletti tengely megjelenő szöveg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha az adatfolyam ellenőrzési csempe engedélyezni kell. Ezt a módszert használja egy másik üzenetet biztosít, ha az adatok nem érhető el. Szokásos használja a megközelítés a üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és az adatok elérhetővé válik. |
| Lekérdezés |A lekérdezés futtatása annak megállapításához, hogy adatok a nézet érhető el. Ha a lekérdezés eredménytelen, egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet akkor jelenik meg, ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem. Ha nincs üzenet ad meg egy *végrehajtása Assessment* állapotüzenet jelenik meg. |


## <a name="two-timelines-tile"></a>Két ütemtervek csempe
A **két ütemtervek** csempe oszlopdiagramok idővel két naplófájl lekérdezések eredményeit jeleníti meg. Valamennyi adatsorozatnál felirat jelenik meg. 

![Két ütemtervek csempe](media/log-analytics-view-designer/tile-two-timelines.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| Első diagram | |
| Jelmagyarázat |A szöveg, a kihívás az első adatsorozathoz jelenik meg. |
| Szín |Az első adatsorozat oszlopok használt szín. |
| A diagram lekérdezés |A lekérdezés futtatása az első adatsorozathoz. A diagram oszlopokat keresztül minden időközhöz rekordok számát jelöli. |
| Művelet |A művelet, amely a value tulajdonság, a kihívás az érték összefoglalásképpen hajtja végre.<ul><li>Átlagos: Minden rekordokban levő értékek átlaga.</li><li>Számláló: A lekérdezés által visszaadott összes rekord száma.</li><li>Legutóbbi minta: az utolsó időköze, amely megtalálható a diagram értékét.</li><li>Maximális: A diagramban szereplő időszakok maximális értékét.</li></ul> |
| **Második diagram** | |
| Jelmagyarázat |A szöveg, a második adatsor kihívás jelenik meg. |
| Szín |A második sorozat oszlopok használt szín. |
| A diagram lekérdezés |A lekérdezés, amely a második adatsorozathoz. A diagram oszlopokat keresztül minden időközhöz rekordok számát jelöli. |
| Művelet |A művelet, amely a value tulajdonság, a kihívás az érték összefoglalásképpen hajtja végre.<ul><li>Átlagos: Minden rekordokban levő értékek átlaga.</li><li>Számláló: A lekérdezés által visszaadott összes rekord száma.</li><li>Legutóbbi minta: az utolsó időköze, amely megtalálható a diagram értékét.</li><li>Maximális: A diagramban szereplő időszakok maximális értékét. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha az adatfolyam ellenőrzési csempe engedélyezni kell. Ezt a módszert használja egy másik üzenetet biztosít, ha az adatok nem érhető el. Szokásos használja a megközelítés a üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és az adatok elérhetővé válik. |
| Lekérdezés |A lekérdezés futtatása annak megállapításához, hogy adatok a nézet érhető el. Ha a lekérdezés eredménytelen, egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet akkor jelenik meg, ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem. Ha nincs üzenet ad meg egy *végrehajtása Assessment* állapotüzenet jelenik meg. |


## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) csempéiben lévő lekérdezések támogatásához.
* Adja hozzá [képi megjelenítés részek](log-analytics-view-designer-parts.md) a egyéni nézetben.
