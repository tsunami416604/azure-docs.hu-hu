---
title: A referencia-útmutató az adatforrásnézet-tervezőből csempékhez az Azure Log Analyticsben |} A Microsoft Docs
description: Nézettervező a Log Analytics használatával egyéni nézeteket hozhat létre az Azure Portalon adatvizualizációk széles megjelennek a Log Analytics-munkaterületet. Ez a cikk egy referencia-útmutató a csempék az egyéni nézetekben elérhető beállításai.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 5b01b55c8b2367861459069cc255840e883b9fc5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817386"
---
# <a name="reference-guide-to-view-designer-tiles-in-log-analytics"></a>Az adatforrásnézet-tervezőből csempék a Log Analytics használati útmutató
Nézettervező az Azure Log Analytics használatával egyéni nézeteket hozhat létre az Azure Portalon, amelyek a Log Analytics-munkaterület adatvizualizációk széles. Ez a cikk egy referencia-útmutató a csempék az egyéni nézetekben elérhető beállításai.

Nézettervező kapcsolatos további információkért lásd:

* [Megtekintheti a tervező](view-designer.md): Nézettervező és eljárások áttekintést nyújt a létrehozásának és szerkesztésének egyéni nézeteket.
* [Vizualizációs rész referenciája](view-designer-parts.md): Itt egy referencia-útmutató a beállítások a vizualizációs rész, amely az egyéni nézetek érhetők el.


Nézettervező csempe érhető el az alábbi táblázat ismerteti:  

