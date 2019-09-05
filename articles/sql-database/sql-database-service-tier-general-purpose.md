---
title: Általános célú szolgáltatási réteg – Azure SQL Database | Microsoft Docs
description: További információ a Azure SQL Database általános célú rétegéről
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 711552501825e5d34d3547ede8dc998065083c96
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566730"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Általános célú szolgáltatási réteg – Azure SQL Database

> [!NOTE]
> A virtuális mag-alapú vásárlási modell általános célú szolgáltatási rétegét a DTU-alapú vásárlási modell standard szintű szolgáltatási rétegének nevezzük. A virtuális mag-alapú vásárlási modellnek a DTU-alapú vásárlási modellel való összehasonlítását lásd: [Azure SQL Database vásárlási modellek és erőforrások](sql-database-purchase-models.md).

A Azure SQL Database a felhőalapú környezethez igazított SQL Server adatbázismotor-architektúrán alapul, hogy az infrastruktúra meghibásodása esetén is biztosítson 99,99%-os rendelkezésre állást. A Azure SQL Databaseben három szolgáltatási szintet használunk, amelyek mindegyike különböző építészeti modellekkel rendelkezik. A szolgáltatási szintek a következők:

- Általános célú
- Üzletileg kritikus
- Rugalmas skálázás

Az általános célú szolgáltatási réteg építészeti modellje a számítás és a tárolás elkülönítésén alapul. Ez az építészeti modell az Azure Blob Storage magas rendelkezésre állására és megbízhatóságára támaszkodik, amely az adatbázis-fájlokat átlátható módon replikálja, és a mögöttes infrastruktúra meghibásodása esetén nem garantálja az adatvesztést.

Az alábbi ábrán a standard építészeti modell négy csomópontja látható a elkülönített számítási és tárolási rétegekkel.

![A számítás és a tárolás elkülönítése](media/sql-database-managed-instance/general-purpose-service-tier.png)

Az általános célú szolgáltatási réteg építészeti modellje két rétegből áll:

- Egy állapot nélküli számítási réteg, amely futtatja a `sqlserver.exe` folyamatot, és csak átmeneti és gyorsítótárazott adatokból áll (például: terv gyorsítótár, puffer készlet, oszlopdiagram-készlet). Ezt az állapot nélküli SQL Server csomópontot az Azure Service Fabric működteti, amely inicializálja a folyamatot, szabályozza a csomópont állapotát, és szükség esetén feladatátvételt hajt végre egy másik helyre.
- Az Azure Blob Storage-ban tárolt, adatbázis-fájlokat (. MDF/. ldf) tartalmazó állapot-nyilvántartó adatréteg. Az Azure Blob Storage garantálja, hogy az adatbázis-fájlokban elhelyezett összes rekord adatvesztést nem eredményez. Az Azure Storage beépített adatelérhetőséget/redundanciát biztosít, amely biztosítja, hogy a naplófájlban vagy az adatfájlban lévő lapok minden rekordja megmaradjon, még akkor is, ha SQL Server folyamat összeomlik.

Az adatbázismotor vagy az operációs rendszer frissítésekor az alapul szolgáló infrastruktúra egy része meghibásodik, vagy ha SQL Server folyamatban valamilyen kritikus problémát észlel, az Azure Service Fabric áthelyezi az állapot nélküli SQL Server folyamatot egy másik állapot nélküli számítási csomópontra. A tartalék csomópontok készlete új számítási szolgáltatás futtatására vár, ha az elsődleges csomópont feladatátvétele a feladatátvételi idő csökkentése érdekében történik. Az Azure Storage-rétegben tárolt adatműveletek nem érintettek, és az adatfájlok és a naplófájlok az újonnan inicializált SQL Server folyamathoz vannak csatolva. Ez a folyamat 99,99%-os rendelkezésre állást garantál, azonban lehetséges, hogy a nagy terhelésű számítási feladatok esetében az átmeneti idő miatt az új SQL Server csomópont a hideg gyorsítótárral kezdődik.

## <a name="when-to-choose-this-service-tier"></a>Mikor válassza ezt a szolgáltatási szintet

Általános célú szolgáltatási szinten a Azure SQL Database alapértelmezett szolgáltatási szintje, amelyet az általános munkaterhelések többsége számára terveztek. Ha a legtöbb esetben egy teljes körűen felügyelt adatbázismotor 99,99%-os SLA-val, valamint 5 és 10 MS közötti tárolási késéssel rendelkezik, amelyek az esetek többségében megfelelnek az Azure SQL-IaaS, általános célú a szint az Ön számára.

## <a name="next-steps"></a>További lépések

- Az erőforrás jellemzői (magok, IO, memória) a felügyelt példányban általános célú/standard [](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)szint, a [virtuális mag](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) -modellben vagy a [DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)-modellben található önálló adatbázis, illetve a [virtuális mag-modell](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) és a [DTU-modell](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits)rugalmas készlete.
- Ismerkedjen meg [üzletileg kritikus](sql-database-service-tier-business-critical.md) -és [nagy kapacitású](sql-database-service-tier-hyperscale.md) -szintekkel.
- A [Service Fabric](../service-fabric/service-fabric-overview.md)megismerése.
- A magas rendelkezésre állással és a vész-helyreállítással kapcsolatos további lehetőségekért lásd az [üzletmenet folytonosságát](sql-database-business-continuity.md)ismertető témakört.
