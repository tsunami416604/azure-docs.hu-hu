---
title: Azure Monitor munkafüzetek egyéni paraméterekkel
description: Az összetett jelentések egyszerűsítése előre összeállított és egyéni paraméterezett munkafüzetekkel
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658268"
---
# <a name="interactive-workbooks"></a>Interaktív munkafüzetek

A munkafüzetek lehetővé teszik a szerzők számára, hogy interaktív jelentéseket és élményeket hozzanak létre a fogyasztók számára. Az interaktivitás számos módon támogatott.

## <a name="parameter-changes"></a>Paraméterváltozások
Amikor egy munkafüzet-felhasználó frissít egy paramétert, a paramétert használó vezérlőautomatikusan frissül, majd újrarajzolja az új állapotot. Az Azure Portal legtöbb jelentése így támogatja az interaktivitást. Munkafüzetek biztosítják ezt egy nagyon egyenes előre módon minimális felhasználói erőfeszítést.

További információ [a munkafüzetek paramétereiről](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>A rácssor kattintásai
A munkafüzetek lehetővé teszik a szerzők számára, hogy olyan eseteket dolgozzanak ki, amelyekben a rács egy sorára kattintva frissíti a következő diagramokat a sor tartalma alapján. 

Például egy felhasználó rendelkezhet egy rács, amely megjeleníti a kérelmek listáját, és néhány statisztika, mint a hiba számít. Ők is létre úgy, hogy egy sorra kattintva megfelelő kérelmet, azt eredményezi, hogy részletes diagramok alatt frissítése, hogy kiszűrje le, hogy csak a kérelmet.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Az interaktivitás beállítása a rácssor kattintásakor
1. Az eszköztárelemre kattintva átváltson _Edit_ a munkafüzetet szerkesztési módra.
2. A _Lekérdezés hozzáadása_ hivatkozással naplólekérdezés-vezérlőt adhat a munkafüzethez. 
3. Válassza ki a lekérdezés típusát _naplóként,_ erőforrástípusként (például Application Insights) és a megcélozandó erőforrásokként.
4. A Lekérdezés szerkesztő vel adja meg a KQL-t elemzésre
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`hogy az eredményeket
6. Kattintson a speciális _beállítások_ ikonra a lekérdezés láblécén (az ikon úgy néz ki, mint egy fogaskerék). Ezzel megnyitja a speciális beállítások ablaktáblát 
7. Ellenőrizze a beállítást:`When an item is selected, export a parameter`
    1. Exportálandó mező:`Request`
    2. Paraméter neve:`SelectedRequest`
    3. Alapértelmezett érték:`All requests`
    
    ![A speciális szerkesztőt bemutató kép a mezők paraméterekként történő exportálásának beállításaival](./media/workbooks-interactive/advanced-settings.png)

8. Kattintson a `Done Editing` gombra.
9. A 2.
10. A Lekérdezés szerkesztő vel adja meg a KQL-t elemzésre
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`az eredmények megtekintéséhez.
12. _Képi megjelenítés_ módosítása`Area chart`
12. Kattintson egy sorra az első rácsban. Figyelje meg, hogy az alábbi területdiagram hogyan szűri a kijelölt kérelmet.

Az eredményül kapott jelentés szerkesztési módban így néz ki:

![A létrehozás interaktív élményét ábrázoló kép rácssorkattintásokkal](./media/workbooks-interactive//grid-click-create.png)

Az alábbi képen egy bonyolultabb interaktív jelentés olvasható módban, ugyanazon elvek alapján. A jelentés rácskattintások segítségével exportálja a paramétereket , amelyeket két diagramban és egy szövegblokkban is használnak.

![A létrehozás interaktív élményét ábrázoló kép rácssorkattintásokkal](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Teljes sor tartalmának exportálása
Néha kívánatos, hogy a kijelölt sor teljes tartalmát exportálja egy adott oszlop helyett. Ilyen esetekben hagyja `Field to export` a tulajdonságot a fenti 7.1 lépésben beállítatlanul. A munkafüzetek a teljes sor tartalmát jsonként exportálják a paraméterbe. 

A hivatkozó KQL vezérlő, `todynamic` használja a funkciót a json elemzéséhez és az egyes oszlopok eléréséhez.

 ## <a name="grid-cell-clicks"></a>Rácscella kattintások
A munkafüzetek lehetővé teszik a szerzők számára, hogy `link renderer`interaktivitást adjanak hozzá egy speciális típusú rácsoszlop-megjelenítőn keresztül, amelyet . A hivatkozásmegjelenítő a cella tartalma alapján hiperhivatkozássá alakítja a rácscellát. A munkafüzetek sokféle hivatkozásmegjelenítőt támogatnak - beleértve azokat is, amelyek lehetővé teszik az erőforrás-áttekintési pengék megnyitását, az ingatlantáska-megtekintőket, az App Insights-keresést, a használatot, a tranzakciókövetést stb.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Az interaktivitás beállítása rácscella-kattintásokkal
1. Az eszköztárelemre kattintva átváltson _Edit_ a munkafüzetet szerkesztési módra.
2. A _Lekérdezés hozzáadása_ hivatkozással naplólekérdezés-vezérlőt adhat a munkafüzethez. 
3. Válassza ki a lekérdezés típusát _naplóként,_ erőforrástípusként (például Application Insights) és a megcélozandó erőforrásokként.
4. A Lekérdezés szerkesztő vel adja meg a KQL-t elemzésre
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`hogy az eredményeket
6. A Beállítások ablaktábla megnyitásához kattintson az _Oszlopbeállítások_ gombra.
7. Az _Oszlopok_ szakaszban állítsa be a következőt:
    1. _Minta_ - Oszlopmegjelenítő: `Link`, `Cell Details`Megnyitás nézet: , Hivatkozáscímke:`Sample`
    2. _Count_ - Oszlopmegjelenítő: `Bar`, `Blue`Színpaletta: , Minimális érték:`0`
    3. _Kérés_ - Oszlopmegjelenítő:`Automatic`
    4. A módosítások alkalmazásához kattintson a _Mentés és a Bezárás_ gombra
8. Kattintson az `Sample` egyik linkre a rácsban. Ez megnyit egy tulajdonságpanelt a mintavételezett kérelem részleteivel.

    ![A létrehozást ábrázoló kép egy interaktív élményt jelenít meg a rácscella-kattintások használatával](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Hivatkozásmegjelenítői műveletek
| Hivatkozásművelet | Művelet kattintásra |
|:------------- |:-------------|
| `Generic Details` | A tulajdonságrács környezetének paneljének sorértékei |
| `Cell Details` | A cellaértékét jeleníti meg egy tulajdonságrács környezetpaneljén. Akkor hasznos, ha a cella tartalmaz egy dinamikus típust információkkal (például json kéréstulajdonságokkal, például hely, szerepkörpéldány stb.). |
| `Cell Details` | A cellaértékét jeleníti meg egy tulajdonságrács környezetpaneljén. Akkor hasznos, ha a cella tartalmaz egy dinamikus típust információkkal (például json kéréstulajdonságokkal, például hely, szerepkörpéldány stb.). |
| `Custom Event Details` | Megnyitja az Application Insights keresési adatait a cellában lévő egyéni eseményazonosítóval (itemId) |
| `* Details` | Az Egyéni esemény részleteihez hasonlóan, kivéve a függőségeket, a kivételeket, az oldalmegtekintéseket, a kéréseket és a nyomkövetéseket. |
| `Custom Event User Flows` | Megnyitja az Application Insights felhasználói folyamatok felületét, amely a cellában lévő egyéni eseménynévre mutat |
| `* User Flows` | Hasonló az egyéni eseményfelhasználói folyamatokhoz, kivéve a kivételeket, az oldalmegtekintéseket és a kéréseket |
| `User Timeline` | A felhasználói idővonal megnyitása a cellában lévő felhasználói azonosítóval (user_Id) |
| `Session Timeline` | Megnyitja az Application Insights keresési felületét a cellában lévő értékhez (például az "abc" szöveg keresése, ahol az abc a cella értéke) |
| `Resource overview` | Az erőforrás áttekintésének megnyitása a portálon a cellában lévő erőforrásazonosító értéke alapján |

## <a name="conditional-visibility"></a>Feltételes láthatóság
A munkafüzet lehetővé teszi a felhasználók számára, hogy bizonyos vezérlők megjelenjenek vagy eltűnjenek a paraméterek értékei alapján. Ez lehetővé teszi a szerzők számára, hogy a jelentések a felhasználói bevitel vagy a telemetriai állapot alapján eltérőek legyenek. Egy példa van bemutatás fogyasztó csak egy összefoglalt mikor dolog van jó de mutat teli részlet mikor valami van rossz.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Az interaktivitás beállítása feltételes láthatósághasználatával
1. Két interaktív vezérlő `Setting up interactivity on grid row click` beállításához kövesse a szakasz lépéseit.
2. Új paraméter hozzáadása felül:
    1. név:`ShowDetails`
    2. Paraméter típusa:`Drop down`
    3. Szükséges:`checked`
    4. Adatok beszerezése innen:`JSON`
    5. JSON bemenet:`["Yes", "No"]`
    6. Mentés a módosítások véglegesítéséhez.
3. Paraméterérték beállítása`Yes`
4. A területdiagramot tartalmazó lekérdezésvezérlőben kattintson a _Speciális beállítások_ ikonra (fogaskerék ikon)
5. A beállítás ellenőrzése`Make this item conditionally visible`
    1. Ez az elem `ShowDetails` akkor `equals` látható, ha a paraméter értéke`Yes`
6. A módosítások véglegesítéséhez kattintson a _Kész szerkesztés gombra._
7. Az olvasási módba való belépéshez kattintson a munkafüzet eszköztárán a _Szerkesztés_ elvégezve gombra.
8. Váltson a `ShowDetails` `No`paraméter értékére a értékre. Figyelje meg, hogy az alábbi táblázat eltűnik.

Az alábbi képen látható `ShowDetails` eset látható, hol van`Yes`

![A diagram látható feltételes láthatóságát ábrázoló kép](./media/workbooks-interactive/conditional-visibility.png)

Az alábbi képen látható `ShowDetails` a rejtett eset, ahol`No`

![A diagram rejtett feltételes láthatóságát ábrázoló kép](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>További lépések


* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.
