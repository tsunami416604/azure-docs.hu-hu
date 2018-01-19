---
title: "Hivatkozás részt az Azure Naplóelemzés Nézettervező |} Microsoft Docs"
description: "A Naplóelemzési Nézettervező egyéni nézetek létrehozása az Azure portálon, amelyek tartalmazzák az adatok a Naplóelemzési munkaterület különböző képi teszi lehetővé. Ez a cikk ismerteti a beállítások az egyes a képi megjelenítés részeket is az egyéni nézetekben használható hivatkozást."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 1fdfb237fcea6a10d38f3be8524ea5fbcf1f3728
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="log-analytics-view-designer-visualization-part-reference"></a>Napló Analytics Nézettervező képi megjelenítés rész referencia
Az adatforrásnézet-tervezőből Naplóelemzési az egyéni nézetek létrehozása az Azure portálon másik képi megjelenítéseket adatok Naplóelemzési munkaterületet tartalmazó teszi lehetővé. Ez a cikk ismerteti a beállítások az egyes a képi megjelenítés részeket is az egyéni nézetekben használható hivatkozást.

Az adatforrásnézet-tervezőből elérhető további cikkeit a következők:

* [Megtekintheti a tervező](log-analytics-view-designer.md) -adatforrásnézet-tervezőből és létrehozásának és szerkesztésének egyéni nézetek eljárások áttekintését.
* [Hivatkozás csempe](log-analytics-view-designer-tiles.md) -hivatkozás a beállítások az egyes a csempék, hogy az egyéni nézetek használható.

>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), akkor minden nézetben lekérdezések úgy kell megírni, a [új lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856078).  A munkaterület verziófrissítése előtt készült nézetekkel lesz automtically alakítja át.

A következő táblázat ismerteti a különböző típusú csempe érhető el az adatforrásnézet-tervezőből.  Az alábbi részek ismertetik a csempe típusonként részletek és azok tulajdonságait.

