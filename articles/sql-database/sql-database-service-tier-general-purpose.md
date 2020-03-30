---
title: Általános célú szolgáltatási szint
description: Ismerje meg az Azure SQL Database általános célú rétegét
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
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255885"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Általános célú szolgáltatási szint – Azure SQL-adatbázis

> [!NOTE]
> A virtuálismag-alapú vásárlási modell általános célú szolgáltatási szintjét a DTU-alapú vásárlási modell standard szolgáltatási szintjének nevezzük. A virtuálismag-alapú vásárlási modell és a DTU-alapú vásárlási modell összehasonlítását az [Azure SQL Database modellek és erőforrások vásárlása](sql-database-purchase-models.md)című témakörben ismeri.

Az Azure SQL Database az SQL Server adatbázis-motorarchitektúráján alapul, amelyet a felhőkörnyezethez igazítottak, hogy infrastruktúra-hibák esetén is 99,99%-os rendelkezésre állást biztosítson. Az Azure SQL Database három szolgáltatási szinttel van használatos, amelyek mindegyike különböző architekturális modellekkel van. Ezek a szolgáltatási szintek a következők:

- Általános célú
- Üzleti kritikus
- Rugalmas skálázás

Az általános célú szolgáltatási szint architekturális modellje a számítási és tárolási elkülönítésen alapul. Ez az architekturális modell az Azure Blob storage magas rendelkezésre állására és megbízhatóságára támaszkodik, amely transzparens módon replikálja az adatbázisfájlokat, és garantálja az adatvesztést, ha az alapul szolgáló infrastruktúra meghibásodása megtörténik.

Az alábbi ábrán négy csomópont látható a szabványos architekturális modellben a leválasztott számítási és tárolási rétegekkel.

![A számítás és a tárolás szétválasztása](media/sql-database-managed-instance/general-purpose-service-tier.png)

Az általános célú szolgáltatási szint architekturális modelljében két réteg van:

- Állapot nélküli számítási réteg, amely `sqlservr.exe` fut a folyamat, és csak átmeneti és gyorsítótárazott adatokat tartalmaz (például – terv cache, pufferkészlet, oszlop tárolókészlet). Ezt az állapotmentes SQL Server-csomópontot az Azure Service Fabric üzemelteti, amely inicializálja a folyamatot, szabályozza a csomópont állapotát, és szükség esetén feladatátvételt hajt végre egy másik helyre.
- Állapotalapú adatréteg az Azure Blob storage-ban tárolt adatbázisfájlokkal (.mdf/.ldf). Az Azure Blob storage garantálja, hogy nem lesz adatvesztés bármely rekord, amely bármely adatbázisfájlba kerül. Az Azure Storage beépített adatrendelkezésre állási/redundancia, amely biztosítja, hogy a naplófájlban vagy az adatfájlban lévő összes rekord akkor is megőrződjön, ha az SQL Server folyamat összeomlik.

Amikor az adatbázis-motor vagy az operációs rendszer frissül, az alapul szolgáló infrastruktúra egy része meghibásodik, vagy ha az SQL Server folyamatában kritikus problémát észlel, az Azure Service Fabric áthelyezi az állapotnélküli SQL Server-folyamatot egy másik állapotnélküli számítási csomópontra. Van egy tartalék csomópontkészlet, amely arra vár, hogy új számítási szolgáltatást futtasson, ha az elsődleges csomópont feladatátvételtörténik a feladatátvételi idő minimalizálása érdekében. Az Azure storage-rétegben lévő adatokat ez nem érinti, és az adatok/naplófájlok az újonnan inicializált SQL Server-folyamathoz kapcsolódnak. Ez a folyamat garantálja a 99,99%-os rendelkezésre állást, de előfordulhat, hogy bizonyos teljesítményhatással van a nagy számítási feladatok, amely az átmeneti idő miatt fut, és az a tény, az új SQL Server csomópont kezdődik hideg gyorsítótár.

## <a name="when-to-choose-this-service-tier"></a>Mikor válassza ezt a szolgáltatási szintet?

Általános célú szolgáltatási szint egy alapértelmezett szolgáltatási szint az Azure SQL Database, amely a legtöbb általános számítási feladatok. Ha egy teljes körűen felügyelt adatbázis-motor99,99%-os SLA-val rendelkező, 5 és 10 ms közötti tárolási késéssel, amely a legtöbb esetben megfelel az Azure SQL IaaS-nek, az Általános célú szint az Ön számára.

## <a name="next-steps"></a>További lépések

- Keresse meg az általános cél/standard szint erőforrás-jellemzőit (magok száma, I/O-, memória) [a felügyelt példányban,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)az egyetlen [adatbázist a virtuálismag-modellben](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) vagy [a DTU-modellben,](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)vagy rugalmas készletet [a virtuálismag-modellben](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) és [a DTU-modellben.](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits)
- További információ [az üzleti legkritikusabb](sql-database-service-tier-business-critical.md) és [a nagy méretű](sql-database-service-tier-hyperscale.md) szintekről.
- További információ a [Service Fabricről.](../service-fabric/service-fabric-overview.md)
- A magas rendelkezésre állás és a vészhelyreállítás további lehetőségeiről az [Üzletmenet folytonossága](sql-database-business-continuity.md)témakörben található.
