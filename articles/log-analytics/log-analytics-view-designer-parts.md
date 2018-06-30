---
title: Az adatforrásnézet-tervezőből különböző részeinek az Azure Naplóelemzés egy referencia-útmutató |} Microsoft Docs
description: Naplóelemzési adatforrásnézet-tervezőből használatával egyéni nézeteket hozhat létre az Azure portálon, amelyeknél a Naplóelemzési munkaterület különböző adatmegjelenítési. Ez a cikk egy referencia-útmutató az egyéni nézetek elérhető képi megjelenítés összetevőkre vonatkozó beállításokat.
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
ms.component: na
ms.openlocfilehash: b560022f1f727949021a599e1ab342551f64c084
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129132"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>A Naplóelemzési adatforrásnézet-tervezőből képi megjelenítés különböző részeinek használati útmutató
Azure Naplóelemzés adatforrásnézet-tervezőből használatával egyéni nézeteket hozhat létre az Azure portálon, hogy az adatmegjelenítéseket a Naplóelemzési munkaterület különböző. Ez a cikk egy referencia-útmutató az egyéni nézetek elérhető képi megjelenítés összetevőkre vonatkozó beállításokat.

Az adatforrásnézet-tervezőből kapcsolatos további információkért lásd:

* [Megtekintheti a tervező](log-analytics-view-designer.md): egy áttekintést nyújt az adatforrásnézet-tervezőből és eljárások létrehozásának és szerkesztésének egyéni nézetek.
* [Hivatkozás csempe](log-analytics-view-designer-tiles.md): biztosítja a beállítások az egyes elérhető csempe az egyéni nézetekben mutató hivatkozás.


Az adatforrásnézet-tervezőből csempe elérhető típusok az alábbi táblázat ismerteti:

