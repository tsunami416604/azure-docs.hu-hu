---
title: A referencia-útmutató az adatforrásnézet-tervezőből részére az Azure Log Analyticsben |} A Microsoft Docs
description: Nézettervező a Log Analytics használatával egyéni nézeteket hozhat létre az Azure Portalon adatvizualizációk széles megjelennek a Log Analytics-munkaterületet. Ez a cikk egy referencia-útmutató a beállítások a vizualizációs rész, amely szerepel az egyéni nézetek.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 876cb037aab718a91f469f8862c501bb9a29e4d6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846426"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Nézettervező vizualizációs részére a Log Analytics használati útmutató
Nézettervező az Azure Log Analytics használatával egyéni nézeteket hozhat létre az Azure Portalon, amelyek a Log Analytics-munkaterület adatvizualizációk széles. Ez a cikk egy referencia-útmutató a beállítások a vizualizációs rész, amely szerepel az egyéni nézetek.

Nézettervező kapcsolatos további információkért lásd:

* [Megtekintheti a tervező](view-designer.md): Nézettervező és eljárások áttekintést nyújt a létrehozásának és szerkesztésének egyéni nézeteket.
* [Csempe referencia](view-designer-tiles.md): tartalmaz egy hivatkozást az egyéni nézetekben elérhető csempékhez beállításait.


A rendelkezésre álló Nézettervező csempe típusokat az alábbi táblázat ismerteti:

