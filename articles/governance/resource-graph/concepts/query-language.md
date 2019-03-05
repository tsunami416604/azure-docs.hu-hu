---
title: A lekérdezési nyelv ismertetése
description: Ismerteti az elérhető Kusto-operátorok és funkciók lehet majd használni az Azure Erőforrás-grafikon.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 618445c5b792317d4de4b668e7ea1a186707007c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317917"
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

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelv [alapszintű lekérdezések](../samples/starter.md)
- Tekintse meg a speciális használ [összetettebb lekérdezésekhez](../samples/advanced.md)
- Információ az [erőforrások felfedezéséről](explore-resources.md)