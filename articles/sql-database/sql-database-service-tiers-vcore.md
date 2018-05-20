---
title: Az Azure SQL Database szolgáltatás - vCore |} Microsoft Docs
description: Ismerje meg egyetlen szolgáltatásszintek és teljesítményszintek és tárolási méretek készlet adatbázisok.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 05/14/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 9abe7743906064d182453fea403ff94a097c3558
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="vcore-based-purchasing-model-for-azure-sql-database-preview"></a>vCore-alapú alapjául szolgáló vásárlási modell az Azure SQL Database (előzetes verzió)

[Az Azure SQL Database](sql-database-technical-overview.md) két vásárlási modell számítási, tárolási és IO erőforrásokat kínál: a DTU-alapú alapjául szolgáló vásárlási modell és a vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió). A következő diagram és táblázat összehasonlítása és két vásárlási modell.

> [!IMPORTANT]
> A DTU-alapú alapjául szolgáló vásárlási modell, lásd: [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md).


|**Alapjául szolgáló vásárlási modell**|**Leírás**|**A legjobb**|
|---|---|---|
|DTU-alapú modell|Ez a modell egy számítási, tárolási és IO erőforrások csomagolt mértéken alapul. A teljesítményszintek különálló adatbázisok esetében DTU-k (adatbázis-tranzakciós egységek), rugalmas készletek esetében pedig rugalmas DTU-k formájában vannak meghatározva. Dtu és edtu-k kapcsolatban bővebben lásd: [Dtu és edtu-k](sql-database-what-is-a-dtu.md)?|Ajánlott az ügyfelek számára, akik egyszerű, előre konfigurált beállításai.| 
|vCore alapuló modell|Ez a modell lehetővé teszi, hogy egymástól függetlenül válik a számítási és tárolási erőforrások – legfeljebb 80 vCores adattárolásra 4 TB-os és 200000 iops-érték. Lehetővé teszi az SQL Server Azure hibrid juttatás segítségével költséghatékony.|Az ügyfelek, akik a rugalmasságot, a vezérlési és az átláthatóság érték legmegfelelőbb.|
||||  

![árképzési modellt](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)

Egy virtuális core jelenti. a logikai Processzor érhető el, hogy a hardver generációja közül választhat. VCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) biztosít, a rugalmasságot, vezérlő, az egyéni erőforrás-felhasználás átláthatóság és egy egyszerű módon lefordítani a helyszíni munkaterhelési követelményeinek a felhőbe. Ez a modell méretezési számítási, memória és az alkalmazások és szolgáltatások igényeik alapján tároló lehetővé teszi. A vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió), az ügyfelek választhat általános célú és üzleti kritikus szolgáltatási szinteket (előzetes verzió) mindkét [adatbázisok egyetlen](sql-database-single-database-resources.md) és [rugalmas készletek](sql-database-elastic-pool.md). 

Számos különböző teljesítményszintek, a magas rendelkezésre állású kialakítása, a tartalék elszigetelésére, a tárolótípusokat és IO tartomány szerint megkülönböztetett forgalomosztályból a szolgáltatási szinteket. Az ügyfél külön-külön kell konfigurálni a kívánt tároló és a megőrzési időszakot a biztonsági mentésekhez. VCore modellt használja, ha önálló adatbázisok és rugalmas készletek jogosultak a mentése a 30 százalékos megtakarítások a [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Az a vCore-alapú vásárlási modell (előzetes verzió) ügyfelek díja:
- Számítási (szolgáltatásréteg + vCores + hardver generációja száma) *
- Típus és adatainak és naplókönyvtárainak tárolókapacitást 
- Hány IOs **
- Biztonsági mentés tárolás (RA-GRS) ** 

\* A kezdeti nyilvános előzetes verziójában a generációból 4 logikai processzorok alapján Intel E5-2673 v3 (Haswell) 2.4-GHz-es processzor

\*\* Előzetes amelyek biztonsági mentések és IOs 7 nap

> [!IMPORTANT]
> Számítás-, IOs, adatok, és a naplók tárolásához van szó, adatbázis vagy a rugalmas készlet. Biztonsági másolatok tárolási fel van töltve, egyes adatbázisonként. Felügyelt példány díjak részletekért tekintse meg a [Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md).

> [!IMPORTANT]
> A régióban korlátozások vonatkoznak: 
>
> A vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) még nincs Ausztrália délkeleti érhető el. A kép nem érhető el a következő régióban: Nyugat-Európában, Franciaország központi, Egyesült Királyság déli régiója és Egyesült Királyság nyugati régiója.
> 

## <a name="choosing-service-tier-compute-memory-storage-and-io-resources"></a>Szolgáltatási réteg, számítási, memória, tárolási és IO erőforrások kiválasztása

A vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) konvertálása lehetővé teszi egymástól függetlenül válik a számítási és tárolási erőforrások, felel meg a helyszíni teljesítmény és ár optimalizálása. Ha az adatbázis vagy a rugalmas készlethez használ több mint 300 DTU átalakítás vCore csökkentheti az költsége. Átválthat a választott API használatával vagy az Azure-portálon az állásidő nélkül. Azonban az átváltási nincs szükség. A DTU alapjául szolgáló vásárlási modell megfelel-e a teljesítményt és üzleti követelményeinek megfelelően, ha Ön továbbra is használja. Ha úgy dönt, hogy a DTU-modellből átalakítása vCore-modell, ki kell jelölni a használatával a következő tapasztalatok teljesítményszintet: minden 100 DTU Standard szint szükséges legalább 1 vCore általános célú réteg; Prémium szinten lévő minden egyes 125 DTU legalább 1 vCore fontos üzleti szint szükséges.

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

A meghatározott teljesítményszintet és a tárolási méret választható egyetlen adatbázis részletekért lásd: [SQL-adatbázis vCore-alapú erőforrás korlátok az önálló adatbázisok](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels) és rugalmas készletek című [SQL-adatbázis a rugalmas vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

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

Georeplikálási között két rugalmas készletek használatakor erősen ajánlott, hogy jelöljön ki egy készletet az elsődleges, míg a másik – másodlagos. Áttelepítése rugalmas készletek ebben az esetben az azonos útmutatást kell használnia.  Azonban azt technikailag lehetséges, hogy egy rugalmas készlet tartalmazza-e az elsődleges és másodlagos adatbázisok. Ebben az esetben megfelelően áttelepítéséhez meg kell kezelni a címkészlet, amely az "elsődleges" magasabb kihasználtsági és kövesse sorrendi szabályok ennek megfelelően.  

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

- A meghatározott teljesítményszintet és a tárolási méret választható részletekért lásd: [SQL Database DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits.md) és [SQL-adatbázis vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits.md).
- Lásd: [SQL-adatbázis – gyakori kérdések](sql-database-faq.md) gyakran feltett kérdésekre adott válaszokat.
- További tudnivalók [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md)
