---
title: Az Azure SQL Database szolgáltatás - vCore |} Microsoft Docs
description: VCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) lehetővé teszi egymástól függetlenül válik a számítási és tárolási erőforrások felel meg a helyszíni teljesítmény és ár optimalizálása.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: bfa32796b40033a13d1ced9f8431bd19492e6498
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309579"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>VCore szolgáltatásréteg, számítási, memória, tárolási és IO erőforrások kiválasztása

Számos különböző teljesítményszintek, a magas rendelkezésre állású kialakítása, a tartalék elszigetelésére, a tárolótípusokat és IO tartomány szerint megkülönböztetett forgalomosztályból a szolgáltatási szinteket. Az ügyfél külön-külön kell konfigurálni a kívánt tároló és a megőrzési időszakot a biztonsági mentésekhez. A vCore modellel, az egyszeri adatbázisok és rugalmas készletek fel a megtakarítások 30 százalékos a [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Az alábbi táblázat segít a két szintje közötti különbségek megismeréséhez:

||**Általános célú**|**Kritikus fontosságú üzleti**|
|---|---|---|
|A következőkre alkalmas|A legtöbb üzleti számítási feladat. Ajánlatok költségvetés objektumorientált elosztott terhelésű és méretezhető számítási és tárolási beállításai.|Magas I/O-igényű üzleti alkalmazások. Több elkülönített replika használatával ez biztosítja a legmagasabb hibatűrést.|
|Compute|1 és 80 vCore generációs 4. és 5 létrehozása |1 és 80 vCore generációs 4. és 5 létrehozása|
|Memory (Memória)|7 GB / mag |7 GB / mag |
|Storage|Prémium szintű távtároló, 5 GB – 4 TB-os|Helyi SSD tároló, 5 GB – 4 TB-os|
|IO átviteli sebesség (hozzávetőleges)|500 iops-értéket vCore a 7000-es maximális IOPS|5000 iops teljesítményt nyújtanak core rendelkező 200000 maximális IOPS|
|Rendelkezésre állás|1 replika, nem olvasható méretezési|3 replikákat, 1 [olvasási méretű](sql-database-read-scale-out.md), redundáns magas rendelkezésre ÁLLÁSÚ zóna|
|Biztonsági másolatok|RA-GRS, 7 – 35 nap (alapértelmezés szerint 7 nap)|RA-GRS, 7 – 35 nap (alapértelmezés szerint 7 nap) *|
|A memóriában|–|Támogatott|
|||

\* Előzetes a biztonsági mentések megőrzési időtartam nem konfigurálható, 7 napra rögzített.

> [!IMPORTANT]
> Ha a számítási kapacitás kisebb, mint egy vCore van szüksége, használja a DTU-alapú alapjául szolgáló vásárlási modell.

Lásd: [SQL-adatbázis – gyakori kérdések](sql-database-faq.md) gyakran feltett kérdésekre adott válaszokat. 

## <a name="storage-considerations"></a>A tárterülettel kapcsolatos szempontok

A következőket ajánljuk figyelmébe:
- A lefoglalt tárolót használja az adatfájlok (MDF) és a naplófájlok fájlok (LDF).
- Egyes egy adatbázis maximális mérete 32 GB maximális mérettel alapértelmezett támogatja.
- A szükséges adatbázis mérete (MDF mérete) konfigurálásakor automatikusan hozzáadott 30 %-a további tárhely LDF támogatja
- Kiválaszthatja, hogy bármely adatbázis mérete 10 GB-os és a támogatott maximális érték közötti
 - Standard szintű tárolást, az növeli vagy 10 GB-os lépésekben méretének csökkentése
 - Prémium szintű storage, az növeli vagy 250 GB-os lépésekben méretének csökkentése
- Általános célú rétegben `tempdb` egy csatolt SSD és a tárolási költség szerepel a vCore ár használ.
- A fontos üzleti szolgáltatási rétegben `tempdb` a csatolt SSD az MDF és az LDF-fájlokat és a tempDB tárterület költség szerepel a vCore ár megosztások.

> [!IMPORTANT]
> Az MDF- és az LDF lefoglalt teljes tárolására van szó.

A jelenlegi összmérete MDF és az LDF-figyelése [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Az egyes MDF és az LDF-fájlok aktuális méretét figyelése [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

## <a name="backups-and-storage"></a>Biztonsági mentés és tárolás

Az adatbázis biztonsági mentések tárolási rendelt a pont támogatja az SQL adatbázis idő visszaállítása (PITR) és a hosszú távú megőrzési (LTR) képességeit. Ez a tároló külön-külön az egyes adatbázisok számára lefoglalt és két külön adatbázis-díjak terheli. 

- **PITR**: egyedi biztonsági másolatok átmásolt az RA-GRS tárolóba adatbázis automatikusan vannak. A tároló mérete dinamikusan nő, amikor az új biztonsági mentéseket hoz létre.  A tárolót a heti teljes biztonsági mentésekhez, a napi differenciális biztonsági mentésekhez, illetve a tranzakciónaplók 5 percenként másolt biztonsági másolataihoz használja a rendszer. A tároló fogyasztása attól függ, hogy módosítsa az adatbázis és a megőrzési időszak aránya. Konfigurálhatja az egyes adatbázisok 7 és 35 nap közötti külön megőrzési időtartamot. A minimális tárolási mennyiségű egyenlő 1 x adatméret külön díjfizetés nélkül valósul meg. Ez a mennyiség legtöbb adatbázis esetén elegendő 7 nap, a biztonsági mentések tárolására.
- **Balról jobbra**: SQL-adatbázis lehetővé teszi a hosszú távú megőrzési teljes biztonsági mentések konfigurálása legfeljebb 10 év. Ha balról jobbra házirend engedélyezve van, ezek biztonsági másolatai RA-GRS tárolási automatikusan, de megadhatja, milyen gyakran a biztonsági mentések kerülnek. Teljesíti-e több megfelelőségi követelményt, jelölje ki az eltérő megőrzési idejű a heti, havi vagy éves biztonsági mentésekhez. Ez a konfiguráció határozza meg az mennyi tárhelyre lesz a balról jobbra biztonsági mentéseket. A balról jobbra árképzési Számológép segítségével balról jobbra tárolási költségét. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

A vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió), a kedvezményes sebesség az SQL-adatbázis használata a meglévő licenceinek továbbíthatja a [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Az Azure előnyök használatát teszi a helyszíni SQL Server-licenceket 30 %-kal menti az Azure SQL Database Software Assurance használatával a helyszíni SQL Server-licenceket.

![díjszabás](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>Áttelepítés önálló adatbázisok georeplikáció hivatkozásokkal

Modellhez vCore alapú DTU-alapú modellből történő hasonlít a frissítést vagy alacsonyabb verziójúra változtatása a georeplikáció, a Standard és Premium adatbázisok közötti kapcsolatokat. Nem szükséges leállítja a georeplikáció, de a felhasználó be kell tartania az alkalmazás-előkészítés szabályokat. Frissítésekor, először frissítse a másodlagos adatbázist, és az majd frissíteni az elsődleges. Amikor alacsonyabb verziójúra változtatása, fordított sorrendben: kell visszaminősítését először az elsődleges adatbázis, és a másodlagos majd használni. 

Georeplikálási között két rugalmas készletek használatakor javasoljuk, hogy jelöljön ki egy készletet az elsődleges, míg a másik – másodlagos. Áttelepítése rugalmas készletek ebben az esetben az azonos útmutatást kell használnia.  Azonban azt technikailag lehetséges, hogy egy rugalmas készlet tartalmazza-e az elsődleges és másodlagos adatbázisok. Ebben az esetben megfelelően áttelepítéséhez meg kell kezelni a címkészlet, amely az "elsődleges" magasabb kihasználtsági és kövesse sorrendi szabályok ennek megfelelően.  

Az alábbi táblázatban az adott áttelepítési forgatókönyvre vonatkozó útmutatást: 

|Aktuális szolgáltatási rétegben|Cél szolgáltatási rétegben|Áttelepítés típusa|Felhasználói műveletek|
|---|---|---|---|
|Standard|Általános célú|Oldalirányú|Bármilyen sorrendben áttelepíteni, de is kell arról, hogy egy megfelelő vCore méretezési *|
|Prémium|Üzletileg kritikus|Oldalirányú|Bármilyen sorrendben áttelepíteni, de is kell arról, hogy megfelelő vCore méretezési *|
|Standard|Üzletileg kritikus|Frissítés|Kell telepítenie a másodlagos először|
|Üzletileg kritikus|Standard|Visszalépés|Át kell elsődleges először|
|Prémium|Általános célú|Visszalépés|Át kell elsődleges először|
|Általános célú|Prémium|Frissítés|Kell telepítenie a másodlagos először|
|Üzletileg kritikus|Általános célú|Visszalépés|Át kell elsődleges először|
|Általános célú|Üzletileg kritikus|Frissítés|Kell telepítenie a másodlagos először|
||||

\* Minden 100 DTU normál rétegben legalább 1 vCore igényel, és minden 125 DTU prémium csomagban a szükséges legalább 1 vCore

## <a name="migration-of-failover-groups"></a>A feladatátvételi csoportok áttelepítése 

Több adatbázisból feladatátvételi csoportok áttelepítése megköveteli, az elsődleges és másodlagos adatbázisok egyes migrálási. A folyamat során ugyanazok a feltételek és sorrendi szabályok alkalmazása Miután az adatbázisokat a vCore-alapú modell lettek konvertálva, a feladatátvételi csoport érvényben marad az azonos házirend-beállításokkal. 

## <a name="creation-of-a-geo-replication-secondary"></a>A georeplikáció másodlagos létrehozása

Csak egy földrajzi-másodlagos használja ugyanazt a szolgáltatási rétegben, mint az elsődleges hozhat létre. Az adatbázis magas napló generációs sebességet javasoljuk, hogy a másodlagos hozza létre az elsődleges teljesítmény szintjét. A rugalmas készletben egy önálló elsődleges adatbázis létrehozásakor egy földrajzi másodlagos, javasoljuk, hogy rendelkezik-e a készlet a `maxVCore` beállítástól, amely megfelel az elsődleges adatbázis teljesítményszintjét. A rugalmas készletben egy másik rugalmas készletben elsődleges létrehozásakor egy földrajzi másodlagos, javasoljuk, hogy a címkészleteknek azonos `maxVCore` beállítások

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Adatbázis-másolat használatával történő DTU-alapú adatbázis konvertálása vCore-alapú adatbázis.

A DTU-alapú teljesítményszintet adatbázis vCore-alapú teljesítmény, korlátozás nélkül szint vagy speciális alkalmazás-előkészítés mindaddig, amíg a cél teljesítményszintjét támogatja az adatbázis maximális mérete a forrásadatbázis adatbázishoz másolhatja. Ennek az az oka az adatbázis másolatát a másolási művelet kezdési időpontban meglévő adatok pillanatképet készít, és nem végzi el a forrás és a cél-adatainak szinkronizálása. 

## <a name="next-steps"></a>További lépések

- A meghatározott teljesítményszintet és a tárolási méret választható egyetlen adatbázis részletekért lásd: [az önálló adatbázisok SQL-adatbázis vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)
- Meghatározott teljesítményszintet és tároló mérete lehetőségeit a rugalmas tájékozódhat [a rugalmas SQL-adatbázis vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).