---
title: Automatikus, georedundáns biztonsági mentések
description: Az SQL Database néhány percenként automatikusan létrehoz egy helyi adatbázis-biztonsági mentést, és az Azure olvasási hozzáférésű georedundáns tárolását használja a georedundancia érdekében.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061761"
---
# <a name="automated-backups"></a>Automatikus biztonsági mentések

Az Azure SQL Database automatikusan létrehozza az adatbázis biztonsági mentéseit, amelyek a konfigurált megőrzési időszak alatt maradnak. Az Azure [olvasási hozzáférésű georedundáns tárolás (RA-GRS)](../storage/common/storage-redundancy.md) használatával biztosítja a biztonsági mentések megőrzése akkor is, ha az adatközpont nem érhető el.

Az adatbázis-biztonsági mentések minden üzletmenet-folytonossági és vész-helyreállítási stratégia alapvető részét képezik, mivel védik az adatokat a véletlen sérüléstől vagy törléstől. Ha a biztonsági szabályok megkövetelik, hogy a biztonsági mentések hosszabb ideig (legfeljebb 10 évig) elérhetők, beállíthatja a [hosszú távú megőrzési](sql-database-long-term-retention.md) singleton adatbázisok és rugalmas adatbáziskészletek.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Mi az SQL Database biztonsági mentése?

Az SQL Database az SQL Server technológiával készít [hetente teljes biztonsági mentést,](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) 12 óránként [különbözeti biztonsági mentéseket,](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) és 5–10 percenként biztonsági másolatot készít a [tranzakciós naplóról.](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) A biztonsági mentések [ra-GRS tárolási blobokban](../storage/common/storage-redundancy.md) tárolódnak, amelyek egy [párosított adatközpontba](../best-practices-availability-paired-regions.md) replikálódnak az adatközpont kimaradása elleni védelem érdekében. Adatbázis visszaállításakor a szolgáltatás határozza meg, hogy mely teljes, különbözeti és tranzakciós naplóbiztonsági mentéseket kell visszaállítani.

A biztonsági másolatokat a következő célokra használhatja:

