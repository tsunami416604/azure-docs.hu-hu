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
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796069"
---
| Korlát | Leírás |
|:---|:---|
| Lekérdezés nyelve | Azure Monitor ugyanazt a [Kusto-lekérdezési nyelvet](/azure/kusto/query/) használja, mint az Azure adatkezelő. Lásd: [Azure monitor a naplózási lekérdezés nyelvi eltérései](../articles/azure-monitor/log-query/data-explorer-difference.md) a KQL nyelvi elemeihez Azure monitor nem támogatottak. |
| Azure-régiók | A naplók lekérdezése túlzott terhelést jelenthet, ha az adatLog Analytics több Azure-régióban lévő munkaterületek is átnyúlnak. Részletekért lásd a [lekérdezési korlátokat](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Több erőforrás lekérdezése | Application Insights erőforrások és Log Analytics munkaterületek maximális száma egyetlen lekérdezésben 100-ra korlátozva.<br>Az erőforrások közötti lekérdezés nem támogatott a Tervező nézetében.<br>Az új scheduledQueryRules API támogatja a naplózási riasztásokban lévő erőforrás-lekérdezések közötti lekérdezést.<br>További részletekért lásd: [erőforrások közötti lekérdezési korlátok](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |
| Lekérdezés szabályozása | A felhasználók 30 másodpercenként legfeljebb 200 lekérdezést foglalhatnak le tetszőleges számú munkaterületen. Ez a korlát a programozott lekérdezésekre, illetve a vizualizációs részek, például az Azure-irányítópultok és a Log Analytics munkaterület összefoglaló lapja által kezdeményezett lekérdezésekre vonatkozik. |