| A nézettípus | Leírás |
|:--- |:--- |
| [A lekérdezések listája](#list-of-queries-part) |A naplófájl-keresési lekérdezések listáját jeleníti meg.  A felhasználó rákattinthat minden egyes lekérdezés az eredmények megtekintése céljából. |
| [Lista és száma](#number-amp-list-part) |Fejléce egyetlen számú ábrázoló bejegyzések száma a napló keresési lekérdezés rendelkezik.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti. |
| [Két lista és számok](#two-numbers-amp-list-part) |Fejléce rendelkezik két szám megjelenítő érkező saját naplófájlt keresési lekérdezések száma.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti. |
| [Fánk & lista](#donut-amp-list-part) |Fejléc egy egyetlen napló lekérdezésben érték oszlop összesített számát jeleníti meg.  A fánk grafikusan felső három rekord eredményeit jeleníti meg. |
| [Két lista és ütemtervek](#two-timelines-amp-list-part) |Egy kihívás, a napló lekérdezésben érték oszlop összesített egyetlen több megjelenítése a fejléc megjelenítése két naplófájl-lekérdezések eredményének oszlopdiagramok idővel.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti. |
| [Információ](#information-part) |Fejléc statikus szöveget és egy nem kötelező hivatkozást jeleníti meg.  Lista egy vagy több elem statikus szöveget és címét jeleníti meg. |
| [Lista, és az vonaldiagram, a kihívás](#line-chart-callout-amp-list-part) |Fejléc egy grafikonon napló lekérdezésből több adatsorozattal időt és az összesített érték egy kihívás keresztül.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti. |
| [Lista és a vonaldiagram](#line-chart-amp-list-part) |Fejléc egy grafikonon napló lekérdezésből több adatsorozattal adott idő alatt.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti. |
| [Sor diagramok rész verem](#stack-of-line-charts-part) |Megjeleníti a napló lekérdezésből több sorozat három különálló vonaldiagramok adott idő alatt. |

## <a name="list-of-queries-part"></a>Lekérdezések rész listája
A naplófájl-keresési lekérdezések listáját jeleníti meg.  A felhasználó rákattinthat minden egyes lekérdezés az eredmények megtekintése céljából.  A nézet alapértelmezés szerint egyetlen lekérdezést tartalmazza, és kattintson **+ lekérdezés** további lekérdezések hozzáadni.

![Lekérdezések nézet listája](media/log-analytics-view-designer/view-list-queries.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Beosztás |A nézet tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Az előre kijelölt szűrők |Vesszővel tagolt lista a tulajdonságok közé tartoznak a szűrő bal oldali ablaktáblán, amikor a felhasználó kijelöl egy lekérdezést. |
| Leképezési módban |Kezdeti nézet jelenik meg, ha a lekérdezés van kiválasztva.  A felhasználó kiválaszthatja a rendelkezésre álló nézetek után nyissa meg a lekérdezést. |
| **Lekérdezések** | |
| Keresési lekérdezés |A lekérdezés futtatásához. |
| Felhasználóbarát név |A lekérdezés a felhasználó számára megjelenített leíró neve. |

## <a name="number--list-part"></a>Lista és a szám része
Fejléce egyetlen számú ábrázoló bejegyzések száma a napló keresési lekérdezés rendelkezik.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti.

![Lekérdezések nézet listája](media/log-analytics-view-designer/view-number-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A nézet tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Ikon |Mellett az eredmény a fejlécében megjelenítendő kép fájlt. |
| Ikon használata |Válassza ki, szeretné, hogy a ikon megjelenítése. |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatásához be az igazítását.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát.  Az első két tulajdonságait az első tíz rögzíti az eredmények között megjelenik.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie.  Sávok automatikusan numerikus oszlop relatív érték alapján hozzák létre.<br><br>A lekérdezés rendezési paranccsal rendezheti a rekordokat a listában.  A felhasználó a lekérdezés futtatásához és az összes rekordot ad vissza az összes lásd kattintással. |
| Graph elrejtése |Válassza ki a diagram jobb oldalán a numerikus oszlopot letiltása. |
| Értékgörbéket engedélyezése |Válassza ki a értékgörbe helyett vízszintes sáv megjelenítése.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Név és érték elválasztó |Elválasztó karaktert, ha text tulajdonságához értelmezhető több értéket szeretne.  Lásd: [általános beállítások](#name-value-separator) részleteiről. |
| Navigációs lekérdezés |A lekérdezés a futtatást, ha a felhasználó kiválaszt egy elemet a listában.  Lásd: [általános beállítások](#navigation-query) részleteiről. |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlopban a lista tetején megjelenő szöveg. |
| Érték |A második oszlopban a lista tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ki ahhoz, hogy küszöbértékeket.  Lásd: [általános beállítások](#thresholds) részleteiről. |

## <a name="two-numbers--list-part"></a>Két szám & a lista része
Fejléce rendelkezik két szám megjelenítő érkező saját naplófájlt keresési lekérdezések száma.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti.

![Két szám & a listanézet](media/log-analytics-view-designer/view-two-numbers-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A nézet tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Ikon |Mellett az eredmény a fejlécében megjelenítendő kép fájlt. |
| Ikon használata |Válassza ki, szeretné, hogy a ikon megjelenítése. |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatásához be az igazítását.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát.  Az első két tulajdonságait az első tíz rögzíti az eredmények között megjelenik.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie.  Sávok automatikusan numerikus oszlop relatív érték alapján hozzák létre.<br><br>A lekérdezés rendezési paranccsal rendezheti a rekordokat a listában.  A felhasználó a lekérdezés futtatásához és az összes rekordot ad vissza az összes lásd kattintással. |
| Graph elrejtése |Válassza ki a diagram jobb oldalán a numerikus oszlopot letiltása. |
| Értékgörbéket engedélyezése |Válassza ki a értékgörbe helyett vízszintes sáv megjelenítése.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Név és érték elválasztó |Elválasztó karaktert, ha text tulajdonságához értelmezhető több értéket szeretne.  Lásd: [általános beállítások](#name-value-separator) részleteiről. |
| Navigációs lekérdezés |A lekérdezés a futtatást, ha a felhasználó kiválaszt egy elemet a listában.  Lásd: [általános beállítások](#navigation-query) részleteiről. |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlopban a lista tetején megjelenő szöveg. |
| Érték |A második oszlopban a lista tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ki ahhoz, hogy küszöbértékeket.  Lásd: [általános beállítások](#thresholds) részleteiről. |

## <a name="donut--list-part"></a>Lista és fánk része
Fejléc egy egyetlen napló lekérdezésben érték oszlop összesített számát jeleníti meg.  A fánk grafikusan felső három rekord eredményeit jeleníti meg.

![Fánk & lista megtekintése](media/log-analytics-view-designer/view-donut-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Ikon |Mellett az eredmény a fejlécében megjelenítendő kép fájlt. |
| Ikon használata |Válassza ki, szeretné, hogy a ikon megjelenítése. |
| **Header** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején a címben megjelenítendő szöveg. |
| **Fánk** | |
| Lekérdezés |Futtassa a fánk a lekérdezést.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie. |
| **Fánk** |**> Center** |
| Szöveg |A fánk értéket alatt megjelenő szöveg. |
| Művelet |A value tulajdonság egyetlen értéket az összesítendő végrehajtani a műveletet.<br><br>-Összeg: Vegye fel az összes rekord értékeit.<br>-Százalékos: Százalékos értékének által visszaadott rekord **center művelet során használt értékek eredménye** a teljes rekordok a lekérdezésben. |
| Center művelet során használt eredményt értékek |Opcionálisan kattintson a plusz jelre hozzáadása egy vagy több értéket.  A lekérdezés eredményeit rögzíti a megadott tulajdonságértéket korlátozódik.  Ha nincs érték ad hozzá, majd az összes rekord szerepelnek a lekérdezés. |
| **További beállítások** |**> Színek** |
| Szín 1<br>Szín 2<br>Szín 3 |Minden, a fánk a megjelenített értékek színének kiválasztása. |
| **További beállítások** |**> Speciális szín leképezése** |
| Mezőérték |Írja be azt egy másik színt formátumban jeleníti meg, ha a fánk megtalálható mező. |
| Szín |Jelölje ki az egyedi mező színét. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| Graph elrejtése |Válassza ki a diagram jobb oldalán a numerikus oszlopot letiltása. |
| Értékgörbéket engedélyezése |Válassza ki a értékgörbe helyett vízszintes sáv megjelenítése.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Név és érték elválasztó |Elválasztó karaktert, ha text tulajdonságához értelmezhető több értéket szeretne.  Lásd: [általános beállítások](#name-value-separator) részleteiről. |
| Navigációs lekérdezés |A lekérdezés a futtatást, ha a felhasználó kiválaszt egy elemet a listában.  Lásd: [általános beállítások](#navigation-query) részleteiről. |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlopban a lista tetején megjelenő szöveg. |
| Érték |A második oszlopban a lista tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ki ahhoz, hogy küszöbértékeket.  Lásd: [általános beállítások](#thresholds) részleteiről. |

## <a name="two-timelines--list-part"></a>Két lista és ütemtervek része
Egy kihívás, a napló lekérdezésben érték oszlop összesített egyetlen több megjelenítése a fejléc megjelenítése két naplófájl-lekérdezések eredményének oszlopdiagramok idővel.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti.

![Két ütemtervek & lista megtekintése](media/log-analytics-view-designer/view-two-timelines-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Ikon |Mellett az eredmény a fejlécében megjelenítendő kép fájlt. |
| Ikon használata |Válassza ki, szeretné, hogy a ikon megjelenítése. |
| **Először diagram<br>második diagram** | |
| Jelmagyarázat |Az első adatsorozathoz kihívás alatt megjelenő szöveg. |
| Szín |Az adatsorozatban szereplő oszlopok használandó szín. |
| Lekérdezés |A lekérdezés futtatásához az első adatsorozathoz.  A diagram oszlopokat meghatározott keresztül minden időközhöz rekordok száma. |
| Művelet |A value tulajdonság egyetlen értéket a kihívás az összesítendő végrehajtani a műveletet.<br><br>-Összeg: Az értéket az összes rekord összege.<br>-Átlagos: Az értéket az összes rekord átlaga.<br>-Utolsó minta: Az utolsó időköze a diagramban szereplő értéket.<br>-Első minta: Az első intervallum a diagramban szereplő értéket.<br>-Szám: A lekérdezés által visszaadott összes rekord száma. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| Graph elrejtése |Válassza ki a diagram jobb oldalán a numerikus oszlopot letiltása. |
| Értékgörbéket engedélyezése |Válassza ki a értékgörbe helyett vízszintes sáv megjelenítése.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Navigációs lekérdezés |A lekérdezés a futtatást, ha a felhasználó kiválaszt egy elemet a listában.  Lásd: [általános beállítások](#navigation-query) részleteiről. |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlopban a lista tetején megjelenő szöveg. |
| Érték |A második oszlopban a lista tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ki ahhoz, hogy küszöbértékeket.  Lásd: [általános beállítások](#thresholds) részleteiről. |

## <a name="information-part"></a>Információkat
Fejléc statikus szöveget és egy nem kötelező hivatkozást jeleníti meg.  Lista egy vagy több elem statikus szöveget és címét jeleníti meg.

![Információk megtekintése](media/log-analytics-view-designer/view-information.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Szín |A fejléc háttérszínét. |
| **Header** | |
| Kép |A fejlécében megjelenítendő kép fájlt. |
| Címke |A fejlécben megjelenő szöveg. |
| **Header** |**> Link** |
| Címke |Hivatkozás szövege. |
| URL-cím |Hivatkozás URL-címe. |
| **Elemek** | |
| Beosztás |Az egyes elemek címben megjelenítendő szöveg. |
| Tartalom |Minden elemhez megjelenő szöveg. |

## <a name="line-chart-callout--list-part"></a>Vonaldiagram, kihívás & lista része
Fejléc egy grafikonon napló lekérdezésből több adatsorozattal időt és az összesített érték egy kihívás keresztül.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti.

![Vonaldiagram, kihívás & listanézet](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Ikon |Mellett az eredmény a fejlécében megjelenítendő kép fájlt. |
| Ikon használata |Válassza ki, szeretné, hogy a ikon megjelenítése. |
| **Header** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején a címben megjelenítendő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés a vonaldiagram fussanak.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie.  Ez általában az a lekérdezés, amely használja a **mérték** , hogy összesítse a eredmények kulcsszó.  Ha a lekérdezés használ a **időköz** kulcsszót, majd az x tengely a diagram fogja használni az adott időszakban.  Ha a lekérdezés nem tartalmazza a **időköz** kulcsszót, majd óránként intervallumok x tengelyéhez szolgálnak. |
| **Vonaldiagram** |**> Kihívás** |
| Kihívás cím |A képfelirat érték felett megjelenő szöveg. |
| Az adatsorozat neve |A képfelirat érték használandó adatsorozat tulajdonság értéke.  Adatsorozatok valósul meg, ha a lekérdezés összes rekordot szolgálnak. |
| Művelet |A value tulajdonság egyetlen értéket a kihívás az összesítendő végrehajtani a műveletet.<br><br>-Átlagos: Az értéket az összes rekord átlaga.<br>-A lekérdezés által visszaadott összes rekord száma száma.<br>-Utolsó minta: Az utolsó időköze a diagramban szereplő értéket.<br>-Maximális: A diagramban szereplő időszakok közül a maximális érték.<br>-Min: A diagramban szereplő időszakok közül a minimális érték.<br>-Összeg: Az értéket az összes rekord összege. |
| **Vonaldiagram** |**> Y tengely** |
| A Logaritmikus skála használata |A Logaritmikus skála tengely használata mellett dönt. |
| egység |Adja meg a lekérdezés által visszaadott értékek használt mértékegységet.  Ezt az információt a diagram, amely jelzi, az értéktípusokat és opcionálisan az értékek alakításának feliratok megjelenítéséhez használatos.  Egységtípus egység kategória határozza meg, és határozza meg az elérhető aktuális egységtípus értékek.  Ha az érték átalakítása, majd a numerikus érték az aktuális egységtípus alakulnak típusúra átalakítása. |
| Egyéni felirat |Az Y tengely egységtípus felirat mellett megjelenő szöveg.  Ha nincs címke van megadva, akkor csak a egységtípus jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| Graph elrejtése |Válassza ki a diagram jobb oldalán a numerikus oszlopot letiltása. |
| Értékgörbéket engedélyezése |Válassza ki a értékgörbe helyett vízszintes sáv megjelenítése.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Név és érték elválasztó |Elválasztó karaktert, ha text tulajdonságához értelmezhető több értéket szeretne.  Lásd: [általános beállítások](#name-value-separator) részleteiről. |
| Navigációs lekérdezés |A lekérdezés a futtatást, ha a felhasználó kiválaszt egy elemet a listában.  Lásd: [általános beállítások](#navigation-query) részleteiről. |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlopban a lista tetején megjelenő szöveg. |
| Érték |A második oszlopban a lista tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ki ahhoz, hogy küszöbértékeket.  Lásd: [általános beállítások](#thresholds) részleteiről. |

## <a name="line-chart--list-part"></a>Sor lista és a diagram része
Fejléc egy grafikonon napló lekérdezésből több adatsorozattal adott idő alatt.  Lista egy numerikus oszlopot vagy adott idő alatt a módosítás relatív értékének jelző grafikon az első tíz egy lekérdezés eredményeként előálló megjeleníti.

![Sor lista és a diagram nézet](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Ikon |Mellett az eredmény a fejlécében megjelenítendő kép fájlt. |
| Ikon használata |Válassza ki, szeretné, hogy a ikon megjelenítése. |
| **Header** | |
| Beosztás |A fejléc tetején megjelenő szöveg. |
| Alcím |A fejléc tetején a címben megjelenítendő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés a vonaldiagram fussanak.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie.  Ez általában az a lekérdezés, amely használja a **mérték** , hogy összesítse a eredmények kulcsszó.  Ha a lekérdezés használ a **időköz** kulcsszót, majd az x tengely a diagram fogja használni az adott időszakban.  Ha a lekérdezés nem tartalmazza a **időköz** kulcsszót, majd óránként intervallumok x tengelyéhez szolgálnak. |
| **Vonaldiagram** |**> Y tengely** |
| A Logaritmikus skála használata |A Logaritmikus skála tengely használata mellett dönt. |
| egység |Adja meg a lekérdezés által visszaadott értékek használt mértékegységet.  Ezt az információt a diagram, amely jelzi, az értéktípusokat és opcionálisan az értékek alakításának feliratok megjelenítéséhez használatos.  Egységtípus egység kategória határozza meg, és határozza meg az elérhető aktuális egységtípus értékek.  Ha az érték átalakítása, majd a numerikus érték az aktuális egységtípus alakulnak típusúra átalakítása. |
| Egyéni felirat |Az Y tengely egységtípus felirat mellett megjelenő szöveg.  Ha nincs címke van megadva, akkor csak a egységtípus jelenik meg. |
| **List** | |
| Lekérdezés |A lekérdezés futtatásához a listát.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| Graph elrejtése |Válassza ki a diagram jobb oldalán a numerikus oszlopot letiltása. |
| Értékgörbéket engedélyezése |Válassza ki a értékgörbe helyett vízszintes sáv megjelenítése.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Szín |A klasszikus vagy értékgörbéket színét. |
| Művelet |Az értékgörbe végrehajtani a műveletet.  Lásd: [általános beállítások](#sparklines) részleteiről. |
| Név és érték elválasztó |Elválasztó karaktert, ha text tulajdonságához értelmezhető több értéket szeretne.  Lásd: [általános beállítások](#name-value-separator) részleteiről. |
| Navigációs lekérdezés |A lekérdezés a futtatást, ha a felhasználó kiválaszt egy elemet a listában.  Lásd: [általános beállítások](#navigation-query) részleteiről. |
| **List** |**> Oszlopfejlécek** |
| Name (Név) |Az első oszlopban a lista tetején megjelenő szöveg. |
| Érték |A második oszlopban a lista tetején megjelenő szöveg. |
| **List** |**> Küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ki ahhoz, hogy küszöbértékeket.  Lásd: [általános beállítások](#thresholds) részleteiről. |

## <a name="stack-of-line-charts-part"></a>Sor diagramok rész verem
Megjeleníti a napló lekérdezésből több sorozat három különálló vonaldiagramok adott idő alatt.

![Navigációs veremben vonaldiagramok](media/log-analytics-view-designer/view-stack-line-charts.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport cím |A csempe tetején megjelenő szöveg. |
| Új csoport |Válassza ki a nézetben az aktuális nézet kezdődő új csoport létrehozásához. |
| Ikon |Mellett az eredmény a fejlécében megjelenítendő kép fájlt. |
| **Diagram – 1<br>2 diagram<br>3 diagram** |**> Header** |
| Beosztás |A diagram tetején megjelenő szöveg. |
| Alcím |A diagram tetején a címben megjelenítendő szöveg. |
| **Diagram – 1<br>2 diagram<br>3 diagram** |**Vonaldiagram** |
| Lekérdezés |A lekérdezés a vonaldiagram fussanak.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie.  Ez általában az a lekérdezés, amely használja a **mérték** , hogy összesítse a eredmények kulcsszó.  Ha a lekérdezés használ a **időköz** kulcsszót, majd az x tengely a diagram fogja használni az adott időszakban.  Ha a lekérdezés nem tartalmazza a **időköz** kulcsszót, majd óránként intervallumok x tengelyéhez szolgálnak. |
| **A diagram** |**> Y tengely** |
| A Logaritmikus skála használata |A Logaritmikus skála tengely használata mellett dönt. |
| egység |Adja meg a lekérdezés által visszaadott értékek használt mértékegységet.  Ezt az információt a diagram, amely jelzi, az értéktípusokat és opcionálisan az értékek alakításának feliratok megjelenítéséhez használatos.  Egységtípus egység kategória határozza meg, és határozza meg az elérhető aktuális egységtípus értékek.  Ha az érték átalakítása, majd a numerikus érték az aktuális egységtípus alakulnak típusúra átalakítása. |
| Egyéni felirat |Az Y tengely egységtípus felirat mellett megjelenő szöveg.  Ha nincs címke van megadva, akkor csak a egységtípus jelenik meg. |

## <a name="common-settings"></a>Általános beállítások
A következő szakaszok ismertetik a képi megjelenítés több részből vonatkozó beállításokat.

### <a name="name-value-separator">Név és érték elválasztó</a>
Ha a lista lekérdezésből text tulajdonságához értelmezhető több értéket szeretne egy karakter elválasztó.  Ha megad egy elválasztó, megadhatja a név mezőbe azonos elválasztó elválasztott mezők nevei.

Vegyük példaként a tulajdonságot, *hely* például része, amely értékek *Redmond-összeállító 41* és *Bellevue-Building12*.  Megadhatja – a név és érték elválasztó és *város-összeállító* nevét.  Ez lenne az egyes értékek értelmezhető nevű két tulajdonságok *Város* és *épület*.

### <a name="navigation-query">Navigációs lekérdezés</a>
A lekérdezés a futtatást, ha a felhasználó kiválaszt egy elemet a listában.  Használjon *{kijelölt elem}* elem, a felhasználó által kiválasztott szintaxisát tartalmazza.

Például, ha a lekérdezés tartalmaz egy nevű oszlopot *számítógép* , és a navigációs lekérdezés *{kijelölt elem}*, többek között a lekérdezés *számítógép = "Sajátgép"* futna, amikor a felhasználó egy számítógépet jelölt.  Ha a navigációs lekérdezés *típus = {kijelölt elem} esemény* majd a lekérdezés *típus = esemény számítógép = "Sajátgép"* lesz futtatható.

### <a name="sparklines">Sparklines</a>
Értékgörbe kis vonaldiagramról, amely bemutatja a lista bejegyzés adott idő alatt.  A képi megjelenítés részei a listájával kiválaszthatja egy numerikus oszlopot vagy jelző értéke időbeli értékgörbe relatív értékét sáv vízszintes megjelenjen-e.

Az alábbi táblázat bemutatja a értékgörbéket beállításait.

| Beállítás | Leírás |
|:--- |:--- |
| Értékgörbéket engedélyezése |Válassza ki a értékgörbe helyett vízszintes sáv megjelenítése. |
| Művelet |Ha értékgörbéket engedélyezve vannak, akkor minden egyes tulajdonsága az értékgörbe az értékek listában végre a műveletet.<br><br>-Utolsó minta: Legutóbbi értéket az időtartam alatt az adatsorozathoz.<br>-Maximális: Az adatsorozathoz alatt az időtartam alatt a maximális érték.<br>-Min: Az adatsorozathoz alatt az időtartam alatt a minimális érték.<br>-Összeg: Sum az adatsorok alatt az időtartam alatt.<br>-Összegzés: Ugyanaz a mérték parancs használja, mint a lekérdezés a fejlécben. |

### <a name="thresholds">Küszöbértékek</a>
Küszöbértékek lehetővé teszik a színes ikon minden elem mellett megjelenő felkínálva egy gyors visual jelzi, hogy lehet egy adott értéket, vagy egy adott tartományba esnek elemek listáját.  Például egy elfogadható érték, a sárga, ha az érték, amely figyelmeztet, egy tartományon belül, és a piros elemek zöld ikonnal lehetett megjeleníteni, ha az érték meghaladja a hibaérték.

Ha engedélyezi a küszöbértékeket egy részére, meg kell adnia egy vagy több küszöbértékeket.  Ha egy elem értéke nagyobb, mint a küszöbérték és alacsonyabb, mint a következő küszöbértéket, szín szerepel.  Ha az elem nagyobb, mint majd legmagasabb küszöbértéket, szín van beállítva.   

Minden egyes küszöbérték beállítása rendelkezik egy küszöbértéket értékkel rendelkező **alapértelmezett**.  Azt állítja be, ha nincs más értékek túllépése színét.  Adja hozzá, vagy távolítsa el a küszöbértékek kattintva a  **+**  vagy **x** gombra.

Az alábbi táblázat bemutatja a küszöbértékek beállításait.

| Beállítás | Leírás |
|:--- |:--- |
| Küszöbértékek engedélyezése |Válassza ki a szín ikonjának megjelenítése minden egyes megadott küszöbértékek viszonyítva az állapotát jelző érték balra. |
| Name (Név) |A küszöbérték azonosító nevet. |
| Küszöbérték |Értéke a küszöbérték.  A legmagasabb küszöbérték túllépte a cikk érték színének beállítása mindegyik listaelemhez állapotfigyelő színét.  Van egy alapértelmezett küszöböt, akkor a szín, ha nincs küszöbértéket. |
| Szín |A küszöbérték színét. |

## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) támogatja a képi megjelenítés részből áll.
