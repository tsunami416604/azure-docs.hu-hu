---
title: Üzleti legkritikusabb szolgáltatási szint
description: További információ az Azure SQL Database üzleti legkritikusabb rétegéről
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268677"
---
# <a name="business-critical-tier---azure-sql-database"></a>Üzleti legkritikusabb szint – Azure SQL-adatbázis

> [!NOTE]
> Az üzleti legkritikusabb szint neve Prémium a DTU vásárlási modellben. A virtuálismag-alapú vásárlási modell és a DTU-alapú vásárlási modell összehasonlítását az [Azure SQL Database modellek és erőforrások vásárlása](sql-database-purchase-models.md)című témakörben ismeri.

Az Azure SQL Database az SQL Server Database Engine architektúrán alapul, amely a felhőkörnyezethez van igazítva, hogy még infrastruktúra-hibák esetén is 99,99%-os rendelkezésre állást biztosítson. Az Azure SQL Database három architekturális modellt használ:
- Általános cél/szabvány 
- Üzleti kritikus /Prémium
- Rugalmas skálázás

Prémium/üzleti legkritikusabb szolgáltatási szint modell adatbázismotor-folyamatok fürtje alapul. Ez az architekturális modell azon a tényen alapul, hogy mindig van egy kvóruma a rendelkezésre álló adatbázis-motor csomópontok, és minimális teljesítményhatással van a számítási feladatok ra is karbantartási tevékenységek során.

Az Azure-frissítések és javítások az operációs rendszer, az illesztőprogramok és az SQL Server Database Engine átlátható anammindát, a végfelhasználók minimális állásidejét kivetve. 

A prémium szintű rendelkezésre állás engedélyezve van az Azure SQL Database prémium és üzleti legkritikusabb szolgáltatási szintjein, és olyan intenzív munkaterhelések esetén készült, amelyek a folyamatban lévő karbantartási műveletek miatt nem tűrik a teljesítményre gyakorolt hatást.

A prémium szintű modellben az Azure SQL-adatbázis integrálja a számítási és tárolási egyetlen csomóponton. Ebben az architekturális modellben a négy csomópontfürtben telepített számítási (SQL Server Database Engine folyamat) és tároló (helyileg csatlakoztatott SSD) replikálásával érhető el, az SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)technológiához hasonló technológiával.

![Adatbázis-motor csomópontjainak fürtje](media/sql-database-managed-instance/business-critical-service-tier.png)

Mind az SQL adatbázis-motor folyamata, mind az alapul szolgáló mdf/ldf fájlok ugyanazon a csomóponton helyezkednek el a helyileg csatlakoztatott SSD-tárolóval, amely alacsony késleltetést biztosít a munkaterhelésnek. A magas rendelkezésre állás az SQL Server Always On Availability Groups technológiához hasonló technológiával [valósítva](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)meg. Minden adatbázis adatbáziscsomópontok egy fürtje, amely egy elsődleges adatbázissal, amely elérhető az ügyfél munkaterhelés, és egy három másodlagos folyamatok, amelyek adatok másolatait. Az elsődleges csomópont folyamatosan leküldi a módosításokat a másodlagos csomópontokannak érdekében, hogy az adatok elérhetők legyenek a másodlagos replikákon, ha az elsődleges csomópont bármilyen okból összeomlik. A feladatátvételt az SQL Server Database Engine kezeli – egy másodlagos replika lesz az elsődleges csomópont, és egy új másodlagos replika jön létre, amely elegendő csomópontot biztosít a fürtben. A munkaterhelés automatikusan átirányítja az új elsődleges csomópontra.

Emellett az üzleti legkritikusabb fürt beépített [olvasási horizontális felskálázási](sql-database-read-scale-out.md) képességgel rendelkezik, amely ingyenes, beépített írásvédett csomópontot biztosít, amely csak olvasható lekérdezések (például jelentések) futtatására használható, amelyek nem befolyásolhatják az elsődleges számítási feladatok teljesítményét.

## <a name="when-to-choose-this-service-tier"></a>Mikor válassza ezt a szolgáltatási szintet?

Az üzleti legkritikusabb szolgáltatási szint azokra az alkalmazásokra készült, amelyek alacsony késleltetésű válaszokat igényelnek az alapul szolgáló SSD-tárolóból (átlagosan 1–2 ms), gyors helyreállítást, ha az alapul szolgáló infrastruktúra meghibásodik, vagy jelentéseket, elemzéseket és csak olvasást kell kitölteni az elsődleges adatbázis ingyenesen olvasható másodlagos replikájára.

