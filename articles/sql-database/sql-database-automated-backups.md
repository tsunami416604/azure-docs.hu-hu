---
title: Automatikus, Geo-redundáns biztonsági másolatok
description: A SQL Database néhány percenként automatikusan létrehoz egy helyi adatbázis biztonsági mentését, és az Azure olvasási hozzáférésű geo-redundáns tárterületet használja a Geo-redundancia érdekében.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061761"
---
# <a name="automated-backups"></a>Automatikus biztonsági mentések

A Azure SQL Database automatikusan létrehozza a konfigurált megőrzési időszak időtartamára megőrzött adatbázis-biztonsági másolatokat. Az Azure [olvasási hozzáférésű geo-redundáns tárolást (ra-GRS)](../storage/common/storage-redundancy.md) használ annak biztosítására, hogy a biztonsági másolatok megmaradjanak, még akkor is, ha az adatközpont nem érhető el.

Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel az adatok véletlen sérüléstől vagy törléstől való védelme érdekében szükségesek. Ha a biztonsági szabályok megkövetelik, hogy a biztonsági másolatok hosszabb ideig is elérhetők legyenek (akár 10 évig), konfigurálhat [hosszú távú adatmegőrzést](sql-database-long-term-retention.md) az önálló adatbázisokhoz és a rugalmas adatbázis-készletekhez.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Mi az SQL Database biztonsági másolat?

