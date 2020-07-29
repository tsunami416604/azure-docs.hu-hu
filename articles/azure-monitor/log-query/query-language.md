---
title: Azure Monitor log-lekérdezések | Microsoft Docs
description: Az erőforrásokra mutató hivatkozások, amelyekkel megtudhatja, hogyan írhat napló lekérdezéseket Azure Monitorban.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 080af271870362e6e0633aaf590820f2fadb6144
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325389"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor-naplólekérdezések

Azure Monitor naplók az Azure Adatkezelő-ra épülnek, és Azure Monitor a napló lekérdezései ugyanazt a Kusto-lekérdezési nyelvet használják. A [Kusto lekérdezési nyelv dokumentációja](/azure/kusto/query) tartalmazza a nyelv összes részletét, és az elsődleges erőforrásnak kell lennie Azure monitor log-lekérdezések írásához. Ez a lap más forrásokra mutató hivatkozásokat tartalmaz, amelyekkel megtudhatja, hogyan írhat lekérdezéseket és a nyelv Azure Monitor megvalósításával kapcsolatos különbségeket.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Első lépések

- Első [lépések a Azure Monitor log Analytics](get-started-portal.md) a lekérdezések írásához és az eredményekkel való együttműködéshez szükséges lecke a Azure Portalban.
- A [Azure monitor log-lekérdezések használatának első lépései](get-started-queries.md) a lekérdezések Azure monitor naplózási adataival való írásának tanulságai.

## <a name="concepts"></a>Alapelvek

- A [Azure monitor naplózási információinak elemzése](./log-query-overview.md) rövid áttekintést nyújt a naplók lekérdezéséről, és leírja, hogyan épülnek fel Azure monitor naplózási információk.
- A [naplózási adatok megtekintése és elemzése Azure monitor ismerteti azokat](./log-query-overview.md) a portálokat, amelyeken a naplók létrehozása és futtatása folyamatban van.

## <a name="reference"></a>Referencia

- A [lekérdezés nyelvi referenciája](/azure/kusto/query) a Kusto lekérdezési nyelvének teljes nyelvi referenciája.
- [Azure monitor a naplózási lekérdezés nyelvi eltérései](data-explorer-difference.md) a Kusto lekérdezési nyelv verziói közötti különbségeket ismertetik.
- [A Azure monitor naplóbejegyzések szabványos tulajdonságai](../platform/log-standard-properties.md) olyan tulajdonságokat mutatnak be, amelyek az összes Azure monitor naplózási adatként szabványosak.
- [Erőforrás-naplózási lekérdezések végrehajtása Azure monitor ismerteti a](./cross-workspace-query.md) több log Analytics munkaterületről és Application Insights alkalmazásokból származó adatokkal rendelkező naplók írását.

## <a name="examples"></a>Példák

- A [Azure monitor log lekérdezési példák](examples.md) példaként szolgálnak a Azure monitor naplózási adataival.

## <a name="lessons"></a>Leckék

- A karakterláncok [használata Azure monitor napló lekérdezésekben](string-operations.md) leírja, hogyan használható a sztring-adatmennyiség.
- A dátum-és [időértékek Azure monitor napló lekérdezésekben való használata](datetime-operations.md) a dátum-és időadatokkal való munkavégzést ismerteti.
- [Az Azure monitor naplózási lekérdezésekben](aggregations.md) és [a speciális összesítésekben szereplő összesítések a Azure monitor-naplókban](advanced-aggregations.md) azt írják le, hogyan összesítheti és összesítheti az adatokat.
- [A Azure monitor log-lekérdezések](joins.md) összekapcsolásai azt írják le, hogyan lehet több táblából összekapcsolni az adatokkal.
- A [JSON-és adatszerkezetek használata a Azure monitor-napló lekérdezésekben](json-data-structures.md) leírja a JSON-adatelemzés módját.
- [A speciális naplók írásakor a Azure monitor](advanced-query-writing.md) ismerteti az összetett lekérdezések létrehozásához és a kód újbóli felhasználásához szükséges stratégiákat.
- [Diagramok és diagramok létrehozása Azure monitor naplókból](charts.md) – ez a cikk azt ismerteti, Hogyan jeleníthető meg az adatok naplózási lekérdezésből.

## <a name="cheatsheets"></a>Hasznos tanácsok

- [Az SQL – Azure monitor a log lekérdezés](sql-cheatsheet.md) segítséget nyújt a már ismerős SQL-felhasználók számára.
- [A Splunk Azure monitor log lekérdezés](splunk-cheatsheet.md) segítséget nyújt a splunk már ismerős felhasználók számára.

## <a name="next-steps"></a>További lépések

- Nyissa [meg a Kusto lekérdezési nyelvének teljes dokumentációját](/azure/kusto/query/).

