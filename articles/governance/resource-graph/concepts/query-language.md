---
title: Az Azure-erőforrás Graph lekérdezési nyelv ismertetése
description: A lekérdezési nyelv, az Azure-erőforrás Graph működését ismerteti.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 09bcedc5250755f06ba23b84a0ae90b4d43a23db
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086165"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure-erőforrás Graph lekérdezési nyelv ismertetése

A lekérdezési nyelv, az Azure Erőforrás-grafikon a kezelők és a funkciók számos támogatja. Minden működik, és üzemeltethet alapján [Azure adatkezelő](../../../data-explorer/data-explorer-overview.md).

Erőforrás-grafikon által használt lekérdezési nyelvvel kapcsolatos legjobb módja az, hogy kezdje a dokumentáció az Azure Data Explorer [lekérdezési nyelv](/azure/kusto/query/index). Egy ismertetése, hogyan épül fel a nyelvet, és hogyan a különböző támogatott operátorok biztosít, és functions működnek együtt.

## <a name="supported-tabular-operators"></a>Támogatja a táblázatos operátorok

A következő erőforrás Graph támogatott táblázatos szereplők listáját:

- [count](/azure/kusto/query/countoperator)
- [Különböző](/azure/kusto/query/distinctoperator)
- [Kiterjesztése](/azure/kusto/query/extendoperator)
- [Korlát](/azure/kusto/query/limitoperator)
- [rendezési](/azure/kusto/query/orderoperator)
- [Projekt](/azure/kusto/query/projectoperator)
- [Projekt kötelező](/azure/kusto/query/projectawayoperator)
- [Minta](/azure/kusto/query/sampleoperator)
- [minta – különálló](/azure/kusto/query/sampledistinctoperator)
- [Rendezés](/azure/kusto/query/sortoperator)
- [Összegzés](/azure/kusto/query/summarizeoperator)
- [hajtsa végre a megfelelő](/azure/kusto/query/takeoperator)
- [felső](/azure/kusto/query/topoperator)
- [felső – beágyazott](/azure/kusto/query/topnestedoperator)
- [TOP-hitters](/azure/kusto/query/tophittersoperator)
- [ahol](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Támogatott funkciók

A következő erőforrás Graph támogatott funkciók listáját:

- [Ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [ToString()](/azure/kusto/query/tostringfunction)

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelv [alapszintű lekérdezések](../samples/starter.md)
- Tekintse meg a speciális használ [összetettebb lekérdezésekhez](../samples/advanced.md)
- Információ az [erőforrások felfedezéséről](explore-resources.md)