A legfontosabb okok, hogy miért érdemes az Üzleti legkritikusabb szolgáltatási szintet választani az Általános célú szint helyett:
-   Alacsony i/o-késési követelmények – a számítási feladatok, amelyek a tárolási réteggyors válaszát (átlagosan 1–2 ezredmásodperc) az üzleti kritikus szintű számítási feladatokat kell használniuk. 
-   Gyakori kommunikáció az alkalmazás és az adatbázis között. Alkalmazás, amely nem tudja kihasználni az alkalmazás-réteg gyorsítótárazásvagy [kérelem kötegelés,](sql-database-use-batching-to-improve-performance.md) és kell küldeni sok SQL-lekérdezések, amelyeket gyorsan fel kell dolgozni jó jelöltek üzleti kritikus szintű.
-   Nagy számú frissítések – beszúrása, frissítése és törlése műveletek módosítják az adatlapok a memóriában (piszkos oldal), hogy el kell menteni az adatfájlok `CHECKPOINT` at művelet. Az adatbázis-motor folyamatának lehetséges összeomlása vagy az adatbázis nagy számú piszkos lappal való átvétele növelheti a helyreállítási időt az Általános célú rétegben. Használja az üzleti legkritikusabb szintet, ha olyan számítási feladatokkal rendelkezik, amelyek számos memórián belüli módosítást okoznak. 
-   Hosszú ideig futó tranzakciók, amelyek módosítják az adatokat. A hosszabb ideig megnyitott tranzakciók megakadályozzák a naplófájl csonkolását, ami növelheti a napló méretét és a [virtuális naplófájlok (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)számát. A VLF nagy száma lelassíthatja az adatbázis helyreállítását a feladatátvétel után.
-   Számítási feladatok jelentéskészítési és analitikus lekérdezések, amelyek átirányíthatók az ingyenes másodlagos csak olvasható replika.
- Nagyobb rugalmasság és gyorsabb helyreállítás a hibákból. Rendszerhiba esetén az elsődleges példányon lévő adatbázis le lesz tiltva, és az egyik másodlagos replikák azonnal lesz nek új írás-olvasás elsődleges adatbázis, amely készen áll a lekérdezések feldolgozására. Az adatbázis-kezelőmotornak nem kell elemeznie és újravégrehajtania a naplófájltranzakcióit, és be töltenie az összes adatot a memóriapufferbe.
- Speciális adatsérülés-védelem – Az üzleti szempontból kritikus szintű részleg az adatbázis-replikákat használja a színfalak mögött az üzletmenet folytonossága érdekében, így a szolgáltatás automatikus oldaljavítást is alkalmaz, amely az SQL Server [adatbázis-tükrözési és rendelkezésre állási csoportokhoz](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)használt technológiája. Abban az esetben, ha egy replika adatintegritási probléma miatt nem tud lapolvasni, a lap egy másik replikából származó friss másolatot kér, és az olvashatatlan oldalt adatvesztés vagy ügyfél-állásidő nélkül cseréli le. Ez a funkció általános célú szintre alkalmazható, ha az adatbázis geoszekunder replika rendelkezik.
- Magasabb rendelkezésre állás – A Multi-AZ konfigurációban az üzleti legkritikusabb szint 99,995%-os rendelkezésre állást garantál, szemben az általános célú szint 99,99%-ával.
- Gyors geo-helyreállítási – A georeplikációval konfigurált üzleti legkritikusabb szint garantált helyreállításipont-célkitűzéssel (RPO) rendelkezik, amely 30 mp-es helyreállítási időcélkitűzés (RTO) az üzembe helyezett órák 100%-a számára.

## <a name="next-steps"></a>További lépések

- Keresse meg az üzleti legkritikusabb réteg erőforrás-jellemzőit (magok száma, I/O- és memória) a [felügyelt példányban,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)az egyetlen [adatbázist a virtuálismag-modellben](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) vagy [a DTU-modellben,](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)vagy rugalmas készletet [a virtuálismag-modellben](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) és [a DTU-modellben.](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits)
- Ismerje meg [az általános célú](sql-database-service-tier-general-purpose.md) és a nagy [kapacitású](sql-database-service-tier-hyperscale.md) szinteket.
- További információ a [Service Fabricről.](../service-fabric/service-fabric-overview.md)
- A magas rendelkezésre állás és a vészhelyreállítás további lehetőségeiről az [Üzletmenet folytonossága](sql-database-business-continuity.md)témakörben található.
