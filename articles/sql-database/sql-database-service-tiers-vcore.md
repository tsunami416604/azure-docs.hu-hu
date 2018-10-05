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
ms.date: 10/04/2018
ms.openlocfilehash: 9056eb6d93b0ead2a62dc5f58984fa6342af2d8e
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801659"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>Virtuális mag szolgáltatásszint kiválasztása, számítási, memória, tárolási és i/o-erőforrások

A Virtuálismag-alapú vásárlási modell lehetővé teszi, hogy egymástól függetlenül méretezheti a számítási és tárolási erőforrások, a helyszíni teljesítmény és optimalizálás ár. Lehetővé teszi hardvertől kiválasztása:
- 4 – általános legfeljebb 24 logikai CPU-alapú Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzor, a virtuális mag = 1 PP (fizikai mag), egy mag, 7 GB-os csatlakoztatott SSD
- 5 – általános legfeljebb 80 logikai CPU-alapú Intel E5-2673 v4 (Broadwell) 2,3 GHz-es processzor, a virtuális mag = 1. LP (a hyper-szál), egy 5.5-ös. GB / core, gyors eNVM SSD

Virtuálismag-modell azt is lehetővé teszi, hogy [Azure Hybrid Use Benefitet az SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) költségmegtakarítást biztosítanak.

## <a name="service-tier-characteristics"></a>Szolgáltatási szint tulajdonságok

A Virtuálismag-modell általános célú és a kritikus fontosságú üzleti szolgáltatáscsomagban biztosítja. Szolgáltatásszintek számítási méretű, a magas rendelkezésre állás kialakítása, a hibák elszigetelését, a tárolási típust kínál számos és i/o-tartomány különbözteti meg. Az ügyfél külön-külön kell konfigurálnia a szükséges tárolási és a megőrzési időszak a biztonsági mentésekhez.

Az alábbi táblázat segít a két szintek közötti különbségeket:

