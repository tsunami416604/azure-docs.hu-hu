---
title: Adatbázis-áttelepítési forgatókönyv állapot |} A Microsoft Docs
description: További információ az Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapotát.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/08/2019
ms.openlocfilehash: 9e153cca321e94233cfda2a03cf52ba85a0f6b02
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102805"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Az Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapota
Az Azure Database Migration Service úgy tervezték, hogy különböző áttelepítési forgatókönyveinek (forrás – cél párok) kapcsolat nélküli módban is támogatja (egyszeri) és az online (folyamatos szinkronizálása) áttelepítéseket. Az Azure Database Migration Service által biztosított forgatókönyv lefedettséget idővel történő kiterjesztése. Új forgatókönyvek rendszeresen jelennek meg. Ez a cikk azonosítja az jelenleg támogatja az Azure Database Migration Service és az állapot-áttelepítési forgatókönyvek (privát [vagy korlátozott] Preview-ban, nyilvános előzetes és általánosan elérhető) vagy az egyes forgatókönyvek.

## <a name="offline-versus-online-migrations"></a>Offline és online áttelepítések
Ha adatbázisokat telepít át az Azure-bA az Azure Database Migration Service segítségével, offline vagy egy online migrálás is elvégezheti. A *offline* áttelepítéseket alkalmazás üzemszünet megkezdése a migráláshoz kell egy időben. A *online* áttelepítések, állásidő korlátozódik, az áttelepítés befejezése után az új környezetben keresztül Kivágás szükséges időt. Azt javasoljuk, hogy annak megállapításához, hogy az állásidő elfogadható; offline áttelepítés tesztelése Ha nem, online történő áttelepítés.

## <a name="migration-scenario-status"></a>Áttelepítési forgatókönyv állapota
Az egyes áttelepítési forgatókönyvek az Azure Database Migration Service támogatja az állapot ideje függ. Általában a forgatókönyvek első kiadásakor a **Private Preview**, és a funkció kihasználásával megköveteli, hogy egy ügyfél keresztül nevezés beküldése a [DMS Preview-hely](https://aka.ms/dms-preview). Amikor befejeződött a privát előzetes verzió, a forgatókönyv állapota **nyilvános előzetes verzióban**. Az összes Azure Database Migration Service-felhasználók is igénybe vehet a nyilvános előzetes verzióban érhető el áttelepítési forgatókönyvek előnyeit. Azonban az áttelepítés során nem érhető el minden régióban, és a funkciók további változásokon előfordulhat, hogy a végleges kiadás előtt. Ha az áttelepítési forgatókönyvben válik **általánosan elérhető**, a végső, engedélyezett állapotát, funkciója teljes és az összes Azure Database Migration Service-felhasználók számára is elérhető. 

## <a name="migration-scenario-support"></a>Áttelepítés a forgatókönyv támogatása

Az alábbi táblázatban megtekintheti, mely áttelepítési forgatókönyvek támogatottak, amikor az Azure Database Migration Service segítségével.

> [!NOTE]
> Ha megjelenik a listában, az alábbi támogatott forgatókönyv a felhasználói felületen belül nem jelenik meg, forduljon a [adatok Migrálási csapathoz](mailto:datamigrationteam@microsoft.com) további információt.

### <a name="offline-one-time-migration-support"></a>A kapcsolat nélküli (egyszeri) áttelepítés támogatása
Az alábbi táblázat az Azure Database Migration Service támogatja az offline áttelepítéshez.

| Cél  | Forrás | Támogatás |
| ------------- | ------------- | :-------------: |
| **Az Azure SQL DB**  | SQL Server | ✔ |
|   | A TÁVOLI ASZTALI SZOLGÁLTATÁSOK SQL  |  ✔ |
|   | Oracle  |   |
| **Az Azure SQL DB Buszpéldány**  | SQL Server  | ✔ |
|   | A TÁVOLI ASZTALI SZOLGÁLTATÁSOK SQL  | ✔ |
|   | Oracle  | ✔  |
| **Az Azure SQL virtuális gép**  | SQL Server  | ✔ |
|   | Oracle  |   |
| **Cosmos DB**  | MongoDB  | ✔ |
| **MySQL-hez készült Azure DB**  | MySLQ  |  |
|   | RDS MySQL  |  |
| **Azure DB for PostgresSQL**  | PostgreSQL |  |
|  | A távoli asztali szolgáltatások PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>Online (folyamatos szinkronizálása) áttelepítés támogatása
Az alábbi táblázat az Azure Database Migration Service támogatása online áttelepítéseket.

| Cél  | Forrás | Támogatás |
| ------------- | ------------- | :-------------: |
| **Az Azure SQL DB**  | SQL Server | ✔ |
|   | A TÁVOLI ASZTALI SZOLGÁLTATÁSOK SQL  |   |
|   | Oracle  |  ✔ |
| **Az Azure SQL DB Buszpéldány**  | SQL Server  | ✔ |
|   | A TÁVOLI ASZTALI SZOLGÁLTATÁSOK SQL  |  |
|   | Oracle  | ✔  |
| **Az Azure SQL virtuális gép**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Cosmos DB**  | MongoDB  | ✔ |
| **MySQL-hez készült Azure DB**  | MySLQ  | ✔ |
|   | RDS MySQL  | ✔ |
| **Azure DB for PostgresSQL**  | PostgreSQL | ✔ |
|  | A távoli asztali szolgáltatások PostgreSQL  | ✔ |

## <a name="next-steps"></a>További lépések
Az Azure Database Migration Service és a régiónkénti rendelkezésre állás áttekintését lásd: a cikk [Mi az az Azure Database Migration Service](dms-overview.md). 
