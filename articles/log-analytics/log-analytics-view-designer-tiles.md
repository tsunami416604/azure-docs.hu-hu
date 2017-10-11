---
title: "Hivatkozás csempéjén adatforrásnézet-tervezőből az OMS szolgáltatáshoz |} Microsoft Docs"
description: "A Naplóelemzési adatforrásnézet-tervezőből lehetővé teszi egyéni nézeteket hozhat létre, amelyek tartalmazzák az OMS-tárházban található adatok különböző képi OMS-konzolon. Ez a cikk ismerteti a beállítások az egyes a csempék, hogy az egyéni nézetek használható hivatkozást."
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
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 2bce5c63b4c6edd3753f1b234cc4f493dcf53dea
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="log-analytics-view-designer-tile-reference"></a>Napló Analytics Nézettervező csempe leírása
Az adatforrásnézet-tervezőből a Naplóelemzési lehetővé teszi egyéni nézeteket hozhat létre, amelyek tartalmazzák az OMS-tárházban található adatok különböző képi OMS-konzolon. Ez a cikk ismerteti a beállítások az egyes a csempék, hogy az egyéni nézetek használható hivatkozást.

Az adatforrásnézet-tervezőből elérhető további cikkeit a következők:

* [Megtekintheti a tervező](log-analytics-view-designer.md) -adatforrásnézet-tervezőből és létrehozásának és szerkesztésének egyéni nézetek eljárások áttekintését.
* [A képi megjelenítés része hivatkozás](log-analytics-view-designer-parts.md) -hivatkozás a beállítások az egyes a csempék, hogy az egyéni nézetek használható.

>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), akkor minden nézetben lekérdezések úgy kell megírni, a [új lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856078).  A munkaterület verziófrissítése előtt készült nézetekkel lesz automtically alakítja át.

Az alábbi táblázat a különböző típusú csempe érhető el az adatforrásnézet-tervezőből.  Az alábbi részek ismertetik a csempe típusonként részletek és azok tulajdonságait.

