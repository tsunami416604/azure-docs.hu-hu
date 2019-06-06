---
title: Az Azure SQL Database – általános célú és a kritikus fontosságú üzleti |} A Microsoft Docs
description: A cikk ismerteti az általános célú és üzleti kritikus szolgáltatási szintet a Virtuálismag-alapú vásárlási modell.
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
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431339"
---
# <a name="azure-sql-database-service-tiers"></a>Az Azure SQL Database szolgáltatási csomagjai

Az Azure SQL Database az SQL Server adatbázis motor architektúra, amely 99,99 %-os rendelkezésre állást, hogy a felhőalapú környezet módosul, akkor is, ha az infrastruktúra sikertelen alapul. Három szolgáltatásszinten az Azure SQL Database, egy másik architekturális modell egyes szolgálnak. Szolgáltatási szintek a következők:

- [Általános célú](sql-database-service-tier-general-purpose.md), amely leginkább általános számítási feladatokhoz lett kialakítva.
- [Üzletileg kritikus](sql-database-service-tier-business-critical.md), amely kis késleltetésű számítási feladatok és egy olvasható replika lett tervezve.
- [Nagy kapacitású](sql-database-service-tier-hyperscale.md), ami nagyon nagy méretű adatbázisok számára hoztuk (akár 100 TB-os) rendelkező több olvasható replika.

Ez a cikk ismerteti a biztonsági szempontok az általános célú és üzleti kritikus szolgáltatási szintet a Virtuálismag-alapú vásárlási modell.

> [!NOTE]
> A nagy kapacitású szolgáltatási szinten a Virtuálismag-alapú vásárlási modell kapcsolatos információkért lásd: [nagy kapacitású szolgáltatásszint](sql-database-service-tier-hyperscale.md). A Virtuálismag-alapú vásárlási modell a DTU-alapú vásárlási modell összehasonlításáért lásd: [vásárlási modellek és az erőforrások az Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Adatok és a naplók tárolásához

Az alábbi tényezők befolyásolják az adatok és a naplófájlok a felhasznált tárterület mérete:

- A lefoglalt tárolót adatfájlok (MDF) és a naplófájlok (LDF) használja.
- Minden önálló adatbázis az adatbázis maximális méretét, és a egy alapértelmezett maximális mérete 32 GB-os számítási mérete által támogatott.
- Amikor konfigurálja a szükséges egyetlen adatbázis mérete (az mdf mérete), 30 %-kal több további tárhely automatikusan hozzáadódik LDF-fájlok támogatásához.
- Egy SQL Database felügyelt példány a tároló mérete 32 GB többszörösének kell adni.
- Kiválaszthatja, hogy minden önálló adatbázis mérete 10 GB-os és a támogatott maximális érték között.
  - A standard vagy általános célú szolgáltatásszinten tároláshoz növelje, vagy csökkentse az mérete 10-GB onként növelhető tárhelyet.
  - A prémium szintű vagy üzleti kritikus szolgáltatási szintet, növekedést vagy csökkenést mérete 250 GB-os lépésekben tárolás céljából.
- Az általános célú szolgáltatásszinten lévő `tempdb` használ egy csatlakoztatott SSD és a tárolási költségek a virtuális mag díja tartalmazza.
- Az üzleti kritikus fontosságú szolgáltatási szinten lévő `tempdb` a MDF és az LDF-fájlokat osztanak meg a csatlakoztatott SSD és a `tempdb` tárolási költségek a virtuális mag díja tartalmazza.

> [!IMPORTANT]
> A teljes tárterület MDF és az LDF-fájlok számára lefoglalt díjkötelesek.

Az aktuális az MDF és az LDF-fájlok összesített mérete monitorozásához használja [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Az egyes MDF és az LDF-fájlok aktuális mérete monitorozásához használja [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Biztonsági mentés és tárolás

Az adatbázis biztonsági mentését tároló lefoglalása a időponthoz visszaállítás (PITR) támogatásához és [hosszú távú megőrzésének (LTR)](sql-database-long-term-retention.md) SQL Database képességeit. Ez a tároló lefoglalt külön-külön az egyes adatbázisok, és két külön adatbázis-díj terheli.

- **PITR**: Másolja az egyes adatbázisok biztonsági mentése [írásvédett georedundáns tárolást (RA-GRS) tárolás](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatikusan. A tároló mérete dinamikusan növeli, amikor új biztonsági mentéseket hoz létre. A tárolót a heti teljes biztonsági mentésekhez, a napi differenciális biztonsági mentésekhez és a tranzakciós naplók biztonsági mentését, amelyeket a rendszer másol át 5 percenként használják. A tárhelyhasználat attól függ, hogy az adatbázis és a biztonsági másolatok megőrzési időtartama a változási gyakoriság. Beállíthatja, hogy minden egyes adatbázis 7 és 35 nap közötti, egy különálló megőrzési ideje. Egy az adatbázis méretének 100 %-os (1-szerestől) egyenlő minimális tárterület keletkezett külön díjfizetés nélkül van megadva. A legtöbb adatbázisok esetében ez a mennyiség ez elegendő 7 nap a biztonsági mentések tárolásához.
- **LTR**: SQL-adatbázis teljes biztonsági mentések hosszú távú megőrzésének konfigurálása akár 10 évig lehetőséget kínál. Ha beállította az LTR-szabályzat, ezeket a biztonsági másolatokat automatikusan a RA-GRS tároló vannak tárolva, de szabályozhatja, hogy milyen gyakran a biztonsági másolatok másolódnak. A különböző megfelelőségi követelmények teljesítése érdekében, heti, havi vagy éves biztonsági mentések különböző megőrzési időtartamú választhat. A kiválasztott beállítás határozza meg, mennyi tárhelyre lesz az LTR biztonsági mentések. Az LTR tárolási költségek becsléséhez, használhatja az LTR-díjkalkulátor. További információkért lásd: [SQL-adatbázis hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="next-steps"></a>További lépések

- Az adott adatait számítási méretű és tárolási méretek és a egy önálló adatbázis az általános célú és üzleti kritikus szolgáltatási szinteken érhető el, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok az önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md).
- Az adott adatait számítási méretű és tárolási méretek az általános célú és üzleti kritikus szolgáltatásszintek a rugalmas készletek számára elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok a rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).
