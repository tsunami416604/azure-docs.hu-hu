---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 1e4ae81c85d8de22bc87f22538c85cdc4c15b79c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555395"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Metrikus riasztások (klasszikus) |100 aktív riasztási szabályok/előfizetés. | Telefonos támogatás |
| Metrikákhoz kapcsolódó riasztások |5 000 aktív riasztási szabályok/előfizetés az Azure Public, az Azure China 21Vianet és a Azure Government felhőkben. Ha eléri ezt a korlátot, vizsgálja meg, hogy használhat-e [azonos típusú több erőforrást használó riasztásokat](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).   | Hívja a támogatási szolgálatot. |
| Tevékenységnapló-riasztások | 100 aktív riasztási szabályok/előfizetés. | Ugyanaz, mint az alapértelmezett |
| Naplóriasztások | 512 aktív riasztási szabályok/előfizetés. 200 aktív riasztási szabályok/erőforrás. | Telefonos támogatás |
| Műveletcsoportok |2 000 műveleti csoport/előfizetés. | Telefonos támogatás |
| Riasztási szabályok és műveleti szabályok leírásának hossza| Naplók keresése riasztások 4096 karakter<br/>Minden egyéb 2048 karakter | Ugyanaz, mint az alapértelmezett |