- **Állítsa vissza a meglévő adatbázist egy időpontra a múltban az** adatmegőrzési időszakon belül az Azure Portalon, az Azure PowerShell, az Azure CLI vagy a REST API használatával. Az egyes adatbázisok és rugalmas adatbáziskészletek esetén ez a művelet új adatbázist hoz létre ugyanazon a kiszolgálón, mint az eredeti adatbázis. A felügyelt példányban ez a művelet létrehozhat egy másolatot az adatbázisból, vagy az azonos vagy egy másik felügyelt példány ugyanazon előfizetés alatt.
- **Törölt adatbázis visszaállítása a törlés időpontjára** vagy bármikor a megőrzési időszakon belül. A törölt adatbázis csak ugyanazon a logikai kiszolgálón vagy felügyelt példányon állítható vissza, ahol az eredeti adatbázist létrehozták.
- **Adatbázis visszaállítása másik földrajzi területre**. Geo-visszaállítás lehetővé teszi, hogy helyreállítsa a földrajzi katasztrófa, ha nem tud hozzáférni a kiszolgáló és az adatbázis. Létrehoz egy új adatbázist minden meglévő szerveren, bárhol a világon.
- **Adatbázis visszaállítása egy adott hosszú távú biztonsági mentés** egyetlen adatbázis vagy rugalmas adatbáziskészlet, ha az adatbázis hosszú távú adatmegőrzési házirend (LTR) konfigurálva van. Az LTR lehetővé teszi az adatbázis egy régi verziójának visszaállítását [az Azure Portal vagy az](sql-database-long-term-backup-retention-configure.md#using-azure-portal) Azure [PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) használatával a megfelelőségi kérelmek teljesítéséhez vagy az alkalmazás egy régi verziójának futtatásához. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

A visszaállítás végrehajtásához olvassa el [az Adatbázis visszaállítása biztonsági másolatból (Adatbázis visszaállítása biztonsági másolatból) témakört.](sql-database-recovery-using-backups.md)

> [!NOTE]
> Az Azure Storage-ban a *replikáció* kifejezés fájlok másolása az egyik helyről a másikra. Az SQL Server *adatbázis-replikációja* több másodlagos adatbázis elsődleges adatbázissal való szinkronizálását nevezi.

Az alábbi példák segítségével próbálkozhat néhány művelettel:

| | Az Azure Portal | Azure PowerShell |
|---|---|---|
| Biztonsági másolat megőrzésének módosítása | [Egyetlen adatbázis](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Felügyelt példány](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Egyetlen adatbázis](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Hosszú távú biztonsági mentés megőrzésének módosítása | [Egyetlen adatbázis](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Felügyelt példány - N/A  | [Egyetlen adatbázis](sql-database-long-term-backup-retention-configure.md)<br/>Felügyelt példány - N/A  |
| Adatbázis visszaállítása egy adott időpontból | [Egyetlen adatbázis](sql-database-recovery-using-backups.md#point-in-time-restore) | [Egyetlen adatbázis](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Törölt adatbázis visszaállítása | [Egyetlen adatbázis](sql-database-recovery-using-backups.md) | [Egyetlen adatbázis](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Adatbázis visszaállítása az Azure Blob storage-ból | Egyetlen adatbázis - N/A <br/>Felügyelt példány - N/A  | Egyetlen adatbázis - N/A <br/>[Felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Az SQL Database támogatja az önkiszolgáló időpont-visszaállítást (PITR) azáltal, hogy automatikusan létrehozza a teljes biztonsági mentéseket, a különbözeti biztonsági mentéseket és a tranzakciós napló biztonsági másolatait. A teljes adatbázis-biztonsági mentések hetente jönnek létre, és a különbözeti adatbázis biztonsági mentések általában 12 óránként jönnek létre. A tranzakciónapló biztonsági mentései általában 5–10 percenként jönnek létre. A tranzakciós napló biztonsági mentések gyakorisága a számítási méret és az adatbázis-tevékenység mennyisége alapján. 

Az első teljes biztonsági mentés ütemezése közvetlenül az adatbázis létrehozása után történik. Ez a biztonsági mentés általában 30 percen belül befejeződik, de hosszabb időt vehet igénybe, ha az adatbázis nagy. A kezdeti biztonsági mentés például hosszabb időt vehet igénybe egy visszaállított adatbázisban vagy egy adatbázis-másolaton. Az első teljes biztonsági mentés után a további mentések felhasználói beavatkozás nélkül, a háttérben lesznek automatikusan ütemezve és felügyelve. Az adatbázis-mentések pontos időzítését az SQL Database szolgáltatás határozza meg a teljes rendszer terhelésének kiegyensúlyozásával. A biztonsági mentési feladatok nem módosíthatók és nem tilthatók le.

A PITR biztonsági mentések georedundáns tárolással vannak védve. További információ: [Azure Storage-redundancia](../storage/common/storage-redundancy.md).

A PITR szolgáltatásról további információt a [Pont-in-time visszaállítás című témakörben](sql-database-recovery-using-backups.md#point-in-time-restore)talál.

### <a name="long-term-retention"></a>Hosszú távú megőrzés

Az egy- és készletalapú adatbázisok esetében az Azure Blob storage-ban beállíthatja a teljes biztonsági mentések hosszú távú megőrzését (LTR). Ha engedélyezi az LTR-házirendet, a heti teljes biztonsági mentések automatikusan átkerülnek egy másik RA-GRS tárolóba. A különböző megfelelőségi követelmények teljesítéséhez különböző megőrzési időszakokat választhat ki a heti, havi és/vagy éves biztonsági mentéshez. A tárolási felhasználás a biztonsági mentések kiválasztott gyakoriságától és a megőrzési időszaktól vagy időszakoktól függ. Az [LTR díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=sql-database) segítségével megbecsülheti az LTR-tárhely költségét.

A PITR biztonsági mentésekhez hasonlóan az LTR biztonsági mentések is georedundáns tárolással vannak védve. További információ: [Azure Storage-redundancia](../storage/common/storage-redundancy.md).

Az LTR-ről további információt a [Hosszú távú biztonsági mentés megőrzése](sql-database-long-term-retention.md)című témakörben talál.

## <a name="backup-storage-consumption"></a>Tartalék tárolófelhasználás

Az egyes adatbázisok esetében ez az egyenlet a teljes biztonsági mentési tárterület-használat kiszámításához használható:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Rugalmas adatbáziskészletek esetén a teljes biztonsági mentési tárterület mérete a készlet szintjén összesül kerül, és a következőképpen kerül kiszámításra:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

A megőrzési időszak előtt előforduló biztonsági mentések automatikusan törlődnek az időbélyegük alapján. Mivel a különbözeti biztonsági mentések és a naplóbiztonsági mentések hasznosak kell, hogy egy korábbi teljes biztonsági mentéshasznos legyen, a rendszer hetente együtt törli őket.

Az Azure SQL Database a teljes adatmegőrzési biztonsági mentési tárolót összesítő értékként számítja ki. Ezt az értéket óránként jelenti az Azure számlázási folyamat, amely felelős az óránkénti használat összesítése kiszámításához a felhasználás minden hónap végén. Az adatbázis eldobása után a felhasználás a biztonsági mentés korával csökken. Miután a biztonsági mentések régebbiek lesznek, mint a megőrzési időszak, a számlázás leáll.

   > [!IMPORTANT]
   > Az adatbázis biztonsági másolatai a megadott megőrzési időszakra is megőrződnek, még akkor is, ha az adatbázis el lett dobva. Bár az adatbázis ledobása és újbóli létrehozása gyakran megtakaríthatja a tárolási és számítási költségeket, növelheti a biztonsági mentés i. tárolási költségeit, mivel a Microsoft megőrzi a biztonsági mentést a megadott megőrzési időszakra (ami legalább 7 nap) minden egyes eldobott adatbázisra vonatkozóan, minden egyes eldobott adatbázishoz amikor leejtik.

### <a name="monitor-consumption"></a>A felhasználás figyelése

A biztonsági mentés minden típusa (teljes, különbözeti és napló) külön metrikaként van jelentve az adatbázis-figyelési panelen. Az alábbi ábrán bemutatja, hogyan figyelheti a biztonsági mentési tárolási felhasználás egyetlen adatbázis. Ez a szolgáltatás jelenleg nem érhető el a felügyelt példányok esetében.

![Adatbázis-biztonsági mentési felhasználás figyelése az Azure Portalon](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>A biztonsági mentési tárhely felhasználásának finomhangolása

A tartalék tárolási többletfelhasználás a munkaterheléstől és az egyes adatbázisok méretétől függ. A biztonsági mentési tárhely felhasználásának csökkentése érdekében vegye figyelembe az alábbi finomhangolási technikákat:

* Csökkentse a [biztonsági mentési megőrzési időszakot](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) az igényeinek megfelelő minimumra.
* Kerülje a nagy írási műveletek, például az index újraépítések, gyakrabban, mint szükséges.
* Nagy adatbetöltési műveletek esetén fontolja meg [a fürtözött oszlopcentrikus indexek](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)használatát, csökkentse a nem fürtözött indexek számát, és fontolja meg a tömeges betöltési műveleteket, amelyek sorszáma körülbelül 1 millió körül van.
* Az általános célú szolgáltatási szint a kiépített adattárolás olcsóbb, mint a felesleges biztonsági mentési tároló ára. Ha folyamatosan magas a biztonsági mentési tárolási költségek, érdemes lehet növelni az adattárolást a biztonsági mentési tárolóba való mentéshez.
* Az ideiglenes eredmények tárolásához használja a TempDB-t az etl-logika állandó táblái helyett. (Csak felügyelt példányra alkalmazható.)
* Fontolja meg a TDE-titkosítás kikapcsolásával olyan adatbázisok esetében, amelyek nem tartalmaznak bizalmas adatokat (például fejlesztési vagy tesztelési adatbázisokat). A nem titkosított adatbázisok biztonsági másolatai általában nagyobb tömörítési aránysal vannak tömörítve.

> [!IMPORTANT]
> Az analitikus data mart \ adattárház számítási feladatok esetén javasoljuk, hogy [fürtözött oszlopcentrikus indexeket](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)használjon, csökkentse a nem fürtözött indexek számát, és fontolja meg a tömeges betöltési műveleteket, amelyek sorszáma körülbelül 1 millió körül van a felesleges biztonsági mentési tárolási felhasználás csökkentése érdekében.

## <a name="storage-costs"></a>Tárolási költségek

A tárolási ár attól függően változik, hogy a DTU-modellt vagy a virtuálismag-modellt használja.The price for storage varieon to whether you're using the DTU model or the vCore model.

### <a name="dtu-model"></a>DTU-modell

A DTU-modell használata esetén az adatbázisok és a rugalmas adatbáziskészletek biztonsági mentési tárolása nem számít fel külön díjat.

### <a name="vcore-model"></a>Virtuálismag-alapú modell

Az egyes adatbázisok esetében az adatbázis méretének 100 százalékával egyenlő minimális biztonsági mentési tárterület ingyenesen biztosított. Rugalmas adatbáziskészletek és felügyelt példányok esetén a készlethez vagy a példánymérethez lefoglalt adattár 100 százalékának megfelelő minimális biztonsági mentési tárterület ingyenes. A biztonsági mentési tár további felhasználása GB/hó alapon van felszámítva. Ez a további felhasználás az egyes adatbázisok munkaterhelésétől és méretétől függ.

Az Azure SQL Database a teljes adatmegőrzési biztonsági mentési tárolót összesítő értékként számítja ki. Ezt az értéket óránként jelenti az Azure számlázási folyamat, amely felelős az óránkénti használat összesítése, hogy a felhasználás minden hónap végén. Az adatbázis eldobása után a Microsoft csökkenti a biztonsági mentés korának csökkenését. Miután a biztonsági mentések régebbiek lesznek, mint a megőrzési időszak, a számlázás leáll. Mivel az összes naplóbiztonsági mentés és különbözeti biztonsági mentések megmaradnak a teljes megőrzési időszakban, erősen módosított adatbázisok magasabb biztonsági mentési díjakat.

Tegyük fel, hogy egy adatbázis 744 GB biztonsági mentési tárhelyet halmozott fel, és ez az összeg állandó marad egy teljes hónap alatt. A halmozott tárolási felhasználás óránkénti felhasználássá alakításához ossza el 744,0-vel (31 nap havonta * a nap 24 órájában). Így az SQL Database jelenti, hogy az adatbázis óránként 1 GB PITR biztonsági mentést fogyasztott. Az Azure-számlázás összesíti ezt a felhasználást, és 744 GB-os használatot jelenít meg az egész hónapban. A költség a régió $/GB/havi díja alapján kerül fel.

Most, egy bonyolultabb példa. Tegyük fel, hogy az adatbázis megőrzése a hónap közepén 14 napra nőtt. Tegyük fel, hogy ez a növekedés (hipotetikusan) azt eredményezi, hogy a teljes biztonsági mentési tárterület megduplázódik 1488 GB-ra. Az SQL Database 1 GB használatot jelent 1 és 372 között. Ez azt jelenti, hogy a használat 2 GB órákig 373 és 744 között. Ez a használat lenne összesítve a végső számlát 1116 GB/hó.

### <a name="monitor-costs"></a>A költségek figyelése

A biztonsági mentési tárolási költségek megértéséhez nyissa meg a **Cost Management + Billing (Költségkezelés + Számlázás) lehetőséget** az Azure Portalon, válassza a **Költségkezelés**lehetőséget, majd válassza a **Költségelemzés**lehetőséget. Válassza ki a kívánt **előfizetést hatókörként,** majd szűrje az önt érdeklő időszakra és szolgáltatásra.

Adjon hozzá egy szűrőt a **szolgáltatás nevéhez,** majd válassza az **SQL-adatbázist** a legördülő listában. A **mérőalkategória** szűrő segítségével válassza ki a szolgáltatás számlázási számlálóját. Egyetlen adatbázis vagy rugalmas adatbáziskészlet esetén válassza az **egy/rugalmas készlet biztonsági mentési tárolóját.** Felügyelt példány esetén válassza a **mi pitr backup storage lehetőséget.** A **storage** és **a számítási** alkategóriák is érdekelhetik Önt, de nem kapcsolódnak a biztonsági mentési tárolási költségekhez.

![Tárolási költségek elemzése](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Biztonsági mentés megőrzése

Minden Azure SQL-adatbázis (egy-, készletezésű és felügyelt példányadatbázisok) alapértelmezett biztonsági mentési megőrzési ideje 7 nap. [Módosíthatja a biztonsági mentésmegőrzési időszak,](#change-the-pitr-backup-retention-period) amíg 35 nap.

Ha töröl egy adatbázist, az SQL Database ugyanúgy megőrzi a biztonsági másolatokat, mint egy online adatbázis esetében. Ha például töröl egy alapszintű adatbázist, amelynek megőrzési ideje hét nap, a négy napos biztonsági mentést további három napig menti a rendszer.

Ha a biztonsági mentések a maximális megőrzési időtartamnál hosszabb ideig kell megtartania, módosíthatja a biztonsági mentési tulajdonságokat, hogy egy vagy több hosszú távú megőrzési időszakot adjon hozzá az adatbázishoz. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Ha törli az SQL-adatbázisokat tároló Azure SQL-kiszolgálót, a kiszolgálóhoz tartozó összes rugalmas adatbáziskészlet és adatbázis is törlődik. Nem lehet őket visszakésni. Törölt kiszolgáló nem állítható vissza. Ha azonban hosszú távú megőrzést konfigurált, az LTR-rel rendelkező adatbázisok biztonsági másolatai nem törlődnek, és ezek az adatbázisok visszaállíthatók.

## <a name="encrypted-backups"></a>Titkosított biztonsági mentések

Ha az adatbázis TDE-vel van titkosítva, a biztonsági mentések automatikusan titkosítva lesznek az inaktív állapotban, beleértve az LTR biztonsági mentéseket is. Ha a TDE engedélyezve van egy Azure SQL-adatbázishoz, a biztonsági mentések is titkosítva lesznek. Minden új Azure SQL-adatbázis alapértelmezés szerint tde engedélyezve van. A TDE-ről további információt az [Átlátszó adattitkosítás az Azure SQL-adatbázissal](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)című témakörben talál.

## <a name="backup-integrity"></a>Biztonsági mentés integritása

Az Azure SQL Database mérnöki csapata folyamatosan teszteli a logikai kiszolgálókba és rugalmas adatbáziskészletekbe helyezett adatbázisok automatikus adatbázis-biztonsági mentései visszaállítását. (Ez a tesztelés nem érhető el a felügyelt példányban.) A pont-az-időben visszaállításkor az adatbázisok a DBCC CHECKDB integritási ellenőrzéseit is megkapják.

A felügyelt példány automatikus `CHECKSUM` anamtálást `RESTORE` hajt végre a natív paranccsal vagy az Azure Data Migration Service-lel az áttelepítés befejezése után visszaállított adatbázisok automatikus kezdeti biztonsági mentésével.

Az integritás-ellenőrzés során talált problémák riasztást eredményeznek a mérnöki csapat számára. További információ: [Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Megfelelőség

Amikor az adatbázist egy DTU-alapú szolgáltatási szintről egy virtuálismag-alapú szolgáltatási szintre telepíti át, a PITR-megőrzés megmarad annak érdekében, hogy az alkalmazás adat-helyreállítási szabályzata ne sérüljön. Ha az alapértelmezett adatmegőrzési követelmények nem felelnek meg, módosíthatja a PITR megőrzési időszak segítségével PowerShell vagy a REST API használatával. További információ: [Pitr biztonsági mentési megőrzési időszak módosítása.](#change-the-pitr-backup-retention-period)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>A PITR biztonsági mentési megőrzési időszakának módosítása

Módosíthatja az alapértelmezett PITR biztonsági mentési megőrzési időszak az Azure Portalon, a PowerShell vagy a REST API használatával. Az alábbi példák bemutatják, hogyan módosíthatja a PITR-megőrzés28 napra.

> [!WARNING]
> Ha csökkenti az aktuális megőrzési időszak, az összes meglévő biztonsági mentések, amelyek régebbiek, mint az új megőrzési időszak már nem érhető el. Ha növeli az aktuális megőrzési időszakot, az SQL Database megtartja a meglévő biztonsági másolatokat, amíg a hosszabb megőrzési időszak végéig el nem éri.

> [!NOTE]
> Ezek az API-k csak a PITR megőrzési időszak. Ha az LTR-t az adatbázishoz konfigurálta, az nem lesz hatással. Az LTR megőrzési időszakának módosításáról a [Hosszú távú megőrzés](sql-database-long-term-retention.md)című témakörben talál további információt.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>A PITR biztonsági mentési megőrzési időszakának módosítása az Azure Portal használatával

A PITR biztonsági mentési megőrzési időszakának módosítása az Azure Portal használatával, nyissa meg a kiszolgálóobjektumot, amelynek megőrzési időszakát módosítani szeretné a portálon. Ezután válassza ki a megfelelő beállítást a módosítani kívánt kiszolgálóobjektum alapján.

#### <a name="single-database-and-elastic-database-pools"></a>[Egyetlen adatbázis- és rugalmas adatbáziskészletek](#tab/single-database)

A PITR biztonsági mentési megőrzése egyetlen Azure SQL-adatbázisok ban történik a kiszolgáló szintjén. A kiszolgáló szintjén végrehajtott módosítások a kiszolgálón lévő adatbázisokra vonatkoznak. Ha módosítani szeretné a PITR-megőrzést egy Azure SQL Database-kiszolgálóhoz az Azure Portalról, nyissa meg a kiszolgáló áttekintése panelt. Válassza a **Biztonsági másolatok kezelése lehetőséget** a bal oldali ablaktáblában, majd a képernyő tetején válassza az **Adatmegőrzés konfigurálása** lehetőséget:

![PITR-megőrzés módosítása, kiszolgálói szint](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

A PITR biztonsági mentési megőrzésének módosításai az SQL Database által felügyelt példányok esetében egyéni adatbázisszinten történnek. A PITR biztonsági mentési megőrzése egy példány adatbázis ának az Azure Portalról, nyissa meg az egyes adatbázis-áttekintési panel. Ezután válassza a **Biztonsági mentés megtartásának konfigurálása** lehetőséget a képernyő tetején:

![PITR-megőrzés módosítása, felügyelt példány](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>A PITR biztonsági mentési megőrzési időszakának módosítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell AzureRM-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. További információ: [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Az modul parancsainak argumentumai lényegében megegyeznek az AzureRm-modulok parancsaival.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>A PITR biztonsági mentési megőrzési időszakának módosítása a REST API használatával

#### <a name="sample-request"></a>Mintakérelem

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>A kérés törzse

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Mintaválasz

Állapotkód: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

További információ: [Backup Retention REST API.](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)

## <a name="next-steps"></a>További lépések

- Az adatbázis-biztonsági mentések minden üzletmenet-folytonossági és vész-helyreállítási stratégia alapvető részét képezik, mivel védik az adatokat a véletlen sérüléstől vagy törléstől. Az Azure SQL Database üzletmenet-folytonossági megoldásairól az [Üzletmenet folytonosságának áttekintése című témakörben](sql-database-business-continuity.md)olvashat.
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontban az Azure Portal használatával.](sql-database-recovery-using-backups.md)
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontban a PowerShell használatával.](scripts/sql-database-restore-database-powershell.md)
- Az automatikus biztonsági mentések azure Blob storage-ban való hosszú távú megőrzéséből az Azure Portal használatával történő konfigurálásáról, kezeléséről és visszaállításáról [az Azure Portal használatával a Hosszú távú biztonsági mentés megőrzésének kezelése](sql-database-long-term-backup-retention-configure.md)című témakörben talál.
- Az automatikus biztonsági mentések hosszú távú megőrzéséből az Azure Blob storage-ban a PowerShell használatával történő konfigurálásáról, kezeléséről és visszaállításáról a [Hosszú távú biztonsági mentés megőrzésének kezelése a PowerShell használatával](sql-database-long-term-backup-retention-configure.md)című témakörben talál.
