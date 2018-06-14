---
title: A tervezett karbantartások - Azure SQL Data Warehouse |} Microsoft Docs
description: Ismerje meg, hogyan készíti elő az Azure SQL Data warehouse tervezett karbantartási események.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31800054"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Az Azure SQL data warehouse karbantartása tervezése

Ismerje meg, hogyan készíti elő az Azure SQL data warehouse a tervezett karbantartási események.

## <a name="what-is-a-planned-maintenance-event"></a>Mi az a tervezett karbantartási események?
A tervezett karbantartási események ablak idő, amikor az adatraktár kell lennie a karbantartási műveletet offline állapotban. Ezek az események a lehetőségek a frissítése, új funkciókat vagy javítások alkalmazása. 

## <a name="frequency"></a>Gyakoriság
Legalább egy tervezett karbantartási események átlagosan havonta következik be. 

## <a name="notifications-and-downtime"></a>Értesítések és állásidő
Minden tervezett karbantartási esemény előtt egy értesítést fog kapni. A karbantartási esemény megszakítja az összes futó lekérdezés, és az adatraktár bontja a kapcsolatot. Minden adatraktár várható állásidő körülbelül 30 percet. Egy értesítést is várhatnak, amikor befejeződött a karbantartás. 

## <a name="setting-up-alerts"></a>Riasztások beállítása

Azt javasoljuk, [Azure figyelő](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) beállítása tervezett karbantartás napló riasztásokat. A riasztások segítségével tervezheti meg a szükséges karbantartás biztosít a vállalkozásoknak hatás minimalizálása érdekében. 

Értesítések beállításához használja a [riasztási utasításokat Jelentkezzen](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>További lépések
Figyelésével kapcsolatos további információkért lásd: [a számítási feladat figyeléséhez](sql-data-warehouse-manage-monitor.md).
