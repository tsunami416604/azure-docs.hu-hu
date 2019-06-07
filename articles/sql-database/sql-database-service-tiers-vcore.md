---
title: Az Azure SQL Database szolgáltatás – a virtuális mag |} A Microsoft Docs
description: A Virtuálismag-alapú vásárlási modell lehetővé teszi az egymástól függetlenül méretezheti a számítási és tárolási erőforrások, a helyszíni teljesítmény és optimalizálás ár.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693332"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>A virtuális mag szolgáltatási szintek közül választhat, és telepítse át a dtu-k szolgáltatásszintek

A virtuális mag (más néven vCore)-alapú vásárlási modell lehetővé teszi egymástól függetlenül méretezheti a számítási és tárolási erőforrások, a helyszíni teljesítmény és optimalizálás ár. Ezenkívül lehetővé teszi a hardver generációja kiválasztása:

- **Gen4**: Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzor, a virtuális mag alapján akár 24 logikai CPU = 1 PP (fizikai mag), egy mag, 7 GB-os csatlakoztatott SSD
- **Gen5**: Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzor, a virtuális mag alapján akár 80 logikai CPU = 1. LP (a hyper-szál), 5.1-es GB / core, gyors eNVM SSD

Gen4 hardver / virtuális mag lényegesen több memóriát biztosít. Azonban Gen5 hardver lehetővé teszi a vertikális felskálázása a számítási erőforrások sokkal magasabb.

