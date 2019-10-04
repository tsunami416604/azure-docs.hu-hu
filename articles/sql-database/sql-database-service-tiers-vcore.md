---
title: Azure SQL Database szolgáltatás – virtuális mag | Microsoft Docs
description: A virtuális mag-alapú vásárlási modell lehetővé teszi a számítási és tárolási erőforrások egymástól független méretezését, a helyszíni teljesítmény egyeztetését és az árak optimalizálását.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 4af269faab21207e1a754e309cac16e5e0a94b69
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164342"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Válasszon a virtuális mag szolgáltatási szintjei közül, és térjen át a DTU szolgáltatási szintjeiről

A Virtual Core (virtuális mag) alapú vásárlási modell lehetővé teszi a számítási és tárolási erőforrások egymástól független méretezését, a helyszíni teljesítmény egyeztetését és az árak optimalizálását. Azt is lehetővé teszi, hogy kiválassza a hardverek generációját:

- **Gen4**: Akár 24 logikai processzor Intel E5-2673 v3 (Haswell) 2,4-GHz processzorok, virtuális mag = 1 PP (fizikai mag), 7 GB/virtuális mag, csatlakoztatott SSD
- **Gen5**: Akár 80 logikai processzor Intel E5-2673 v4 (Broadwell) 2,3-GHz processzorok, virtuális mag = 1 LP (Hyper-thread), 5,1 GB/virtuális mag a kiépített számítási feladatokhoz és akár 24 GB/virtuális mag a kiszolgáló nélküli számításokhoz, gyors eNVM SSD

