---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657792"
---
| Korlát | Leírás |
|:---|:---|
| Lekérdezés nyelve | Azure Monitor ugyanazt a [Kusto-lekérdezési nyelvet](/azure/kusto/query/) használja, mint az Azure adatkezelő. Lásd: [Azure monitor a naplózási lekérdezés nyelvi eltérései](../articles/azure-monitor/log-query/data-explorer-difference.md) a KQL nyelvi elemeihez Azure monitor nem támogatottak. |
| Azure-régiók | A naplók lekérdezése túlzott terhelést jelenthet, ha az adatLog Analytics több Azure-régióban lévő munkaterületek is átnyúlnak. Részletekért lásd a [lekérdezési korlátokat](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Több erőforrás lekérdezése | Application Insights erőforrások és Log Analytics munkaterületek maximális száma egyetlen lekérdezésben 100-ra korlátozva.<br>Az erőforrások közötti lekérdezés nem támogatott a Tervező nézetében.<br>Az új scheduledQueryRules API támogatja a naplózási riasztásokban lévő erőforrás-lekérdezések közötti lekérdezést.<br>További részletekért lásd: [erőforrások közötti lekérdezési korlátok](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |