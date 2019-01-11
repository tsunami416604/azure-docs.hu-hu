---
title: Az Azure SQL Database szolgáltatás – a virtuális mag |} A Microsoft Docs
description: A Virtuálismag-alapú vásárlási modell lehetővé teszi, hogy egymástól függetlenül méretezheti a számítási és tárolási erőforrások, a helyszíni teljesítmény és optimalizálás ár.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 01/08/2019
ms.openlocfilehash: 6b5ff7294735048347c500d64b411f16bda5422f
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54212935"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>virtuális mag szolgáltatásszintek, Azure Hybrid Benefit és migrálása

A Virtuálismag-alapú vásárlási modell lehetővé teszi, hogy egymástól függetlenül méretezheti a számítási és tárolási erőforrások, a helyszíni teljesítmény és optimalizálás ár. Lehetővé teszi hardvertől kiválasztása:

- 4 – általános legfeljebb 24 logikai CPU-alapú Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzor, a virtuális mag = 1 PP (fizikai mag), egy mag, 7 GB-os csatlakoztatott SSD
- 5 – általános legfeljebb 80 logikai CPU-alapú Intel E5-2673 v4 (Broadwell) 2,3 GHz-es processzor, a virtuális mag = 1. LP (a hyper-szál), 5.1-es GB / core, gyors eNVM SSD

Virtuálismag-modell azt is lehetővé teszi, hogy [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) költségmegtakarítást biztosítanak.

> [!NOTE]
> További információ a DTU-alapú szolgáltatásszintek: [DTU-alapú szolgáltatásszintek](sql-database-service-tiers-dtu.md). További információ a DTU-alapú szolgáltatásszintek és a Virtuálismag-alapú szolgáltatásszintek sokoldalúbbá: [vásárlási modellek az Azure SQL Database](sql-database-service-tiers.md).

## <a name="service-tier-characteristics"></a>Szolgáltatási szint tulajdonságok

A Virtuálismag-modell három szolgáltatásszinten, általános célú, nagy kapacitású és kritikus fontosságú üzleti biztosít. Szolgáltatásszintek számos számítási méretű, magas rendelkezésre állás kialakítása, hibák elszigetelését, típusok és tárterület mérete és i/o-tartomány különbözteti meg. Külön-külön konfigurálnia kell a szükséges tárolási és a megőrzési időszak a biztonsági mentésekhez. Az Azure Portalon nyissa meg a kiszolgáló (nem az adatbázis) > felügyelt biztonsági mentés > szabályzat konfigurálása > pont az idő visszaállítása konfiguráció > 7 – 35 nap.

Az alábbi táblázat segítséget nyújt a három réteg közötti különbségeket:

