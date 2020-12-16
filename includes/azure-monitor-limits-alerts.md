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
ms.openlocfilehash: 2f02f9dcc47d41917112dfac21fcbe0f0e02d6ff
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97532036"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Metrikus riasztások (klasszikus) |100 aktív riasztási szabályok/előfizetés. | Telefonos támogatás |
| Metrikákhoz kapcsolódó riasztások |5 000 aktív riasztási szabályok/előfizetés az Azure Public, az Azure China 21Vianet és a Azure Government felhőkben. Ha eléri ezt a korlátot, vizsgálja meg, hogy használhat-e [azonos típusú több erőforrást használó riasztásokat](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).   | Hívja a támogatási szolgálatot. |
| Tevékenységnapló-riasztások | 100 aktív riasztási szabály/előfizetés (nem növelhető). | Ugyanaz, mint az alapértelmezett |
| Naplóriasztások | 512 aktív riasztási szabályok/előfizetés. 200 aktív riasztási szabályok/erőforrás. | Telefonos támogatás |
| Riasztási szabályok és műveleti szabályok leírásának hossza| Naplók keresése riasztások 4096 karakter<br/>Minden egyéb 2048 karakter | Ugyanaz, mint az alapértelmezett |
