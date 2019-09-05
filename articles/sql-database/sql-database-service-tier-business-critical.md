---
title: üzletileg kritikus szintű Azure SQL Database szolgáltatás | Microsoft Docs
description: Tudnivalók a Azure SQL Database üzletileg kritikus szintjéről
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 48cde2f96083779bdeb13ba5f39b68c18b395045
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515378"
---
# <a name="business-critical-tier---azure-sql-database"></a>Üzletileg kritikus réteg – Azure SQL Database

> [!NOTE]
> A üzletileg kritikus szint a DTU beszerzési modelljében prémium szintű. A virtuális mag-alapú vásárlási modellnek a DTU-alapú vásárlási modellel való összehasonlítását lásd: [Azure SQL Database vásárlási modellek és erőforrások](sql-database-purchase-models.md).

A Azure SQL Database a felhőalapú környezethez igazított SQL Server adatbázismotor-architektúrán alapul, hogy az infrastruktúra meghibásodása esetén is biztosítson 99,99%-os rendelkezésre állást. A Azure SQL Database három építészeti modellt használ:
- Általános célú/standard 
- Üzletileg kritikus/prémium
- Rugalmas skálázás

A prémium/üzletileg kritikus szolgáltatási szint modellje az adatbázismotor folyamatainak egyik fürtjén alapul. Ez az építészeti modell arra támaszkodik, hogy mindig a rendelkezésre álló adatbázismotor-csomópontok kvóruma van, és a karbantartási tevékenységek során a számítási feladatok minimális teljesítményére is hatással van.

Az Azure-ban az operációs rendszer, az illesztőprogramok és a SQL Server adatbázis-motorja transzparens módon, a végfelhasználók minimális lejárati ideje alatt is átlátható. 

A prémium szintű rendelkezésre állás a prémium és üzletileg kritikus Azure SQL Database szolgáltatási szintjein van engedélyezve, és olyan nagy teljesítményű számítási feladatokhoz lett tervezve, amelyek a folyamatban lévő karbantartási műveletek miatt nem tolerálják a teljesítményt.

A prémium modellben az Azure SQL Database egyetlen csomóponton integrálja a számítási és a tárolási kapacitást. A magas rendelkezésre állást ebben az építészeti modellben a számítási (SQL Server adatbázismotor-folyamat) és a (helyileg csatlakoztatott SSD-k) a négy csomópontos fürtön üzembe helyezett (a következőhöz hasonló technológiával rendelkező, SQL Server [Always On rendelkezésre állási csoportokhoz hasonló technológia használatával) ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Az adatbázismotor csomópontjainak fürtje](media/sql-database-managed-instance/business-critical-service-tier.png)

Az SQL-adatbázismotor és a mögöttes MDF-/ldf-fájlok is ugyanarra a csomópontra kerülnek, mint a helyileg csatlakoztatott SSD-tároló, amely kis késleltetést biztosít a munkaterhelés számára. A magas rendelkezésre állás a SQL Server [Always On rendelkezésre állási csoportokhoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)hasonló technológia használatával valósul meg. Minden adatbázis olyan adatbázis-csomópontok fürtje, amelyek egy elsődleges adatbázissal elérhetők az ügyfelek munkaterhelése számára, valamint egy három másodlagos folyamat, amely az adatok másolatait tartalmazza. Az elsődleges csomópont folyamatosan leküldi a másodlagos csomópontok módosításait annak érdekében, hogy az adat elérhető legyen a másodlagos replikákban, ha az elsődleges csomópont valamilyen okból összeomlik. A feladatátvételt az SQL Server adatbázismotor kezeli – az egyik másodlagos replika lesz az elsődleges csomópont, és létrejön egy új másodlagos replika, hogy a fürtben elegendő csomópont legyen. A rendszer automatikusan átirányítja a munkaterhelést az új elsődleges csomópontra.

Emellett üzletileg kritikus-fürt beépített [olvasási Felskálázási](sql-database-read-scale-out.md) képességgel rendelkezik, amely ingyenes, beépített írásvédett csomópontot biztosít, amely csak olvasható lekérdezések (például jelentések) futtatására használható, amelyek nem érinthetik elsődlegesen a teljesítményt. munkaterhelés.

## <a name="when-to-choose-this-service-tier"></a>Mikor válassza ezt a szolgáltatási szintet?

Üzletileg kritikus szolgáltatási szintet azon alkalmazások számára tervezték, amelyek alacsony késleltetésű válaszokat igényelnek a mögöttes SSD-tárolóból (átlagosan 1-2 MS), a gyors helyreállítást, ha az alapul szolgáló infrastruktúra meghibásodik, vagy ki kell töltenie a jelentéseket, az elemzéseket és a csak olvasható adatokat az elsődleges adatbázis díjmentesen olvasható másodlagos replikájának lekérdezése.

## <a name="next-steps"></a>További lépések

- Az erőforrás jellemzői (magok, IO, memória) üzletileg kritikus réteg [felügyelt példányban](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), önálló adatbázis [virtuális mag](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) -modellben vagy [DTU-modellben](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), vagy rugalmas készlet a [virtuális mag-modellben](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) és a [DTU-modellben](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Ismerkedjen meg [általános célú](sql-database-service-tier-general-purpose.md) -és [nagy kapacitású](sql-database-service-tier-hyperscale.md) -szintekkel.
- A [Service Fabric](../service-fabric/service-fabric-overview.md)megismerése.
- A magas rendelkezésre állással és a vész-helyreállítással kapcsolatos további lehetőségekért lásd az [üzletmenet folytonosságát](sql-database-business-continuity.md)ismertető témakört.
