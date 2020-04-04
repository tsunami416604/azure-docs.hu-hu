---
title: Azure Monitor naplólekérdezései | Microsoft dokumentumok
description: Az Azure Monitor naplólekérdezéseinek írása ként szolgáló erőforrásokra mutató hivatkozások.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 6b81aba553fc775821c80631aa83bbb3e8ac63b5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631798"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor naplólekérdezései

Az Azure Monitor-naplók az Azure Data Explorerre épülnek, és az Azure Monitor naplólekérdezései ugyanannak a Kusto lekérdezési nyelvnek a verzióját használják. A [Kusto lekérdezési nyelvi dokumentáció](/azure/kusto/query) tartalmazza a nyelv összes részletét, és az Azure Monitor naplólekérdezéseinek írásához elsődleges erőforrásnak kell lennie. Ez a lap hivatkozásokat tartalmaz más forrásokhoz a lekérdezések írásának és a nyelv Azure Monitor-implementációjával kapcsolatos különbségek megismeréséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Első lépések

- [Az Azure Monitor Log Analytics használata](get-started-portal.md) egy lecke a lekérdezések írásához és az eredmények nek az Azure Portalon való kezeléséhez.
- [Az Azure Monitor naplólekérdezéseinek első lépései](get-started-queries.md) az Azure Monitor naplóadatainak használatával történő lekérdezések írása.

## <a name="concepts"></a>Alapelvek

- [Az Azure Monitor naplóadatainak elemzése](../../azure-monitor/log-query/log-query-overview.md) rövid áttekintést nyújt a naplólekérdezésekről, és ismerteti az Azure Monitor naplóadatainak felépítését.
- [A naplóadatok megtekintése és elemzése az Azure Monitorban](../../azure-monitor/log-query/portals.md) ismerteti azokat a portálokat, ahol naplólekérdezéseket hoz létre és futtat.

## <a name="reference"></a>Referencia

- [A lekérdezési nyelv hivatkozása](/azure/kusto/query) a Kusto lekérdezési nyelv teljes nyelvi hivatkozása.
- [Az Azure Monitor naplólekérdezési nyelvi különbségek](data-explorer-difference.md) ismerteti a Kusto lekérdezési nyelv verziói közötti különbségeket.
- [Az Azure Monitor naplórekordjainak szabványos tulajdonságai](../../azure-monitor/platform/log-standard-properties.md) olyan tulajdonságokat írnak le, amelyek az Azure Monitor összes naplóadataihoz szabványosak.
- [Az Azure Monitor ban erőforrásközi naplólekérdezéseket hajthat végre,](../../azure-monitor/log-query/cross-workspace-query.md) amely leírja, hogyan írhat naplólekérdezéseket, amelyek több Log Analytics-munkaterületről és Application Insights-alkalmazásból származó adatokat használnak.

## <a name="examples"></a>Példák

- [Az Azure Monitor naplólekérdezési példái](examples.md) példalekérdezéseket tartalmaz az Azure Monitor naplóadatai használatával.

## <a name="lessons"></a>Leckék

- [A karakterláncok az Azure Monitor naplólekérdezések](string-operations.md) használatával a karakterlánc-adatok kal való együttműködés.
- [A dátumidő-értékek használata az Azure Monitor naplólekérdezésekben](datetime-operations.md) ismerteti, hogyan dolgozhat a dátum- és időadatokkal.
- [Aggregációk az Azure Monitor naplólekérdezések](aggregations.md) és [a speciális összesítések az Azure Monitor naplólekérdezések](advanced-aggregations.md) ismertetik, hogyan összesítheti és összegezheti az adatokat.
- [Az Azure Monitor naplólekérdezéseiaz Azure Monitor naplólekérdezéseinek](joins.md) használatával ismertetjük, hogyan lehet több táblából adatokat összeadni.
- [A JSON és az azure-figyelő naplólekérdezéseinek json- és adatstruktúrák](json-data-structures.md) kal való együttműködése ismerteti a jsonadatok elemzésével.
- [Speciális naplólekérdezések írása az Azure Monitorban](advanced-query-writing.md) ismerteti az összetett lekérdezések létrehozásának és a kód újrafelhasználásának stratégiáit.
- [Diagramok és diagramok létrehozása az Azure Monitor naplólekérdezések](charts.md) ismerteti, hogyan vizualizálhatja az adatokat egy naplólekérdezés.

## <a name="cheatsheets"></a>Hasznos tanácsok

- [SQL-Azure Monitor napló lekérdezés](sql-cheatsheet.md) segíti a felhasználókat, akik már ismerik az SQL.
- [Splunk az Azure Monitor napló lekérdezési](splunk-cheatsheet.md) segíti a felhasználókat, akik már ismerik a Splunk.

## <a name="next-steps"></a>További lépések

- A [Kusto lekérdezési nyelvének teljes referenciadokumentációjának](/azure/kusto/query/)elérése.
