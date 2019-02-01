---
title: Az Azure SQL Database Premium RS szolgáltatási szint használatból való kivonást egyaránt |} A Microsoft Docs
description: A prémium RS szolgáltatási rétegben kivezetjük és támogatása, a befejezési – tekintse meg az áttelepítési lehetőségek.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c223b55e8854c9d304354d917e07f4dd7720e72c
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507810"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Az Azure SQL adatbázis Premium RS szolgáltatási szint (előzetes verzió) kivezetjük - áttelepítési lehetőségek

2018 Februárjától a Microsoft bejelentette, hogy az Azure SQL Database, a prémium RS szolgáltatási rétegben elérhetőség nem lenne elérhető, és a 2019. január 31. után már nem szeretne támogatott. A támogatási határidő végén 2019. június 30. most már elérhető. Ez a cikk ismerteti az áttelepítés a Premium RS szolgáltatási rétegben egy másik szolgáltatási szinten a lehetőségei. 2019. június 30. után a Microsoft lesz automatikusan az adatbázisokat a prémium RS, amely a legjobban illik a prémium RS adatbázis teljesítmény-követelmények általánosan elérhető a szolgáltatási réteg.

A migrálás célhelyek és díjszabási lehetőségeit, amely lehet prémium RS ügyfelek alkalmas a következők:

- szolgáltatásszintek virtuális mag

  A **általános célú** és **üzletileg kritikus fontosságú** a szolgáltatáshoz a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md). Ezek a két szolgáltatási csomagok lehet az általánosan elérhető. A Virtuálismag-alapú vásárlási modell is kínál a **nagy kapacitású** szolgáltatásréteg (nyilvános előzetes verzióban elérhető), amely alkalmazkodik a számítási feladat igényeinek megfelelően az automatikus skálázást adatbázisonként legfeljebb 100 TB-os igény. A nagy kapacitású szolgáltatásszint összehasonlítható a prémium szintű szolgáltatási rétegben lévő i/o-teljesítményt nyújt a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) áron közelebb a prémium RS szolgáltatási szintre.