| A nézettípus | Leírás |
|:--- |:--- |
| [A lekérdezések listája](#list-of-queries-part) |A naplófájl-keresési lekérdezések listáját jeleníti meg. Kiválaszthatja, hogy minden egyes lekérdezés az eredmények megtekintése céljából. |
| [Száma és a lista](#number-and-list-part) |A fejléc megjelenítése egyetlen számát jeleníti meg a napló keresési lekérdezés bejegyzések száma. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi. |
| [Két szám és listája](#two-numbers-and-list-part) |A fejléc megjelenítése számát is rögzíti a különálló naplófájl-keresési lekérdezések két szám jeleníti meg. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi. |
| [Fánk és listája](#donut-and-list-part) |A fejléc összegzi a napló lekérdezésben érték oszlop egyetlen számot jeleníti meg. A fánk grafikusan felső három rekord eredményeit jeleníti meg. |
| [Két ütemtervek és listája](#two-timelines-and-list-part) |A fejléc két naplófájl-lekérdezések eredményének oszlopdiagramok, a kihívás, amely egy számot, amely összefoglalja a napló lekérdezésben érték oszlop jeleníti meg az idővel jeleníti meg. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi. |
| [Információ](#information-part) |A fejléc statikus szöveget és egy nem kötelező hivatkozást jeleníti meg. A lista megjeleníti elemek legalább egy olyan statikus cím és a szöveg. |
| [Vonaldiagram, a kihívás, és a listája](#line-chart-callout-and-list-part) |A fejléc egy grafikonon, több adatsorozattal idő és az összesített érték egy kihívás napló lekérdezésből. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi. |
| [Vonaldiagram és listája](#line-chart-and-list-part) |A fejléc egy grafikonon, több adatsorozattal időbeli napló lekérdezésből. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi. |
| [Sor diagramok rész verem](#stack-of-line-charts-part) |Megjeleníti a három különálló vonaldiagramok esetében, több sorozat időbeli napló lekérdezésből. |

A következő szakaszok ismertetik a mozaik típusát és azok tulajdonságait részletesen.

## <a name="list-of-queries-part"></a>Lekérdezések rész listája
A lekérdezések rész listáját napló keresési lekérdezések listáját jeleníti meg. Kiválaszthatja, hogy minden egyes lekérdezés az eredmények megtekintése céljából. A nézet alapértelmezés szerint egyetlen lekérdezést tartalmazza, és kijelölhet **+ lekérdezés** további lekérdezések hozzáadni.

![Lekérdezések nézet listája](media/log-analytics-view-designer/view-list-queries.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Beosztás |A nézet tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Előre kijelölt szűrők |Amikor kiválaszt egy lekérdezést a bal oldali keresőablak felvenni tulajdonságok vesszővel tagolt listája. |
| Megjelenítési mód |A kezdeti nézet jelenik meg, ha a lekérdezés van kiválasztva. Bármely elérhető nézeteket választhatja ki a lekérdezés telepítése után. |
| **Lekérdezések** | |
| Keresési lekérdezés |A lekérdezés futtatásához. |
| Felhasználóbarát név | A leíró nevet, amely akkor jelenik meg. |

## <a name="number-and-list-part"></a>Száma és a lista része
A fejléc megjelenítése egyetlen számát jeleníti meg a napló keresési lekérdezés bejegyzések száma. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi.

![Lekérdezések nézet listája](media/log-analytics-view-designer/view-number-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A nézet tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Ikon |A bináris fájl, amely mellett a fejlécben eredménye megjelenik. |
| Ikon használata |Kattintson erre a hivatkozásra a ikon megjelenítéséhez. |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatásához be az igazítását. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Kattintson a navigációs | A fejléc kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát. Az eredmények első tíz rekordját első két tulajdonságai jelennek meg. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. Sávok automatikusan létrejönnek a numerikus oszlopot relatív értékének alapuló.<br><br>Használja a `Sort` parancsot a a listában szereplő bejegyzések rendezése a lekérdezésben. A lekérdezés futtatásához és az összes rekordot ad vissza, jelölje ki **láthatja az összes**. |
| Gráf elrejtése |Kattintson erre a hivatkozásra letiltja a diagram jobb oldalán a numerikus oszlopot. |
| Értékgörbéket engedélyezése |Válassza ki a hivatkozás értékgörbe vízszintes vonal helyett. További információkért lásd: [általános beállítások](#sparklines). |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Név-érték elválasztó |A határolójel a text tulajdonságához értelmezhető több érték is. További információkért lásd: [általános beállítások](#sparklines). |
| Kattintson a navigációs | A lista egy elemére kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson erre a hivatkozásra küszöbértékek engedélyezéséhez. További információkért lásd: [általános beállítások](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Két szám és a lista része
A fejléc van két szám, amely megjeleníti a különálló naplófájl-keresési lekérdezések a rekordok számát. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi.

![Két szám & a listanézet](media/log-analytics-view-designer/view-two-numbers-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A nézet tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Ikon |A bináris fájl, amely mellett a fejlécben eredménye megjelenik. |
| Ikon használata |Kattintson erre a hivatkozásra a ikon megjelenítéséhez. |
| **Cím navigációs** | |
| Kattintson a navigációs | A fejléc kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatásához be az igazítását. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát. Az eredmények első tíz rekordját első két tulajdonságai jelennek meg. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. Sávok automatikusan numerikus oszlop relatív érték alapján hozzák létre.<br><br>Használja a `Sort` parancsot a a listában szereplő bejegyzések rendezése a lekérdezésben. A lekérdezés futtatásához és az összes rekordot ad vissza, jelölje ki **láthatja az összes**. |
| Gráf elrejtése |Kattintson erre a hivatkozásra letiltja a diagram jobb oldalán a numerikus oszlopot. |
| Értékgörbéket engedélyezése |Válassza ki a hivatkozás értékgörbe vízszintes vonal helyett. További információkért lásd: [általános beállítások](#sparklines). |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [általános beállítások](#sparklines). |
| Név-érték elválasztó |A határolójel a text tulajdonságához értelmezhető több érték is. További információkért lásd: [általános beállítások](#sparklines). |
| Kattintson a navigációs | A lista egy elemére kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson erre a hivatkozásra küszöbértékek engedélyezéséhez. További információkért lásd: [általános beállítások](#thresholds). |

## <a name="donut-and-list-part"></a>Fánk és lista része
A fejléc összegzi a napló lekérdezésben érték oszlop egyetlen számot jeleníti meg. A fánk grafikusan felső három rekord eredményeit jeleníti meg.

![Fánk és lista megtekintése](media/log-analytics-view-designer/view-donut-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Ikon |A bináris fájl, amely mellett a fejlécben eredménye megjelenik. |
| Ikon használata |Kattintson erre a hivatkozásra a ikon megjelenítéséhez. |
| **Fejléc** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején címe alatt megjelenő szöveg. |
| **Fánk** | |
| Lekérdezés |A lekérdezés a fánk fussanak. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. |
| Kattintson a navigációs | A fejléc kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **Fánk** |**> Center** |
| Szöveg |A szöveg, amely alatt a fánk értéket jelenik meg. |
| Művelet |A value tulajdonság egy értékként legyen megadva összefoglalásképpen végre a műveletet.<ul><li>Sum: Hozzáadja az értékeket az összes rekord.</li><li>Százalékos: Levő értékek alapján visszaadott rekordok aránya **center művelet során használt értékek eredménye** a teljes rekordok a lekérdezésben.</li></ul> |
| A fánkdiagram közepén megjelenített eredményértékek |Ha kijelöl egy vagy több érték hozzáadása a plusz jelre (+). A lekérdezés eredményeit rögzíti a megadott tulajdonságértéket korlátozódnak. Ha nincs érték ad hozzá, a lekérdezés szereplő összes rekordok. |
| **További beállítások** |**> Színek** |
| Szín 1<br>Szín 2<br>Szín 3 |Az egyes fánk a megjelenített érték színének kiválasztása. |
| **További beállítások** |**> Speciális szín leképezése** |
| Mezőérték |Írja be azt egy másik színt formátumban jeleníti meg, ha a fánk megtalálható mező. |
| Szín |Jelölje ki az egyedi mező színét. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Gráf elrejtése |Kattintson erre a hivatkozásra letiltja a diagram jobb oldalán a numerikus oszlopot. |
| Értékgörbéket engedélyezése |Válassza ki a hivatkozás értékgörbe vízszintes vonal helyett. További információkért lásd: [általános beállítások](#sparklines). |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [általános beállítások](#sparklines). |
| Név-érték elválasztó |A határolójel a text tulajdonságához értelmezhető több érték is. További információkért lásd: [általános beállítások](#sparklines). |
| Kattintson a navigációs | A lista egy elemére kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson erre a hivatkozásra küszöbértékek engedélyezéséhez. További információkért lásd: [általános beállítások](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Két ütemtervek és lista része
A fejléc két naplófájl-lekérdezések eredményének oszlopdiagramok, a kihívás, amely egy számot, amely összefoglalja a napló lekérdezésben érték oszlop jeleníti meg az idővel jeleníti meg. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi.

![Két ütemtervek és lista megtekintése](media/log-analytics-view-designer/view-two-timelines-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Ikon |A bináris fájl, amely mellett a fejlécben eredménye megjelenik. |
| Ikon használata |Kattintson erre a hivatkozásra a ikon megjelenítéséhez. |
| **Cím navigációs** | |
| Kattintson a navigációs | A fejléc kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **Először diagram<br>második diagram** | |
| Jelmagyarázat |A szöveg, a kihívás az első adatsorozathoz jelenik meg. |
| Szín |Az adatsorozatban szereplő oszlopok használandó szín. |
| Lekérdezés |A lekérdezés futtatásához az első adatsorozathoz. A bejegyzések száma az egyes időtartam alatt a diagram oszlopokat jelképezi. |
| Művelet |A value tulajdonság, hogy összesítse a egy értékként legyen megadva a kihívás az elvégzendő műveletet.<ul><li>Sum: Minden rekordokban levő értékek összegét.</li><li>Átlagos: Minden rekordokban levő értékek átlaga.</li><li>Legutóbbi minta: az utolsó időköze, amely megtalálható a diagram az értéket.</li><li>Először a mintavételhez: az értéket az első intervallum, amely megtalálható a diagramon.</li><li>Számláló: A lekérdezés által visszaadott összes rekord száma.</li></ul> |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Gráf elrejtése |Kattintson erre a hivatkozásra letiltja a diagram jobb oldalán a numerikus oszlopot. |
| Értékgörbéket engedélyezése |Válassza ki a hivatkozás értékgörbe vízszintes vonal helyett. További információkért lásd: [általános beállítások](#sparklines). |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [általános beállítások](#sparklines). |
| Kattintson a navigációs | A lista egy elemére kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson erre a hivatkozásra küszöbértékek engedélyezéséhez. További információkért lásd: [általános beállítások](#thresholds). |

## <a name="information-part"></a>Információkat
A fejléc statikus szöveget és egy nem kötelező hivatkozást jeleníti meg. A lista megjeleníti elemek legalább egy olyan statikus cím és a szöveg.

![Információk megtekintése](media/log-analytics-view-designer/view-information.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Szín |A fejléc háttérszínét. |
| **Fejléc** | |
| Kép |A képfájl a fejlécben megjelenő. |
| Címke |A fejlécben megjelenő szöveget. |
| **Fejléc** |**> Link** |
| Címke |A hivatkozást a vágólapra. |
| URL-cím |A hivatkozás URL-címét. |
| **Elemek** | |
| Beosztás |A cím az egyes elemek megjelenített szöveg. |
| Tartalom |Az egyes elemekhez tartozó megjelenített szöveg. |

## <a name="line-chart-callout-and-list-part"></a>Vonaldiagram, a kihívás, és a lista részére
A fejléc egy grafikonon napló lekérdezésből több adatsorozattal időt és az összesített érték egy kihívás keresztül. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi.

![Vonaldiagram, a kihívás, és a listanézet](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Ikon |A bináris fájl, amely mellett a fejlécben eredménye megjelenik. |
| Ikon használata |Kattintson erre a hivatkozásra a ikon megjelenítéséhez. |
| **Fejléc** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején címe alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés a vonaldiagram fussanak. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. Ez a lekérdezés szokásos használja a *mérték* , hogy összesítse a eredmények kulcsszó. Ha a lekérdezés használ a *időköz* kulcsszóval, a diagram x tengelyéhez használja az adott időszakban. Ha a lekérdezés nem tartalmazza a *időköz* kulcsszót, az x tengely használ óránként történik. |
| Kattintson a navigációs | A fejléc kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **Vonaldiagram** |**> Kihívás** |
| Kihívás cím |A képfelirat érték felett megjelenő szöveg. |
| Adatsorozat neve |A képfelirat érték használandó adatsorozat tulajdonság értéke. Adatsorozatok valósul meg, ha a lekérdezés összes rekordot szolgálnak. |
| Művelet |A value tulajdonság, hogy összesítse a egy értékként legyen megadva a kihívás az elvégzendő műveletet.<ul><li>Átlagos: Minden rekordokban levő értékek átlaga.</li><li>Számláló: A lekérdezés által visszaadott összes rekord száma.</li><li>Legutóbbi minta: az utolsó időköze, amely megtalálható a diagram az értéket.</li><li>Maximális: A maximális értéket a diagramban szereplő időszakok.</li><li>Minimális: A minimális értéke az a diagramban szereplő időszakok.</li><li>Sum: Minden rekordokban levő értékek összegét.</li></ul> |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ki a tengely logaritmikus skála használata ezen a hivatkozáson. |
| Egység |A lekérdezés által visszaadott értékek a egységeket adjon meg. Az adatok megjelenítési adatfelirat, amelyek jelzik az értéktípusokat és opcionálisan az az értékeket átalakítani. A *egység* típusa határozza meg az egység a kategóriát, és határozza meg a rendelkezésre álló *aktuális egység* írja be az értékeket. Ha válasszon ki egy értéket *átalakítása*, számértékek konvertálja a *aktuális egység* típus a *átalakítása* típusa. |
| Egyéni felirat |A felirat melletti tengely megjelenő szöveg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Gráf elrejtése |Kattintson erre a hivatkozásra letiltja a diagram jobb oldalán a numerikus oszlopot. |
| Értékgörbéket engedélyezése |Válassza ki a hivatkozás értékgörbe vízszintes vonal helyett. További információkért lásd: [általános beállítások](#sparklines). |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [általános beállítások](#sparklines). |
| Név-érték elválasztó |A határolójel a text tulajdonságához értelmezhető több érték is. További információkért lásd: [általános beállítások](#sparklines). |
| Kattintson a navigációs | A lista egy elemére kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson erre a hivatkozásra küszöbértékek engedélyezéséhez. További információkért lásd: [általános beállítások](#thresholds). |

## <a name="line-chart-and-list-part"></a>Sor diagram és a lista része
A fejléc egy grafikonon napló lekérdezésből több adatsorozattal adott idő alatt. A lista megjeleníti a felső tíz egy lekérdezés eredményeként előálló, a diagramját, amelyek egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékét jelzi.

![Sor diagram és a lista megtekintése](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Ikon |A bináris fájl, amely mellett a fejlécben eredménye megjelenik. |
| Ikon használata |Kattintson erre a hivatkozásra a ikon megjelenítéséhez. |
| **Fejléc** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején címe alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés a vonaldiagram fussanak. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. Ez a lekérdezés szokásos használja a *mérték* , hogy összesítse a eredmények kulcsszó. Ha a lekérdezés használ a *időköz* kulcsszóval, a diagram x tengelyéhez használja az adott időszakban. Ha a lekérdezés nem tartalmazza a *időköz* kulcsszót, az x tengely használ óránként történik. |
| Kattintson a navigációs | A fejléc kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **Vonaldiagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ki a tengely logaritmikus skála használata ezen a hivatkozáson. |
| Egység |A lekérdezés által visszaadott értékek a egységeket adjon meg. Az adatok megjelenítési adatfelirat, amelyek jelzik az értéktípusokat és opcionálisan az az értékeket átalakítani. A *egység* típusa határozza meg az egység a kategóriát, és határozza meg a rendelkezésre álló *aktuális egység* írja be az értékeket. Ha válasszon ki egy értéket *átalakítása*, számértékek konvertálja a *aktuális egység* típus a *átalakítása* típusa. |
| Egyéni felirat |A felirat melletti tengely megjelenő szöveg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát. A lekérdezés által visszaadott rekordok száma jelenik meg. |
| Gráf elrejtése |Kattintson erre a hivatkozásra letiltja a diagram jobb oldalán a numerikus oszlopot. |
| Értékgörbéket engedélyezése |Válassza ki a hivatkozás értékgörbe vízszintes vonal helyett. További információkért lásd: [általános beállítások](#sparklines). |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet. További információkért lásd: [általános beállítások](#sparklines). |
| Név-érték elválasztó |A határolójel a text tulajdonságához értelmezhető több érték is. További információkért lásd: [általános beállítások](#sparklines). |
| Kattintson a navigációs | A lista egy elemére kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlop tetején megjelenő szöveg. |
| Érték |A második oszlop tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Kattintson erre a hivatkozásra küszöbértékek engedélyezéséhez. További információkért lásd: [általános beállítások](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Sor diagramok rész verem
A vonaldiagram navigációs veremben három különálló vonaldiagramok esetében, több sorozat idővel napló lekérdezésből jeleníti meg, ahogy az itt látható:

![Vonaldiagram-halom](media/log-analytics-view-designer/view-stack-line-charts.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Jelölje ki erre a hivatkozásra kattintva hozzon létre egy új csoportot a nézetben, az aktuális nézet kezdve. |
| Ikon |A bináris fájl, amely mellett a fejlécben eredménye megjelenik. |
| **Diagram – 1<br>2 diagram<br>3 diagram** |**> Header** |
| Beosztás |A diagram tetején megjelenő szöveg. |
| Alcím |A diagram tetején a cím alatt megjelenő szöveg. |
| **Diagram – 1<br>2 diagram<br>3 diagram** |**Vonaldiagram** |
| Lekérdezés |A lekérdezés a vonaldiagram fussanak. Az első tulajdonság egy szöveges értéket, és a második tulajdonsága egy numerikus érték. Ez a lekérdezés szokásos használja a *mérték* , hogy összesítse a eredmények kulcsszó. Ha a lekérdezés használ a *időköz* kulcsszóval, a diagram x tengelyéhez használja az adott időszakban. Ha a lekérdezés nem tartalmazza a *időköz* kulcsszót, az x tengely használ óránként történik. |
| Kattintson a navigációs | A fejléc kattintva végrehajtott műveletet.  További információkért lásd: [általános beállítások](#click-through-navigation). |
| **A diagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ki a tengely logaritmikus skála használata ezen a hivatkozáson. |
| Egység |A lekérdezés által visszaadott értékek a egységeket adjon meg. Az adatok megjelenítési adatfelirat, amelyek jelzik az értéktípusokat és opcionálisan az az értékeket átalakítani. A *egység* típusa határozza meg az egység a kategóriát, és határozza meg a rendelkezésre álló *aktuális egység* írja be az értékeket. Ha válasszon ki egy értéket *átalakítása*, számértékek konvertálja a *aktuális egység* típus a *átalakítása* típusa. |
| Egyéni felirat |A felirat melletti tengely megjelenő szöveg a *egység* típusa. Ha nincs címke van megadva, csak a *egység* típus jelenik meg. |

## <a name="common-settings"></a>Általános beállítások
A következő szakaszok ismertetik a képi megjelenítés több részből vonatkozó beállításokat.

### <a name="name-value-separator"></a>Név-érték elválasztó
A név-érték elválasztó a határolójel a lista lekérdezésből text tulajdonságához értelmezhető több értéket is. Elválasztó megadása esetén megadhatja az egyes mezők, szóközzel elválasztva a azonos elválasztó, a neveket a **neve** mezőbe.

Vegyük példaként a tulajdonságot, *hely* például része, amely értékek *Redmond-összeállító 41* és *Bellevue-összeállító 12*. A név-érték elválasztó kötőjellel (-) is megadhat, és *város-összeállító* nevét. Ezt a módszert használja az egyes értékek elemez nevű két tulajdonság be *Város* és *épület*.

### <a name="click-through-navigation"></a>Kattintson a navigációs
Kattintson a navigációs határozza meg, mit kell tenni, a fejléc vagy listaelem nézetben gombjára kattintva.  Megnyílik a lekérdezés vagy a [naplófájl-keresési portál](log-analytics-log-search-portals.md#log-search) vagy egy másik nézetre indítsa el.

Az alábbi táblázat bemutatja a kattintások navigációs beállításait.

| Beállítás           | Leírás |
|:--|:--|
| Naplókeresés (automatikus) | Amikor kiválaszt egy Fejlécelem futtatásához keresési napló.  Ez az elem alapuló ugyanazon napló keresés.
| Naplók keresése        | Napló keresési fut, amikor kiválaszt egy elemet a listában.  Írja be a lekérdezést a **navigációs lekérdezés** mezőbe.   Használjon *{kijelölt elem}* a cikk a felhasználó által kiválasztott szintaxisát tartalmazza.  Például, ha a lekérdezés tartalmaz egy nevű oszlopot *számítógép* , és a navigációs lekérdezés *{kijelölt elem}*, többek között a lekérdezés *számítógép = "Sajátgép"* fut, amikor kiválaszt egy számítógép. Ha a navigációs lekérdezés *típus = {kijelölt elem} esemény*, a lekérdezés *típus = esemény számítógép = "Sajátgép"* futtatása. |
| Nézet              | Nézet megnyitásához, amikor kiválaszt egy Fejlécelem vagy elem listáját.  Válassza ki a nézet nevét a munkaterületén a **nézetnév** mezőbe. |



### <a name="sparklines"></a>Értékgörbéket
Értékgörbe kis vonaldiagramról, amely bemutatja a lista bejegyzés adott idő alatt. A képi megjelenítés részei a listájával kiválaszthatja, amely megadja, hogy a relatív értéket egy numerikus oszlopot, vagy egy értékgörbe, amely megadja, hogy annak értéke időbeli vízszintes vonal megjelenjen-e.

Az alábbi táblázat bemutatja a értékgörbéket beállításait:

| Beállítás | Leírás |
|:--- |:--- |
| Értékgörbék engedélyezése |Válassza ki a hivatkozás értékgörbe vízszintes vonal helyett. |
| Művelet |Ha értékgörbéket engedélyezve vannak, akkor minden egyes tulajdonsága az értékgörbe az értékek listában végre a műveletet.<ul><li>Legutóbbi minta: az utolsó olyan értéket az időtartam alatt az adatsorozathoz.</li><li>Maximális: Alatt az időtartam alatt a sorozat maximális értéke.</li><li>Minimum: Az adatsorozat alatt az időtartam minimális értéke.</li><li>Sum: Alatt az időtartam alatt a adatsor összege.</li><li>Összefoglalás: Használja ugyanazt `measure` parancsot a lekérdezés a fejlécben.</li></ul> |

### <a name="thresholds"></a>Küszöbértékek
Küszöbértékek használatával jelenítheti meg egy színes ikon minden elem mellett a listán. Küszöbértékek adjon egy rövid visual jelzi, hogy lehet egy adott értéket, vagy egy adott tartományba esnek elemek. Például elemek egy elfogadható érték, a sárga, ha az érték, amely figyelmeztet, egy tartományon belül, és a piros zöld ikonnal megjelenítése, ha az érték meghaladja a hibaérték.

Ha engedélyezi a küszöbértékeket egy részére, meg kell adnia egy vagy több küszöbértékeket. Ha egy elem értéke nagyobb, mint a küszöbérték és alacsonyabb, mint a következő küszöbértéket, az adott érték színének szolgál. Ha az elem nagyobb, mint a legmagasabb küszöbértéket, egy másik színt használja. 

Minden egyes küszöbérték beállítása rendelkezik egy küszöbértéket értékkel rendelkező **alapértelmezett**. Ez egy, a színt, amely állítja be, ha nincs más értékek túllépése. Adja hozzá, vagy távolítsa el a küszöbértékek kiválasztásával a **Hozzáadás** (+) vagy **törlése** (x) gombra.

Az alábbi táblázat bemutatja a küszöbértékek beállításait:

| Beállítás | Leírás |
|:--- |:--- |
| Küszöbértékek engedélyezése |Kattintson erre a hivatkozásra egy szín ikon megjelenítése a bal oldali minden egyes érték. Ikon azt jelzi, hogy az érték állapotfigyelő megadott küszöbértékek viszonyítva. |
| Name (Név) |A küszöbérték neve. |
| Küszöbérték |Értéke a küszöbérték. Mindegyik listaelemhez állapotfigyelő színét értéke a legmagasabb küszöbérték, amely a cikk érték túl van színét. Ha nincs küszöbértéket, egy alapértelmezett színt használja. |
| Szín |A színt, amely azt jelzi, a küszöbérték. |

## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) támogatja a képi megjelenítés részből áll.