| Nézet típusa | Leírás |
|:--- |:--- |
| [Lekérdezések listája](#list-of-queries-part) |Naplóbeli keresési lekérdezések listáját jeleníti meg. Kiválaszthatja az egyes lekérdezések eredményeinek megjelenítéséhez. |
| [Szám és lista](#number-and-list-part) |A fejléc egyetlen szám, amely megjeleníti a naplóbeli keresési lekérdezés rekordjainak számát jeleníti meg. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg. |
| [Két szám és lista](#two-numbers-and-list-part) |A fejléc két szám azt mutatják be, különálló naplóbeli keresési lekérdezések rekordjainak számát jeleníti meg. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg. |
| [Fánkdiagram és lista](#donut-and-list-part) |A fejléc egyetlen szám, amely összefoglalja a log lekérdezésben érték oszlop jeleníti meg. A fánkdiagram grafikusan felső három rekord eredményeit jeleníti meg. |
| [Két idősor és lista](#two-timelines-and-list-part) |A fejléc két naplófájl lekérdezések eredményeit oszlopdiagramként, a kihívás, amely egyetlen szám, amely összefoglalja a log lekérdezésben érték oszlop megjeleníti az idő függvényében jeleníti meg. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg. |
| [Információ](#information-part) |A fejléc statikus szöveget és a egy nem kötelező hivatkozást jeleníti meg. A listában egy vagy több elemet egy statikus címet és szöveget jeleníti meg. |
| [Vonaldiagram, képfelirat és lista](#line-chart-callout-and-list-part) |A fejléc egy vonaldiagram látható, az idő függvényében, és az összesített érték felirat log lekérdezésből több sorozat. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg. |
| [Vonaldiagram és lista](#line-chart-and-list-part) |A fejléc jeleníti meg a vonaldiagramot, több sorozat idővel log lekérdezésből. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg. |
| [Sor diagramok rész verem](#stack-of-line-charts-part) |Megjeleníti a három külön vonaldiagramok használata több sorozat idővel log lekérdezésből. |

A következő szakaszok ismertetik a csempe típusát és azok tulajdonságait az részletei.

## <a name="list-of-queries-part"></a>Lekérdezések rész listája
A lista lekérdezések egy részét a naplóbeli keresési lekérdezések listáját jeleníti meg. Kiválaszthatja az egyes lekérdezések eredményeinek megjelenítéséhez. A nézet tartalmazza az alapértelmezés szerint egyetlen lekérdezést, és kiválaszthatja **+ lekérdezés** további lekérdezések hozzáadásához.

![Lekérdezések megtekintése listája](media/view-designer-parts/view-list-queries.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Beosztás |A nézet tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Előre kijelölt szűrők |A bal oldali szűrőterület felhőtulajdonságok, amikor kiválaszt egy vesszővel tagolt listája. |
| Megjelenítési mód |A kezdeti nézet jelenik meg, ha a lekérdezés van kiválasztva. Minden elérhető nézetek után, nyissa meg a lekérdezés választhat. |
| **Lekérdezések** | |
| Keresési lekérdezés |A lekérdezés futtatásához. |
| Felhasználóbarát név | A megjelenített leíró nevet. |

## <a name="number-and-list-part"></a>Szám és lista
A fejléc egyetlen szám, amely megjeleníti a naplóbeli keresési lekérdezés rekordjainak számát jeleníti meg. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg.

![Lekérdezések megtekintése listája](media/view-designer-parts/view-number-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A nézet tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Ikon |A result fejlécben mellett megjelenik az képfájl. |
| Ikon használata |Válassza ki ezt a hivatkozást a ikonjának megjelenítése. |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatása a fejléc. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Átkattintásos navigáció | A fejlécére kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **List** | |
| Lekérdezés |A lekérdezés futtatása a listát. Az első tíz az eredményekben lévő rekordokra első két tulajdonságai jelennek meg. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. Sávok automatikusan jönnek létre, amely a numerikus oszlop relatív érték alapján vannak.<br><br>Használja a `Sort` a lekérdezést, hogy a rekordok rendezése a listán a parancsot. Futtassa a lekérdezést, és minden rekordot ad vissza, kiválaszthatja a **összes**. |
| Gráf elrejtése |Válassza ki ezt a hivatkozást az letiltja a diagram jobb oldalán a numerikus oszlopára. |
| Értékgörbék engedélyezése |Válassza ki ezt a hivatkozást az értékgörbe helyett egy vízszintes sáv megjelenítése. További információkért lásd: [közös beállítások](#sparklines). |
| Szín |A klasszikus vagy Értékgörbék színe. |
| Név és érték elválasztó |A határolójel elemezni a text tulajdonság be több érték használata. További információkért lásd: [közös beállítások](#sparklines). |
| Átkattintásos navigáció | A lista elemeire kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **List** |**> Oszlopcímek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson a küszöbértékek engedélyezése. További információkért lásd: [közös beállítások](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Két szám és lista része
A fejléc van két szám, amely megjeleníti az külön naplóbeli keresési lekérdezések rekordjainak számát. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg.

![Két szám és lista megtekintése](media/view-designer-parts/view-two-numbers-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A nézet tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Ikon |A result fejlécben mellett megjelenik az képfájl. |
| Ikon használata |Válassza ki ezt a hivatkozást a ikonjának megjelenítése. |
| **Cím navigáció** | |
| Átkattintásos navigáció | A fejlécére kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatása a fejléc. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatása a listát. Az első tíz az eredményekben lévő rekordokra első két tulajdonságai jelennek meg. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. Sávok automatikusan a numerikus oszlop relatív értéke alapján hozzák létre.<br><br>Használja a `Sort` a lekérdezést, hogy a rekordok rendezése a listán a parancsot. Futtassa a lekérdezést, és minden rekordot ad vissza, kiválaszthatja a **összes**. |
| Gráf elrejtése |Válassza ki ezt a hivatkozást az letiltja a diagram jobb oldalán a numerikus oszlopára. |
| Értékgörbék engedélyezése |Válassza ki ezt a hivatkozást az értékgörbe helyett egy vízszintes sáv megjelenítése. További információkért lásd: [közös beállítások](#sparklines). |
| Szín |A klasszikus vagy Értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [közös beállítások](#sparklines). |
| Név és érték elválasztó |A határolójel elemezni a text tulajdonság be több érték használata. További információkért lásd: [közös beállítások](#sparklines). |
| Átkattintásos navigáció | A lista elemeire kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **List** |**> Oszlopcímek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson a küszöbértékek engedélyezése. További információkért lásd: [közös beállítások](#thresholds). |

## <a name="donut-and-list-part"></a>Fánkdiagram és lista
A fejléc egyetlen szám, amely összefoglalja a log lekérdezésben érték oszlop jeleníti meg. A fánkdiagram grafikusan felső három rekord eredményeit jeleníti meg.

![Fánkdiagram és lista megtekintése](media/view-designer-parts/view-donut-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Ikon |A result fejlécben mellett megjelenik az képfájl. |
| Ikon használata |Válassza ki ezt a hivatkozást a ikonjának megjelenítése. |
| **Fejléc** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A szöveg, amely a fejléc tetején a cím alatt jelenik meg. |
| **fánkdiagram** | |
| Lekérdezés |A lekérdezés futtatása a fánkdiagram. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. |
| Átkattintásos navigáció | A fejlécére kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **fánkdiagram** |**> Center** |
| Szöveg |A szöveg, amely alatt a fánkdiagram belül az érték jelenik meg. |
| Művelet |A value tulajdonság együtt, egyetlen értékként kell elvégezni a műveletet.<ul><li>Összeg: Hozzáadja az összes rekord értékeit.</li><li>Százalékban: A szereplő értékek által visszaadott rekordok arány **center művelet során használt értékek eredmény** a teljes rekordot a lekérdezésben.</li></ul> |
| A fánkdiagram közepén megjelenített eredményértékek |Kiválaszthat egy vagy több érték hozzáadása a plusz jelre (+). A lekérdezés eredményeit rögzíti a megadott tulajdonság értékek korlátozódnak. Nincsenek értékek vesznek fel, ha a lekérdezés szereplő összes rekordok. |
| **További beállítások** |**> Színek** |
| 1 színe<br>2. szín<br>Szín 3 |Válassza ki a színt a fánkdiagramon megjelenő értékeit. |
| **További beállítások** |**> Speciális színleképezés** |
| A mező értéke |Írja be a egy mezőt egy-egy formátumban jeleníti meg, ha a fánk szerepeljen. |
| Szín |Válassza ki a színt, az egyedi mező. |
| **List** | |
| Lekérdezés |A lekérdezés futtatása a listát. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Gráf elrejtése |Válassza ki ezt a hivatkozást az letiltja a diagram jobb oldalán a numerikus oszlopára. |
| Értékgörbék engedélyezése |Válassza ki ezt a hivatkozást az értékgörbe helyett egy vízszintes sáv megjelenítése. További információkért lásd: [közös beállítások](#sparklines). |
| Szín |A klasszikus vagy Értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [közös beállítások](#sparklines). |
| Név és érték elválasztó |A határolójel elemezni a text tulajdonság be több érték használata. További információkért lásd: [közös beállítások](#sparklines). |
| Átkattintásos navigáció | A lista elemeire kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **List** |**> Oszlopcímek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson a küszöbértékek engedélyezése. További információkért lásd: [közös beállítások](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Két idősor és lista része
A fejléc két naplófájl lekérdezések eredményeit oszlopdiagramként, a kihívás, amely egyetlen szám, amely összefoglalja a log lekérdezésben érték oszlop megjeleníti az idő függvényében jeleníti meg. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg.

![Két idősor és lista megtekintése](media/view-designer-parts/view-two-timelines-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Ikon |A result fejlécben mellett megjelenik az képfájl. |
| Ikon használata |Válassza ki ezt a hivatkozást a ikonjának megjelenítése. |
| **Cím navigáció** | |
| Átkattintásos navigáció | A fejlécére kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **Első diagram<br>második diagram** | |
| Jelmagyarázat |A kihívás az első adatsor alatt megjelenő szöveg. |
| Szín |Az a sorozat az oszlopok használandó színt. |
| Lekérdezés |A lekérdezés futtatása az első adatsorozathoz. Minden egyes idő alatt rekordok számát a tábla oszlopainak képviseli. |
| Művelet |A művelet végrehajtásához együtt, egyetlen értékként az a kihívás értékének tulajdonság.<ul><li>Összeg: Az összes rekord értékek összegét.</li><li>Átlagos: Az összes rekord értékek átlaga.</li><li>Utolsó minta: az utolsó időközt, amely megtalálható a diagramon az értékét.</li><li>Először minta: az első időközt, amely megtalálható a diagramon az értékét.</li><li>Száma: Minden, a lekérdezés által visszaadott rekordok számát.</li></ul> |
| **List** | |
| Lekérdezés |A lekérdezés futtatása a listát. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Gráf elrejtése |Válassza ki ezt a hivatkozást az letiltja a diagram jobb oldalán a numerikus oszlopára. |
| Értékgörbék engedélyezése |Válassza ki ezt a hivatkozást az értékgörbe helyett egy vízszintes sáv megjelenítése. További információkért lásd: [közös beállítások](#sparklines). |
| Szín |A klasszikus vagy Értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [közös beállítások](#sparklines). |
| Átkattintásos navigáció | A lista elemeire kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **List** |**> Oszlopcímek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson a küszöbértékek engedélyezése. További információkért lásd: [közös beállítások](#thresholds). |

## <a name="information-part"></a>Információs rész
A fejléc statikus szöveget és a egy nem kötelező hivatkozást jeleníti meg. A listában egy vagy több elemet egy statikus címet és szöveget jeleníti meg.

![Információk megtekintése](media/view-designer-parts/view-information.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Szín |A fejléc háttérszíne. |
| **Fejléc** | |
| Kép |A fejléc megjelenik a képfájl. |
| Címke |A fejlécében megjelenő szöveg. |
| **Fejléc** |**> Link** |
| Címke |A hivatkozás szövege. |
| URL-cím |A hivatkozás URL-címét. |
| **Információs elemek** | |
| Beosztás |A cím az egyes elemek megjelenő szöveg. |
| Tartalom |Az egyes elemekhez megjelenített szöveg. |

## <a name="line-chart-callout-and-list-part"></a>Vonaldiagram, képfelirat és lista része
A fejléc log lekérdezésből származó több sorozat vonaldiagramot időt és a egy kihívás az összesített értéket jeleníti meg. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg.

![Vonaldiagram, képfelirat és lista megtekintése](media/view-designer-parts/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Ikon |A result fejlécben mellett megjelenik az képfájl. |
| Ikon használata |Válassza ki ezt a hivatkozást a ikonjának megjelenítése. |
| **Fejléc** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A szöveg, amely a fejléc tetején a cím alatt jelenik meg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés futtatása a vonaldiagramot. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. Ez a lekérdezés parancsmagokéval használ a *mérték* kulcsszó összefoglalva az eredményeket. Ha a lekérdezés a *időköz* kulcsszó, a diagram x tengely ez alatt az időtartam használja. Ha a lekérdezés nem tartalmazza a *időköz* kulcsszó, az x tengely használ óránként történik. |
| Átkattintásos navigáció | A fejlécére kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **Vonaldiagram** |**> Képfelirat** |
| Képfelirat címe |A képfelirat értéke felett megjelenő szöveg. |
| Adatsorozat neve |A képfelirat értékéhez használandó adatsor tulajdonság értéke. Ha nincsenek adatsorozatok áll rendelkezésre, a lekérdezés összes rekordját használja. |
| Művelet |A művelet végrehajtásához együtt, egyetlen értékként az a kihívás értékének tulajdonság.<ul><li>Átlagos: Az összes rekord értékek átlaga.</li><li>Száma: Minden, a lekérdezés által visszaadott rekordok számát.</li><li>Utolsó minta: az utolsó időközt, amely megtalálható a diagramon az értékét.</li><li>Maximális száma: A maximális értékét az intervallumok, a diagram szerepelnek.</li><li>Min.: A minimális érték a a diagramban szereplő időszakok közül.</li><li>Összeg: Az összes rekord értékek összegét.</li></ul> |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ki ezt a hivatkozást az y-tengely logaritmikus skála használata. |
| Egység |Adja meg az egységeket a lekérdezés által visszaadott értékek. Ezt az információt használja megjelenített vonaldiagram-címkék, amelyek jelzik a értéktípusok, és szükség esetén történő átalakítása az értékeket. A *egység* típusát adja meg a kategória az egység, és határozza meg a rendelkezésre álló *aktuális egység* írja be az értékeket. Ha kiválaszt egy értéket a *átalakítása*, a numerikus értékek konvertálja a *aktuális egység* írja be a *átalakítása* típusa. |
| Egyéni felirat |A szöveg, amelyben az y-tengely a címke mellett jelenik meg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatása a listát. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Gráf elrejtése |Válassza ki ezt a hivatkozást az letiltja a diagram jobb oldalán a numerikus oszlopára. |
| Értékgörbék engedélyezése |Válassza ki ezt a hivatkozást az értékgörbe helyett egy vízszintes sáv megjelenítése. További információkért lásd: [közös beállítások](#sparklines). |
| Szín |A klasszikus vagy Értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [közös beállítások](#sparklines). |
| Név és érték elválasztó |A határolójel elemezni a text tulajdonság be több érték használata. További információkért lásd: [közös beállítások](#sparklines). |
| Átkattintásos navigáció | A lista elemeire kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **List** |**> Oszlopcímek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson a küszöbértékek engedélyezése. További információkért lásd: [közös beállítások](#thresholds). |

## <a name="line-chart-and-list-part"></a>Diagram és a lista rész sor
A fejléc log lekérdezésből származó több sorozat vonaldiagramot idővel jeleníti meg. A lista egy lekérdezést, amely azt jelzi, hogy az relatív érték egy numerikus oszlopot vagy annak módosulnak grafikon a felső tíz eredményeit jeleníti meg.

![Sor diagram és a lista megtekintése](media/view-designer-parts/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Ikon |A result fejlécben mellett megjelenik az képfájl. |
| Ikon használata |Válassza ki ezt a hivatkozást a ikonjának megjelenítése. |
| **Fejléc** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A szöveg, amely a fejléc tetején a cím alatt jelenik meg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés futtatása a vonaldiagramot. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. Ez a lekérdezés parancsmagokéval használ a *mérték* kulcsszó összefoglalva az eredményeket. Ha a lekérdezés a *időköz* kulcsszó, a diagram x tengely ez alatt az időtartam használja. Ha a lekérdezés nem tartalmazza a *időköz* kulcsszó, az x tengely használ óránként történik. |
| Átkattintásos navigáció | A fejlécére kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ki ezt a hivatkozást az y-tengely logaritmikus skála használata. |
| Egység |Adja meg az egységeket a lekérdezés által visszaadott értékek. Ezt az információt használja megjelenített vonaldiagram-címkék, amelyek jelzik a értéktípusok, és szükség esetén történő átalakítása az értékeket. A *egység* típusát adja meg a kategória az egység, és határozza meg a rendelkezésre álló *aktuális egység* írja be az értékeket. Ha kiválaszt egy értéket a *átalakítása*, a numerikus értékek konvertálja a *aktuális egység* írja be a *átalakítása* típusa. |
| Egyéni felirat |A szöveg, amelyben az y-tengely a címke mellett jelenik meg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatása a listát. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Gráf elrejtése |Válassza ki ezt a hivatkozást az letiltja a diagram jobb oldalán a numerikus oszlopára. |
| Értékgörbék engedélyezése |Válassza ki ezt a hivatkozást az értékgörbe helyett egy vízszintes sáv megjelenítése. További információkért lásd: [közös beállítások](#sparklines). |
| Szín |A klasszikus vagy Értékgörbék színe. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [közös beállítások](#sparklines). |
| Név és érték elválasztó |A határolójel elemezni a text tulajdonság be több érték használata. További információkért lásd: [közös beállítások](#sparklines). |
| Átkattintásos navigáció | A lista elemeire kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **List** |**> Oszlopcímek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson a küszöbértékek engedélyezése. További információkért lásd: [közös beállítások](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Sor diagramok rész verem
A vonaldiagram-verem három külön vonaldiagramok használata több sorozat idővel log lekérdezésből jeleníti meg, itt látható módon:

![Vonaldiagram-halom](media/view-designer-parts/view-stack-line-charts.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki ezt a hivatkozást az új csoport létrehozása a nézetben, a jelenlegi nézet ellenében. |
| Ikon |A result fejlécben mellett megjelenik az képfájl. |
| **Diagram – 1<br>2 diagram<br>3 mutató részletes diagram** |**> Header** |
| Beosztás |A diagram tetején megjelenő szöveg. |
| Alcím |A szöveg, amely a diagram tetején a cím alatt jelenik meg. |
| **Diagram – 1<br>2 diagram<br>3 mutató részletes diagram** |**Vonaldiagram** |
| Lekérdezés |A lekérdezés futtatása a vonaldiagramot. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus értéket. Ez a lekérdezés parancsmagokéval használ a *mérték* kulcsszó összefoglalva az eredményeket. Ha a lekérdezés a *időköz* kulcsszó, a diagram x tengely ez alatt az időtartam használja. Ha a lekérdezés nem tartalmazza a *időköz* kulcsszó, az x tengely használ óránként történik. |
| Átkattintásos navigáció | A fejlécére kattintva végrehajtott műveletet.  További információkért lásd: [közös beállítások](#click-through-navigation). |
| **Diagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ki ezt a hivatkozást az y-tengely logaritmikus skála használata. |
| Egység |Adja meg az egységeket a lekérdezés által visszaadott értékek. Ezt az információt használja megjelenített vonaldiagram-címkék, amelyek jelzik a értéktípusok, és szükség esetén történő átalakítása az értékeket. A *egység* típusát adja meg a kategória az egység, és határozza meg a rendelkezésre álló *aktuális egység* írja be az értékeket. Ha kiválaszt egy értéket a *átalakítása*, a numerikus értékek konvertálja a *aktuális egység* írja be a *átalakítása* típusa. |
| Egyéni felirat |A szöveg, amelyben az y-tengely a címke mellett jelenik meg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |

## <a name="common-settings"></a>Általános beállítások
A következő szakaszok ismertetik a beállításokat, amelyek közös több Vizualizáció részére.

### <a name="name-value-separator"></a>Név és érték elválasztó
A név és érték elválasztó a határolójel használatával a text tulajdonság listalekérdezés értelmezhető több érték. Ha megad egy elválasztó karaktert, megadhatja az azonos elválasztója elválasztott mezők nevei a **neve** mezőbe.

Vegyük példaként a tulajdonságot, *hely* például tartalmazza, amely értékek *Redmond-összeállító 41* és *Bellevue-összeállító 12*. Név és érték elválasztó is megadhat egy kötőjelet (-) és *város-összeállító* neve. Ez a megközelítés elemzi az egyes értékek be két tulajdonság nevű *Város* és *épület*.

### <a name="click-through-navigation"></a>Átkattintásos navigáció
Átkattintásos navigáció határozza meg, milyen műveletet nem hajt egy fejléc vagy a lista elemére kattintva.  Vagy megnyílik a lekérdezés a [Naplókeresési portál](../../log-analytics/log-analytics-log-search-portals.md) vagy egy másik nézet indítása.

Az alábbi táblázat az átkattintásos navigáció vonatkozó beállításait ismerteti.

| Beállítás           | Leírás |
|:--|:--|
| Naplókeresés (automatikus) | Naplókeresés futtatásához, amikor kiválaszt egy fejléc elemet.  Ez az azonos naplóbeli keresés a cikk alapján.
| Naplók keresése        | Naplóbeli keresés futtatását, amikor kiválaszt egy elemet a listában.  Írja be a lekérdezést a **navigációs lekérdezés** mezőbe.   Használat *{kijelölt elem}* a szintaxist, az a felhasználó által kiválasztott elem tartalmazza.  Például, ha a lekérdezés nevű oszlop *számítógép* és a navigációs lekérdezés *{kijelölt elem}*, mint például a lekérdezés *számítógép = "Sajátgép"* fut, amikor kiválaszt egy számítógép. Ha a navigációs lekérdezés *típus = {kijelölt elem} esemény*, a lekérdezés *típusa = esemény számítógép = "Sajátgép"* futtatása. |
| Nézet              | Nyissa meg a listában egy fejléc elemet vagy egy elem kijelölésekor nézetet.  Válassza ki a nézet nevét a munkaterület a **nézetnév** mezőbe. |



### <a name="sparklines"></a>Értékgörbék
Értékgörbe egy kis vonaldiagramot, amely bemutatja egy regisztrációslista-bejegyzés értéke idővel. Vizualizációs rész listájával választhat, amely azt jelzi, hogy egy numerikus oszlopot, vagy egy értékgörbe, amely azt jelzi, hogy az értékét, idővel relatív értékét vízszintes vonal megjelenjen-e.

Az alábbi táblázat az Értékgörbék vonatkozó beállításait ismerteti:

| Beállítás | Leírás |
|:--- |:--- |
| Értékgörbék engedélyezése |Válassza ki ezt a hivatkozást az értékgörbe helyett egy vízszintes sáv megjelenítése. |
| Művelet |Ha Értékgörbék engedélyezve vannak, akkor az értékgörbe értékeinek kiszámítása a listában lévő minden egyes tulajdonság elvégezni a műveletet.<ul><li>Utolsó minta: az idő alatt a sorozat utolsó értékét.</li><li>Maximális száma: A maximális érték az idő alatt az adatsorozathoz.</li><li>Min.: A minimális érték az idő alatt az adatsorozathoz.</li><li>Összeg: Az idő alatt az adatsorozathoz értékeinek összegét.</li><li>Összefoglalás: Ugyanazokkal a `measure` parancsot, mivel a lekérdezés a fejlécben.</li></ul> |

### <a name="thresholds"></a>Küszöbértékek
Küszöbértékek használatával megjelenítheti, hogy egy színes ikon mellett az egyes elemek listáját. Küszöbértékek teszik lehetővé, amely meghaladja az egy adott érték vagy egy adott tartományon belüli elemek gyors láthatja. Például egy elfogadható érték, a sárga, ha az érték, amely figyelmeztet, egy tartományon belül, és a piros elemek zöld ikonnal megjelenítése, ha ez meghaladja a hibát.

Ha engedélyezi a küszöbértékeket egy részére, meg kell adnia egy vagy több küszöbértékeket. Ha egy elem értéke nagyobb, mint a küszöbérték és alacsonyabb, mint a következő küszöbértéket, az adott érték színének szolgál. Ha az elem nagyobb, mint a legmagasabb küszöbértéket, egy másik színét használja. 

Minden egyes küszöbérték beállítása egy küszöbértéket értékkel rendelkezik **alapértelmezett**. Ez az meg van adva, ha nincs más értékek túllépése. Adja hozzá, vagy távolítsa el a küszöbértékek kiválasztásával a **Hozzáadás** (+) vagy **törlése** (x) gombra.

A következő táblázat a küszöbértékek beállításai:

| Beállítás | Leírás |
|:--- |:--- |
| Küszöbértékek engedélyezése |Válassza ki ezt a hivatkozást egy szín ikon megjelenítése a bal oldali részén minden érték. Az ikon azt jelzi, hogy az érték egészségügyi viszonyítva megadott küszöbértékeket. |
| Name (Név) |A küszöbérték neve. |
| Küszöbérték |Az értéke a küszöbérték. Az egyes listaelemek egészségügyi színe a legmagasabb küszöbérték, amely az elem értéke túl van színét értékre van állítva. Ha nincs küszöbértéket, egy alapértelmezett szín használja. |
| Szín |A szín, amely azt jelzi, hogy a küszöbérték. |

## <a name="next-steps"></a>További lépések
* Ismerje meg [naplókereséseket](../../azure-monitor/log-query/log-query-overview.md) Vizualizáció részeiben lekérdezések támogatásához.