||**Általános célú**|**Üzletileg kritikus**|**Nagy kapacitású (előzetes verzió)**|
|---|---|---|---|
|A következőkre alkalmas|A legtöbb üzleti számítási feladathoz. Ajánlatok költségvetés-orientált elosztott és skálázható számítási és tárolási lehetőségek.|Magas I/O-igényű üzleti alkalmazások. Több elkülönített replika használatával ez biztosítja a legmagasabb hibatűrést.|A legtöbb üzleti célú a rugalmasan skálázható a tárolás és olvasási szintű követelmények|
|Compute|Gen4: 1-24 virtuális mag<br/>Gen5: 1 és 80 virtuális mag|Gen4: 1-24 virtuális mag<br/>Gen5: 1 és 80 virtuális mag|Gen4: 1-24 virtuális mag<br/>Gen5: 1 és 80 virtuális mag|
|Memory (Memória)|Gen4: Magonként 7 GB<br>Gen5: 5.1 GB / mag | Gen4: Magonként 7 GB<br>Gen5: 5.1 GB / mag |Gen4: Magonként 7 GB<br>Gen5: 5.1 GB / mag|
|Storage|Használja a [távoli prémium szintű storage](../virtual-machines/windows/premium-storage.md):<br/>Önálló adatbázis: 5 GB – 4 TB-IG<br/>Felügyelt példány: 32 GB – 8 TB |Helyi SSD-alapú tárolást használ:<br/>Önálló adatbázis: 5 GB – 4 TB-IG<br/>Felügyelt példány: 32 GB – 4 TB-IG |Az automatikus növekedési rugalmas, igény szerint tárhelyet. Támogatja az akár 100 TB tárterület és más alkalmazásokhoz. Helyi SSD-tárhely a helyi puffer készlet cache és a helyi adatok tárolását. Az Azure távoli tárhely végső hosszú távú adatok tárolását. |
|IO-átviteli sebesség (becsült)|Önálló adatbázis: A 7000-es maximális IOPS / virtuális mag 500 IOPS</br>Felügyelt példány: Attól függ, [fájl méretét](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|A maximális iops-érték 200 000 magonként 5000 IOPS|TBD|
|Rendelkezésre állás|1 replika, nincs olvasási szintű|3 replika, 1 [olvasási szintű replika](sql-database-read-scale-out.md),<br/>zóna redundáns magas rendelkezésre ÁLLÁS|?|
|Biztonsági másolatok|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 nap (alapértelmezés szerint 7 nap)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 nap (alapértelmezés szerint 7 nap)|pillanatkép-alapú biztonsági mentés az Azure távoli tároló és a helyreállításokat ezeket a pillanatképeket használni a gyors helyreállítás. A biztonsági mentéseket azonnali, és nincs hatással a számítási i/o-teljesítményét. Visszaállítás nagyon gyors és nem egy adatművelet (véve a perc helyett órák vagy napok) méretét.|
|A memóriában|Nem támogatott|Támogatott|Nem támogatott|
|||

> [!NOTE]
> Egy ingyenes Azure SQL database az alapszintű szolgáltatásszinten, és a egy ingyenes Azure-fiókkal, Fedezze fel az Azure együttes kérheti le. További információ: [felügyelt felhőbeli adatbázis létrehozása az ingyenes Azure-fiók](https://azure.microsoft.com/free/services/sql-database/).

- További információkért lásd: [virtuális mag erőforráskorlátok egyetlen adatbázis](sql-database-vcore-resource-limits-single-databases.md) és [virtuális mag erőforráskorlátok a felügyelt példány](sql-database-managed-instance.md#vcore-based-purchasing-model).
- További információ az általános célú és a kritikus fontosságú üzleti szolgáltatási szintekről: [általános célú és a kritikus fontosságú üzleti szolgáltatási szintekről](sql-database-service-tiers-general-purpose-business-critical.md).
- A Virtuálismag-alapú vásárlási modell a nagy kapacitású szolgáltatásszintre vonatkozó részletekért lásd: [nagy kapacitású szolgáltatásszint](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Ha a számítási kapacitás kevesebb mint 1 virtuális magra van szüksége, használja a DTU-alapú vásárlási modell.

Lásd: [SQL Database: gyakori kérdések](sql-database-faq.md) kapcsolatos gyakori kérdésekre adott válaszokat.

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A Virtuálismag-alapú vásárlási modell, az exchange is kedvezményes díjszabást kínál az SQL Database-adatbázishoz a meglévő licenceit a [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Az Azure-értékelemek lehetővé teszi, hogy a helyszíni SQL Server-licenceivel akár 30 %-os mentése az Azure SQL Database használatával a helyszíni SQL Server-licenceit frissítési garanciával rendelkező.

![díjszabás](./media/sql-database-service-tiers/pricing.png)

Az Azure Hybrid benefittel lehet váltani, csak az alapul szolgáló Azure infrastruktúra az SQL-adatbázismotor magát a meglévő SQL Server-licenc használatával kell fizetnie (**BasePrice**) vagy az alapul szolgáló infrastruktúra kell fizetnie, és az SQL Server-licenc (**LicenseIncluded**). Válassza ki, vagy módosítsa a licencelési modellt, az Azure portal használatával, vagy a következő API-k egyikével.

- Állítsa be, vagy frissítse a PowerShell-lel licenc típusa:

  - [Új AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase):
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql)
  - [Új AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql)

- Állítsa be, vagy frissítse az Azure CLI-vel licenc típusa:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql buszpéldány létrehozása](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql-buszpéldány frissítés](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Állítsa be, vagy frissíteni a licenc típusa, a REST API-val:

  - [-Adatbázis létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Adatbázis - frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Felügyelt példányok – létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Felügyelt példányok – frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Áttelepítés a DTU-modellből Virtuálismag-modell

### <a name="migration-of-a-database"></a>Adatbázis áttelepítése

Adatbázis áttelepítése az a DTU-alapú vásárlási modell a Virtuálismag-alapú vásárlási modell az hasonlít a frissítést, vagy alacsonyabb verziójúra változtatása a DTU-alapú vásárlási modell a Standard és prémium adatbázisok között.

### <a name="migration-of-databases-with-geo-replication-links"></a>Georeplikációs hivatkozásokat tartalmazó adatbázisok migrálása

A DTU-alapú modell alapján a Virtuálismag-alapú modellbe való migrálás hasonlít a frissítést, vagy alacsonyabb verziójúra változtatása a Standard és prémium szintű adatbázisok közötti georeplikációs kapcsolatot. A georeplikáció, de a felhasználó megszakítást okozó meg kell vizsgálni az alkalmazás-előkészítés szabályok nem igényel. Verzióra, először frissítse a másodlagos adatbázist, és az utána frissítse az elsődleges. Ha alacsonyabb szolgáltatásszintre, fordított sorrendben: kell az elsődleges adatbázis alacsonyabbra először, és ezután alacsonyabbra a másodlagos.

Két, rugalmas készletek közötti georeplikációs használatakor javasoljuk, hogy jelöljön ki egy készletet, az elsődleges és a többi – másodlagos. Rugalmas készletek áttelepítése ebben az esetben kövesse az azonos útmutatókat.  Azonban nem, technikailag lehetséges, hogy a rugalmas készlet tartalmazza az elsődleges és másodlagos adatbázisok. Ebben az esetben áttelepítés megfelelően kell kezelni az "elsődleges" a magasabb kihasználtság a készlet, és kövesse az alkalmazás-előkészítés szabályok ennek megfelelően.  

A következő táblázat tartalmazza az adott áttelepítési forgatókönyvre vonatkozó útmutatást:

|Aktuális szolgáltatási rétegben|Cél szolgáltatásszint|Áttelepítés típusa|Felhasználói műveletek|
|---|---|---|---|
|Standard|Általános célú|Oldalirányú|Áttelepítése bármilyen sorrendben is, de kell, hogy a megfelelő virtuális mag méretezési *|
|Prémium|Üzletileg kritikus|Oldalirányú|Áttelepítése bármilyen sorrendben is, de kell, hogy a megfelelő virtuális mag méretezési *|
|Standard|Üzletileg kritikus|Frissítés|Át kell telepítenie a másodlagos először|
|Üzletileg kritikus|Standard|Visszalépés|Át kell telepítenie a elsődleges először|
|Prémium|Általános célú|Visszalépés|Át kell telepítenie a elsődleges először|
|Általános célú|Prémium|Frissítés|Át kell telepítenie a másodlagos először|
|Üzletileg kritikus|Általános célú|Visszalépés|Át kell telepítenie a elsődleges először|
|Általános célú|Üzletileg kritikus|Frissítés|Át kell telepítenie a másodlagos először|
||||

\* Minden 100 DTU standard szintű csomag szükséges legalább 1 virtuális mag, és minden egyes 125 DTU prémium szintű csomag szükséges legalább 1 virtuális mag

### <a name="migration-of-failover-groups"></a>Feladatátvételi csoportok áttelepítése

Több adatbázis feladatátvételi csoportok áttelepítése az elsődleges és másodlagos adatbázisok az egyes áttelepítési van szükség. A folyamat során a Ha a fenti szempontok és műveleti sorrend vonatkozik. Az adatbázisok konvertálja a Virtuálismag-alapú modellbe, miután a feladatátvételi csoport érvényben marad az ugyanazon házirend-beállításokkal.

### <a name="creation-of-a-geo-replication-secondary"></a>A georeplikáció másodlagos létrehozása

Csak a geo-secondary elsődlegesként ugyanazon a szolgáltatásszinten használatával hozhat létre. Adatbázis magas log sebességet javasolt, hogy a másodlagos számítási mérete megegyezik az elsődleges jön létre. Geo-secondary egyetlen elsődleges adatbázishoz a rugalmas készlet létrehozásakor, ezért javasoljuk, hogy rendelkezik-e a készlet a `maxVCore` beállítása, amely megfelel az elsődleges adatbázis számítási mérete. Geo-secondary a egy elsődleges egy másik rugalmas készletben található rugalmas készlet létrehozásakor, ezért javasoljuk, hogy a címkészleteknek azonos `maxVCore` beállításai

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>A DTU-alapú adatbázis konvertálása Virtuálismag-alapú adatbázis adatbázis-másolat használatával

Minden adatbázis DTU-alapú számítási méretű másolhatja vagy speciális alkalmazás-előkészítés, amennyiben a cél számítási méret támogatja a forrásadatbázis adatbázis maximális mérete korlátozások nélkül Virtuálismag-alapú számítási méretű adatbázishoz. Az adatbázis-másolat a másolási művelet kezdési időpontja adatokat pillanatképet készít, és nem hajt végre a forrás- és a cél közötti adatszinkronizálás.

## <a name="next-steps"></a>További lépések

- Részletek az adott számítási méretek és a tároló mérete lehetőségek önálló adatbázis elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok az önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Részletek az adott számítási méretek és a tároló mérete választható rugalmas készletek számára elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok a rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
