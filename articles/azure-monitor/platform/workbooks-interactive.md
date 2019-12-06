---
title: Egyéni paraméterekkel rendelkező munkafüzetek Azure Monitor
description: Összetett jelentéskészítés egyszerűsítése előre elkészített és egyéni paraméterekkel rendelkező munkafüzetekből
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 19fd8c108e8075d30ca494ca75d52952849c284a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872842"
---
# <a name="interactive-workbooks"></a>Interaktív munkafüzetek

A munkafüzetek lehetővé teszik, hogy a szerzők interaktív jelentéseket és tapasztalatokat hozzanak létre a felhasználók számára. Az interaktivitás több módon is támogatott.

## <a name="parameter-changes"></a>Paraméterek módosítása
Amikor egy munkafüzet felhasználó frissíti a paramétert, a paramétert használó vezérlők automatikusan frissítik és újrarajzolják az új állapotot. A Azure Portal-jelentések többsége támogatja az interaktivitást. A munkafüzetek nagyon egyszerű továbbítási módon biztosítják ezt a minimális felhasználói beavatkozást.

További információ a [munkafüzetek paramétereinek](workbooks-parameters.md) használatáról

## <a name="grid-row-clicks"></a>Rácsos sor kattintás
A munkafüzetek lehetővé teszik, hogy a szerzők olyan forgatókönyveket hozzanak létre, amelyekben egy sor egy sorára kattintva frissíti az ezt követő diagramokat a sor tartalma alapján. 

A felhasználók rendelkezhetnek például egy olyan rácstal, amely megjeleníti a kérelmek listáját, és néhány statisztikát, például a hibák számát. Úgy állíthatják be, hogy a kérelemnek megfelelő sorokra kattintva a rendszer a frissítés után részletes diagramokat hoz létre, hogy csak erre a kérésre szűrje.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Az interaktivitás beállítása a rácsvonalak sorában kattintson a
1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A _lekérdezés hozzáadása_ hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez. 
3. Válassza ki a lekérdezés típusát _naplóként_, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query` az eredmények megtekintéséhez
6. Kattintson a _Speciális beállítások_ ikonra a lekérdezés láblécében (az ikon úgy néz ki, mint egy fogaskerék). Ekkor megnyílik a speciális beállítások ablaktábla 
7. A beállítás bejelölése: `When an item is selected, export a parameter`
    1. Exportálandó mező: `Request`
    2. Paraméter neve: `SelectedRequest`
    3. Alapértelmezett érték: `All requests`
    
    ![Kép: a speciális szerkesztő, amely a mezők exportálási beállításait tartalmazza paraméterekként](./media/workbooks-interactive/advanced-settings.png)

8. Kattintson a `Done Editing` gombra.
9. Adjon hozzá egy másik lekérdezés-vezérlőelemet a 2. és a 3. lépéssel.
10. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query` az eredmények megtekintéséhez.
12. _Vizualizáció_ módosítása `Area chart`re
12. Kattintson az első rács egyik sorára. Figyelje meg, hogy az alábbi diagramterület a kiválasztott kérelemre szűri a szűrőket.

Az eredményül kapott jelentés szerkesztési módban így néz ki:

