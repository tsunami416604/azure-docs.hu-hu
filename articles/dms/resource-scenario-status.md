---
title: Adatbázis migrációs forgatókönyvének állapota
titleSuffix: Azure Database Migration Service
description: A Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapotának megismerése.
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
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254928"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>A Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapota

Azure Database Migration Service úgy lett kialakítva, hogy támogassa a különböző áttelepítési forgatókönyveket (forrás/cél párok) mind a kapcsolat nélküli (egyszeri), mind az online (folyamatos szinkronizálás) áttelepítéshez. Az Azure Database Migration Service által biztosított forgatókönyv-lefedettség időbeli kibővítése folyamatban van. Az új forgatókönyvek hozzáadása rendszeresen történik. Ez a cikk a Azure Database Migration Service által jelenleg támogatott áttelepítési forgatókönyveket, valamint az egyes forgatókönyvek (privát előzetes verzió, nyilvános előzetes verzió vagy általános rendelkezésre állás) állapotát azonosítja.

## <a name="offline-versus-online-migrations"></a>Offline és online áttelepítés

A Azure Database Migration Service offline vagy online áttelepítést is végezhet. Az *Offline* áttelepítések esetén az alkalmazás leállása az áttelepítés megkezdésének időpontjában kezdődik. Ha korlátozni szeretné az állásidőt az új környezetbe való áttelepítéshez szükséges időtartamra, használja az *online* áttelepítést. Javasoljuk, hogy tesztelje az offline áttelepítést annak megállapításához, hogy az állásidő elfogadható-e; Ha nem, akkor végezzen online áttelepítést.

## <a name="migration-scenario-status"></a>Áttelepítési forgatókönyv állapota

A Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapota az idő függvényében változhat. A forgatókönyvek általában **privát előzetes**verzióban jelennek meg. A privát előzetes verzióban való részvételhez az szükséges, hogy az ügyfelek a [DMS előzetes](https://aka.ms/dms-preview)verziójának webhelyén küldjenek jelölést. A privát előzetes verzió után a forgatókönyv állapota **nyilvános előzetesre**változik. Azure Database Migration Service felhasználók közvetlenül a felhasználói felületről kipróbálhatják a nyilvános előzetes verzióban elérhető áttelepítési forgatókönyveket. Nincs szükség regisztrációra.  Előfordulhat azonban, hogy a nyilvános előzetes verzióban az áttelepítési forgatókönyvek nem érhetők el minden régióban, és a végleges kiadás előtt további változtatások is előfordulhatnak. A nyilvános előzetes verzió után a forgatókönyv állapota **általánosan elérhetővé**vált. Az általános elérhetőség (GA) a végső kiadás állapota, és a funkciók teljesek, és minden felhasználó számára elérhetők.

## <a name="migration-scenario-support"></a>Áttelepítési forgatókönyv támogatása

Az alábbi táblázatok azt mutatják be, hogy mely áttelepítési forgatókönyvek támogatottak Azure Database Migration Service használatakor.

> [!NOTE]
> Ha az alábbi módon felsorolt forgatókönyvek nem jelennek meg a felhasználói felületen, forduljon az [Azure Database áttelepítési alias kérése](mailto:AskAzureDatabaseMigrations@service.microsoft.com) további információért.

> [!IMPORTANT]
> Ha a Azure Database Migration Service által jelenleg támogatott összes forgatókönyvet szeretné megtekinteni a privát előzetes verzióban, tekintse meg a [DMS előzetes](https://aka.ms/dms-preview)verzióját.

### <a name="offline-one-time-migration-support"></a>Offline (egyszeri) Migrálás támogatása

Az alábbi táblázat az offline áttelepítések Azure Database Migration Service támogatását mutatja be.

| Cél  | Forrás | Támogatás | status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-adatbázis** | SQL Server | ✔ | FE |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | FE |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL virtuális gép** | SQL Server | ✔ | FE |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | FE |
| **MySQL-hez készült Azure-adatbázis** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **PostgreSQL-hez készült Azure-adatbázis** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Online (folyamatos szinkronizálás) áttelepítési támogatás

Az alábbi táblázat az online áttelepítések Azure Database Migration Service támogatását mutatja be.

| Cél  | Forrás | Támogatás | status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-adatbázis** | SQL Server | ✔ | FE |
|   | RDS SQL | ✔ | FE |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | FE |
|   | RDS SQL | ✔ | FE |
|   | Oracle | ✔ | Privát előzetes verzió |
| **Azure SQL virtuális gép** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | FE |
| **MySQL-hez készült Azure-adatbázis** | MySQL | ✔ | FE |
|   | RDS MySQL | ✔ | FE |
| **PostgreSQL-hez készült Azure-adatbázis** | PostgreSQL | ✔ | FE |
|   | RDS PostgreSQL | ✔ | FE |
|   | Oracle | ✔ | Nyilvános előzetes verzió |

## <a name="next-steps"></a>Következő lépések

A Azure Database Migration Service és a regionális elérhetőség áttekintését lásd: [Mi a Azure Database Migration Service](dms-overview.md).
