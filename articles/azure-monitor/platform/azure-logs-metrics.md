---
title: Naplók és mérőszámok az Azure-ban | Microsoft Docs
description: Az Azure-beli diagnosztikai naplók áttekintése, amelyek gazdag és gyakori információkat biztosítanak az Azure-erőforrások működéséről.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262361"
---
# <a name="logs-and-metrics-in-azure"></a>Naplók és mérőszámok az Azure-ban
Különböző [naplók](data-platform-logs.md) és [mérőszámok](data-platform-metrics.md) vannak

Az Azure-beli alkalmazások és szolgáltatások figyelése elkülöníthető az Azure- [adatplatformon tárolt adatokkal](data-platform.md). 

A naplók és a metrikák két kategóriába sorolhatók

- Platform – naplók és metrikák, amelyeket a rendszer a konfigurációtól eltérő kötelezően automatikusan generál, nem csupán 



| Réteg | Platform naplói | Platform Metrikái | Egyéni naplók | Egyéni metrikák |
|:---|:---|:---|:---|:---|
| Alkalmazás  | | | | |
| Vendég operációs rendszer     | Szívverés |  | Diagnosztikai bővítmény<br>Log Analytics ügynök | Diagnosztikai bővítmény |
| Resource     | [Erőforrás-naplók](resource-logs-overview.md)<br>(az egyes szolgáltatásokra jellemző) | [Erőforrás-metrikák](metrics-supported.md)<br>(az egyes szolgáltatásokra jellemző) | | [Egyéni metrikák](metrics-custom-overview.md) |
| Subscription | [Tevékenységnapló](activity-logs-overview.md) | | | |
| Bérlő       | 

## <a name="next-steps"></a>További lépések

* [Az erőforrás-diagnosztikai naplók Stream **az Event Hubs**](resource-logs-stream-event-hubs.md)
* [Módosítsa az erőforrás diagnosztikai beállításait az Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/)
* [Naplók elemzése az Azure Storage-ból Azure Monitor](collect-azure-metrics-logs.md)