||**Általános célú**|**Üzletileg kritikus**|**Nagy kapacitású (előzetes verzió)**|
|---|---|---|---|
|A következőkre alkalmas|A legtöbb üzleti számítási feladathoz. Ajánlatok költségvetés-orientált elosztott és skálázható számítási és tárolási lehetőségek.|Magas I/O-igényű üzleti alkalmazások. Több elkülönített replika használatával ez biztosítja a legmagasabb hibatűrést.|A legtöbb üzleti célú a rugalmasan skálázható a tárolás és olvasási szintű követelmények|
|Compute|Gen4: 1-24 virtuális mag<br/>Gen5: 1 és 80 virtuális mag|Gen4: 1-24 virtuális mag<br/>Gen5: 1 és 80 virtuális mag|Gen4: 1-24 virtuális mag<br/>Gen5: 1 és 80 virtuális mag|
|Memory (Memória)|Gen4: 7 GB / mag<br>Gen5: 5.5-ös GB / mag | Gen4: 7 GB / mag<br>Gen5: 5.5-ös GB / mag |Gen4: 7 GB / mag<br>Gen5: 5.5-ös GB / mag|
|Storage|[Prémium szintű távtároló](../virtual-machines/windows/premium-storage.md),<br/>Önálló adatbázis: 5 GB – 4 TB-ig<br/>Felügyelt példány: 32 GB – 8 TB |Helyi SSD-tárolóval<br/>Önálló adatbázis: 5 GB – 4 TB-ig<br/>Felügyelt példány: 32 GB – 4 TB-ig |Rugalmas, automatikus növekedést tárolási igény szerint. Támogatja az akár 100 TB tárterület és más alkalmazásokhoz. Helyi SSD-tárhely a helyi puffer készlet cache és a helyi adatok tárolását. Az Azure távoli tárhely végső hosszú távú adatok tárolását. |
|IO-átviteli sebesség (becsült)|Önálló adatbázis: 500 IOPS / virtuális mag a 7000-es maximális iops</br>Felügyelt példány: Függ [fájl méretét](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|A maximális iops-érték 200 000 magonként 5000 IOPS|TBD|
|Rendelkezésre állás|1 replika, nincs olvasási szintű|3 replika, 1 [olvasási szintű replika](sql-database-read-scale-out.md),<br/>zóna redundáns magas rendelkezésre ÁLLÁS|?|
|Biztonsági másolatok|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 nap (alapértelmezés szerint 7 nap)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 nap (alapértelmezés szerint 7 nap)|pillanatkép-alapú biztonsági mentés az Azure távoli tároló és a helyreállításokat ezeket a pillanatképeket használni a gyors helyreállítás. A biztonsági mentéseket azonnali, és nincs hatással a számítási i/o-teljesítményét. Visszaállítások rendkívül gyors, és nem az üzemeltetés mérete (percben nem óra/nap).|
|A memóriában|Nem támogatott|Támogatott|Nem támogatott|
|||

További információkért lásd: [virtuális mag erőforráskorlátok egyetlen adatbázis](sql-database-vcore-resource-limits-single-databases.md) és [virtuális mag erőforráskorlátok a felügyelt példány](sql-database-managed-instance.md#vcore-based-purchasing-model). 

> [!IMPORTANT]
> Ha a számítási kapacitás kevesebb mint 1 virtuális magra van szüksége, használja a DTU-alapú vásárlási modell.

Lásd: [SQL Database: gyakori kérdések](sql-database-faq.md) kapcsolatos gyakori kérdésekre adott válaszokat. 

## <a name="storage-considerations"></a>A tárterülettel kapcsolatos szempontok

### <a name="general-purpose-and-business-critical-service-tiers"></a>Általános célú és az üzletileg kritikus szolgáltatási szintek
A következőket ajánljuk figyelmébe:
- A lefoglalt tárolót adatfájlok (MDF) és a naplófájlok fájlok (LDF) használja.
- Minden önálló adatbázis az adatbázis maximális méretét, egy alapértelmezett 32 GB maximális mérettel számítási mérete támogatja.
- Amikor konfigurálja a szükséges egyetlen adatbázis mérete (MDF mérete), 30 %-a további tárhely automatikusan hozzáadott LDF támogatása
- A felügyelt példány tároló mérete 32 GB többszörösének kell adni.
- Kiválaszthatja, hogy minden önálló adatbázis mérete 10 GB-os és a támogatott maximális érték között
 - Standard szintű tárolóra vonatkozó növelése, vagy 10 GB-os lépésekben méretének csökkentése
 - A Premium storage növelheti, vagy 250 GB-os lépésekben méretének csökkentése
- Az általános célú szolgáltatásszinten lévő `tempdb` használ egy csatlakoztatott SSD és a tárolási költségek a virtuális mag díja tartalmazza.
- Az üzletileg kritikus szolgáltatási rétegben található `tempdb` megosztások a csatlakoztatott SSD az MDF és az LDF-fájlok és a TempDB adatbázisban a tárolási költségek a virtuális mag díja tartalmazza.

> [!IMPORTANT]
> A teljes tárterület MDF és az LDF-lefoglalt díjkötelesek.

A jelenlegi teljes mérete MDF és az LDF-monitorozásához használja [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Az egyes MDF és az LDF-fájlok aktuális mérete monitorozásához használja [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

### <a name="hyperscale-service-tier-preview"></a>Nagy kapacitású szolgáltatási szint (előzetes verzió)

Storage szolgáltatás nagy kapacitású adatbázis automatikus által felügyelt. Tárolási igényével együtt növekszik szükséges. "Végtelen" naplótároló a gyors Azure premium storage SSD-kkel rendelkező nincs szükség gyakori rendszernapló csonkolása.

## <a name="backups-and-storage"></a>Biztonsági mentés és tárolás

### <a name="general-purpose-and-business-critical-service-tiers"></a>Általános célú és az üzletileg kritikus szolgáltatási szintek

Az adatbázis biztonsági másolatait lefoglalta a pont támogatja az idő-visszaállítás (PITR) és [hosszú távú adatmegőrzést (LTR)](sql-database-long-term-retention.md) SQL Database képességeit. Ez a tároló lefoglalt külön-külön az egyes adatbázisok, és két külön adatbázis-díj terheli. 

- **PITR**: önálló adatbázis biztonsági másolatait másolja [RA-GRS tároló](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatikusan. A tároló mérete dinamikusan növeli, amikor az új biztonsági mentéseket hoz létre.  A tárolót a heti teljes biztonsági mentésekhez, a napi differenciális biztonsági mentésekhez, illetve a tranzakciónaplók 5 percenként másolt biztonsági másolataihoz használja a rendszer. A tárhelyhasználat attól függ, hogy az adatbázis és a megőrzési időszak a változási gyakoriság. Beállíthatja, hogy minden egyes adatbázis 7 és 35 nap közötti, egy különálló megőrzési ideje. Egy minimális tárolókapacitás egyenlő 1 x-re az adatok méretének külön díjfizetés nélkül van megadva. A legtöbb adatbázisok esetében ez a mennyiség ez elegendő 7 nap a biztonsági mentések tárolásához.
- **Az LTR**: SQL-adatbázis a teljes biztonsági mentések hosszú távú megőrzésének konfigurálása akár 10 évig lehetőséget kínál. Az LTR-szabályzat engedélyezve van, ha a következő biztonsági másolatai RA-GRS tároló automatikusan, de szabályozhatja, hogy milyen gyakran a biztonsági másolatok másolódnak. A különböző megfelelőségi követelménynek megfelel, heti, havi és/vagy éves biztonsági mentések különböző megőrzési időtartamú választhat. Ez a konfiguráció meghatározzuk, mennyi tárhelyre lesz az LTR biztonsági mentéseket. Használhatja az LTR-díjkalkulátor LTR tárolási költségek becsléséhez. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

### <a name="hyperscale-service-tier-preview"></a>Nagy kapacitású szolgáltatási szint (előzetes verzió)

A nagy kapacitású szolgáltatási rétegben a biztonsági mentések a pillanatkép-alapú és a távoli Azure storage-ban tárolt. Visszaállítás a pillanatképek használata gyors helyreállítás. A biztonsági mentéseket azonnali, és nincs hatással a számítási i/o-teljesítményét. Visszaállítások rendkívül gyors, és nem az üzemeltetés mérete (percben nem óra/nap).

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

A Virtuálismag-alapú vásárlási modell, az exchange is kedvezményes díjszabást kínál az SQL Database-adatbázishoz a meglévő licenceit a [SQL Serverhez készült Azure Hybrid Use Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Az Azure-értékelemek lehetővé teszi, hogy a helyszíni SQL Server-licenceivel akár 30 %-os mentése az Azure SQL Database használatával a helyszíni SQL Server-licenceit frissítési garanciával rendelkező.

![díjszabás](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Áttelepítés a DTU-modellből Virtuálismag-modell

### <a name="migration-of-single-databases-with-geo-replication-links"></a>Georeplikációs hivatkozásokat az önálló adatbázisok migrálása

DTU-alapú modell Virtuálismag-alapú modellre való áttérés hasonlít a frissítést, vagy alacsonyabb verziójúra változtatása a Standard és prémium szintű adatbázisok közötti georeplikációs kapcsolatot. A georeplikáció, de a felhasználó megszakítást okozó meg kell vizsgálni az alkalmazás-előkészítés szabályok nem igényel. Verzióra, először frissítse a másodlagos adatbázist, és az utána frissítse az elsődleges. Ha alacsonyabb szolgáltatásszintre, fordított sorrendben: kell az elsődleges adatbázis alacsonyabbra először, és ezután alacsonyabbra a másodlagos. 

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

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Adatbázis másolatának használata a Virtuálismag-alapú adatbázis DTU-alapú adatbázis konvertálása.

Minden adatbázis DTU-alapú számítási méretű másolhatja vagy speciális alkalmazás-előkészítés, amennyiben a cél számítási méret támogatja a forrásadatbázis adatbázis maximális mérete korlátozások nélkül Virtuálismag-alapú számítási méretű adatbázishoz. Ez azért, mert az adatbázis-másolat a másolási művelet kezdési időpontja adatokat pillanatképet készít, és nem hajt végre a forrás- és a cél közötti adatszinkronizálás. 

## <a name="next-steps"></a>További lépések

- Részletek az adott számítási méretek és a tároló mérete lehetőségek önálló adatbázis elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok az önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Részletek az adott méretű számítási és tárolási mérete választható rugalmas készletek számára elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok a rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
