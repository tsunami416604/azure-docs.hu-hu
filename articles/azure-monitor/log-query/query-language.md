---
title: Az Azure Monitor log-lekérdezések |} A Microsoft Docs
description: Források az Azure monitorban log lekérdezések írásával hivatkozik.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: c59ef72f6e836b9cdf6899d917701555fc5e1988
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268769"
---
# <a name="azure-monitor-log-queries"></a>Az Azure Monitor log-lekérdezések
Az Azure Monitor naplóira Azure adatkezelő épül, és az Azure Monitor log-lekérdezések használata az azonos Kusto-lekérdezési nyelv. A [Azure adatkezelő lekérdezési nyelvi dokumentáció](/azure/kusto/query) rendelkezik az összes részletes adat a nyelv és kell lennie az Azure Monitor log-lekérdezések írására szolgáló elsődleges erőforrás. Ez a lap egyéb forrásokra mutató hivatkozásokat biztosít, megtudhatja, hogyan kell lekérdezéseket írni és a különbség az Azure Monitor megvalósításra, a nyelv.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Első lépések

- [Ismerkedés az Azure Monitor log-analytics](get-started-portal.md) van egy leckében lekérdezések írása és használata az eredményeket az Azure Portalon.
- [Ismerkedés az Azure Monitor log-lekérdezések](get-started-queries.md) van egy leckében összetevőjében naplóadatokat az Azure Monitor használatával.

## <a name="concepts"></a>Alapelvek
- [Elemezheti a naplófájlok adatait az Azure monitorban](../../azure-monitor/log-query/log-query-overview.md) biztosít napló rövid áttekintést lekérdezése, és ismerteti, hogyan épül fel az Azure Monitor naplóadatokat.
- [Az Azure Monitor log-adatok megtekintésére és elemzésére](../../azure-monitor/log-query/portals.md) a portálok, ahol létrehozása és futtatása a naplólekérdezések ismerteti.

## <a name="reference"></a>Leírások

- [Lekérdezési nyelvi leírása](/azure/kusto/query) teljes nyelvi referenciaként szolgál a Kusto-lekérdezési nyelv.
- [Azure Monitor log lekérdezési nyelv különbségek](data-explorer-difference.md) a Kusto-lekérdezés nyelvi verziók közötti különbségeket ismerteti.
- [Az Azure monitorban alapvető tulajdonságainak naplórekordok](../../azure-monitor/platform/log-standard-properties.md) szabványos Azure Monitor log tárolt összes tulajdonságokat ismerteti.
- [Erőforrások közötti log-lekérdezések végrehajtása az Azure monitorban](../../azure-monitor/log-query/cross-workspace-query.md) ismerteti, hogyan lehet több Log Analytics-munkaterületek és Application Insights-alkalmazások adatait használó log lekérdezéseket írni.


## <a name="examples"></a>Példák

- [Az Azure Monitor log lekérdezés példák](examples.md) naplóadatokat az Azure Monitor használatával például lekérdezések biztosít.



## <a name="lessons"></a>Leckék

- [Az Azure Monitor log-lekérdezéseket a karakterláncokkal való munka](string-operations.md) a karakterlánc használatát ismerteti.
- [Dátum idő értékek az Azure Monitor log-lekérdezések használata](datetime-operations.md) dátum és idő adatok használatát ismerteti. 
- [Az Azure monitorban összesítések lekérdezések naplózását](aggregations.md) és [speciális összesítések az Azure Monitor log-lekérdezések](advanced-aggregations.md) ismerteti, hogyan egyesítik és összegzik az adatokat.
- [Az Azure Monitor log-lekérdezések csatlakozik](joins.md) azt ismerteti, hogyan lehet az adatok több táblából.
- [JSON és a használata adatok struktúrák az Azure Monitor log-lekérdezések](json-data-structures.md) ismerteti, hogyan lehet json-adatok elemzése.
- [Az Azure monitorban írása speciális lekérdezések naplózását](advanced-query-writing.md) összetett lekérdezések létrehozása és kód újrafelhasználása vonatkozó stratégiákat ismerteti.
- [Diagramok és ábrák létrehozása az Azure Monitor log-lekérdezések](charts.md) azt ismerteti, hogyan jelenítheti meg a napló lekérdezésből származó adatok.

## <a name="cheatsheets"></a>Hasznos tanácsok

-  [SQL-t az Azure Monitor naplólekérdezés](sql-cheatsheet.md) segít a felhasználóknak, akik már jól ismert SQL-lel.
-  [Az Azure Monitor log-lekérdezéshez Splunk](sql-cheatsheet.md) segít a felhasználóknak, akik már ismerik az Splunk.
 
## <a name="next-steps"></a>További lépések

- A teljes hozzáférési [referenciadokumentációt a Kusto-lekérdezés nyelv](/azure/kusto/query/).