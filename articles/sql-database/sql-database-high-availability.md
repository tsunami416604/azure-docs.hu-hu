---
title: Magas rendelkezésre állás – Azure SQL Database szolgáltatás |} A Microsoft Docs
description: További tudnivalók az Azure SQL Database szolgáltatás magas rendelkezésre állás és a szolgáltatások
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698229"
---
# <a name="high-availability-and-azure-sql-database"></a>Magas rendelkezésre állású és az Azure SQL Database

Az Azure SQL Database-ben a magas rendelkezésre állású architektúra célja, hogy garantálja, hogy az adatbázis működik, és futó idő 99,99 %-, nem kell bajlódnunk karbantartási műveleteket és a leállások hatásának. Az Azure automatikusan kezeli a kritikus fontosságú karbantartási feladatokat, például a javítás, biztonsági mentések, Windows és az SQL-frissítéseket, valamint nem tervezett esemény, például a mögöttes hardver-, szoftver- vagy hálózati hibák.  Ha az alapul szolgáló SQL-példány telepítve van, vagy átadja a feladatokat, az állásidő nem észrevehető Ha, [újrapróbálkozási logikát alkalmazni](sql-database-develop-overview.md#resiliency) az alkalmazásban. Az Azure SQL Database még akkor is, biztosítva, hogy az adatok mindig elérhető a kritikus fontosságú esetekben állíthatja helyre.

A magas rendelkezésre állású megoldás célja annak biztosítása érdekében, hogy véglegesített adatokat soha nem, hogy a karbantartási műveleteket nem befolyásolják a számítási feladatok, hibák miatt megszakadt, és, hogy az adatbázis nem lesz a szoftver az architektúrában a hibaérzékeny pont. Nincsenek, a karbantartási időszakok vagy állásidőt eredményezhetett, miközben az adatbázis frissítve vagy fenntartott, állítsa le a számítási feladatok elvégzéséhez szükséges. 

Nincsenek használt Azure SQL Database-ben két magas rendelkezésre állású architekturális modellek:

- Standard szintű rendelkezésre állás modell, amely a számítási és tárolási szétválasztása alapul.  A magas rendelkezésre állás és megbízhatóság a távoli tárolási réteg támaszkodik. Ez az architektúra célozza meg, amely tűri néhány teljesítménycsökkenés karbantartásának idejére költségvetés-orientált üzleti alkalmazásokat.
- Prémium szintű rendelkezésre állás modell, amely egy olyan fürtjét, adatbázis-motor folyamatainak alapul. Az a tény, hogy nincs-e mindig elérhető database engine csomópont támaszkodik. Ez az architektúra célozza meg, alapvető fontosságú alkalmazásokat nagy i/o-teljesítményt, magas tranzakciós sebességű és garanciák hatással van a karbantartás idejére a számítási feladatok minimális teljesítményére.

Az Azure SQL Database az SQL Server adatbázismotor és a Windows operációs rendszer legújabb stabil verziója fut, és a felhasználók többsége nem észre, hogy a frissítések a folyamatosan történik.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>A Basic, Standard és az általános célú szolgáltatási szint rendelkezésre állása

Szolgáltatásszintek kihasználhatja a standard szintű rendelkezésre állású architektúra. A következő ábrán látható négy különböző csomópontokon elkülönített számítási és tárolási rétegeket.

![Számítási és tárolási szétválasztása](media/sql-database-high-availability/general-purpose-service-tier.png)

A standard szintű rendelkezésre állás modell tartalmazza-e két réteg:

- Egy állapot nélküli számítási rétegben, amely futtatja a `sqlserver.exe` feldolgozásához, és csak átmeneti és a gyorsítótárazott adatokat a csatlakoztatott SSD, a tartalmaz, mint például a TempDB, a model adatbázis, a tervek gyorsítótárának, a pufferkészletben és a oszlop tárolni készlet. Ez az állapot nélküli csomópont üzemelteti az Azure Service Fabric inicializáló `sqlserver.exe`, a csomópont állapotát vezérli és hajt végre feladatátvételt egy másik csomópontra, ha szükséges.
- Állapot-nyilvántartó adatrétege az Azure Blob storage-ban tárolt adatbázis-fájlok (.mdf/.ldf). Az Azure blob storage-bA beépített rendelkezésre állás és redundancia a szolgáltatás rendelkezik. Ez garantálja, hogy minden rekordot a naplófájl vagy lap az adatfájlban lévő adatok megmaradnak, még akkor is, ha az SQL Server-folyamat leáll.

Amikor az adatbázismotor vagy az operációs rendszer frissítése, vagy hibát észlel, az Azure Service Fabric helyezi át az állapot nélküli SQL Server-folyamat egy másik az állapot nélküli számítási csomóponton elegendő szabad kapacitás. Az Azure Blob storage-adatokat nem érinti az áthelyezés, és az adatokat vagy naplófájlokat is csatlakozik az újonnan inicializált SQL Server folyamatán. Ez a folyamat 99,99 %-os rendelkezésre állást garantál, de nagy terhelést az áttérés során néhány teljesítménycsökkenés tapasztalhat, mivel az új SQL Server-példány hideg gyorsítótáras kezdődik.

## <a name="premium-and-business-critical-service-tier-availability"></a>Prémium és üzletileg kritikus szolgáltatási szint rendelkezésre állása

Prémium és üzletileg kritikus szolgáltatási szintek kihasználhatja a prémium szintű rendelkezésre állás modell, amely integrálható a számítási erőforrások (SQL Server adatbázismotor folyamat) és a storage (helyileg csatlakoztatott SSD) egyetlen csomóponton. Magas rendelkezésre állás további csomópontokat három, négy - csomópont fürtöt hoz létre a számítási és tárolási replikálásával valósul meg. 

![Database engine fürtben](media/sql-database-high-availability/business-critical-service-tier.png)

Az alapul szolgáló adatbázis-fájlok (.mdf/.ldf) a csatolt SSD-tárolóval, adja meg a számítási feladathoz nagyon alacsony késleltetési i/o kerülnek. Magas rendelkezésre állású SQL Server hasonló technológia használatával lett megvalósítva [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). A fürt tartalmaz egy elsődleges replikával (SQL Server-folyamat), amely elérhető ügyfél olvasási és írási számítási feladatok, és legfeljebb három másodlagos replika (számítási és tárolási) tartalmazó adatok másolatát. Az elsődleges csomópont folyamatosan módosítások leküldi a ahhoz a másodlagos csomópontot, és biztosítja, hogy a rendszer szinkronizálja az adatokat legalább egy másodlagos replikára minden tranzakció véglegesítése előtt. Ez a folyamat garantálja, hogy ha az elsődleges csomópont bármilyen okból leáll, mindig van egy teljesen szinkronizált csomópont átadja a feladatokat. A feladatátvétel az Azure Service Fabric által kezdeményezett. A másodlagos replikát az új elsődleges csomópont lesz, miután egy újabb másodlagos replika jön létre, a fürt rendelkezzen elegendő csomópontok (kvórum beállítása). Feladatátvétel befejezése után a SQL-kapcsolatok a rendszer automatikusan átirányítja az új elsődleges csomóponton.

Egy további előny, mint a prémium szintű rendelkezésre állás modell tartalmazza-e irányítható át a csak olvasható az SQL-kapcsolatok a másodlagos replikára. A szolgáltatás neve [olvasási kibővített](sql-database-read-scale-out.md). 100 %-os további számítási kapacitás, és díjmentes arra a célra csak olvasható műveletekhez, például az elemzési számítási feladatok, az elsődleges replikából biztosít.

## <a name="zone-redundant-configuration"></a>Zóna redundáns konfiguráció

Alapértelmezés szerint a fürt a csomópontok a prémium szintű rendelkezésre állás modell létrehozása ugyanabban az adatközpontban. Bevezetésével [Azure-beli rendelkezésre állási zónák](../availability-zones/az-overview.md), SQL Database különböző replikába elhelyezheti a fürt másik rendelkezésre állási zónák ugyanabban a régióban. Kiküszöbölése a meghibásodási pont, a vezérlő körgyűrűs is duplikálódnak több zónában, három átjárókiszolgáló körök (GW). Egy adott átjáró kör útválasztást vezérlik [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Mivel a zóna redundáns konfigurálása prémium szintű és az üzletileg kritikus szolgáltatási szinten nem hoz létre az adatbázis-redundancia, engedélyezheti az nélkül többletköltség. Egy zóna redundáns konfigurációs kiválasztásával teheti a prémium szintű és az üzletileg kritikus adatbázisok rugalmas jóval nagyobb körét a hiba, katasztrofális adatközpont kimaradások, anélkül, hogy az alkalmazás logikáját. A zóna redundáns konfiguráció bármely meglévő prémium és az üzletileg kritikus adatbázisokat vagy készleteket is konvertálható.

Mivel a zóna zónaredundáns adatbázisok az egyes távolságát különböző adatközpontokban replikával rendelkeznek, a hálózati késés növelheti a véglegesítés ideje, és így hatással az egyes OLTP számítási feladatok teljesítményére. Mindig visszatérhet az Egyzónás konfiguráció a zóna redundancia beállítás letiltásával. Ez a folyamat hasonló a normál szint frissítése egy online művelet. A folyamat végén az adatbázis vagy készlet való áttelepítése a zóna redundáns kört a zónában kört vagy fordítva.

> [!IMPORTANT]
> Zóna zónaredundáns adatbázisok és rugalmas készletek jelenleg csak a prémium és üzletileg kritikus szolgáltatási szinten támogatott. Alapértelmezés szerint biztonsági mentések és a naplózási bejegyzések az RA-GRS storage tárolja, és ezért nem lehet automatikusan elérhető egy zóna kiterjedő szolgáltatáskimaradás esetén. 

A magas rendelkezésre állású architektúra redundáns zóna verziója által az alábbi ábra mutatja be:

![magas rendelkezésre állású architektúra zónaredundáns](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Gyorsított adatbázis-helyreállítás (ADR)

[A gyorsított adatbázis-helyreállítás (ADR)](sql-database-accelerated-database-recovery.md) fut egy új SQL database engine szolgáltatás, amely jelentősen csökkenti az adatbázis-elérhetőségi, különösen hosszú folytonosságát tranzakciókat. Automatikus központi telepítési szabály már érhető el az önálló adatbázisok, rugalmas készletek és Azure SQL Data warehouse-bA.

## <a name="conclusion"></a>Összegzés

Az Azure SQL Database egy beépített magas rendelkezésre állású megoldás, amely szorosan integrálva van az Azure platform szolgáltatásait. A Service Fabric hiba észlelése és a helyreállítási, az Azure Blob storage-adatok védelme és a hibatűrés magasabb rendelkezésre állási zónák függő. Emellett az Azure SQL database a replikációt és feladatátvételt az SQL Server Always On rendelkezésre állási csoport technológiáját használja. Ezek a technológiák kombinációja lehetővé teszi az alkalmazások teljes kihasználására – egy vegyes storage előnyei modellezheti, és a legnagyobb erőforrás-igényű SLA-kat támogatja.

## <a name="next-steps"></a>További lépések

- Ismerje meg [Azure-beli rendelkezésre állási zónák](../availability-zones/az-overview.md)
- Ismerje meg [Service Fabric](../service-fabric/service-fabric-overview.md)
- Ismerje meg [az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- A magas rendelkezésre állású és vész-helyreállítási További beállításokért lásd: [üzletmenet-folytonossági](sql-database-business-continuity.md)
