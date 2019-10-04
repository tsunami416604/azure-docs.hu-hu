---
title: A lekérdezés nyelvének megismerése
description: Az Azure Resource Graph-ban használható Kusto-operátorok és függvények ismertetése.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231513"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure Resource Graph lekérdezési nyelvének megismerése

Az Azure Resource Graph lekérdezési nyelve számos operátort és funkciót támogat. Minden munka és működés az [Azure Adatkezelőon](../../../data-explorer/data-explorer-overview.md)alapul.

Az erőforrás-gráf által használt lekérdezési nyelv megismeréséhez a legjobb módszer az Azure Adatkezelő [lekérdezési nyelv](/azure/kusto/query/index)dokumentációjának használata. Bemutatjuk, hogyan épülnek fel a nyelv, és hogyan működnek együtt a különböző támogatott operátorok és függvények.

## <a name="supported-tabular-operators"></a>Támogatott táblázatos operátorok

Az alábbi lista a támogatott táblázatos operátorok listáját tartalmazza az erőforrás-gráfban:

- [count](/azure/kusto/query/countoperator)
- [különböző](/azure/kusto/query/distinctoperator)
- [kiterjesztése](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [rendezési sorrend](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [minta](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [Rendezés szempontja](/azure/kusto/query/sortoperator)
- [Összegzés](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [Top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [Top-ütő](/azure/kusto/query/tophittersoperator)
- [ahol](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Támogatott függvények

Az alábbi lista a támogatott függvények listáját tartalmazza az erőforrás-gráfban:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [ToString ()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Escape-karakterek

Egyes tulajdonságokat, például `.` a vagy `$`a karaktert tartalmazó neveket be kell csomagolni, vagy el kell menekülni a lekérdezésben, vagy a tulajdonság nevét nem megfelelően értelmezi a rendszer, és nem biztosítja a várt eredményeket.

- `.`– A tulajdonság nevét a következőképpen csomagolja be:`['propertyname.withaperiod']`
  
  Példa lekérdezésre, amely a OData tulajdonságot csomagolja _. írja be_a következőt:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Escape a tulajdonság nevében szereplő karakter. A használatban lévő escape-karakter a rendszerhéj-erőforrás Gráftól függ.

  - **bash** - `\`

    Példa olyan lekérdezésre, amely megmenekül a tulajdonság  _\$típusa_ a bashben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – ne elkerülje a `$` karaktert.

  - **PowerShell** - ``` ` ```

    Példa olyan lekérdezésre, amely megmenekül a tulajdonság  _\$típusa_ a PowerShellben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>További lépések

- Megtekintheti az alapszintű [lekérdezésekben](../samples/starter.md) használt nyelvet
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md)
- Információ az [erőforrások felfedezéséről](explore-resources.md)