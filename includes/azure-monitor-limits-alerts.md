---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0c3cdc21425e0634a725000efb27b3cde0ccd718
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844794"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Metrikus riasztások (klasszikus) |100 aktív riasztási szabályok/előfizetés. | Telefonos támogatás |
| Metrikákhoz kapcsolódó riasztások |5 000 aktív riasztási szabályok/előfizetés az Azure Public, az Azure China 21Vianet és a Azure Government felhőkben. Ha eléri ezt a korlátot, vizsgálja meg, hogy használhat-e [azonos típusú több erőforrást használó riasztásokat](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5 000 metrikai idő sorozatú riasztási szabály. | Hívja a támogatási szolgálatot. |
| Tevékenységnapló-riasztások | 100 aktív riasztási szabály/előfizetés (nem növelhető). | Ugyanaz, mint az alapértelmezett |
| Naplóriasztások | 512 aktív riasztási szabályok/előfizetés. 200 aktív riasztási szabályok/erőforrás. | Telefonos támogatás |
| Riasztási szabályok és műveleti szabályok leírásának hossza| Naplók keresése riasztások 4096 karakter<br/>Minden egyéb 2048 karakter | Ugyanaz, mint az alapértelmezett |
