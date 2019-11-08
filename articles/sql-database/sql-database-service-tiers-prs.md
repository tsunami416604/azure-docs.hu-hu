---
title: prémium RS szolgáltatási szintek nyugdíjazása
description: 'Folyamatban van a prémium RS szolgáltatási réteg kivonása, és a támogatása megszűnik – lásd: áttelepítési beállítások.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821048"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database prémium RS szolgáltatási réteg (előzetes verzió) kivonása folyamatban van – áttelepítési lehetőségek

Február 2018-án a Microsoft bejelentette, hogy a Azure SQL Database prémium RS szolgáltatási szintje nem jelent meg általánosan elérhetővé, és a továbbiakban nem lesz támogatott a 2019. január 31. után. A támogatási határidő lejárt a 2019. június 30-ig. Ez a cikk a prémium RS szolgáltatási szintjéről egy másik szolgáltatási szintjére való Migrálás lehetőségeit ismerteti. 2019. június 30-ig a Microsoft automatikusan áttelepíti a prémium RS-adatbázisokat egy általánosan elérhető szolgáltatási szinten, amely leginkább megfelel a prémium RS-adatbázis teljesítménybeli követelményeinek.

Az alábbi áttelepítési célhelyek és díjszabási lehetőségek lehetnek prémium RS ügyfelek számára megfelelőek:

- Virtuális mag szolgáltatási szintek

  A [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) **általános célú** és **üzletileg kritikus** szolgáltatási szintjei. Ez a két szolgáltatási szint általánosan elérhető. A virtuális mag-alapú vásárlási modell a **nagy kapacitású** szolgáltatási szintet is biztosítja, amely igény szerint alkalmazkodik a számítási feladatokhoz, és akár 100 TB-os automatikus skálázást is biztosít az adatbázison. A nagy kapacitású szolgáltatási szint a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) prémium szolgáltatási szintjéhez hasonló IO-teljesítményt biztosít, amely közelebb áll a prémium rs szolgáltatási szintjéhez.
