---
title: Az Azure Monitor munkafüzet legördülő paraméterei
description: Az összetett jelentéskészítés egyszerűsítése előre összeállított és egyéni paraméterezett munkafüzetekkel, amelyek legördülő paramétereket tartalmaznak
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658283"
---
# <a name="workbook-drop-down-parameters"></a>Munkafüzet legördülő paraméterei

A legördülő menük lehetővé teszik a felhasználó számára, hogy egy ismert készletből egy vagy több bemeneti értéket gyűjtsön (például válassza ki az alkalmazás egyik kérését). A legördülő menük felhasználóbarát módon gyűjtik össze a felhasználók tetszőleges bemeneteit. A legördülő menük különösen hasznosak az interaktív jelentésekszűrésének engedélyezésében. 

A legördülő lista megadásának legegyszerűbb módja, ha statikus listát ad meg a paraméterbeállításban. Egy sokkal érdekesebb módja az, hogy a lista dinamikusan keresztül KQL lekérdezést. A paraméterbeállítások azt is lehetővé teszik, hogy megadja, hogy egyszeres vagy többszörös kijelölésű-e, és ha többszörösen jelöli ki, hogyan kell az eredményhalmazt formázni (határoló, árajánlat stb.).

## <a name="creating-a-static-drop-down-parameter"></a>Statikus legördülő paraméter létrehozása

1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve:`Environment`
    2. Paraméter típusa:`Drop down`
    3. Szükséges:`checked`
    4. Allow `multiple selection`:`unchecked`
    5. Adatok beszerezése innen:`JSON`
5. A JSON beviteli szövegblokkba szúrja be ezt a jsonkódrészletet:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Nyomd `Update` meg a kék gombot.
7. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.
8. A Környezet paraméter a három értékkel rendelkező legördülő menü lesz.

    ![Statikus fulladást ábrázoló kép](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Statikus legördülő menü létrehozása elemcsoportokkal
Ha a lekérdezés eredménye/json "csoport" mezőt tartalmaz, a legördülő menü értékcsoportokat jelenít meg. Kövesse a fenti mintát, de inkább a következő jsont használja:
```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Dinamikus legördülő paraméter létrehozása
1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve:`RequestName`
    2. Paraméter típusa:`Drop down`
    3. Szükséges:`checked`
    4. Allow `multiple selection`:`unchecked`
    5. Adatok beszerezése innen:`Query`
5. A JSON beviteli szövegblokkba szúrja be ezt a jsonkódrészletet:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Nyomd `Run Query` meg a kék gombot.
2. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.
3. A RequestName paraméter az alkalmazás összes kérésének nevét legördülő menülesz.

    ![Dinamikus legördülő kép](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Hivatkozás legördülő paraméterre
### <a name="in-kql"></a>A kql-ban
1. Vegyen fel egy lekérdezésvezérlőt a munkafüzetbe, és jelöljön ki egy Application Insights-erőforrást.
2. A KQL szerkesztőben adja meg ezt a kódrészletet

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Ez kibővíti a lekérdezés kiértékelési idejét a következőkre:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Futtassa a lekérdezést az eredmények megtekintéséhez. Szükség esetén diagramként jelenítheti meg.

    ![KQL-ben hivatkozott legördülő legördülő kép](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Paraméter érték, címke, kijelölés és csoport
A fenti dinamikus legördülő paraméterben használt lekérdezés csak a legördülő listában hűen megjelenő értékek listáját adja vissza. De mi van, ha azt akarta, hogy egy másik megjelenítendő nevet, vagy egy ilyen kell kiválasztani? A legördülő paraméterek ezt az érték-, címke-, kijelölési és csoportoszlopokon keresztül teszik lehetővé.

Az alábbi minta bemutatja, hogyan kaphatja meg az Application Insights-függőségek listáját, amelyek megjelenítendő neveemojival van stílusú, az első tikklik, és műveletnevek szerint van csoportosítva.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Legördülő paraméter beállításai
| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | A kijelölt érték | GET fabrikamaccount |
| `{DependencyName:label}` | A kijelölt címke | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | A kijelölt érték | GET fabrikamaccount |

## <a name="multiple-selection"></a>Többszörös kijelölés
Az eddig bemutatott példák explicit módon úgy állították be a paramétert, hogy csak egy értéket válasszon ki a legördülő menüben. A legördülő paraméterek `multiple selection` is támogatják - ez `Allow multiple selection` olyan egyszerű, mint a beállítás ellenőrzése. 

A felhasználó nak lehetősége van arra is, hogy `delimiter` megadja `quote with` az eredményhalmaz formátumát a és a beállításokon keresztül. Az alapértelmezett érték csak az értékeket adja vissza gyűjteményként ebben a formában: "a", "b", "c". Azt is a lehetőséget, hogy korlátozza a választások száma.

A paraméterre hivatkozó KQL-nek meg kell változnia, hogy működjön az eredmény formátumával. A leggyakoribb módja annak, hogy `in` ez keresztül az üzemeltető.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Íme egy példa a több-select legördülő munka:

![Többválasztós legördülő paramétert megjelenítő kép](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>További lépések

* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.
