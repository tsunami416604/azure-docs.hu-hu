---
title: Adatbázis migrációs forgatókönyvének állapota
titleSuffix: Azure Database Migration Service
description: Ismerje meg az Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapotát.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254928"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Az Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapota

Az Azure Database Migration Service különböző áttelepítési forgatókönyvek (forrás/cél párok) támogatására szolgál mind az offline (egyszeri) és az online (folyamatos szinkronizálás) áttelepítések esetében. Az Azure Database Migration Service által biztosított forgatókönyv-lefedettség idővel meghosszabbodik. Az új forgatókönyvek rendszeresen kerülnek hozzáadásra. Ez a cikk azonosítja az Azure Database Migration Service által jelenleg támogatott áttelepítési forgatókönyveket, valamint az egyes forgatókönyvek állapotát (privát előzetes verzió, nyilvános előzetes verzió vagy általános elérhetőség).

## <a name="offline-versus-online-migrations"></a>Offline és online áttelepítések

Az Azure Database Migration Service segítségével offline vagy online áttelepítést is elérhet. *Kapcsolat nélküli* áttelepítésesetén az alkalmazások állásideje az áttelepítés kezdetével egy időben kezdődik. Ha az áttelepítés befejezésekor az új környezetre való átálláshoz szükséges időre szeretné korlátozni az állásidőt, használjon *online* áttelepítést. Javasoljuk, hogy tesztelje az offline áttelepítést annak megállapítására, hogy az állásidő elfogadható-e; ha nem, ne online migráció.

## <a name="migration-scenario-status"></a>Áttelepítési forgatókönyv állapota

Az Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapota idővel változik. A forgatókönyvek általában először **privát előzetes verzióban**jelennek meg. A privát előzetes verzióban való részvételhez az ügyfeleknek a [DMS Preview webhelyen](https://aka.ms/dms-preview)keresztül kell benyújtaniuk a jelölést. A privát előzetes verzió után a forgatókönyv állapota **nyilvános előnézetre**változik. Az Azure Database Migration Service felhasználói közvetlenül a felhasználói felületről próbálhatják ki az áttelepítési forgatókönyveket nyilvános előzetes verzióban. Nincs szükség regisztrációra.  Előfordulhat azonban, hogy a nyilvános előzetes verzióban lévő áttelepítési forgatókönyvek nem érhetők el minden régióban, és a végleges kiadás előtt további módosításokon is áteshetnek. A nyilvános előzetes verzió után a forgatókönyv állapota **általában rendelkezésre állásra**változik. Az általános elérhetőség (GA) a végleges kiadás állapota, és a funkció teljes és minden felhasználó számára elérhető.

## <a name="migration-scenario-support"></a>Áttelepítési forgatókönyv támogatása

Az alábbi táblázatok azt mutatják be, hogy az Azure Database Migration Service használataesetén mely áttelepítési forgatókönyvek támogatottak.

> [!NOTE]
> Ha az alább támogatottként felsorolt forgatókönyv nem jelenik meg a felhasználói felületen, további információkért forduljon az [Azure-adatbázis-áttelepítések megkérdezése](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aliashoz.

> [!IMPORTANT]
> Az Azure Database Migration Service által jelenleg támogatott összes forgatókönyv privát előzetes verzióban való megtekintéséhez tekintse meg a [DMS előzetes verzióját.](https://aka.ms/dms-preview)

### <a name="offline-one-time-migration-support"></a>Offline (egyszeri) áttelepítés támogatása

Az alábbi táblázat az Azure Database Migration Service offline áttelepítések támogatását mutatja be.

| Cél  | Forrás | Támogatás | status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | FE |
|   | Távoli asztali kapcsolat– távoli asztali kapcsolat – SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | FE |
|   | Távoli asztali kapcsolat– távoli asztali kapcsolat – SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL virtuális gép** | SQL Server | ✔ | FE |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | FE |
| **MySQL-hez készült Azure DB** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB for PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Online (folyamatos szinkronizálás) áttelepítés támogatása

Az alábbi táblázat az Azure Database Migration Service online áttelepítések támogatását mutatja be.

| Cél  | Forrás | Támogatás | status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | FE |
|   | Távoli asztali kapcsolat– távoli asztali kapcsolat – SQL | ✔ | FE |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | FE |
|   | Távoli asztali kapcsolat– távoli asztali kapcsolat – SQL | ✔ | FE |
|   | Oracle | ✔ | Privát előnézet |
| **Azure SQL virtuális gép** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | FE |
| **MySQL-hez készült Azure DB** | MySQL | ✔ | FE |
|   | RDS MySQL | ✔ | FE |
| **Azure DB for PostgreSQL** | PostgreSQL | ✔ | FE |
|   | RDS PostgreSQL | ✔ | FE |
|   | Oracle | ✔ | Nyilvános előzetes verzió |

## <a name="next-steps"></a>További lépések

Az Azure Database Migration Service és a regionális elérhetőség áttekintését a [Mi az Azure-adatbázis-áttelepítési szolgáltatás](dms-overview.md)című cikkben találja.
