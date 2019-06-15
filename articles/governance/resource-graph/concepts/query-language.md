---
title: A lekérdezési nyelv ismertetése
description: Ismerteti az elérhető Kusto-operátorok és funkciók lehet majd használni az Azure Erőforrás-grafikon.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: dcb21a6aedf16b034fad4f0822e22758dda03c33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800509"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure-erőforrás Graph lekérdezési nyelv ismertetése

A lekérdezési nyelv, az Azure Erőforrás-grafikon a kezelők és a funkciók számos támogatja. Minden működik, és üzemeltethet alapján [Azure adatkezelő](../../../data-explorer/data-explorer-overview.md).

Erőforrás-grafikon által használt lekérdezési nyelvvel kapcsolatos legjobb módja az, hogy kezdje a dokumentáció az Azure Data Explorer [lekérdezési nyelv](/azure/kusto/query/index). Egy ismertetése, hogyan épül fel a nyelvet, és hogyan a különböző támogatott operátorok biztosít, és functions működnek együtt.

## <a name="supported-tabular-operators"></a>Támogatja a táblázatos operátorok

A következő erőforrás Graph támogatott táblázatos szereplők listáját:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [rendezési](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [Minta](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [Rendezés](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [hajtsa végre a megfelelő](/azure/kusto/query/takeoperator)
- [felső](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [TOP-hitters](/azure/kusto/query/tophittersoperator)
- [ahol](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Támogatott funkciók

A következő erőforrás Graph támogatott funkciók listáját:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [ToString()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Escape karakter

Néhány tulajdonság nevét, például azokkal, amelyek tartalmazzák a `.` vagy `$`, kell csomagolni, vagy escape-karakterrel a lekérdezés vagy a tulajdonság neve nem megfelelően értelmezi, és nem az elvárt eredményeket biztosít.

- `.` -Wrap ilyen tulajdonság neve: `['propertyname.withaperiod']`
  
  Példa lekérdezés, amely a tulajdonság _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -A a tulajdonságnév karakterek elkerülésére. Az escape-karaktert használja az Erőforrás-grafikon fut shell függ.

  - **bash** - `\`

    Példalekérdezést, amely lehet kilépni a tulajdonság  _\$típus_ a bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -escape nem a `$` karakter.

  - **PowerShell** - ``` ` ```

    Példalekérdezést, amely lehet kilépni a tulajdonság  _\$típus_ a PowerShellben:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelv [alapszintű lekérdezések](../samples/starter.md)
- Tekintse meg a speciális használ [összetettebb lekérdezésekhez](../samples/advanced.md)
- Információ az [erőforrások felfedezéséről](explore-resources.md)