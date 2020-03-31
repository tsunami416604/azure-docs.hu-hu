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
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74796069"
---
| Korlát | Leírás |
|:---|:---|
| Lekérdezés nyelve | Az Azure Monitor ugyanazt a [Kusto lekérdezési nyelvet](/azure/kusto/query/) használja, mint az Azure Data Explorer. Tekintse meg az [Azure Monitor naplózási lekérdezési nyelvi különbségek](../articles/azure-monitor/log-query/data-explorer-difference.md) a KQL nyelvi elemek nem támogatott az Azure Monitor. |
| Azure-régiók | A naplólekérdezések túlzott terhelést tapasztalhatnak, ha az adatok több Azure-régióban is átnyúlnak a Log Analytics-munkaterületeken. A részleteket a [Lekérdezési korlátok](../articles/azure-monitor/log-query/scope.md#query-limits) ban találja. |
| Erőforrásközi lekérdezések | Az Application Insights-erőforrások és a Log Analytics-munkaterületek maximális száma egyetlen, 100-ra korlátozott lekérdezésben.<br>A Nézettervező nem támogatja az erőforrásközi lekérdezést.<br>Az új scheduledQueryRules API támogatja az erőforrások közötti lekérdezést a naplóriasztásokban.<br>A részletekért lásd az [erőforrásközi lekérdezési korlátokat.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |
| Lekérdezés szabályozása | A felhasználó 30 másodpercenként 200 lekérdezésre van korlátozva tetszőleges számú munkaterületen. Ez a korlát az automatizált lekérdezésekre vagy a vizualizációs részek, például az Azure-irányítópultok és a Log Analytics munkaterület összefoglaló lapja által kezdeményezett lekérdezésekre vonatkozik. |