![Kép az interaktív élmény létrehozásához a rácsos sorok használatával kattintás](./media/workbooks-interactive//grid-click-create.png)

Az alábbi ábrán egy részletesebb interaktív jelentés olvasható az olvasási módban, ugyanazon alapelvek alapján. A jelentés rácsos kattintásokat használ a paraméterek exportálására – ez pedig két diagramon és egy szöveges blokkon is használatos.

![Kép az interaktív élmény létrehozásához a rácsos sorok használatával kattintás](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Teljes sor tartalmának exportálása
Előfordulhat, hogy csak egy adott oszlop helyett érdemes exportálni a kijelölt sor teljes tartalmát. Ilyen esetekben ne törölje a `Field to export` tulajdonságot a fenti 7,1. lépésben. A munkafüzetek a teljes sor tartalmát a paraméternek megfelelő JSON-fájlként exportálják. 

A hivatkozó KQL vezérlőelemen a `todynamic` függvénnyel elemezheti a JSON-t, és elérheti az egyes oszlopokat.

 ## <a name="grid-cell-clicks"></a>Rács cellára kattintás
A munkafüzetek lehetővé teszik, hogy a szerzők interaktivitást adjanak hozzá egy speciális, `link renderer`nevű, rácsos oszlop-megjelenítő használatával. A hivatkozás-megjelenítő egy rácsvonalat helyez át egy hiperhivatkozásba a cella tartalma alapján. A munkafüzetek számos különböző típusú csatolást támogatnak – többek között az erőforrás-áttekintő pengék, a tulajdonság-megjelenítők, az alkalmazás-elemzések keresése, a használat, a tranzakciók nyomon követése stb.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Az interaktivitás beállítása a rács cellájának kattintással
1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A _lekérdezés hozzáadása_ hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez. 
3. Válassza ki a lekérdezés típusát _naplóként_, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query` az eredmények megtekintéséhez
6. A beállítások ablaktábla megnyitásához kattintson az _oszlop beállításai_ elemre.
7. Az _oszlopok_ szakaszban állítsa be a következőket:
    1. _Minta_ -oszlop megjelenítő: `Link`, nézet megnyitva: `Cell Details`, hivatkozás felirata: `Sample`
    2. _Count_ -Column megjelenítő: `Bar`, színpaletta: `Blue`, minimális érték: `0`
    3. _Kérelem_ -oszlop megjelenítő: `Automatic`
    4. A módosítások alkalmazásához kattintson a _Mentés és bezárás_ gombra.
8. Kattintson a rács egyik `Sample` hivatkozására. Ekkor megnyílik a Tulajdonságok ablaktábla egy mintavételi kérelem részleteivel.

    ![Kép, amely interaktív élményt nyújt a Grid cella kattintások használatával](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Renderelési műveletek csatolása
| Művelet csatolása | Művelet kattintáskor |
|:------------- |:-------------|
| `Generic Details` | A sortulajdonság-környezet paneljén lévő sorok értékeinek megjelenítése |
| `Cell Details` | A cella értékét jeleníti meg a tulajdonságok rácsának paneljén. Hasznos, ha a cella tartalmaz egy dinamikus típust (például a JSON-t a kérelem tulajdonságaival, például a helytel, a szerepkör-példánnyal stb.). |
| `Cell Details` | A cella értékét jeleníti meg a tulajdonságok rácsának paneljén. Hasznos, ha a cella tartalmaz egy dinamikus típust (például a JSON-t a kérelem tulajdonságaival, például a helytel, a szerepkör-példánnyal stb.). |
| `Custom Event Details` | A Application Insights keresési adatok megnyitása a cella egyéni eseményazonosító (elemazonosító) alapján |
| `* Details` | Az egyéni esemény részleteihez hasonlóan, a függőségek, a kivételek, az oldalletöltések, a kérések és a Nyomkövetések kivételével. |
| `Custom Event User Flows` | Megnyitja a cellában az egyéni esemény nevében felmutatott Application Insights Felhasználókövetési élményt. |
| `* User Flows` | Az egyéni eseményekhez hasonlóan Felhasználókövetés a kivételek, az oldalletöltések és a kérelmek kivételével |
| `User Timeline` | A felhasználó idővonalának megnyitása a felhasználói AZONOSÍTÓval (user_Id) a cellában |
| `Session Timeline` | Megnyitja a cella értékének Application Insights keresési élményét (például az "ABC" szöveg keresése, ahol az ABC a cella értéke) |
| `Resource overview` | Nyissa meg az erőforrás áttekintését a portálon a cella erőforrás-azonosító értéke alapján |

## <a name="conditional-visibility"></a>Feltételes láthatóság
A munkafüzet lehetővé teszi, hogy a felhasználók bizonyos vezérlőelemek megjelenjenek vagy eltűnnek a paraméterek értékei alapján. Ez lehetővé teszi a szerzők számára, hogy a jelentések eltérőek legyenek a felhasználói bevitel vagy a telemetria állapot alapján. Egy példa arra, hogy a felhasználók csak egy összefoglalót jelenítenek meg, ha a dolgok jók, de a teljes részleteket megmutatják, ha valami nem stimmel.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Az interaktivitás beállítása feltételes láthatósággal
1. Két interaktív vezérlő beállításához kövesse a `Setting up interactivity on grid row click` szakasz lépéseit.
2. Új paraméter hozzáadása a tetején:
    1. Név: `ShowDetails`
    2. Paraméter típusa: `Drop down`
    3. Kötelező: `checked`
    4. Adatok beolvasása innen: `JSON`
    5. JSON-bemenet: `["Yes", "No"]`
    6. Mentés a módosítások végrehajtásához.
3. Paraméter értékének beállítása `Yes`
4. A lekérdezés vezérlőelemben a körzet diagramon kattintson a _Speciális beállítások_ ikonra (fogaskerék ikon)
5. A beállítás bejelölése `Make this item conditionally visible`
    1. Ez az elem látható, ha `ShowDetails` paraméter értéke `equals` `Yes`
6. A módosítások _elvégzéséhez kattintson a Szerkesztés kész_ lehetőségre.
7. Az olvasási mód megadásához kattintson a _Szerkesztés kész_ gombra a munkafüzet eszköztárán.
8. A `ShowDetails` paraméter értékét állítsa `No`értékre. Figyelje meg, hogy az alábbi diagram eltűnik.

Az alábbi képen látható az a látható eset, amelyben a `ShowDetails` `Yes`

![A diagram látható feltételes láthatóságát ábrázoló kép](./media/workbooks-interactive/conditional-visibility.png)

Az alábbi képen a rejtett eset látható, ahol a `ShowDetails` `No`

![A diagram elrejtésének feltételes láthatóságát bemutató kép](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Következő lépések


* [Ismerkedjen](workbooks-visualizations.md) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](workbooks-access-control.md) és megosztása.