| Mozaik elrendezés | Leírás |
|:--- |:--- |
| [Szám](#number-tile) |Egyetlen szám megjelenítő lekérdezésből rekordok száma. |
| [Két szám](#two-numbers-tile) |Két egyetlen szám megjelenítő számát is két különböző lekérdezéseket bejegyzéseit. |
| [Fánk](#donut-tile) |Fánk a diagram a központ összefoglaló érték lekérdezés alapján. |
| [Vonaldiagram & kihívás](#line-chart-amp-callout-tile) |A lekérdezés és összefoglaló értékkel felirat alapján vonaldiagram. |
| [Vonaldiagram](#line-chart-tile) |A vonaldiagram lekérdezés alapján. |
| [Két ütemtervek](#two-timelines-tile) |Oszlopdiagram két adatsorozattal egyes külön lekérdezés alapján. |

## <a name="number-tile"></a>Szám csempe
A **szám** csempe egyetlen számot megjelenítő napló lekérdezés és a címkék rekordok számát jeleníti meg.

![Szám csempe](media/log-analytics-view-designer/tile-number.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Mozaik elrendezés** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatásához.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Válassza ki, ha adatfolyam ellenőrzési engedélyezni kell a csempe.  Ez egy másik üzenetet biztosít, ha adatokat csempe nem érhető el.  Ez általában segítségével adja meg egy üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és a rendelkezésre álló adatok származnak. |
| Lekérdezés |Ellenőrizze, hogy a nézet érhető el-e adatok futtatásához lekérdezése.  Ha a lekérdezés eredménytelen, majd egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem megjelenítendő üzenetet.  Ha megadja, hogy nincs üzenet *végrehajtása Assessment* jelenik meg. |


## <a name="two-numbers-tile"></a>Két szám csempéje
A **két szám** csempe megjeleníti az egyes rekordok két különböző naplófájl-lekérdezések és a címkék száma megjelenítő két számot.

![Két szám csempéje](media/log-analytics-view-designer/tile-two-numbers.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Első csempe** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatásához.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| **Második csempe** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg. |
| Lekérdezés |A lekérdezés futtatásához.  A lekérdezés által visszaadott rekordok számát a száma jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Válassza ki, ha adatfolyam ellenőrzési engedélyezni kell a csempe.  Ez egy másik üzenetet biztosít, ha adatokat csempe nem érhető el.  Ez általában segítségével adja meg egy üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és a rendelkezésre álló adatok származnak. |
| Lekérdezés |Ellenőrizze, hogy a nézet érhető el-e adatok futtatásához lekérdezése.  Ha a lekérdezés eredménytelen, majd egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem megjelenítendő üzenetet.  Ha megadja, hogy nincs üzenet *végrehajtása Assessment* jelenik meg. |


## <a name="donut-tile"></a>Fánk csempe
A **fánk** csempe egy egyetlen napló lekérdezésben érték oszlop összesített számát jeleníti meg.  A fánk grafikusan felső három rekord eredményeit jeleníti meg.

![Fánk csempe](media/log-analytics-view-designer/tile-donut.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Fánk** | |
| Lekérdezés |Futtassa a fánk a lekérdezést.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie.  Ez általában az a lekérdezés, amely használja a **mérték** , hogy összesítse a eredmények kulcsszó. |
| **Fánk** |**> Center** |
| Szöveg |A fánk értéket alatt megjelenő szöveg. |
| Művelet |A value tulajdonság egyetlen értéket az összesítendő végrehajtani a műveletet.<br><br>-Összeg: Adja meg a értéket az összes rekord tulajdonság értékét.<br>-Százalékos: Százalékos összegzett értékek rekordból összehasonlítva az összes rekord összegzett értékekkel tulajdonság értékét. |
| Center művelet során használt eredményt értékek |Opcionálisan kattintson a plusz jelre hozzáadása egy vagy több értéket.  A lekérdezés eredményeit rögzíti a megadott tulajdonságértéket korlátozódik.  Ha nincs érték ad hozzá, mint a lekérdezésben szereplő összes rekordok. |
| **Fánk** |**> További beállítások** |
| Színek |Az egyes a három legfontosabb tulajdonságainak megjelenítéséhez színét.  Ha meg szeretné határozni adott tulajdonságértékek másodlagos színt, majd használja speciális szín leképezése. |
| Speciális szín leképezése |Megjeleníti egy adott tulajdonságértékeket színét.  Ha a megadott érték az első három, majd a másodlagos színt jelenik meg a standard szín helyett.  Ha a tulajdonság nem az első három, majd a szín nem jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Válassza ki, ha adatfolyam ellenőrzési engedélyezni kell a csempe.  Ez egy másik üzenetet biztosít, ha adatokat csempe nem érhető el.  Ez általában segítségével adja meg egy üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és a rendelkezésre álló adatok származnak. |
| Lekérdezés |Ellenőrizze, hogy a nézet érhető el-e adatok futtatásához lekérdezése.  Ha a lekérdezés eredménytelen, majd egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem megjelenítendő üzenetet.  Ha megadja, hogy nincs üzenet *végrehajtása Assessment* jelenik meg. |


## <a name="line-chart-tile"></a>Sor diagram csempe
A **vonaldiagram** csempe egy grafikonon napló lekérdezésből több adatsorozattal adott idő alatt.  

![Vonaldiagram & kihívás csempe](media/log-analytics-view-designer/tile-line-chart.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés a vonaldiagram fussanak.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie.  Ez általában az a lekérdezés, amely használja a **mérték** , hogy összesítse a eredmények kulcsszó.  Ha a lekérdezés használ a **időköz** kulcsszót, majd az x tengely a diagram fogja használni az adott időszakban.  Ha a lekérdezés nem tartalmazza a **időköz** kulcsszót, majd óránként intervallumok x tengelyéhez szolgálnak. |
| **Vonaldiagram** |**> Y tengely** |
| A Logaritmikus skála használata |A Logaritmikus skála tengely használata mellett dönt. |
| egység |Adja meg a lekérdezés által visszaadott értékek használt mértékegységet.  Ezt az információt a diagram, amely jelzi, az értéktípusokat és opcionálisan az értékek alakításának feliratok megjelenítéséhez használatos.  A **egységtípus** egység kategória határozza meg, és határozza meg a **aktuális egységtípus** elérhető értékek.  Ha válasszon ki egy értéket **átalakítása** konvertálja a numerikus értéket, majd a **aktuális egység** típus a **átalakítása** típusa. |
| Egyéni felirat |Az Y tengely egységtípus felirat mellett megjelenő szöveg.  Ha nincs címke van megadva, akkor csak a egységtípus jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Válassza ki, ha adatfolyam ellenőrzési engedélyezni kell a csempe.  Ez egy másik üzenetet biztosít, ha adatokat csempe nem érhető el.  Ez általában segítségével adja meg egy üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és a rendelkezésre álló adatok származnak. |
| Lekérdezés |Ellenőrizze, hogy a nézet érhető el-e adatok futtatásához lekérdezése.  Ha a lekérdezés eredménytelen, majd egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem megjelenítendő üzenetet.  Ha megadja, hogy nincs üzenet *végrehajtása Assessment* jelenik meg. |


## <a name="line-chart--callout-tile"></a>Sor diagram & kihívás csempe
A **diagram & kihívás sor** csempe egy grafikonon napló lekérdezésből több adatsorozattal időt és az összesített érték egy kihívás keresztül.  

![Vonaldiagram & kihívás csempe](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| **Vonaldiagram** | |
| Lekérdezés |A lekérdezés a vonaldiagram fussanak.  Az első tulajdonság egy szöveges értéket, a második tulajdonság numerikus értéknek kell lennie.  Ez általában az a lekérdezés, amely használja a **mérték** , hogy összesítse a eredmények kulcsszó.  Ha a lekérdezés használ a **időköz** kulcsszót, majd az x tengely a diagram fogja használni az adott időszakban.  Ha a lekérdezés nem tartalmazza a **időköz** kulcsszót, majd óránként intervallumok x tengelyéhez szolgálnak. |
| **Vonaldiagram** |**> Kihívás** |
| Kihívás |A képfelirat érték felett megjelenő cím szöveg. |
| Az adatsorozat neve |A képfelirat érték használandó adatsorozat tulajdonság értéke.  Adatsorozatok valósul meg, ha a lekérdezés összes rekordot szolgálnak. |
| Művelet |A value tulajdonság egyetlen értéket a kihívás az összesítendő végrehajtani a műveletet.<br>-Átlagos: Az értéket az összes rekord átlaga.<br><br>-Szám: A lekérdezés által visszaadott összes rekord száma.<br>-Utolsó minta: Az utolsó időköze a diagramban szereplő értéket.<br>-Maximális: A diagramban szereplő időszakok közül a maximális érték.<br>-Min: A diagramban szereplő időszakok közül a minimális érték.<br>-Összeg: Az értéket az összes rekord összege. |
| **Vonaldiagram** |**> Y tengely** |
| A Logaritmikus skála használata |A Logaritmikus skála tengely használata mellett dönt. |
| egység |Adja meg a lekérdezés által visszaadott értékek használt mértékegységet.  Ezt az információt a diagram, amely jelzi, az értéktípusokat és opcionálisan az értékek alakításának feliratok megjelenítéséhez használatos.  A **egységtípus** egység kategória határozza meg, és határozza meg a **aktuális egységtípus** elérhető értékek.  Ha válasszon ki egy értéket **átalakítása** konvertálja a numerikus értéket, majd a **aktuális egység** típus a **átalakítása** típusa. |
| Egyéni felirat |Az Y tengely egységtípus felirat mellett megjelenő szöveg.  Ha nincs címke van megadva, akkor csak a egységtípus jelenik meg. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Válassza ki, ha adatfolyam ellenőrzési engedélyezni kell a csempe.  Ez egy másik üzenetet biztosít, ha adatokat csempe nem érhető el.  Ez általában segítségével adja meg egy üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és a rendelkezésre álló adatok származnak. |
| Lekérdezés |Ellenőrizze, hogy a nézet érhető el-e adatok futtatásához lekérdezése.  Ha a lekérdezés eredménytelen, majd egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem megjelenítendő üzenetet.  Ha megadja, hogy nincs üzenet *végrehajtása Assessment* jelenik meg. |


## <a name="two-timelines-tile"></a>Két ütemtervek csempe
A **két ütemtervek** csempe oszlopdiagramok idővel két naplófájl lekérdezések eredményeit jeleníti meg.  Valamennyi adatsorozatnál felirat jelenik meg.  

![Két ütemtervek csempe](media/log-analytics-view-designer/tile-two-timelines.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg. |
| Leírás |A csempe neve alatt megjelenő szöveg. |
| Első diagram | |
| Jelmagyarázat |Az első adatsorozathoz kihívás alatt megjelenő szöveg. |
| Szín |Az első adatsorozat oszlopok használandó szín. |
| A diagram lekérdezés |A lekérdezés futtatásához az első adatsorozathoz.  A diagram oszlopokat meghatározott keresztül minden időközhöz rekordok száma. |
| Művelet |A value tulajdonság egyetlen értéket a kihívás az összesítendő végrehajtani a műveletet.<br><br>-Átlagos: Az értéket az összes rekord átlaga.<br>-Szám: A lekérdezés által visszaadott összes rekord száma.<br>-Utolsó minta: Az utolsó időköze a diagramban szereplő értéket.<br>-Maximális: A diagramban szereplő időszakok közül a maximális érték. |
| **Második diagram** | |
| Jelmagyarázat |A második adatsor kihívás alatt megjelenő szöveg. |
| Szín |A második sorozat oszlopok használandó szín. |
| A diagram lekérdezés |A lekérdezés futtatásához, a második adatsorozathoz.  A diagram oszlopokat meghatározott keresztül minden időközhöz rekordok száma. |
| Művelet |A value tulajdonság egyetlen értéket a kihívás az összesítendő végrehajtani a műveletet.<br><br>-Átlagos: Az értéket az összes rekord átlaga.<br>-Szám: A lekérdezés által visszaadott összes rekord száma.<br>-Utolsó minta: Az utolsó időköze a diagramban szereplő értéket.<br>-Maximális: A diagramban szereplő időszakok közül a maximális érték. |
| **Speciális** |**> Adatfolyamot ellenőrzése** |
| Engedélyezve |Válassza ki, ha adatfolyam ellenőrzési engedélyezni kell a csempe.  Ez egy másik üzenetet biztosít, ha adatokat csempe nem érhető el.  Ez általában segítségével adja meg egy üzenetet az ideiglenes időszakban, amikor a nézet van telepítve, és a rendelkezésre álló adatok származnak. |
| Lekérdezés |Ellenőrizze, hogy a nézet érhető el-e adatok futtatásához lekérdezése.  Ha a lekérdezés eredménytelen, majd egy üzenet jelenik meg az érték a fő lekérdezés helyett. |
| Üzenet |Ha az adatfolyam ellenőrzési lekérdezés visszaadja az adatot nem megjelenítendő üzenetet.  Ha megadja, hogy nincs üzenet *végrehajtása Assessment* jelenik meg. |


## <a name="next-steps"></a>Következő lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) csempéiben lévő lekérdezések támogatásához.
* Adja hozzá [képi megjelenítés részek](log-analytics-view-designer-parts.md) a egyéni nézetben.
