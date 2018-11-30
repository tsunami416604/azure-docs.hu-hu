---
title: Az Azure karbantartási ütemezések (előzetes verzió) |} A Microsoft Docs
description: Karbantartási ütemezés lehetővé teszi, hogy az ügyfelek számára a szükséges ütemezett karbantartási események az Azure SQL Data warehouse szolgáltatást használó vezethet be új funkciók, frissítések és javítások körül megtervezése.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 3c683d5819ded4eb3d286969b8c3fc3f0bd42865
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497515"
---
# <a name="view-a-maintenance-schedule"></a>Karbantartási ütemezés megtekintése 

## <a name="portal"></a>Portál

Alapértelmezés szerint minden újonnan létrehozott Azure SQL Data Warehouse-példány rendelkeznek egy 8 órás elsődleges és másodlagos karbantartási időszak, üzembe helyezés során alkalmazni. Módosíthatja a windows, hamarosan üzembe helyezés nem fejeződik. Nincs karbantartás előzetes értesítés nélkül a megadott karbantartási időszakon kívül kerül sor.

A karbantartási ütemezés, amely az adatraktár lett alkalmazva megtekintéséhez kövesse az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Válassza ki az adattárház, amely meg szeretné jeleníteni. 
3.  A kiválasztott data warehouse az áttekintési panelen nyitja meg. Alatt megjelenik a alkalmazni az adatraktár karbantartási ütemezés **karbantartási ütemezés (előzetes verzió)**.

![Áttekintő panelje](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>További lépések
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) létrehozása, megtekintése és riasztások kezelése az Azure Monitor használatával kapcsolatban.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) kapcsolatos naplóriasztási szabály vonatkozó webhook-műveletek.
- [További](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) létrehozása és kezelése a Műveletcsoportok.
- [További](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure Service Health szolgáltatással kapcsolatos.


