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
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405670"
---
| Korlát | Leírás |
|:---|:---|
| Lekérdezés nyelve | Azure Monitor ugyanazt a [Kusto-lekérdezési nyelvet](/azure/kusto/query/) használja, mint az Azure adatkezelő. Lásd: [Azure monitor a naplózási lekérdezés nyelvi eltérései](../articles/azure-monitor/log-query/data-explorer-difference.md) a KQL nyelvi elemeihez Azure monitor nem támogatottak. |
| Azure-régiók | A naplók lekérdezése túlzott terhelést jelenthet, ha az adatLog Analytics több Azure-régióban lévő munkaterületek is átnyúlnak. Részletekért lásd a [lekérdezési korlátokat](../articles/azure-monitor/log-query/scope.md#query-limits) . |
