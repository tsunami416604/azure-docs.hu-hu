---
title: Az Azure-erőforrás Graph lekérdezési nyelv ismertetése
description: A lekérdezési nyelv, az Azure-erőforrás Graph működését ismerteti.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3600fc47a0fb318a49c1b37722cb7fffa51ec6f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951950"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Az Azure-erőforrás Graph lekérdezési nyelv ismertetése

A lekérdezési nyelv, az Azure Erőforrás-grafikon a kezelők és a funkciók számos támogatja. Minden egyes működnek, és a Kusto Query Language (KQL) hasonlóan működnek. Fontos azonban megérteni, hogy közben az Erőforrás-grafikon lekérdezési nyelve, hasonló [KQL](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators), nem ugyanaz.

> [!NOTE]
> KQL dokumentációs hivatkozásokat hitelesítési lehet szükség.

A legjobb módszer erőforrás Graph által használt lekérdezési nyelvvel kapcsolatos kezdődik, hogyan épül fel a nyelvet, és hogyan a különböző támogatott operátorok végigvezetnek KQL dokumentációját, és a függvények együtt.

## <a name="supported-tabular-operators"></a>Támogatja a táblázatos operátorok

A következő erőforrás Graph támogatott táblázatos szereplők listáját:

- [Különböző](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator)
- [Kiterjesztése](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)
- [Korlát](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/limit-operator)
- [rendezési](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/order-operator)
- [Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)
- [Projekt kötelező](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-away-operator)
- [Minta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-operator)
- [minta – különálló](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-distinct-operator)
- [Rendezés](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
- [Összegzés](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)
- [hajtsa végre a megfelelő](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
- [felső](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator)
- [felső – beágyazott](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-nested-operator)
- [TOP-hitters](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-hitters-operator)
- [ahol](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

## <a name="supported-functions"></a>Támogatott funkciók

A következő erőforrás Graph támogatott funkciók listáját:

- [Ago()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago%28%29)
- [buldschema()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/buildschema%28%29)
- [Count()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/count%28%29)
- [strcat()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/strcat%28%29)
- [isnotempty()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/isnotempty%28%29_-notempty%28%29)
- [ToString()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/tostring%28%29)

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelv [alapszintű lekérdezések](../samples/starter.md)
- Tekintse meg a speciális használ [összetettebb lekérdezésekhez](../samples/advanced.md)
- Ismerje meg, hogyan [erőforrások vizsgálata](explore-resources.md)