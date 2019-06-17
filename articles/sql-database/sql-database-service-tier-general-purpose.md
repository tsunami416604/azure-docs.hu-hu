---
title: Általános célú szolgáltatásszint – Azure SQL Database |} A Microsoft Docs
description: További tudnivalók az Azure SQL Database általános célú csomag
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: b972ea985a09457d8b6a17a292e18754761f5a6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479193"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Általános célú szolgáltatásszint – Azure SQL Database

> [!NOTE]
> Az általános célú szolgáltatási szinten a Virtuálismag-alapú vásárlási modell a standard szintű szolgáltatáscsomagban neve az a DTU-alapú vásárlási modell. A Virtuálismag-alapú vásárlási modell a DTU-alapú vásárlási modell összehasonlításáért lásd: [vásárlási modellek és az erőforrások az Azure SQL Database](sql-database-purchase-models.md).

Az Azure SQL Database az SQL Server adatbázis-motor architektúra 99,99 %-os rendelkezésre állását, még akkor is, az infrastruktúra-hibák esetekben biztosítása érdekében a felhőalapú környezet átalakított alapul. Nincsenek az Azure SQL Database, amelyek mindegyike különböző architekturális modelleket által használt három szolgáltatásszinten. Szolgáltatási szintek a következők:

- Általános célú
- Üzletileg kritikus
- Rugalmas skálázás

Az általános célú szolgáltatási szinten a architekturális modell egy számítási és tárolási szétválasztása alapul. Ez a modell architekturális támaszkodik a magas rendelkezésre állású, és akkor fordul elő, megbízhatóságát, az Azure Blob tároló, amely transzparens módon replikálja az adatbázisfájlokat, és garantálja az adatvesztés nélkül, ha az alapul szolgáló infrastruktúra hibája.

A következő ábrán látható négy csomópont elkülönített számítási és tárolási rétegekkel standard architekturális modellben.

![Számítási és tárolási szétválasztása](media/sql-database-managed-instance/general-purpose-service-tier.png)

Az általános célú szolgáltatásréteg architekturális modellben az alábbi két réteg:

- Állapot nélküli számítási rétegét, amelyen fut a `sqlserver.exe` feldolgozásához, és csak átmeneti és a gyorsítótárazott adatokat tartalmazza (például – tervgyorsítótárból, pufferkészletben, oszlop store készlet). Ez az állapot nélküli SQL Server-csomóponton, inicializálja a folyamatot, a csomópont állapotát vezérli és hajt végre feladatátvételt egy másik helyre, szükség esetén az Azure Service Fabric szolgáltatást.
- Állapot-nyilvántartó adatrétege-adatbázisfájlokat (.mdf/.ldf), amely az Azure Blob storage tárolja. Az Azure Blob storage garantálja, hogy az bármilyen adatbázis-fájlban lévő bármelyik rekordra adatvesztés nélkül lesz. Az Azure Storage rendelkezik beépített rendelkezésre állása/adatredundancia, amely biztosítja, hogy a naplófájl minden rekord vagy adatfájlban lap megőrzi még akkor is, ha az SQL Server-folyamat leáll.

Alapul szolgáló infrastruktúra egy része sikertelen lesz, amikor az adatbázis-kezelő vagy az operációs rendszer frissítve van, vagy az SQL Server-folyamat kritikus probléma észlelése esetén az Azure Service Fabric az állapot nélküli SQL Server-folyamat áthelyezi egy másik az állapot nélküli számítási csomóponton. Nincs meg, ha az elsődleges csomópont feladatátvétel történik, feladatátvételi idő minimalizálása érdekében új számítási szolgáltatás váró tartalék csomópontok. Az Azure storage-rétegben lévő adatok nem érvényesek, és adatokat vagy naplófájlokat csatolt újrainicializált SQL Server-folyamat. Ez a folyamat garantálja a 99,99 %-os rendelkezésre állást, de előfordulhat, hogy rendelkezik néhány teljesítményére nagy terhelést, amely miatt a váltás ideje fut-e a, és azt a tényt az új SQL Server-csomópont hideg gyorsítótáras kezdődik.

## <a name="when-to-choose-this-service-tier"></a>Mikor érdemes a szolgáltatási szint kiválasztása

Általános célú szolgáltatási szinten egy alapértelmezett szolgáltatási rétegben az általános számítási feladatok többsége tervezett, Azure SQL Database-ben. Ha egy teljes körűen felügyelt adatbázis-kezelő a tárolóeszközök késése 5-10 ms, amelyek megfelelnek a legtöbb esetben az Azure az SQL IaaS között 99,99 %-os SLA-val, általános célú csomagban lehetőség az Ön számára.

## <a name="next-steps"></a>További lépések

- Keresse meg az erőforrás tulajdonságait (mag, IO, memória száma) az általános célú/Standard szintű [felügyelt példány](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), egyetlen adatbázis-kezelő [Virtuálismag-modell](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) vagy [DTU modell](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes), vagy A rugalmas készlet [Virtuálismag-modell](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) és [DTU modell](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Ismerje meg [üzletileg kritikus fontosságú](sql-database-service-tier-business-critical.md) és [nagy kapacitású](sql-database-service-tier-hyperscale.md) szinten.
- Ismerje meg [Service Fabric](../service-fabric/service-fabric-overview.md).
- A magas rendelkezésre állású és vész-helyreállítási További beállításokért lásd: [üzletmenet-folytonossági](sql-database-business-continuity.md).
