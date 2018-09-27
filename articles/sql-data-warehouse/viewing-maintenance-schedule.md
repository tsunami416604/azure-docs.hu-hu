---
title: Az Azure karbantartási ütemezések (előzetes verzió) |} A Microsoft Docs
description: Karbantartási ütemezés lehetővé teszi a felhasználóknak szerte a szükséges ütemezett karbantartási események az Azure SQL Data warehouse szolgáltatást használja, vezethet be új funkciók, frissítések és javítások megtervezéséhez.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228362"
---
# <a name="viewing-a-maintenance-schedule"></a>Karbantartási ütemezés megtekintése 

## <a name="portal"></a>Portál

Azure SQL Data Warehouse példányok egy 8 óra rendszer által meghatározott elsődleges és másodlagos karbantartási időszak üzembe helyezés során a alkalmazni fog rendelkezni. minden újonnan létrehozott, alapértelmezés szerint ez szerkesztheti, hamarosan üzembe helyezés nem fejeződik. Nincs karbantartás előzetes értesítés nélkül a megadott karbantartási időszakokon kívül kerül sor.
A következő lépéseket a portálon az adatraktárba telepített karbantartási ütemezés megtekintéséhez.

A következő lépéseket a portálon az adatraktárba telepített karbantartási ütemezés megtekintéséhez.
1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Válassza ki az adattárház, amely szeretné megtekinteni. 
3.  A kiválasztott Azure SQL Data Warehouse az áttekintő panel nyílik meg. A karbantartási ütemezés (előzetes verzió) alatt jelenik meg a karbantartási ütemezés alkalmazása a kiválasztott data warehouse-bA.

![Áttekintő panelje](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>További lépések
[További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) létrehozása, megtekintése és használata az Azure Monitor riasztások kezelése.
[További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) kapcsolatos naplóriasztási szabály vonatkozó Webhook-műveletek.
[További](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure Service Health szolgáltatással kapcsolatos