- Dev/Test-díjszabás

  [Dev/test díjszabása](https://azure.microsoft.com/pricing/dev-test/) és díjszabás licenccel akár 55 % megtakarítás biztosít a Visual Studio-előfizetésében.
- Az Azure Hybrid Benefit és tartalékkapacitást díjszabása

  [Az Azure Hybrid Benefit és tartalékkapacitást díjszabás](https://azure.microsoft.com/pricing/details/sql-database/) adja meg a megtakarítások és díjszabás licenccel akár 80 %. Ezekről a lehetőségekről további információkért lásd: [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) és [Azure SQL Database szolgáltatás számára fenntartott kapacitás](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Intézkedjen most, hogy az a prémium RS adatbázisokat alternatív SQL Database szolgáltatási csomagjai

Tekintse át az ebben a cikkben együtt a díjszabás és a prémium RS számítási feladatokhoz a megfelelő áttelepítési cél(ok) dokumentációjában található útmutatást.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Nagy számítási igényű számítási feladatok migrálása és mentése

A nagy számítási igényű a prémium RS számítási feladatokhoz javasoljuk, hogy az általánosan elérhető Virtuálismag-alapú általános célú szolgáltatásszinthez, és mentse el és a licenccel együtt kínált díjak az SQL Server és a lefoglalt kapacitás ajánlatok az Azure Hybrid Benefit használatával több áttelepítése. Inkább a DTU-alapú vásárlási lehetőség, ha szeretne továbbra is, ha az adatbázisokat nagy számítási igényű a prémium RS a Standard szolgáltatásszinten, és a prémium RS díjszabása (ha általánosan ment), és továbbra is menteni.

> [!WARNING]
> Prémium RS számítási feladatok migrálása az DTU-alapú prémium szintű szolgáltatási csomagokra, növelheti a havi költség-és aktuális árképzési prémium RS. Azt javasoljuk, hogy a nagy kapacitású és az üzletileg kritikus szintet az Azure Hybrid Benefit és tartalékkapacitást díjszabás, mint a prémium RS hasonló vagy alacsonyabb költségek kezelése a mérlegeli.

### <a name="premium-rs-databases"></a>Prémium RS adatbázisok

|**Ha jelenleg...**|**Át összehasonlítható Virtuálismag-alapú...**|**Át összehasonlítható DTU-alapú...**|
|---|---|---|
|1 prémium RS|Általános célú 1 virtuális mag (4. generációs)|Standard 3|
|2. prémium RS|Általános célú, 2 virtuális mag (4. generációs)|Standard 4|
|Prémium RS 4|Általános célú, 4 virtuális magra jogosult (Gen4)|Standard 6|
|Prémium RS 6|Általános célú 6 virtuális magra jogosult (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Prémium RS készlet

|**Ha jelenleg...**|**Át összehasonlítható Virtuálismag-alapú...**|**Át összehasonlítható DTU-alapú...**|
|---|---|---|
|Prémium RS tárolókészlet 125 dtu-k|Általános célú 1 virtuális mag (4. generációs)|Standard készlet 100 edtu-k|
|Prémium RS tárolókészlet 250 dtu-k|Általános célú, 2 virtuális mag (4. generációs)|250 standard készlet edtu-k|
|Prémium RS erőforráskészlet 500 dtu-k|Általános célú, 4 virtuális magra jogosult (Gen4)|Standard készlet 500 edtu-k|
|Prémium RS erőforráskészlet 1000 dtu-k|Általános célú, 8 virtuális maggal (Gen4)|Standard készlet 1000 edtu-k|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Megtakarítások és az i/o-igényes számítási feladatokhoz teljesítményének optimalizálása

Azt javasoljuk, hogy az i/o-igényes egyetlen adatbázisok migrálását a Virtuálismag-alapú nagy kapacitású szint, jelenleg az előzetes verzió és az i/o-igényes adatbáziskészletek az általánosan elérhető kritikus fontosságú üzleti szintre, a teljesítmény és a költség optimális kombinációjára.  A Virtuálismag-alapú következő beállítások fognak karbantartása vagy a jelenlegi teljesítményének javítása, és előfordulhat, hogy pénzt takaríthat meg, ha az Azure Hybrid Benefittel kombinálva, és a fenntartott kapacitás díjszabása.

|**Ha jelenleg...**|**Át összehasonlítható Virtuálismag-alapú...**|**Át összehasonlítható DTU-alapú...**|
|---|---|---|
|1 prémium RS|(Előzetes verzió) Nagy kapacitású 1 virtuális mag (4. generációs) vagy a kritikus fontosságú üzleti-1 virtuális mag (4. generációs)|1 prémium|
|2. prémium RS|(Előzetes verzió) Nagy kapacitású 2 virtuális mag (4. generációs) vagy üzleti kritikus 2 virtuális mag (4. generációs|Premium 2|
|Prémium RS 4|(Előzetes verzió) Nagy kapacitású 4 virtuális magra jogosult (4. generációs) vagy üzleti kritikus 4 virtuális magra jogosult (Gen4)|Prémium szintű 4
|Prémium RS 6|(Előzetes verzió) Nagy kapacitású 6 virtuális magra jogosult (4. generációs) vagy a kritikus fontosságú üzleti-6 virtuális magra jogosult (Gen4)|Prémium szintű 6|

|**Ha jelenleg...**|**Át összehasonlítható Virtuálismag-alapú...**|**Át összehasonlítható DTU-alapú...**|
|---|---|---|
|Prémium RS tárolókészlet 125 dtu-k|Üzleti kritikus 2 virtuális mag (Gen4)|Prémium készlet 125 edtu-k|
|Prémium RS tárolókészlet 250 dtu-k|Üzleti kritikus 2 virtuális mag (Gen4)|Prémium szintű 250 készlet edtu-k|
|Prémium RS erőforráskészlet 500 dtu-k|Üzleti kritikus 4 virtuális magra jogosult (Gen4)|Prémium készlet 500 edtu-k|
|Prémium RS erőforráskészlet 1000 dtu-k|Üzleti kritikus 8 virtuális mag (Gen4)|Prémium készlet 1000 edtu-k|

## <a name="take-advantage-of-our-new-offers"></a>Új csomagajánlatainkat előnyeinek kihasználása

A szolgáltatási szintek a Virtuálismag-alapú vásárlási modell jogosultak takaríthat meg, akár a 80 %-licenccel ellátott díjszabás és ajánlatok küldésére. Az SQL Server Standard vagy Enterprise edition-licencek használata az aktív frissítési garanciával rendelkező mentéséhez és a díjszabás licenccel ellátott 55 %-áig a [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Kombinálhatja a hybrid benefitet az [Azure SQL Database szolgáltatás számára fenntartott kapacitás](sql-database-reserved-capacity.md) díjszabás, és akár 80 % menteni a véglegesíti előzetes költségek, hogy egy vagy három éves távon.  Aktiválhatja a mindkét előnyeit még ma az Azure Portalról.

Ha bármilyen kérdése van, vagy ennek aggály módosítása, vagy forduljon a migrálási Segéddel, ha [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Áttelepítés a Premium RS szolgáltatási réteg egy szolgáltatási szinten a dtu-k vagy a Virtuálismag-modell

### <a name="migration-of-a-database"></a>Adatbázis áttelepítése

-Adatbázis áttelepítése a prémium RS szolgáltatásból a szolgáltatásréteghez bármelyik szinten a dtu-k vagy a Virtuálismag-modell frissítése vagy a prémium RS szolgáltatási rétegben található szolgáltatási szintek közötti alacsonyabb verziójúra változtatása hasonló.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>A prémium RS adatbázis konvertálása a DTU-alapú és a Virtuálismag-alapú adatbázis adatbázis-másolat használatával

Bármilyen számítási méretű prémium RS adatbázis egy adatbázis DTU-alapú vagy a Virtuálismag-alapú számítási méretű vagy speciális alkalmazás-előkészítés, amennyiben a cél számítási méret támogatja a forrásadatbázis adatbázis maximális mérete korlátozások nélkül másolhatja. Az adatbázis-másolat a másolási művelet kezdési időpontja adatokat pillanatképet készít, és nem hajt végre a forrás- és a cél közötti adatszinkronizálás.

## <a name="next-steps"></a>További lépések

- Részletek az adott számítási méretek és a tároló mérete lehetőségek önálló adatbázis elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok az önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Részletek az adott számítási méretek és a tároló mérete választható rugalmas készletek számára elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok a rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
