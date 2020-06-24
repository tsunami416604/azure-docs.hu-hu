---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 61bc3933c699f747da10ac6d1b38ad64c76ab681
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793634"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Metrikus riasztások (klasszikus) |100 aktív riasztási szabályok/előfizetés. | Hívja a támogatási szolgálatot. |
| Metrikákhoz kapcsolódó riasztások |5 000 aktív riasztási szabályok/előfizetés az Azure Public, az Azure China 21Vianet és a Azure Government felhőkben. Ha eléri ezt a korlátot, vizsgálja meg, hogy használhat-e [azonos típusú több erőforrást használó riasztásokat](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor).   | Hívja a támogatási szolgálatot. |
| Tevékenységnapló-riasztások | 100 aktív riasztási szabályok/előfizetés. | Ugyanaz, mint az alapértelmezett. |
| Naplóriasztások | 512 aktív riasztási szabályok/előfizetés. 200 aktív riasztási szabályok/erőforrás. | Hívja a támogatási szolgálatot. |
| Műveletcsoportok |2 000 műveleti csoport/előfizetés. | Hívja a támogatási szolgálatot. |
| Az autoskálázás beállításai |100/régió/előfizetés. | Ugyanaz, mint az alapértelmezett. |
| Autoskálázási profilok |20 profil/autoskálázási beállítás. | Ugyanaz, mint az alapértelmezett. |