A SQL Database a SQL Server technológiával [teljes biztonsági mentést](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) készít hetente, a [különbözeti biztonsági mentést](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 12 óránként, a [tranzakciós napló biztonsági](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) mentését pedig 5 – 10 percenként. A biztonsági mentések az [ra-GRS Storage-blobokban](../storage/common/storage-redundancy.md) vannak tárolva, amelyeket egy adatközpont-kimaradás elleni védelem érdekében egy [párosított adatközpontba](../best-practices-availability-paired-regions.md) replikáltak. Adatbázis visszaállításakor a szolgáltatás határozza meg, hogy a rendszer melyik teljes, különbözeti és tranzakciónapló biztonsági másolatokat szeretné visszaállítani.

A biztonsági másolatokat a következő célokra használhatja:

- A Azure Portal, Azure PowerShell, az Azure CLI vagy a REST API használatával **állítsa vissza a meglévő adatbázist egy korábbi időpontra** a megőrzési időszakon belül. Az önálló adatbázisok és a rugalmas adatbázis-készletek esetében ez a művelet egy új adatbázist fog létrehozni az eredeti adatbázissal megegyező kiszolgálón. Felügyelt példányban ez a művelet létrehozhat egy másolatot az adatbázisról, vagy egy másik felügyelt példányt is ugyanazzal az előfizetéssel.
- A **törölt adatbázis visszaállítása a törlés időpontjára** vagy a megőrzési időtartamon belül bármikor. A törölt adatbázist csak ugyanazon a logikai kiszolgálón vagy felügyelt példányon lehet visszaállítani, ahol az eredeti adatbázis létrejött.
- **Adatbázis visszaállítása egy másik földrajzi régióba**. A Geo-visszaállítás lehetővé teszi a helyreállítást földrajzi katasztrófa esetén, amikor nem fér hozzá a kiszolgálóhoz és az adatbázishoz. Egy új adatbázist hoz létre bármely meglévő kiszolgálón, bárhol a világon.
- Egy adatbázis **visszaállítása egy adott hosszú távú biztonsági másolatból** egy adatbázison vagy rugalmas adatbázis-készleten, ha az adatbázis hosszú távú adatmegőrzési HÁZIRENDDEL (ltr) van konfigurálva. A LTR lehetővé teszi az adatbázis egy korábbi verziójának visszaállítását [a Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) vagy [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) segítségével, hogy megfeleljen a megfelelőségi kérésnek, vagy az alkalmazás egy régebbi verzióját futtassa. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

A visszaállítás végrehajtásához tekintse meg [az adatbázis biztonsági másolatokból való visszaállítását](sql-database-recovery-using-backups.md)ismertető témakört.

> [!NOTE]
> Az Azure Storage-ban a *replikálás* kifejezés arra utal, hogy fájlokat másol az egyik helyről a másikra. SQL Server *adatbázis-replikáció* azt jelenti, hogy több másodlagos adatbázis is szinkronizálva van egy elsődleges adatbázissal.

Az alábbi példákkal kipróbálhatja a következő műveleteket:

| | Az Azure Portal | Azure PowerShell |
|---|---|---|
| Biztonsági másolatok megőrzésének módosítása | [Önálló adatbázis](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Felügyelt példány](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Önálló adatbázis](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| A biztonsági másolatok hosszú távú megőrzésének módosítása | [Önálló adatbázis](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Felügyelt példány – N/A  | [Önálló adatbázis](sql-database-long-term-backup-retention-configure.md)<br/>Felügyelt példány – N/A  |
| Adatbázis visszaállítása egy adott időpontban | [Önálló adatbázis](sql-database-recovery-using-backups.md#point-in-time-restore) | [Önálló adatbázis](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Törölt adatbázis visszaállítása | [Önálló adatbázis](sql-database-recovery-using-backups.md) | [Önálló adatbázis](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Adatbázis visszaállítása az Azure Blob Storage-ból | Önálló adatbázis – N/A <br/>Felügyelt példány – N/A  | Önálló adatbázis – N/A <br/>[Felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

SQL Database támogatja az önkiszolgáló szolgáltatást az időponthoz tartozó visszaállításhoz (PITR) a teljes biztonsági mentések, a különbözeti biztonsági másolatok és a tranzakciónapló biztonsági másolatainak automatikus létrehozásával. A teljes adatbázis biztonsági mentése hetente jön létre, és a különbözeti adatbázis biztonsági mentései általában 12 óránként jönnek létre. A tranzakciónaplók biztonsági mentései általában 5 – 10 percenként jönnek létre. A tranzakciónapló biztonsági mentései gyakorisága a számítási mérettől és az adatbázis-tevékenység mennyiségétől függ. 

Az első teljes biztonsági mentést az adatbázis létrehozása után azonnal ütemezi a rendszer. Ez a biztonsági mentés általában 30 percen belül elvégezhető, de hosszabb időt is igénybe vehet, ha az adatbázis nagy méretű. A kezdeti biztonsági mentés például hosszabb időt vehet igénybe egy visszaállított adatbázison vagy adatbázis-másolaton. Az első teljes biztonsági mentés után a további mentések felhasználói beavatkozás nélkül, a háttérben lesznek automatikusan ütemezve és felügyelve. Az adatbázis-mentések pontos időzítését az SQL Database szolgáltatás határozza meg a teljes rendszer terhelésének kiegyensúlyozásával. A biztonsági mentési feladatok nem módosíthatók és nem tilthatók le.

A PITR biztonsági mentések földrajzilag redundáns tárolással védettek. További információ: [Azure Storage-redundancia](../storage/common/storage-redundancy.md).

További információ a PITR: [időponthoz való visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Hosszú távú megőrzés

Az önálló és a készletezett adatbázisok esetében a teljes biztonsági másolatok hosszú távú megőrzését (LTR) akár 10 évig is beállíthatja az Azure Blob Storage-ban. Ha engedélyezi a LTR szabályzatot, a hetente teljes biztonsági mentést a rendszer automatikusan átmásolja egy másik RA-GRS tárolóba. A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy éves biztonsági mentések esetében eltérő megőrzési időt választhat. A tárterület-használat a biztonsági mentések és a megőrzési időszak vagy időszakok kiválasztott gyakoriságával függ. A [ltr díjszabási számológép](https://azure.microsoft.com/pricing/calculator/?service=sql-database) használatával megbecsülheti a ltr-tároló költségeit.

A PITR biztonsági mentésekhez hasonlóan a LTR biztonsági mentések a földrajzilag redundáns tárolással védettek. További információ: [Azure Storage-redundancia](../storage/common/storage-redundancy.md).

További információ a LTR: a [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Biztonsági másolatok tárolásának felhasználása

Az önálló adatbázisok esetében ez az egyenlet a biztonsági mentési tárterület teljes használatának kiszámítására szolgál:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

A rugalmas adatbázis-készletek esetében a teljes biztonsági mentési tárterület összesített mérete a készlet szintjén történik, és a következőképpen számítjuk ki:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Azok a biztonsági másolatok, amelyek a megőrzési időszak előtt történnek, az időbélyegző alapján automatikusan törlődnek. Mivel a differenciált biztonsági másolatok és a naplók biztonsági mentései egy korábbi teljes biztonsági mentést igényelnek, a rendszer hetente kiüríti őket.

Azure SQL Database a teljes adatmegőrzési biztonsági mentési tárterületet összesítő értékként számítja ki. Ezt az értéket óránként jelenteni kell az Azure számlázási folyamatnak, amelynek feladata az óránkénti használat összesítése az egyes hónapok végén kiszámított felhasználás kiszámításához. Az adatbázis eldobása után a felhasználás a biztonsági mentések kora értékre csökken. Ha a biztonsági másolatok régebbiek lesznek a megőrzési időtartamnál, a számlázás leáll.

   > [!IMPORTANT]
   > Az adatbázisok biztonsági másolatait a rendszer megőrzi a megadott megőrzési időtartamra, még akkor is, ha az adatbázis el lett dobva. Az adatbázisok eldobása és újbóli létrehozása közben a tárolási és a számítási költségek is megváltozhatnak, így növelheti a biztonsági másolatok tárolási költségeit, mivel a Microsoft minden egyes eldobott adatbázis esetében megőrzi a biztonsági mentést a megadott megőrzési időtartamra (amely legalább 7 nap) az egyes eldobott adatbázisok esetében.

### <a name="monitor-consumption"></a>Használat figyelése

A biztonsági másolatok (teljes, különbözeti és napló) mindegyik típusa külön metrikaként szerepel az adatbázis-figyelés panelen. Az alábbi ábra bemutatja, hogyan figyelheti meg a biztonsági másolatok tárolási felhasználását egy adott adatbázisra vonatkozóan. Ez a funkció jelenleg nem érhető el a felügyelt példányok számára.

![Az adatbázis biztonsági mentési felhasználásának figyelése a Azure Portal](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>A biztonsági másolatok tárolásának finomhangolása

A biztonsági másolatok tárolásának túlzott mérete a munkaterheléstől és az egyes adatbázisok méretétől függ. A biztonsági mentési tár használatának csökkentése érdekében vegye figyelembe a következő hangolási technikák némelyikét:

* Csökkentse a [biztonsági másolatok megőrzési időtartamát](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) az igényeinek megfelelő minimálisra.
* Kerülje a nagyméretű írási műveleteket, például az index újraépítését, gyakrabban, mint amennyire szüksége van.
* A nagyméretű adatterhelési műveletek esetében érdemes [fürtözött oszlopcentrikus indexeket](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)használni, csökkenteni a nem fürtözött indexek számát, és figyelembe venni a tömeges betöltési műveleteket a 1 000 000 körüli sorok száma alapján.
* Az általános célú szolgáltatási szinten a kiépített adattárolás kevésbé költséges, mint a felesleges biztonsági mentési tár díja. Ha folyamatosan nagy mennyiségű biztonsági mentési tárolási költséggel rendelkezik, érdemes lehet növelni az adattárolót, hogy mentse a biztonsági mentési tárolót.
* Ideiglenes eredmények tárolására az ETL-logikában található állandó táblák helyett használjon TempDB. (Csak a felügyelt példányra vonatkozik.)
* Érdemes lehet kikapcsolni a TDE titkosítást olyan adatbázisok esetén, amelyek nem tartalmaznak bizalmas adatokat (például fejlesztési vagy tesztelési adatbázisokat). A nem titkosított adatbázisok biztonsági másolatait általában nagyobb tömörítési aránnyal tömöríti a rendszer.

> [!IMPORTANT]
> Az analitikai data mart \ adatraktár számítási feladataihoz erősen ajánlott [fürtözött oszlopcentrikus indexeket](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)használni, csökkenteni a nem fürtözött indexek számát, és figyelembe venni a tömeges betöltési műveleteket a 1 000 000-es számú sorok száma alapján a felesleges biztonsági másolatok tárolásának csökkentése érdekében.

## <a name="storage-costs"></a>Tárolási költségek

A Storage díjszabása attól függően változik, hogy a DTU modellt vagy a virtuális mag modellt használja-e.

### <a name="dtu-model"></a>DTU-modell

A DTU-modell használata esetén nem számítunk fel további díjat az adatbázisok és a rugalmas adatbázis-készletek biztonsági mentési tárhelyéről.

### <a name="vcore-model"></a>Virtuálismag-alapú modell

Az önálló adatbázisok esetében az adatbázis méretének 100%-ának megfelelő minimális biztonsági mentési tárterület külön díj nélkül elérhető. A rugalmas adatbázis-készletek és a felügyelt példányok esetében a minimális biztonsági mentési tárterület a készlethez vagy a példány méretéhez lefoglalt tárterület 100 százalékával egyenlő, külön díj nélkül. A biztonsági mentési tár további felhasználása GB/hó alapon van felszámítva. Ez a további felhasználás az egyes adatbázisok munkaterhelésével és méretétől függ.

Azure SQL Database a teljes adatmegőrzési biztonsági mentési tárterületet összesítő értékként számítja ki. Ezt az értéket minden órában jelenteni kell az Azure számlázási folyamatnak, amelynek feladata az óránkénti használat összesítése, hogy az egyes hónapok végén felhasználja a felhasználást. Az adatbázis eldobása után a Microsoft a biztonsági mentések kora után csökkenti a felhasználást. Ha a biztonsági másolatok régebbiek lesznek a megőrzési időtartamnál, a számlázás leáll. Mivel az összes napló biztonsági mentése és különbözeti biztonsági mentése megmarad a teljes megőrzési időszakra vonatkozóan, a nagy mértékben módosított adatbázisok magasabb biztonsági mentési költségekkel fognak rendelkezni.

Tegyük fel, hogy egy adatbázis 744 GB-nyi biztonsági mentési tárterülettel rendelkezik, és hogy ez az összeg állandó marad egész hónapban. Ha át szeretné alakítani ezt az összesített tárterület-használatot óránkénti használatra, ossza fel 744,0-ra (havonta 31 nap * 24 óra/nap). Így SQL Database fogja jelenteni, hogy az adatbázis óránként 1 GB PITR biztonsági mentést használ. Az Azure-számlázás összesíti ezt a felhasználást, és az egész hónapra vonatkozóan 744 GB-ot fog megjeleníteni. A díj a régió $/GB/month arányán alapul.

Most egy összetettebb példa. Tegyük fel, hogy az adatbázis megőrzése a hónap közepén 14 napig nő. Tegyük fel, hogy ez a növekedés (elméletileg) a teljes biztonsági mentési tárterület 1 488 GB-ra való megkettőzését eredményezi. A SQL Database 1 GB-nyi használatot jelent az 1 – 372. órában. A 373-es és a 744-os óra közötti időszakban 2 GB-ot jelent a használatban. Ez a használat a 1 116 GB/hó utolsó számlára lesz összesítve.

### <a name="monitor-costs"></a>Költségek figyelése

A biztonsági másolatok tárolási költségeinek megismeréséhez lépjen a **Cost Management + számlázás** elemre a Azure Portalban, válassza a **Cost Management**lehetőséget, majd válassza a **Cost Analysis**elemet. Válassza ki a kívánt előfizetést **hatókörként**, majd szűrje azt az időszakot és szolgáltatást, amelyre kíváncsi.

Adjon hozzá egy szűrőt a **szolgáltatás neveként**, majd válassza az **SQL Database** elemet a legördülő listából. A **mérési alkategória** szűrővel válassza ki a szolgáltatás számlázási számlálóját. Önálló adatbázis vagy rugalmas adatbázis-készlet esetén válassza a **pitr egy-egy vagy rugalmas készletet**. Felügyelt példány esetén válassza a **mi pitr biztonsági mentési tár**lehetőséget. A **tárolási** és a **számítási** alkategóriák is hasznosak lehetnek, de nem a biztonsági másolatok tárolási költségeihez vannak társítva.

![A biztonsági mentési tár költséghatékonyságának elemzése](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Biztonsági mentés megőrzése

Az összes Azure SQL Database-adatbázis (egy, készletezett és felügyelt példány-adatbázis) alapértelmezett biztonsági mentési megőrzési időtartama 7 nap. [A biztonsági másolatok megőrzési időszaka](#change-the-pitr-backup-retention-period) a 35 napnál hosszabb ideig is megváltoztatható.

Ha töröl egy adatbázist, SQL Database a biztonsági mentések ugyanúgy megmaradnak, mint az online adatbázishoz. Ha például olyan alapszintű adatbázist töröl, amely hét napos megőrzési időtartammal rendelkezik, akkor a négy napos biztonsági mentés még három napig is elmenthető.

Ha a biztonsági mentéseket a maximális megőrzési időtartamnál hosszabb ideig kell megtartania, módosíthatja a biztonsági mentési tulajdonságokat úgy, hogy egy vagy több hosszú távú megőrzési időszakot adjon hozzá az adatbázishoz. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Ha törli az SQL-adatbázisokat futtató Azure SQL Servert, a kiszolgálóhoz tartozó összes rugalmas adatbázis-készlet és adatbázis is törlődik. Nem állíthatók helyre. A törölt kiszolgálók nem állíthatók vissza. Ha azonban hosszú távú adatmegőrzést konfigurált, a LTR-adatbázisok biztonsági másolatait nem törli a rendszer, és ezek az adatbázisok visszaállíthatók.

## <a name="encrypted-backups"></a>Titkosított biztonsági másolatok

Ha az adatbázis TDE van titkosítva, a biztonsági másolatok automatikusan titkosítva maradnak, beleértve a LTR biztonsági mentéseket is. Ha a TDE engedélyezve van egy Azure SQL Database-adatbázishoz, a biztonsági mentések is titkosítva lesznek. Az összes új Azure SQL-adatbázis alapértelmezés szerint engedélyezve van a TDE. További információ a TDE-ről: [transzparens adattitkosítás Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Biztonsági mentés integritása

A Azure SQL Database mérnöki csapat folyamatosan ellenőrzi a logikai kiszolgálókon és a rugalmas adatbázis-készletekben elhelyezett adatbázisok automatizált adatbázis-biztonsági másolatának visszaállítását. (Ez a teszt a felügyelt példányban nem érhető el.) Az időponthoz tartozó visszaállításkor az adatbázisok a DBCC CHECKDB UTASÍTÁST is megkapják.

A felügyelt példány az áttelepítés befejezése `CHECKSUM` után a natív `RESTORE` paranccsal vagy az Azure adatáttelepítési szolgáltatással visszaállított adatbázisok automatikus kezdeti biztonsági mentését végzi.

A sértetlenség-ellenőrzés során észlelt problémák miatt a mérnöki csapat riasztást küld. További információ: az [adatintegritás Azure SQL Databaseban](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Megfelelőség

Ha az adatbázist egy DTU-alapú szolgáltatási rétegből virtuális mag-alapú szolgáltatási rétegre telepíti át, a rendszer megőrzi a PITR megőrzését annak érdekében, hogy az alkalmazás adathelyreállítási házirendje ne legyen veszélyeztetve. Ha az alapértelmezett megőrzés nem felel meg a megfelelőségi követelményeknek, akkor a PowerShell vagy a REST API használatával módosíthatja a PITR megőrzési időtartamát. További információ: [a PITR biztonsági mentés megőrzési időtartamának módosítása](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>A PITR biztonsági mentés megőrzési idejének módosítása

Az alapértelmezett PITR biztonsági mentési megőrzési időszakot a Azure Portal, a PowerShell vagy a REST API használatával módosíthatja. Az alábbi példák bemutatják, hogyan módosíthatja a PITR megőrzését 28 napra.

> [!WARNING]
> Ha csökkenti az aktuális megőrzési időtartamot, az új megőrzési időtartamnál régebbi összes meglévő biztonsági mentés már nem érhető el. Ha növeli az aktuális megőrzési időt, SQL Database a hosszabb megőrzési időtartam végéig megtartja a meglévő biztonsági mentéseket.

> [!NOTE]
> Ezek az API-k csak a PITR megőrzési időszakot érintik. Ha a LTR-t konfigurálta az adatbázishoz, az nem lesz hatással. További információ a LTR megőrzési időtartamának módosításáról: [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a Azure Portal használatával

Ha módosítani szeretné a PITR biztonsági mentési megőrzési időtartamát a Azure Portal használatával, ugorjon arra a kiszolgálói objektumra, amelynek megőrzési időszakát módosítani kívánja a portálon. Ezután válassza ki a megfelelő beállítást a módosítani kívánt kiszolgálói objektum alapján.

#### <a name="single-database-and-elastic-database-pools"></a>[Önálló adatbázis és rugalmas adatbázis-készletek](#tab/single-database)

Az egyes Azure SQL-adatbázisok PITR biztonsági mentésének megőrzése a kiszolgálói szinten történik. A kiszolgáló szintjén végrehajtott módosítások a kiszolgálón lévő adatbázisokra vonatkoznak. Ha módosítani szeretné egy Azure SQL Database-kiszolgáló PITR megőrzését a Azure Portal, lépjen a kiszolgáló áttekintés paneljére. Válassza a **biztonsági másolatok kezelése** lehetőséget a bal oldali ablaktáblán, majd válassza a **megőrzés beállítása** a képernyő tetején:

![PITR-megőrzés, kiszolgáló szintjének módosítása](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

A SQL Database felügyelt példányok PITR biztonsági mentési megőrzésének módosításai egy adott adatbázis szintjén hajthatók végre. Ha módosítani szeretné a PITR biztonsági mentési megőrzését egy példány-adatbázishoz a Azure Portal, lépjen az egyes adatbázisok áttekintés paneljére. Ezután válassza a **biztonsági másolatok megőrzésének konfigurálása** lehetőséget a képernyő tetején:

![PITR-megőrzés, felügyelt példány módosítása](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database továbbra is támogatja a PowerShell AzureRM modult, de a jövőbeli fejlesztés az az. SQL modulhoz készült. További információ: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul parancsaihoz tartozó argumentumok lényegében azonosak a AzureRm-modulokban szereplő parancsokkal.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a REST API használatával

#### <a name="sample-request"></a>Példa a kérelemre

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

További információ: a [biztonsági másolatok megőrzésének REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>További lépések

- Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel az adatok véletlen sérüléstől vagy törléstől való védelme érdekében szükségesek. Azure SQL Database az üzletmenet-folytonossági megoldásokkal kapcsolatos további információkért lásd: az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontra a Azure Portal használatával](sql-database-recovery-using-backups.md).
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontra a PowerShell használatával](scripts/sql-database-restore-database-powershell.md).
- Az Azure Blob Storage-ban az Azure Portal használatával történő automatikus biztonsági mentések konfigurálásával, kezelésével és visszaállításával kapcsolatos további információkért lásd: [a biztonsági másolatok hosszú távú megőrzésének kezelése a Azure Portal használatával](sql-database-long-term-backup-retention-configure.md).
- További információ arról, hogyan konfigurálható, kezelhető és állítható vissza az automatikus biztonsági mentések az Azure Blob Storage-ban a PowerShell használatával: a [biztonsági másolatok hosszú távú megőrzésének kezelése a PowerShell használatával](sql-database-long-term-backup-retention-configure.md).