- Dev/Test-díjszabás

  A [dev/test díjszabása](https://azure.microsoft.com/pricing/dev-test/) a Visual Studio-előfizetéssel együtt akár 55%-os megtakarítást is biztosít, valamint a licencekhez tartozó díjszabást.
- A Azure Hybrid Benefit és a fenntartott kapacitás díjszabása

  A [Azure Hybrid Benefit és a fenntartott kapacitás díjszabása](https://azure.microsoft.com/pricing/details/sql-database/) akár 80%-os megtakarítást biztosít a licencekkel együtt. További információ ezekről a lehetőségekről: [Azure Hybrid Benefit SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) és [Azure SQL Database fenntartott kapacitással](sql-database-reserved-capacity.md)kapcsolatban.

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Cselekedjen most, hogy áttelepítse a prémium RS adatbázisait alternatív SQL Database szolgáltatási rétegekbe

Tekintse át az ebben a cikkben található útmutatást, valamint a díjszabást és a dokumentációt a prémium RS munkaterhelések megfelelő áttelepítési célhelyének meghatározásához.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Nagy számítási igényű számítási feladatok migrálása és mentés

A nagy számítási igényű prémium RS munkaterhelések esetében javasoljuk, hogy az általánosan elérhető virtuális mag-alapú általános célú szolgáltatási szintjére váltson át, és a SQL Server és a fenntartott kapacitási ajánlatok Azure Hybrid Benefit használatával több, a licencekkel kapcsolatos díjszabást is mentsen. Ha inkább egy DTU-alapú vásárlási lehetőségre lenne szüksége, akkor áttelepítheti a nagy számítási igényű prémium RS-adatbázisokat egy standard szolgáltatási rétegre, és továbbra is mentheti a prémium RS általános elérhetőségi díjszabását (ha az általánosan elérhetővé vált).

> [!WARNING]
> A prémium RS számítási feladatok DTU-alapú prémium szintű szolgáltatási szintjére való áttelepítése növelheti a havi költségeket és a jelenlegi prémium RS díjszabását. Azt javasoljuk, hogy a nagy kapacitású vagy a üzletileg kritikus csomagokat a Azure Hybrid Benefit és a fenntartott kapacitás díjszabása alapján a prémium RS hasonló és alacsonyabb költségek fenntartása érdekében.

### <a name="premium-rs-databases"></a>Adatbázisok prémium RS

|**Ha jelenleg be van kapcsolva...**|**Migrálás hasonló virtuális mag-alapú...**|**Migrálás hasonló DTU-alapú...**|
|---|---|---|
|prémium RS 1|Általános célú 1 virtuális mag (Gen4)|Standard 3|
|prémium RS 2|Általános célú 2 virtuális mag (Gen4)|Standard 4|
|prémium RS 4|Általános célú 4 virtuális mag (Gen4)|Standard 6|
|prémium RS 6|Általános célú 6 virtuális mag (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>prémium RS készletek

|**Ha jelenleg be van kapcsolva...**|**Migrálás hasonló virtuális mag-alapú...**|**Migrálás hasonló DTU-alapú...**|
|---|---|---|
|prémium RS Pool 125 DTU|Általános célú 1 virtuális mag (Gen4)|Standard Pool 100 Edtu|
|prémium RS Pool 250 DTU|Általános célú 2 virtuális mag (Gen4)|Standard Pool 250 Edtu|
|prémium RS Pool 500 DTU|Általános célú 4 virtuális mag (Gen4)|Standard Pool 500 Edtu|
|prémium RS Pool 1000 DTU|Általános célú 8 virtuális mag (Gen4)|Standard Pool 1000 Edtu|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Az IO-igényes számítási feladatok megtakarításának és teljesítményének optimalizálása

Javasoljuk, hogy az IO-igényes önálló adatbázisok áttelepítését a virtuális mag-alapú nagy kapacitású-rétegre, amely jelenleg előzetes verzióban érhető el, valamint az IO-igényes adatbázis-készleteket az általánosan elérhető üzletileg kritikus szintjére a teljesítmény és a költséghatékonyság optimális kombinációja érdekében.  A következő virtuális mag-alapú lehetőségek megtartják vagy javítják az aktuális teljesítményt, és a Azure Hybrid Benefit és a fenntartott kapacitás díjszabásával kombinálva pénzt takarítanak meg.

|**Ha jelenleg be van kapcsolva...**|**Migrálás hasonló virtuális mag-alapú...**|**Migrálás hasonló DTU-alapú...**|
|---|---|---|
|prémium RS 1| Nagy kapacitású 1 virtuális mag (Gen4) vagy üzletileg kritikus 1 virtuális mag (Gen4)|1\. prémium|
|prémium RS 2| Nagy kapacitású 2 virtuális mag (Gen4) vagy üzletileg kritikus 2 virtuális mag (Gen4|Prémium 2|
|prémium RS 4| Nagy kapacitású 4 virtuális mag (Gen4) vagy üzletileg kritikus 4 virtuális mag (Gen4)|Prémium 4
|prémium RS 6| Nagy kapacitású 6 virtuális mag (Gen4) vagy üzletileg kritikus 6 virtuális mag (Gen4)|Prémium 6|

|**Ha jelenleg be van kapcsolva...**|**Migrálás hasonló virtuális mag-alapú...**|**Migrálás hasonló DTU-alapú...**|
|---|---|---|
|prémium RS Pool 125 DTU|Üzletileg kritikus 2 virtuális mag (Gen4)|Prémium szintű Pool 125 Edtu|
|prémium RS Pool 250 DTU|Üzletileg kritikus 2 virtuális mag (Gen4)|Prémium szintű Pool 250 Edtu|
|prémium RS Pool 500 DTU|Üzletileg kritikus 4 virtuális mag (Gen4)|Prémium szintű Pool 500 Edtu|
|prémium RS Pool 1000 DTU|Üzletileg kritikus 8 virtuális mag (Gen4)|Prémium szintű Pool 1000 Edtu|

## <a name="take-advantage-of-our-new-offers"></a>Használja ki az új ajánlataink előnyeit

Szolgáltatási szintjeink a virtuális mag-alapú vásárlási modellben olyan speciális ajánlatokra jogosultak, amelyek akár 80%-ot is megtakaríthat, a licencekkel együtt. Az aktív frissítési garanciával rendelkező SQL Server Standard vagy Enterprise Edition-licencek használatával akár 55%-ot is megtakaríthat, valamint a licencekkel kapcsolatos díjszabást a [SQL Server Azure Hybrid Benefitával](https://azure.microsoft.com/pricing/hybrid-benefit/). A Hybrid benefitet a [Azure SQL Database fenntartott kapacitás](sql-database-reserved-capacity.md) díjszabásával kombinálhatja, és akár 80%-ot is megtakaríthat, ha előzetesen egy vagy három éves időszakra véglegesít.  Mindkét előny aktiválása Azure Portal.

Ha bármilyen kérdése vagy problémája van a változással kapcsolatban, vagy ha áttelepítési segítségre van szüksége, forduljon a [Microsofthoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Áttelepítés prémium RS szolgáltatási szinten a DTU vagy a virtuális mag modellben lévő szolgáltatási szintjére

### <a name="migration-of-a-database"></a>Adatbázis áttelepítése

A prémium RS szolgáltatási szintjéről a DTU vagy a virtuális mag modellbe való Migrálás hasonló a szolgáltatási szintek prémium RS szolgáltatási szinten való frissítéséhez vagy visszalépéséhez.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Adatbázis-másolat használata prémium RS adatbázis átalakítására DTU-alapú vagy virtuális mag-alapú adatbázisba

A prémium RS számítási mérettel rendelkező adatbázisok átmásolhatók olyan adatbázisra, amely korlátozások vagy speciális sorrendek nélkül DTU vagy virtuális mag számítási mérettel rendelkezik, feltéve, hogy a célként megadott számítási méret támogatja a forrásadatbázis maximális adatbázis-méretét. Az adatbázis-másolat létrehoz egy pillanatképet az adatokról a másolási művelet kezdési időpontjáról, és nem hajtja végre az adatszinkronizálást a forrás és a cél között.

## <a name="next-steps"></a>További lépések

- Az önálló adatbázisokhoz rendelkezésre álló számítási méretekről és a tárolási méretekről a következő témakörben talál további információt: [SQL Database virtuális mag-alapú erőforrás-korlátok egyetlen](sql-database-vcore-resource-limits-single-databases.md) adatbázishoz
- A rugalmas készletekhez rendelkezésre álló számítási méretekről és a tárhelyek méretére vonatkozó választási lehetőségekről a [rugalmas készletek SQL Database virtuális mag-alapú erőforrás-korlátozásait](sql-database-vcore-resource-limits-elastic-pools.md)ismertető cikk nyújt tájékoztatást.
