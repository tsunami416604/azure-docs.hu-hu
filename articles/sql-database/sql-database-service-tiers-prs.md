---
title: Prémium RS szolgáltatási szint kivonása
description: A prémium RS szolgáltatási szint kivonásalatt áll, és annak támogatása megszűnik – lásd az áttelepítési lehetőségeket.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821048"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Az Azure SQL Database Premium RS szolgáltatási szint (előzetes verzió) kivonása folyamatban van – áttelepítési lehetőségek

2018 februárjában a Microsoft bejelentette, hogy az Azure SQL Database premium RS szolgáltatási szintje nem lesz általánosan elérhető, és 2019. A támogatási határidőt 2019. június 30-ig meghosszabbították. Ez a cikk ismerteti a prémium RS szolgáltatási szintről egy másik szolgáltatási szintre való áttelepítés lehetőségeit. 2019. június 30-a után a Microsoft automatikusan áttelepíti a prémium szintű RS-adatbázisokat egy általánosan elérhető szolgáltatási szintre, amely a leginkább megfelel a Prémium RS-adatbázis teljesítménykövetelményeinek.

Az áttelepítési célpontok és az árképzési lehetőségek a következők:

- virtuálismagos szolgáltatási szintek

  Az **általános célú** és üzleti **legkritikusabb** szolgáltatási szintek a [virtuálismag-alapú vásárlási modellben.](sql-database-service-tiers-vcore.md) Ez a két szolgáltatási szint általánosan elérhető. A virtuálismag-alapú vásárlási modell is kínál a **nagy kapacitású** szolgáltatási szint, amely alkalmazkodik az igény szerinti a számítási feladatok igényeinek automatikus skálázás akár 100 TB adatbázisonként. A nagy kapacitású szolgáltatási szint a Prémium RS szolgáltatási szinthez közelebbi áron biztosítja a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) prémium szolgáltatási szintjéhez hasonló I/O-teljesítményt.
