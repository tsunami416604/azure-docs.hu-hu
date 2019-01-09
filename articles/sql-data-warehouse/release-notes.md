---
title: Az Azure SQL Data Warehouse-kibocsátási megjegyzései |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/11/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: a4737f4308f49547178bb216b90589c596f604c9
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118438"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Az Azure SQL Data Warehouse kibocsátási megjegyzései

Az Azure SQL Data Warehouse egy felhőalapú vállalati adatok adattárház (EDW), amely ötvözi a nagymértékben párhuzamos feldolgozási (MPP) gyorsan összetett lekérdezések futtatása több petabájtnyi adat. Az SQL Data Warehouse a big data-megoldások egyik fő összetevőjeként használható. Importálja a big data-adatokat az SQL Data Warehouse-ba egyszerű PolyBase T-SQL lekérdezések használatával, majd az MPP nyújtotta lehetőségeket kiaknázva nagy teljesítményű elemzéseket futtathat. Az adatok integrálásával és elemzésével az adattárház válik azzá az egyetlen egységes forrássá, ahonnan a vállalat a valós adatokat és kielemzett eredményeket lekérheti.

Kattintson az alábbi hivatkozásokra kattintva további információk az új funkciók és fejlesztések, amelyek az Azure SQL Data Warehouse legújabb verzióját. Ezek a szolgáltatásfrissítések fogadásához az azonosított karbantartási ütemezés során várható.

- [A 2018. december](./release-notes-december-2018.md)
- [2018. október](./release-notes-october-2018.md)
- [2018. szeptember](./release-notes-september-2018.md)
- [2018. augusztus](./release-notes-august-2018.md)
- [2018. július](./release-notes-july-2018.md)
- [2018. június](./release-notes-june-2018.md)
- [2018. május](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>A kód verziója, amely az adatraktár lett alkalmazva ellenőrzése

Ellenőrizze, hogy melyik kiadás lett, az adattárház lett alkalmazva. Csatlakozás az adattárházhoz az SSMS használatával, és futtassa a következő szintaxist az SQL Data Warehouse aktuális verzióját.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Példa a kimenetre: ![Az SQL Data Warehouse-verzió](./media/release-notes/sql_data_warehouse_version.png)

A dátum, győződjön meg arról, melyik kiadás telepítve van az Azure SQL Data Warehouse azonosított használja. 


## <a name="next-steps"></a>További lépések
- [További](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) karbantartási ütemezés megtekintése. 
- [További](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) karbantartási ütemezés módosítása.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) létrehozása, megtekintése és riasztások kezelése az Azure Monitor használatával kapcsolatban.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) kapcsolatos naplóriasztási szabály vonatkozó webhook-műveletek.
- [További](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure Service Health szolgáltatással kapcsolatos