A Gen4-hardver lényegesen több memóriát kínál virtuális mag. A Gen5-hardverek azonban jóval nagyobb számítási erőforrások vertikális felskálázását teszik lehetővé.

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.
> [!NOTE]
> További információ a DTU-alapú szolgáltatási rétegekről: [szolgáltatási szintek a DTU-alapú vásárlási modellhez](sql-database-service-tiers-dtu.md). További információ a DTU-alapú és a virtuális mag-alapú vásárlási modellek szolgáltatási szintjei közötti különbségekről: [Azure SQL Database vásárlási modellek](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>A szolgáltatási réteg jellemzői

A virtuális mag-alapú vásárlási modell három szolgáltatási szintet biztosít: általános célú, nagy kapacitású és üzleti szempontból kritikus fontosságú. Ezeket a szolgáltatási szinteket számos számítási méret, magas rendelkezésre állású kialakítás, hibatűrési módszerek, a tárolási típusok és méretek, valamint az I/O-tartományok különböztetik meg.

A biztonsági mentések elvégzéséhez külön kell konfigurálni a szükséges tárolási és megőrzési időt. A biztonsági mentés megőrzési időtartamának beállításához nyissa meg a Azure Portal, lépjen a kiszolgálóra (nem az adatbázisra), majd lépjen a **biztonsági másolatok** > kezelése a**házirend** > időpontjának konfigurálása**az idő visszaállításához konfiguráció** > **7 – 35 nap**.

A következő táblázat a három szintje közötti különbségeket ismerteti:

||**Általános célú**|**Üzleti szempontból kritikus**|**Nagy kapacitású**|
|---|---|---|---|
|Ajánlott alkalmazási terület|A legtöbb üzleti számítási feladat. A szolgáltatás költségvetés-orientált, kiegyensúlyozott és méretezhető számítási és tárolási lehetőségeket kínál.|Magas I/O-követelményeknek megfelelő üzleti alkalmazások. Több elkülönített replika használatával maximális rugalmasságot biztosít a hibákhoz.|A legtöbb üzleti számítási feladat nagy mértékben méretezhető tárolási és olvasási méretezési követelményekkel.|
|Compute|**Kiépített számítás**:<br/>Gen4 1 – 24 virtuális mag<br/>Gen5 2 – 80 virtuális mag<br/>**Kiszolgáló nélküli számítás**:<br/>Gen5 0,5 – 16 virtuális mag|**Kiépített számítás**:<br/>Gen4 1 – 24 virtuális mag<br/>Gen5 2 – 80 virtuális mag|**Kiépített számítás**:<br/>Gen4 1 – 24 virtuális mag<br/>Gen5 2 – 80 virtuális mag|
|Memory (Memória)|**Kiépített számítás**:<br/>Gen4 7 GB/virtuális mag<br/>Gen5 5,1 GB/virtuális mag<br/>**Kiszolgáló nélküli számítás**:<br/>Gen5 Akár 24 GB/virtuális mag|**Kiépített számítás**:<br/>Gen4 7 GB/virtuális mag<br/>Gen5 5,1 GB/virtuális mag |**Kiépített számítás**:<br/>Gen4 7 GB/virtuális mag<br/>Gen5 5,1 GB/virtuális mag|
|Storage|Távoli tárterületet használ.<br/>**Önálló adatbázis és rugalmas készlet kiépített számítási**felszámítása:<br/>5 GB – 4 TB<br/>**Kiszolgáló nélküli számítás**:<br/>5 GB – 3 TB<br/>**Felügyelt példány**: 32 GB - 8 TB |A helyi SSD-tárolót használ.<br/>**Önálló adatbázis és rugalmas készlet kiépített számítási**felszámítása:<br/>5 GB – 4 TB<br/>**Felügyelt példány**:<br/>32 GB – 4 TB |A tárterület rugalmas automatikus növekedése igény szerint. Akár 100 TB tárterületet is támogat. A helyi SSD-tárolót használ a helyi puffer-készlet gyorsítótárához és a helyi adattároláshoz. Az Azure-beli távoli tárterületet használja végső hosszú távú adattárként. |
|I/O-átviteli sebesség (hozzávetőleges)|**Önálló adatbázis és rugalmas készlet**: 500 IOPS/virtuális mag legfeljebb 40000 maximális IOPS.<br/>**Felügyelt példány**: A [fájl méretétől](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)függ.|5000 IOPS maximum 200 000 maximális IOPS|A nagy kapacitású egy többrétegű architektúra, több szinten történő gyorsítótárazással. A hatékony IOPs a munkaterheléstől függ.|
|Rendelkezésre állás|1 replika, nincsenek olvasási méretezésű replikák|3 replika, 1 [olvasási léptékű replika](sql-database-read-scale-out.md),<br/>zóna – redundáns magas rendelkezésre állás (HA)|1 írható-olvasható replika, valamint 0-4 [-es olvasási léptékű replika](sql-database-read-scale-out.md)|
|Biztonsági másolatok|[Olvasási hozzáférés – geo-redundáns tárolás (ra-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 nap (alapértelmezés szerint 7 nap)|[Ra-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 nap (alapértelmezés szerint 7 nap)|Pillanatkép-alapú biztonsági másolatok az Azure-beli távoli tárolóban. A visszaállítja ezeket a pillanatképeket a gyors helyreállításhoz. A biztonsági másolatok azonnaliek, és nem befolyásolják a számítási I/O-teljesítményt. A visszaállítások gyorsak, és nem az adatmennyiség (óra vagy nap helyett percekben).|
|Memóriabeli|Nem támogatott|Támogatott|Nem támogatott|
|||

> [!NOTE]
> Az alapszintű szolgáltatási szinten ingyenes Azure SQL-adatbázist szerezhet be az ingyenes Azure-fiókkal együtt. További információkért lásd: [felügyelt felhőalapú adatbázis létrehozása az ingyenes Azure](https://azure.microsoft.com/free/services/sql-database/)-fiókkal.

- További információ a virtuális mag erőforrás-korlátairól: [virtuális mag erőforrás-korlátai egyetlen adatbázisban](sql-database-vcore-resource-limits-single-databases.md) és a [virtuális mag erőforrás-korlátai felügyelt példányokban](sql-database-managed-instance.md#vcore-based-purchasing-model).
- További információ az általános célú és az üzleti szempontból kritikus szolgáltatási szinten: [általános célú és üzleti szempontból kritikus szolgáltatási szintek](sql-database-service-tiers-general-purpose-business-critical.md).
- További információ a nagy kapacitású szolgáltatási szintjéről a virtuális mag-alapú vásárlási modellben: [nagy kapacitású szolgáltatási réteg](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit értékelem

A virtuális mag-alapú vásárlási modell kiépített számítási szintjein a meglévő licenceket a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával SQL Database kedvezményes díjszabásra cserélheti fel. Ez az Azure-kedvezmény lehetővé teszi, hogy Azure SQL Database akár 30%-ot is megtakaríthat a helyszíni SQL Server-licencek frissítési garanciával való használatával.

![díjszabás](./media/sql-database-service-tiers/pricing.png)

A Azure Hybrid Benefit segítségével csak az alapul szolgáló Azure-infrastruktúráért kell fizetnie, ha az SQL-adatbázis motorjának meglévő SQL Server licencét használja (alapszintű számítási díjszabás), vagy a mögöttes infrastruktúráért és a SQL Serverért is fizethet. licenc (licencbe foglalt díjszabás).

A licencelési modellt a Azure Portal használatával vagy a következő API-k egyikével választhatja ki vagy módosíthatja:

- A licenc típusának beállítása vagy frissítése a PowerShell használatával:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- A licenc típusának beállítása vagy frissítése az Azure CLI használatával:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- A licenc típusának beállítása vagy frissítése a REST API használatával:

  - [Adatbázisok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Adatbázisok – frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Felügyelt példányok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Felügyelt példányok – frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrálás a DTU-alapú modellből a virtuális mag-alapú modellbe

### <a name="migrate-a-database"></a>Adatbázisok migrálása

A DTU-alapú vásárlási modellből a virtuális mag-alapú vásárlási modellbe való Migrálás hasonló a standard és prémium szintű szolgáltatási szintek a DTU-alapú vásárlási modellben való frissítéséhez vagy visszalépéséhez.

### <a name="migrate-databases-with-geo-replication-links"></a>Adatbázisok migrálása geo-replikációs hivatkozásokkal

A DTU-alapú modellről a virtuális mag-alapú vásárlási modellre való Migrálás hasonló a standard és prémium szintű szolgáltatási szinten lévő adatbázisok közötti földrajzi replikálási kapcsolatok frissítéséhez vagy visszaminősítéséhez. Az áttelepítés során nem kell leállítania a Geo-replikálást, de a következő sorrendi szabályokat kell követnie:

- A frissítéskor először frissítenie kell a másodlagos adatbázist, majd frissítenie kell az elsődlegest.
- A visszalépéskor a sorrend megfordítása: először az elsődleges adatbázist kell visszaadnia, majd vissza kell állítani a másodlagosat.

Ha a Geo-replikációt két rugalmas készlet között használja, javasoljuk, hogy jelöljön ki egy készletet elsődlegesként, a másikat pedig másodlagosként. Ebben az esetben, ha rugalmas készleteket telepít át, ugyanazt a sorrendi útmutatást kell használnia. Ha azonban olyan rugalmas készletekkel rendelkezik, amelyek elsődleges és másodlagos adatbázisokat is tartalmaznak, akkor a készletet a magasabb kihasználtsággal kell kezelni elsődlegesként, és ennek megfelelően kövesse az előkészítési szabályokat.  

Az alábbi táblázat a speciális áttelepítési forgatókönyvekhez nyújt útmutatást:

|Aktuális szolgáltatási szintek|Cél szolgáltatási szintje|Áttelepítés típusa|Felhasználói műveletek|
|---|---|---|---|
|Standard|Általános célú|Oldalirányú|Az áttelepíthető bármilyen sorrendben, de biztosítani kell a megfelelő virtuális mag-méretezést *|
|Prémium|Üzletileg kritikus|Oldalirányú|Az áttelepíthető bármilyen sorrendben, de biztosítani kell a megfelelő virtuális mag-méretezést *|
|Standard|Üzletileg kritikus|Frissítés|Először át kell telepítenie a másodlagost|
|Üzletileg kritikus|Standard|Visszalépés|Először át kell telepítenie az elsődlegest|
|Prémium|Általános célú|Visszalépés|Először át kell telepítenie az elsődlegest|
|Általános célú|Prémium|Frissítés|Először át kell telepítenie a másodlagost|
|Üzletileg kritikus|Általános célú|Visszalépés|Először át kell telepítenie az elsődlegest|
|Általános célú|Üzletileg kritikus|Frissítés|Először át kell telepítenie a másodlagost|
||||

\*A standard szint 100 DTU legalább 1 virtuális mag kell lennie, és a prémium szint 125 DTU legalább 1 virtuális mag kell lennie.

### <a name="migrate-failover-groups"></a>Feladatátvételi csoportok áttelepíthetők

A több adatbázissal rendelkező feladatátvételi csoportok áttelepítése az elsődleges és a másodlagos adatbázisok egyedi áttelepítését igényli. A folyamat során ugyanazok a megfontolások és az előkészítési szabályok érvényesek. Miután az adatbázisok át lettek konvertálva a virtuális mag-alapú vásárlási modellre, a feladatátvételi csoport ugyanazokkal a házirend-beállításokkal marad érvényben.

### <a name="create-a-geo-replication-secondary-database"></a>Geo-replikációs másodlagos adatbázis létrehozása

A Geo-replikációs másodlagos adatbázist (a Geo-másodlagost) csak az elsődleges adatbázishoz használt szolgáltatási réteg használatával lehet létrehozni. A nagy log-generálási arányú adatbázisok esetében javasoljuk, hogy a Geo-másodlagost ugyanazzal a számítási mérettel hozza létre, mint az elsődleges.

Ha egyetlen elsődleges adatbázishoz hoz létre egy geo-másodlagost a rugalmas készletben, akkor győződjön meg arról `maxVCore` , hogy a készlet beállítása megfelel az elsődleges adatbázis számítási méretének. Ha egy másik rugalmas készletben elsődlegesként hoz létre egy geo-másodlagost, akkor azt javasoljuk, hogy a készletek azonos `maxVCore` beállításokkal rendelkezzenek.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Adatbázis-másolat használata DTU-alapú adatbázis virtuális mag-alapú adatbázisba való átalakításához

A DTU-alapú számítási mérettel rendelkező adatbázisok bármely adatbázisba másolhatók egy virtuális mag-alapú számítási mérettel korlátozás vagy speciális sorrendbe rendezés nélkül, feltéve, hogy a célként megadott számítási méret támogatja a forrásadatbázis maximális adatbázis-méretét. Az adatbázis-másolat létrehoz egy pillanatképet az adatokról a másolási művelet kezdési időpontjáról, és nem szinkronizálja az adatokat a forrás és a cél között.

## <a name="next-steps"></a>További lépések

- Az önálló adatbázisok számára elérhető számítási méretek és a tárolási méretek tekintetében lásd: [SQL Database virtuális mag-alapú erőforrás-korlátok az önálló adatbázisokhoz](sql-database-vcore-resource-limits-single-databases.md).
- A rugalmas készletekhez rendelkezésre álló számítási méretek és a tárhelyek méretére vonatkozó választási lehetőségekért tekintse meg a [rugalmas készletek SQL Database virtuális mag-alapú erőforrás-korlátozásait](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