- Fejlesztési/tesztelési árképzés

  [A fejlesztési és tesztelési díjszabás](https://azure.microsoft.com/pricing/dev-test/) akár 55%-os megtakarítást is eredményezhet a Visual Studio-előfizetéssel a licencet tartalmazó díjakkal szemben.
- Azure Hybrid Benefit és fenntartott kapacitásdíjszabás

  [Az Azure Hybrid Benefit és a fenntartott kapacitásdíj](https://azure.microsoft.com/pricing/details/sql-database/) akár 80%-os megtakarítást is eredményezhet a licenccel együtt foglalt díjakkal szemben. Ezekről a lehetőségekről az [Azure Hybrid Benefit for SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) és az Azure SQL Database fenntartott [kapacitáscímű témakörben](sql-database-reserved-capacity.md)talál további információt.

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>A prémium szintű RS-adatbázisok alternatív SQL Database szolgáltatási szintekre való áttelepítéséhez

Tekintse át az ebben a cikkben található útmutatást az árképzéssel és a dokumentációval együtt, hogy meghatározza a prémium szintű RS-munkaterhelések megfelelő áttelepítési célhelyét.Review the guidance in this article along with our pricing and documentation to determine the right migration destination(s) for your Premium RS workloads.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Nagy számítási feladatok áttelepítése és mentés

A számításigényes prémium szintű prémium szintű RS-számítási feladatok esetében azt javasoljuk, hogy migráljon az általánosan elérhető virtuálismag-alapú Általános célú szolgáltatási szintre, és takarítson meg többet az Azure Hybrid Benefit for SQL Server és a reserved capacity ajánlatok használatával. Ha inkább egy DTU-alapú vásárlási lehetőség, áttelepítheti a számításigényes prémium szintű prémium RS-adatbázisok egy standard szolgáltatási szintre, és továbbra is menteni, szemben a prémium RS általános rendelkezésre állási díjszabás (ha ment az általános elérhetőség).

> [!WARNING]
> A Prémium RS-számítási feladatok DTU-alapú prémium szolgáltatási szintekre való áttelepítése növelheti a havi költségeket a jelenlegi Prémium RS-díjszabással szemben. Azt javasoljuk, hogy fontolja meg a nagy kapacitású vagy üzleti legkritikusabb szintek az Azure Hybrid Benefit és a fenntartott kapacitás díjszabása fenntartása hasonló vagy alacsonyabb költségek, mint a prémium RS.

### <a name="premium-rs-databases"></a>Prémium RS adatbázisok

|**Ha jelenleg a ...**|**Áttelepítés hasonló virtuálismag-alapú...**|**Áttelepítés hasonló DTU-alapú...**|
|---|---|---|
|Prémium RS 1|Általános célú 1 virtuális mag (Gen4)|3. sz.|
|Prémium RS 2|Általános célú 2 virtuális magok (Gen4)|4.|
|Prémium RS 4|Általános célú 4 virtuális magok (Gen4)|6. sz.|
|Prémium RS 6|Általános célú 6 virtuális magok (Gen4)|7. sz.|

### <a name="premium-rs-pools"></a>Prémium RS-medencék

|**Ha jelenleg a ...**|**Áttelepítés hasonló virtuálismag-alapú...**|**Áttelepítés hasonló DTU-alapú...**|
|---|---|---|
|Prémium RS medence 125 DTU|Általános célú 1 virtuális mag (Gen4)|Standard készlet 100 eDTO|
|Prémium RS medence 250 DTU|Általános célú 2 virtuális magok (Gen4)|Standard készlet 250 eDTO|
|Prémium RS medence 500 DTU|Általános célú 4 virtuális magok (Gen4)|Standard készlet 500 eDTO|
|Prémium RS medence 1000 DTU|Általános célú 8 virtuális magok (Gen4)|Standard készlet 1000 eDTO|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimalizálja a megtakarításokat és a teljesítményt az I/O-igényes munkaterhelések számára

Azt javasoljuk, hogy az Io-igényes egyetlen adatbázisok at a virtuálismag-alapú, jelenleg előzetes verzióban elérhető, és az IO-igényes adatbázis-készletek a mi általánosan elérhető üzleti kritikus szint, az optimális kombinációja a teljesítmény és a költségek optimális kombinációja érdekében.  A következő virtuálismag-alapú beállítások fenntartják vagy javítják az aktuális teljesítményt, és pénzt takaríthatnak meg az Azure Hybrid Benefit és a fenntartott kapacitás díjszabása kombinálva.

|**Ha jelenleg a ...**|**Áttelepítés hasonló virtuálismag-alapú...**|**Áttelepítés hasonló DTU-alapú...**|
|---|---|---|
|Prémium RS 1| 1 virtuális mag (Gen4) vagy business critical 1 virtuális mag (Gen4)|Prémium 1|
|Prémium RS 2| 2 virtuális mag (Gen4) vagy üzleti legkritikusabb 2 virtuális mag (Gen4)|Prémium 2|
|Prémium RS 4| 4 virtuális mag (Gen4) vagy üzleti legkritikusabb 4 virtuális mag (Gen4)|4. prémium
|Prémium RS 6| 6 virtuális mag (Gen4) vagy üzleti legkritikusabb 6 virtuális mag (Gen4)|Prémium 6|

|**Ha jelenleg a ...**|**Áttelepítés hasonló virtuálismag-alapú...**|**Áttelepítés hasonló DTU-alapú...**|
|---|---|---|
|Prémium RS medence 125 DTU|Üzleti legkritikusabb 2 virtuális magok (Gen4)|Prémium pool 125 eDTO-k|
|Prémium RS medence 250 DTU|Üzleti legkritikusabb 2 virtuális magok (Gen4)|Prémium pool 250 eDTO|
|Prémium RS medence 500 DTU|Üzleti legkritikusabb 4 virtuális magok (Gen4)|Prémium pool 500 eDTO-k|
|Prémium RS medence 1000 DTU|Üzleti legkritikusabb 8 virtuális mag (Gen4)|Prémium alap 1000 eDTO|

## <a name="take-advantage-of-our-new-offers"></a>Használja ki új ajánlatainkat

A virtuálismag-alapú vásárlási modellben lévő szolgáltatási szintjeink olyan különleges ajánlatokra jogosultak, amelyek akár 80%-ot is megtakaríthatnak a licencet tartalmazó díjszabáshoz képest. Az SQL Server Standard vagy Enterprise Edition licencekkel akár 55%-ot is megtakaríthat az [Azure Hybrid Benefit for SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/)licencével. A hibrid előnyöket kombinálhatja az [Azure SQL Database által fenntartott kapacitásdíjszabással,](sql-database-reserved-capacity.md) és akár 80%-ot is megtakaríthat, ha előre elkötelezi magát egy vagy három éves időtartamra.  Aktiválja mindkét előnyt még ma az Azure Portalon.

Ha bármilyen kérdése vagy aggálya van a módosítással kapcsolatban, vagy áttelepítési segítségre van szüksége, forduljon a [Microsofthoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>A prémium RS-szolgáltatási szintről a DTU-n vagy a virtuálismag-modellben lévő szolgáltatási szintre való áttérés

### <a name="migration-of-a-database"></a>Adatbázis áttelepítése

Az adatbázis átáttelepítése egy prémium RS szolgáltatási szintről egy szolgáltatási szintre a DTU vagy a virtuálismag-modellben hasonló a prémium rs szolgáltatási szint szolgáltatási szintek frissítése vagy visszaminősítése.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Prémium RS-adatbázis DTU- vagy virtuálismag-alapú adatbázissá konvertálása adatbázismásoló használatával

Bármely prémium rs-számítási méretű adatbázist átmásolhat egy DTU-alapú vagy virtuálismag-alapú számítási mérettel rendelkező adatbázisba korlátozás vagy speciális szekvenálás nélkül, feltéve, hogy a cél számítási méret támogatja a forrásadatbázis maximális adatbázisméretét. Az adatbázis-másolat a másolási művelet kezdő időpontjától pillanatképet hoz létre az adatokról, és nem hajt végre adatszinkronizálást a forrás és a cél között.

## <a name="next-steps"></a>További lépések

- Az egyes adatbázisokhoz rendelkezésre álló konkrét számítási méretekkel és tárterület-méretválasztékkal kapcsolatos részletekért tekintse meg [az SQL Database virtuálismagalapú erőforráskorlátokat az egyes adatbázisokhoz.](sql-database-vcore-resource-limits-single-databases.md)
- A rugalmas készletekhez rendelkezésre álló konkrét számítási méretekkel és tárolási méretválasztékkal kapcsolatos részleteket az [SQL Database virtuálismagalapú erőforrások rugalmas készleteihez című témakörben](sql-database-vcore-resource-limits-elastic-pools.md)találja.