> [!NOTE]
> A DTU-alapú szolgáltatásszintek kapcsolatos információkért lásd: [szolgáltatásszintek esetében a DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md). A szolgáltatási szintek esetében a DTU-alapú és a Virtuálismag-alapú beszerzési modell közti különbségekkel kapcsolatos további információkért lásd: [vásárlási modellek az Azure SQL Database](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Szolgáltatásszint jellemzők

A Virtuálismag-alapú vásárlási modell három szolgáltatásszinten nyújt: általános célú, nagy kapacitású és kritikus fontosságú üzleti. Szolgáltatásszintek különbözteti meg a számítási méretű, magas rendelkezésre állású kialakításokat, tartalék-elkülönítési, típusú és méretű tárhelyet számos és i/o-címtartományokat.

Külön-külön konfigurálnia kell a szükséges tárolási és a megőrzési időszak a biztonsági mentésekhez. A backup-megőrzési időtartam, nyissa meg az Azure Portalon, nyissa meg a kiszolgáló (az adatbázis nem), és folytassa a **biztonsági másolatok kezelése** > **házirend konfigurálása**  >   **Pont az idő visszaállítása konfiguráció** > **7 – 35 nap**.

Az alábbi táblázat ismerteti a különbségeket a három réteg között:

||**Általános célú**|**Üzletileg kritikus**|**Nagy kapacitású**|
|---|---|---|---|
|A következőkre alkalmas|A legtöbb üzleti számítási feladathoz. Ajánlatok szempontú, elosztott és skálázható számítási és tárolási lehetőségeket.|Üzleti alkalmazások nagy i/o-követelményeket. Több elkülönített replika használatával biztosítja a legmagasabb hibatűrést.|A legtöbb üzleti számítási feladatok kiválóan méretezhető tárolási és olvasási szintű követelményeknek.|
|Compute|**Üzembe helyezett számítási**:<br/>Gen4: 1-24 virtuális mag<br/>Gen5: 2 80 virtuális mag<br/>**Kiszolgáló nélküli számítási**:<br/>Gen5: 0,5 - 4 virtuális mag|**Üzembe helyezett számítási**:<br/>Gen4: 1-24 virtuális mag<br/>Gen5: 2 80 virtuális mag|**Üzembe helyezett számítási**:<br/>Gen4: 1-24 virtuális mag<br/>Gen5: 2 80 virtuális mag|
|Memory (Memória)|**Üzembe helyezett számítási**:<br/>Gen4: 7 GB / virtuális mag<br/>Gen5: 5.1 GB / virtuális mag<br/>**Kiszolgáló nélküli számítási**:<br/>Gen5: 3 GB / virtuális mag|**Üzembe helyezett számítási**:<br/>Gen4: 7 GB / virtuális mag<br/>Gen5: 5.1 GB / virtuális mag |**Üzembe helyezett számítási**:<br/>Gen4: 7 GB / virtuális mag<br/>Gen5: 5.1 GB / virtuális mag|
|Storage|Használ.<br/>**Önálló adatbázis kiépítése a számítási**:<br/>5 GB – 4 TB-IG<br/>**Önálló adatbázis, kiszolgáló nélküli számítási**:<br/>5 GB - 1 TB<br/>**Felügyelt példány**: 32 GB - 8 TB |Helyi SSD-alapú tárolást használ.<br/>**Önálló adatbázis kiépítése a számítási**:<br/>5 GB – 4 TB-IG<br/>**Felügyelt példány**:<br/>32 GB - 4 TB |Az automatikus növekedési rugalmas tárolási igény szerint. Támogatja az akár 100 TB-os tárhelyet. Helyi SSD-tárkapacitáson helyi pufferkészlet-gyorsítótár és a helyi adattárolást használ. Végső hosszú távú adatokat tároló Azure távoli tárolást használ. |
|I/o-teljesítmény (becsült)|**Önálló adatbázis**: 500 IOPS / virtuális mag a 7000-es maximális IOPS-fiókkal.<br/>**Felügyelt példány**: Attól függ, [fájl méretét](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|A maximális iops-érték 200 000 magonként 5000 IOPS|Nagy kapacitású egy többrétegű architektúra gyorsítótár-több szinten. Hatékony IOPs a számítási feladat függ.|
|Rendelkezésre állás|1 replika, olvasási méretű replika.|3 replika, 1 [olvasási szintű replika](sql-database-read-scale-out.md),<br/>zónaredundáns magas rendelkezésre ÁLLÁS|1 írható-olvasható replika, és 0 – 4 [olvasási szintű replikák](sql-database-read-scale-out.md)|
|Biztonsági másolatok|[Írásvédett georedundáns tárolás (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 nap (alapértelmezés szerint 7 nap)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 nap (alapértelmezés szerint 7 nap)|Pillanatkép-alapú biztonsági mentések távoli az Azure storage-ban. Visszaállítás a pillanatképek használata gyors helyreállítás. A biztonsági mentéseket azonnali és számítási i/o-teljesítményt nem befolyásolja. Visszaállítás gyors, és nem a data-méret művelet (véve a perc helyett óra vagy nap).|
|Memóriabeli|Nem támogatott|Támogatott|Nem támogatott|
|||

> [!NOTE]
> Egy ingyenes Azure SQL database az alapszintű szolgáltatásszinten együtt ingyenes Azure-fiókkal kérheti le. További információkért lásd: [felügyelt felhőbeli adatbázis létrehozása az ingyenes Azure-fiók](https://azure.microsoft.com/free/services/sql-database/).

- Virtuális mag erőforrás-korlátozásaival kapcsolatos további információkért lásd: [virtuális mag erőforráskorlátok egyetlen adatbázisban](sql-database-vcore-resource-limits-single-databases.md) és [virtuális mag erőforráskorlátok a felügyelt példány](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Az általános célú és a kritikus fontosságú szolgáltatásszintek üzleti kapcsolatos további információkért lásd: [általános célú és a kritikus fontosságú üzleti szolgáltatásszintek](sql-database-service-tiers-general-purpose-business-critical.md).
- A nagy kapacitású szolgáltatási szinten a Virtuálismag-alapú vásárlási modell kapcsolatos további információkért lásd: [nagy kapacitású szolgáltatásszint](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Kiépített számítási szinten a vCore-alapú vásárlási modell használatával is exchange kedvezményes díjszabást kínál az SQL Database meglévő licenceivel [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Az Azure-értékelemek akár 30 százalékos megtakarítást az Azure SQL Database, frissítési garanciával rendelkező helyszíni SQL Server-licenceivel használatával teszi lehetővé.

![Díjszabás](./media/sql-database-service-tiers/pricing.png)

Az Azure Hybrid Benefittel dönthet úgy, hogy csak azért kell fizetnie az alapul szolgáló Azure-infrastruktúra az SQL-adatbázismotor magát (alap számítási díjszabása) a meglévő SQL Server-licenc használatával, vagy fizetnie mind az alapul szolgáló infrastruktúrát, és az SQL Server a licenc (licenccel együtt kínált díjszabása).

Válassza ki, vagy módosítsa a licencelési modellt, az Azure portal használatával, vagy a következő API-k egyikével:

- Állítsa be, vagy a PowerShell használatával módosítsa a licenc típusa:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Állítsa be, vagy az Azure CLI használatával módosítsa a licenc típusa:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql buszpéldány létrehozása](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql-buszpéldány frissítés](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Állítsa be, vagy a REST API használatával módosítsa a licenc típusa:

  - [-Adatbázis létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Adatbázis - frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Felügyelt példányok – létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Felügyelt példányok – frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>A DTU-alapú modell át a Virtuálismag-alapú modellbe

### <a name="migrate-a-database"></a>Adatbázisok migrálása

Adatbázis áttelepítése az a DTU-alapú vásárlási modell a Virtuálismag-alapú vásárlási modell az hasonlít a frissítést, vagy alacsonyabb verziójúra változtatása a standard és prémium szolgáltatási szinten a DTU-alapú vásárlási modell között.

### <a name="migrate-databases-with-geo-replication-links"></a>Georeplikációs hivatkozásokat tartalmazó adatbázisok migrálása

Áttelepítés a DTU-alapú modell a Virtuálismag-alapú vásárlási modell a hasonlít a frissítést, vagy alacsonyabb verziójúra változtatása a georeplikációs kapcsolatot a standard és prémium szolgáltatási szinteken adatbázisok között. Az áttelepítés során nem kell leállítani a georeplikáció, de ezeket alkalmazás-előkészítés a szabályokat kell követnie:

- Verzióra, először frissítse a másodlagos adatbázist, és az utána frissítse az elsődleges.
- Ha alacsonyabb szolgáltatásszintre, fordított sorrendben: kell az elsődleges adatbázis alacsonyabbra először, és ezután alacsonyabbra a másodlagos.

Ha georeplikációt használ két rugalmas készletek között, azt javasoljuk, hogy jelöljön ki egy készletet, az elsődleges és a többi másodlagos. Ebben az esetben való migráláshoz rugalmas készletek használjon az ugyanazon alkalmazás-előkészítés útmutatót. Ha az elsődleges és másodlagos adatbázist tartalmazó rugalmas készletek, a magasabb kihasználtság a készlet tekinti az elsődleges, és ennek megfelelően kövesse az alkalmazás-előkészítés szabályokat.  

A következő táblázat az adott áttelepítési forgatókönyvre vonatkozó útmutatást tartalmaz:

|Aktuális szolgáltatási rétegben|Cél szolgáltatásszint|Áttelepítés típusa|Felhasználói műveletek|
|---|---|---|---|
|Standard|Általános célú|Oldalirányú|Áttelepítése bármilyen sorrendben is, de kell, hogy a megfelelő virtuális mag méretezési *|
|Prémium|Üzletileg kritikus|Oldalirányú|Áttelepítése bármilyen sorrendben is, de kell, hogy a megfelelő virtuális mag méretezési *|
|Standard|Üzletileg kritikus|Frissítés|Át kell telepítenie a másodlagos először|
|Üzletileg kritikus|Standard|Alacsonyabb szintre|Át kell telepítenie a elsődleges először|
|Prémium|Általános célú|Alacsonyabb szintre|Át kell telepítenie a elsődleges először|
|Általános célú|Prémium|Frissítés|Át kell telepítenie a másodlagos először|
|Üzletileg kritikus|Általános célú|Alacsonyabb szintre|Át kell telepítenie a elsődleges először|
|Általános célú|Üzletileg kritikus|Frissítés|Át kell telepítenie a másodlagos először|
||||

\* Minden 100 dtu-k egységára standard szinten van szükség, legalább 1 virtuális mag, és a prémium szintű minden 125 dtu-k legalább 1 virtuális mag.

### <a name="migrate-failover-groups"></a>Feladatátvételi csoportok áttelepítése

Több adatbázis feladatátvételi csoportok áttelepítése az elsődleges és másodlagos adatbázisok az egyes áttelepítési van szükség. A folyamat során a Ha a fenti szempontok és műveleti sorrend vonatkozik. Miután az adatbázisokat a Virtuálismag-alapú vásárlási modell alakítja, a feladatátvételi csoport érvényben marad az ugyanazon házirend-beállításokkal.

### <a name="create-a-geo-replication-secondary-database"></a>A georeplikáció másodlagos adatbázis létrehozása

A georeplikáció másodlagos adatbázis (geo-secondary) az ugyanazon a szolgáltatásszinten, használja az elsődleges adatbázis hozhat létre. Olyan adatbázisok esetén olyan nagy log-sebességet javasoljuk a geo-secondary számítási mérete megegyezik az elsődleges való létrehozását.

Ha egyetlen elsődleges adatbázishoz a rugalmas készlet geo-secondary próbál létrehozni, ellenőrizze, hogy a `maxVCore` a készlet megegyezik az elsődleges adatbázis számítási méret beállítása. Ha egy másik rugalmas készletben található egy elsődleges geo-secondary próbál létrehozni, azt javasoljuk, hogy a címkészleteknek azonos `maxVCore` beállításait.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Használja az adatbázis DTU-alapú adatbázis konvertálása Virtuálismag-alapú adatbázis

Minden adatbázis DTU-alapú számítási méretű másolhatja vagy speciális alkalmazás-előkészítés, amennyiben a cél számítási méret támogatja a forrásadatbázis adatbázis maximális mérete korlátozások nélkül Virtuálismag-alapú számítási méretű adatbázishoz. Az adatbázis-másolat az adatokat a másolási művelet kezdési időpontja pillanatképet készít, és nem szinkronizálja az adatokat a forrás- és a cél között.

## <a name="next-steps"></a>További lépések

- Az adott számítási méretek és a tároló mérete lehetőségek az önálló adatbázisok számára elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok az önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md).
- Az adott számítási méretek és a tároló mérete választható rugalmas készletek számára elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok a rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