| Csempe | Leírás |
|:--- |:--- |
| [Szám](#number-tile) |Egy lekérdezés által rekordok száma. |
| [Két szám](#two-numbers-tile) |Két különböző lekérdezéseket rekordjainak számát. |
| [fánkdiagram](#donut-tile) | Egy diagram, amely egy lekérdezést a központban összefoglaló értékkel alapul. |
| Vonaldiagram és képfelirat | Egy vonaldiagramot, amely egy lekérdezést és a egy összegző értékkel felirat alapján. |
| [Vonaldiagram](#line-chart-tile) |Egy vonaldiagramot, amely egy lekérdezésen alapul. |
| [Két idősor](#two-timelines-tile) | A két sorozat oszlopdiagram, minden egyes külön lekérdezés alapján. |

A következő szakaszok ismertetik a csempe típusát és azok tulajdonságait az részletei.

## <a name="number-tile"></a>Számot tartalmazó csempe
A **szám** csempe mindkét naplófájl lekérdezés és a egy címkét a rekordok számát jeleníti meg.

![Számot tartalmazó csempe](media/view-designer-tiles/tile-number.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **csempe** | |
| Jelmagyarázat |A szöveg, amely alatt az érték jelenik meg. |
| Lekérdezés |A lekérdezés futtatásakor. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| **Speciális** |**> Adatfolyam-ellenőrzés** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha a csempe engedélyezni kell a adatfolyamot. Ez a megközelítés egy másik üzenet biztosít, ha az adatok nem érhetők el. Általában használatával megközelítés adja meg egy üzenetet során az ideiglenes időszak, amikor a nézet telepítve van, és az adatok elérhetővé válnak. |
| Lekérdezés |A lekérdezés, amely elérhető a nézet adatok meghatározásához. Ha a lekérdezés eredménytelen, megjelenik egy üzenet, az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet jelenik meg, ha az adatfolyam-ellenőrzési lekérdezés visszaadja az adatot nem. Ha ad meg semmilyen üzenet egy *értékelés folyamatban* állapotüzenet jelenik meg. |


## <a name="two-numbers-tile"></a>Két szám csempe
Ez a csempe minden két különböző naplófájl-lekérdezések és a egy címkét a rekordok számát jeleníti meg.

![Két szám csempe](media/view-designer-tiles/tile-two-numbers.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Első csempe** | |
| Jelmagyarázat |A szöveg, amely alatt az érték jelenik meg. |
| Lekérdezés |A lekérdezés futtatásakor. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| **Második csempe** | |
| Jelmagyarázat |A szöveg, amely alatt az érték jelenik meg. |
| Lekérdezés |A lekérdezés futtatásakor. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| **Speciális** |**> Adatfolyam-ellenőrzés** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha a csempe engedélyezni kell a adatfolyamot. Ez a megközelítés egy másik üzenet biztosít, ha az adatok nem érhetők el. Általában használatával megközelítés adja meg egy üzenetet során az ideiglenes időszak, amikor a nézet telepítve van, és az adatok elérhetővé válnak. |
| Lekérdezés |A lekérdezés, amely elérhető a nézet adatok meghatározásához. Ha a lekérdezés eredménytelen, megjelenik egy üzenet, az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet jelenik meg, ha az adatfolyam-ellenőrzési lekérdezés visszaadja az adatot nem. Ha ad meg semmilyen üzenet egy *értékelés folyamatban* állapotüzenet jelenik meg. |


## <a name="donut-tile"></a>Fánkdiagram csempe
A **Fánkdiagram** csempe egyetlen szám, amely összefoglalja a log lekérdezésben érték oszlop jeleníti meg. A fánkdiagram grafikusan felső három rekord eredményeit jeleníti meg.

![Fánkdiagram csempe](media/view-designer-tiles/tile-donut.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **fánkdiagram** | |
| Lekérdezés |A lekérdezés, amely a fánkdiagram számára. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. Ez a lekérdezés parancsmagokéval használ a *mérték* kulcsszó összefoglalva az eredményeket. |
| **fánkdiagram** |**> Center** |
| Szöveg |A szöveg, amely alatt a fánkdiagram belül az érték jelenik meg. |
| Művelet |A műveletet, hogy együtt, egyetlen értékként a value tulajdonság.<ul><li>Összeg: Adja hozzá a tulajdonság értéke az összes rekord értékeit.</li><li>Százalékos aránya: A tulajdonság értéke az összes rekord összegzett értékek képest rekordokból az összegzett értékek százalékos értéke.</li></ul> |
| A fánkdiagram közepén megjelenített eredményértékek |Kiválaszthat egy vagy több érték hozzáadása a plusz jelre (+). A lekérdezés eredményeit rögzíti a megadott tulajdonság értékek korlátozódnak. Nincsenek értékek vesznek fel, ha a lekérdezés szereplő összes rekordok. |
| **fánkdiagram** |**> További beállítások** |
| Színek |Minden, a három leggyakoribb tulajdonságainak megjelenő szín. Másodlagos színe tulajdonságértékeket megadásához használja *szín leképezési speciális*. |
| Speciális színleképezés |Megjeleníti egy adott tulajdonság értéket jelölő szín. Ha a megadott értéket az első három, a másodlagos színt helyett a standard szintű szín jelenik meg. Ha a tulajdonság nem szerepel az első három, a szín nem jelenik meg. |
| **Speciális** |**> Adatfolyam-ellenőrzés** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha a csempe engedélyezni kell a adatfolyamot. Ez a megközelítés egy másik üzenet biztosít, ha az adatok nem érhetők el. Általában használatával megközelítés adja meg egy üzenetet során az ideiglenes időszak, amikor a nézet telepítve van, és az adatok elérhetővé válnak. |
| Lekérdezés |A lekérdezés, amely elérhető a nézet adatok meghatározásához. Ha a lekérdezés eredménytelen, megjelenik egy üzenet, az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet jelenik meg, ha az adatfolyam-ellenőrzési lekérdezés visszaadja az adatot nem. Ha ad meg semmilyen üzenet egy *értékelés folyamatban* állapotüzenet jelenik meg. |


## <a name="line-chart-tile"></a>Sor diagram csempe
Ez a csempe nem egy vonaldiagramot, amely megjeleníti a log lekérdezésből származó több sorozat idővel. 

![Vonal és képfelirat diagram csempe](media/view-designer-tiles/tile-line-chart.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés, amely a vonaldiagramon. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. Ez a lekérdezés parancsmagokéval használ a *mérték* kulcsszó összefoglalva az eredményeket. Ha a lekérdezés a *időköz* kulcsszó, az x tengely ez alatt az időtartam használja. Ha a lekérdezés nem használ a *időköz* kulcsszó, az x tengely használ óránként történik. |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ki ezt a hivatkozást az y-tengely logaritmikus skála használata. |
| Egység |Adja meg a lekérdezés által visszaadott értékek használt mértékegységet. Ez az információ segítségével megjeleníthesse a címkéket a diagramon, érték jelző és opcionálisan alakítása. az értékeket. A **egység típusa** meghatározza, melyik az egységet, és meghatározza a **aktuális egység típusa** elérhető értékek. Ha kiválaszt egy értéket a **átalakítása** a numerikus értékek konvertálja, majd a **aktuális egység** írja be a **átalakítása** típusa. |
| Egyéni felirat |A szöveg, amelyben az y-tengely a címke mellett jelenik meg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |
| **Speciális** |**> Adatfolyam-ellenőrzés** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha a csempe engedélyezni kell a adatfolyamot. Ez a megközelítés egy másik üzenet biztosít, ha az adatok nem érhetők el. Általában használatával megközelítés adja meg egy üzenetet során az ideiglenes időszak, amikor a nézet telepítve van, és az adatok elérhetővé válnak. |
| Lekérdezés |A lekérdezés, amely elérhető a nézet adatok meghatározásához. Ha a lekérdezés eredménytelen, megjelenik egy üzenet, az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet jelenik meg, ha az adatfolyam-ellenőrzési lekérdezés visszaadja az adatot nem. Ha ad meg semmilyen üzenet egy *értékelés folyamatban* állapotüzenet jelenik meg. |


## <a name="line-chart-and-callout-tile"></a>Vonal és képfelirat diagram csempe
Ez a csempe rendelkezik mindkét egy sor diagram azt mutatja, hogy a naplóból származó több sorozat lekérdezési idő és a egy kihívás az összesített érték. 

![Vonal és képfelirat diagram csempe](media/view-designer-tiles/tile-line-chart-callout.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés, amely a vonaldiagramon. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. Ez a lekérdezés parancsmagokéval használ a *mérték* kulcsszó összefoglalva az eredményeket. Ha a lekérdezés a *időköz* kulcsszó, az x tengely ez alatt az időtartam használja. Ha a lekérdezés nem használ a *időköz* kulcsszó, az x tengely használ óránként történik. |
| **Vonaldiagram** |**> Képfelirat** |
| Képfelirat címe | A képfelirat értéke felett megjelenő szöveg. |
| Az adatsorozat neve |A sorozat tulajdonság értéke, a képfelirat értéke használható. Ha nincsenek adatsorozatok áll rendelkezésre, a lekérdezés összes rekordját használja. |
| Művelet |A művelet együtt, egyetlen értékként az a kihívás értékének tulajdonság végrehajtott műveletek.<ul><li>Átlagos: Az összes rekord értékek átlaga.</li><li>Száma: A lekérdezés által visszaadott összes rekord száma.</li><li>Legutóbbi minta: Az utolsó időközt, amely megtalálható a diagramon értéke.</li><li>Max: A maximális érték a diagramon szereplő időszakok.</li><li>Min: A diagram szerepelnek az intervallumok minimális értéke.</li><li>Összeg: Az összes rekord értékek összegét.</li></ul> |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ki ezt a hivatkozást az y-tengely logaritmikus skála használata. |
| Egység |Adja meg az egységeket a lekérdezés által visszaadott értékek. Ezt az információt használja megjelenített vonaldiagram-címkék, amelyek jelzik a értéktípusok, és szükség esetén történő átalakítása az értékeket. A *egység* típusát adja meg a kategória az egység, és határozza meg a rendelkezésre álló *aktuális egység* írja be az értékeket. Ha kiválaszt egy értéket a *átalakítása*, a numerikus értékek konvertálja a *aktuális egység* írja be a *átalakítása* típusa. |
| Egyéni felirat |A szöveg, amelyben az y-tengely a címke mellett jelenik meg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |
| **Speciális** |**> Adatfolyam-ellenőrzés** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha a csempe engedélyezni kell a adatfolyamot. Ez a megközelítés egy másik üzenet biztosít, ha az adatok nem érhetők el. Általában használatával megközelítés adja meg egy üzenetet során az ideiglenes időszak, amikor a nézet telepítve van, és az adatok elérhetővé válnak. |
| Lekérdezés |A lekérdezés, amely elérhető a nézet adatok meghatározásához. Ha a lekérdezés eredménytelen, megjelenik egy üzenet, az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet jelenik meg, ha az adatfolyam-ellenőrzési lekérdezés visszaadja az adatot nem. Ha ad meg semmilyen üzenet egy *értékelés folyamatban* állapotüzenet jelenik meg. |


## <a name="two-timelines-tile"></a>Két idősor csempe
A **két idősor** csempe oszlopdiagramként idővel két naplófájl lekérdezések eredményeit jeleníti meg. Minden sorozatot a felirat jelenik meg. 

![Két idősor csempe](media/view-designer-tiles/tile-two-timelines.png)

| Beállítás | Leírás |
|:--- |:--- |
| Name (Név) |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| Első diagram | |
| Jelmagyarázat |A kihívás az első adatsor alatt megjelenő szöveg. |
| Szín |Az oszlopok az első sorozat használt szín. |
| Diagramlekérdezés |A lekérdezés futtatása az első adatsorozathoz. Minden egyes idő alatt a rekordok számát a tábla oszlopainak képviseli. |
| Művelet |A művelet együtt, egyetlen értékként az a kihívás értékének tulajdonság végrehajtott műveletek.<ul><li>Átlagos: Az összes rekord értékek átlaga.</li><li>Száma: A lekérdezés által visszaadott összes rekord száma.</li><li>Legutóbbi minta: Az utolsó időközt, amely megtalálható a diagramon értéke.</li><li>Max: A maximális érték a diagramon szereplő időszakok.</li></ul> |
| **Második diagram** | |
| Jelmagyarázat |A kihívás, a második adatsor alatt megjelenő szöveg. |
| Szín |A második sor az oszlopokat használt szín. |
| Diagramlekérdezés |A lekérdezés, amely a második adatsorozathoz. Minden egyes idő alatt a rekordok számát a tábla oszlopainak képviseli. |
| Művelet |A művelet együtt, egyetlen értékként az a kihívás értékének tulajdonság végrehajtott műveletek.<ul><li>Átlagos: Az összes rekord értékek átlaga.</li><li>Száma: A lekérdezés által visszaadott összes rekord száma.</li><li>Legutóbbi minta: Az utolsó időközt, amely megtalálható a diagramon értéke.</li><li>Max: A maximális érték a diagramon szereplő időszakok. |
| **Speciális** |**> Adatfolyam-ellenőrzés** |
| Engedélyezve |Kattintson erre a hivatkozásra, ha a csempe engedélyezni kell a adatfolyamot. Ez a megközelítés egy másik üzenet biztosít, ha az adatok nem érhetők el. Általában használatával megközelítés adja meg egy üzenetet során az ideiglenes időszak, amikor a nézet telepítve van, és az adatok elérhetővé válnak. |
| Lekérdezés |A lekérdezés, amely elérhető a nézet adatok meghatározásához. Ha a lekérdezés eredménytelen, megjelenik egy üzenet, az érték a fő lekérdezés helyett. |
| Üzenet |Az üzenet jelenik meg, ha az adatfolyam-ellenőrzési lekérdezés visszaadja az adatot nem. Ha ad meg semmilyen üzenet egy *értékelés folyamatban* állapotüzenet jelenik meg. |


## <a name="next-steps"></a>További lépések
* Ismerje meg [naplókereséseket](../../azure-monitor/log-query/log-query-overview.md) lévő csempék a lekérdezések támogatásához.
* Adjon hozzá [Vizualizáció részek](view-designer-parts.md) a egyéni nézethez.
