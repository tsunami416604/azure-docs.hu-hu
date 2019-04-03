---
title: Adatbázis-áttelepítési forgatókönyv állapot |} A Microsoft Docs
description: További információ az Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapotát.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 7017566092530dce2359d45314ac00dca63b8ad0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886233"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapota
Azure Database Migration Service úgy tervezték, hogy különböző áttelepítési forgatókönyveinek (forrás – cél párok) kapcsolat nélküli módban is támogatja (egyszeri) és az online (folyamatos szinkronizálása) áttelepítéseket. Az Azure Database Migration Service által biztosított forgatókönyv lefedettséget idővel történő kiterjesztése. Új forgatókönyvek rendszeresen jelennek meg. Ez a cikk az Azure Database Migration Service és az egyes forgatókönyvek állapota (privát előzetes verzió, nyilvános előzetes verzióban, vagy mindenki számára elérhető) által jelenleg támogatott áttelepítési forgatókönyvek azonosítja.

## <a name="offline-versus-online-migrations"></a>Offline és online áttelepítések
Azure Database Migration Service, az offline vagy egy online migrálás teheti. A *offline* áttelepítéseket alkalmazás üzemszünet megkezdése a migráláshoz kell egy időben. Leállását, mivel a migrálás befejezése után az új környezetben keresztül Kivágás szükséges idő korlátozásához használja egy *online* áttelepítés. Azt javasoljuk, hogy annak megállapításához, hogy az állásidő elfogadható; offline áttelepítés tesztelése Ha nem, hajtsa végre egy online migrálás.

## <a name="migration-scenario-status"></a>Áttelepítési forgatókönyv állapota
Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapotának ideje függ. Általában a forgatókönyvek első kiadásakor a **Private Preview**. Beküldés jelölésre keresztül az ügyfelek részt vesz a privát előzetes verzió van szükség a [DMS Preview-hely](https://aka.ms/dms-preview). Privát előzetes verzió, miután a forgatókönyv állapota **nyilvános előzetes verzióban**. Azure Database Migration Service-felhasználók közvetlenül a felhasználói felület a nyilvános előzetes verzióban érhető el áttelepítési forgatókönyvek kipróbálhatja. Nincs előfizetés nem szükséges.  Nyilvános előzetes verzióban érhető el áttelepítési forgatókönyvek azonban nem érhető el minden régióban, és további változásokon előfordulhat, hogy végleges kiadás előtt. A nyilvános Előzetest követően a forgatókönyv állapota **általánosan elérhető**. Általánosan elérhető (GA)-e a végleges állapotát, és a funkciók teljes és minden felhasználó számára elérhető. 

## <a name="migration-scenario-support"></a>Áttelepítés a forgatókönyv támogatása
Az alábbi táblázatban megtekintheti, mely áttelepítési forgatókönyvek támogatottak, Azure Database Migration Service használata esetén.

> [!NOTE]
> Ha megjelenik a listában, az alábbi támogatott forgatókönyv a felhasználói felületen belül nem jelenik meg, forduljon a [adatok Migrálási csapathoz](mailto:datamigrationteam@microsoft.com) további információt.

> [!IMPORTANT]
> Azure Database Migration Service privát előzetes verzióban által jelenleg támogatott forgatókönyveket, tekintse meg a [DMS Preview-hely](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>A kapcsolat nélküli (egyszeri) áttelepítés támogatása
Az alábbi táblázat az Azure Database Migration Service támogatja az offline áttelepítéshez.

| Cél  | Forrás | Támogatás | status |
| ------------- | ------------- | :-------------: | :-------------: |
| **Azure SQL DB** | SQL Server | ✔ | FE |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | FE |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL virtuális gép** | SQL Server | ✔ | FE |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Nyilvános előzetes verzió |
| **MySQL-hez készült Azure DB** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB for PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Online (folyamatos szinkronizálása) áttelepítés támogatása
Az alábbi táblázat az Azure Database Migration Service, nyilvános előzetes és általánosan elérhető, való online migrálás támogatása.

| Cél  | Forrás | Támogatás | status |
| ------------- | ------------- | :-------------: | :-------------: |
| **Azure SQL DB** | SQL Server | ✔ | FE |
|   | RDS SQL | ✔ | FE |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | FE |
|   | RDS SQL | ✔ | FE |
|   | Oracle | ✔ | Privát előzetes verzió |
| **Azure SQL virtuális gép** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Nyilvános előzetes verzió |
| **MySQL-hez készült Azure DB** | MySQL | ✔ | FE |
|   | RDS MySQL | ✔ | FE |
| **Azure DB for PostgreSQL** | PostgreSQL | ✔ | FE |
|   | RDS PostgreSQL | ✔ | FE |
|   | Oracle | ✔ | Privát előzetes verzió |

## <a name="next-steps"></a>További lépések
Azure Database Migration Service és a régiónkénti rendelkezésre állás áttekintését lásd: a cikk [Mi az az Azure Database Migration Service](dms-overview.md).
