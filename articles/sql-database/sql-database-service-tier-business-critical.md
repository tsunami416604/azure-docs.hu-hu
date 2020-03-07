---
title: üzletileg kritikus szolgáltatási szintet
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
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380090"
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

A prémium modellben az Azure SQL Database egyetlen csomóponton integrálja a számítási és a tárolási kapacitást. A magas rendelkezésre állást ebben az építészeti modellben a számítási (SQL Server adatbázismotor-folyamat) és a (helyileg csatlakoztatott SSD-k) a négy csomópontos fürtben üzembe helyezett, a SQL Server [Always On rendelkezésre állási csoportokhoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)hasonló technológia használatával lehet megvalósítani.

![Az adatbázismotor csomópontjainak fürtje](media/sql-database-managed-instance/business-critical-service-tier.png)

Az SQL-adatbázismotor és a mögöttes MDF-/ldf-fájlok is ugyanarra a csomópontra kerülnek, mint a helyileg csatlakoztatott SSD-tároló, amely kis késleltetést biztosít a munkaterhelés számára. A magas rendelkezésre állás a SQL Server [Always On rendelkezésre állási csoportokhoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)hasonló technológia használatával valósul meg. Minden adatbázis olyan adatbázis-csomópontok fürtje, amelyek egy elsődleges adatbázissal elérhetők az ügyfelek munkaterhelése számára, valamint egy három másodlagos folyamat, amely az adatok másolatait tartalmazza. Az elsődleges csomópont folyamatosan leküldi a másodlagos csomópontok módosításait annak érdekében, hogy az adat elérhető legyen a másodlagos replikákban, ha az elsődleges csomópont valamilyen okból összeomlik. A feladatátvételt az SQL Server adatbázismotor kezeli – az egyik másodlagos replika lesz az elsődleges csomópont, és létrejön egy új másodlagos replika, hogy a fürtben elegendő csomópont legyen. A rendszer automatikusan átirányítja a munkaterhelést az új elsődleges csomópontra.

Emellett üzletileg kritikus-fürt beépített [olvasási Felskálázási](sql-database-read-scale-out.md) képességgel rendelkezik, amely ingyenes, beépített írásvédett csomópontot biztosít, amely csak olvasható lekérdezések (például jelentések) futtatására használható, amelyek nem befolyásolhatják az elsődleges munkaterhelés teljesítményét.

## <a name="when-to-choose-this-service-tier"></a>Mikor válassza ezt a szolgáltatási szintet?

Üzletileg kritikus szolgáltatási szintet azon alkalmazások számára tervezték, amelyek alacsony késleltetésű válaszokat igényelnek a mögöttes SSD-tárolóból (átlagosan 1-2 MS), a gyors helyreállítást, ha az alapul szolgáló infrastruktúra meghibásodik, vagy ki kell töltenie a jelentéseket, az elemzéseket és a csak olvasható adatokat az elsődleges adatbázis díjmentesen olvasható másodlagos replikájának lekérdezése.

A legfontosabb ok, amiért érdemes kiválasztania üzletileg kritikus szolgáltatási szintet általános célú szintje helyett:
-   Alacsony IO-késési követelmények – a tárolási réteg gyors válaszát igénylő munkaterhelés (átlagosan 1-2 ezredmásodperc) üzletileg kritikus szintet kell használnia. 
-   Gyakori kommunikáció az alkalmazás és az adatbázis között. Olyan alkalmazás, amely nem tudja kihasználni az alkalmazás-rétegbeli gyorsítótárazást, vagy [igénylést](sql-database-use-batching-to-improve-performance.md) végez, és sok olyan SQL-lekérdezést kell elküldenie, amelyet gyorsan fel kell dolgozni, üzletileg kritikus szinten jó jelöltek.
-   Nagy számú frissítés – az INSERT, a Update és a DELETE művelet módosítja a memóriában (piszkos oldal) található adatlapokat, amelyeket `CHECKPOINT` művelettel rendelkező adatfájlba kell menteni. A lehetséges adatbázismotor-folyamatok összeomlása vagy az adatbázis nagy mennyiségű piszkos oldallal rendelkező feladatátvétele általános célú szinten növelheti a helyreállítási időt. Üzletileg kritikus réteg használata, ha olyan számítási feladattal rendelkezik, amely sok memóriában végzett módosítást okoz. 
-   Az adatmódosítást követően hosszú ideig futó tranzakciók. A hosszú ideig megnyitott tranzakciók megakadályozzák a naplófájlok méretének és a [virtuális naplófájlok (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)számának növelését. A feladatátvétel után a VLF nagy száma lelassíthatja az adatbázis helyreállítását.
-   Munkaterhelések jelentési és elemzési lekérdezésekkel, amelyek átirányíthatók a díjmentes másodlagos írásvédett replikára.
- Nagyobb rugalmasság és gyorsabb helyreállítás a hibáktól. Rendszerhiba esetén az elsődleges példányon lévő adatbázis le lesz tiltva, és az egyik másodlagos replika azonnal új írható-olvasható elsődleges adatbázis lesz, amely készen áll a lekérdezések feldolgozására. Az adatbázismotor nem kell elemezni és megismételni a tranzakciókat a naplófájlból, és be kell töltenie a memória pufferében lévő összes adatmennyiséget.
- Advanced adatsérülés elleni védelem – a üzletileg kritikus szintű adatbázis-replikákat használ az üzletmenet folytonossága érdekében, és így a szolgáltatás automatikusan kihasználja az oldal javítását, amely ugyanazt a technológiát használja SQL Server adatbázis [-tükrözési és rendelkezésre állási csoportok](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)esetében. Abban az esetben, ha egy replika nem tud beolvasni egy oldalt egy adatintegritási probléma miatt, a lap egy másik replikából lesz beolvasva, és az adatvesztés vagy az ügyfél leállása nélkül nem olvasható oldal jelenik meg. Ez a funkció általános célú szinten alkalmazható, ha az adatbázis rendelkezik geo-másodlagos replikával.
- Magasabb rendelkezésre állás – a többszintű konfigurációban üzletileg kritikus szint az 99,995%-os rendelkezésre állást garantálja, az általános célú szint 99,99%-ában.
- A Geo-replikációval konfigurált gyors geo-helyreállítási üzletileg kritikusi réteg 5 sec-os garantált helyreállítási időcélkitűzéssel (RPO) rendelkezik, és 30 mp-es helyreállítási időszakot (RTO) biztosít a telepített órák 100%-ában.

## <a name="next-steps"></a>Következő lépések

- Az erőforrás jellemzői (magok, IO, memória) üzletileg kritikus réteg [felügyelt példányban](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), önálló adatbázis [virtuális mag](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) -modellben vagy [DTU-modellben](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), vagy rugalmas készlet a [virtuális mag-modellben](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) és a [DTU-modellben](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Ismerkedjen meg [általános célú](sql-database-service-tier-general-purpose.md) -és [nagy kapacitású](sql-database-service-tier-hyperscale.md) -szintekkel.
- A [Service Fabric](../service-fabric/service-fabric-overview.md)megismerése.
- A magas rendelkezésre állással és a vész-helyreállítással kapcsolatos további lehetőségekért lásd az [üzletmenet folytonosságát](sql-database-business-continuity.md)ismertető témakört.
