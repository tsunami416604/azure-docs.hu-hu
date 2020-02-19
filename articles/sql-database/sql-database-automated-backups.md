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
ms.openlocfilehash: 16ee8c1e271f0aa3e6565322f9a4a422dd90b8b8
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461772"
---
# <a name="automated-backups"></a>Automatikus biztonsági mentések

A SQL Database automatikusan létrehozza a konfigurált megőrzési időszak időtartamára fenntartott adatbázis-biztonsági másolatokat, és az Azure [olvasási hozzáférés geo-redundáns tárolóját (ra-GRS)](../storage/common/storage-redundancy.md) használja annak érdekében, hogy a rendszer akkor is megőrizzék azokat, ha az adatközpont nem érhető el. Ezek a biztonsági másolatok automatikusan létrejönnek. Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel az adatok véletlen sérüléstől vagy törléstől való védelme érdekében szükségesek. Ha a biztonsági szabályok megkövetelik, hogy a biztonsági másolatok hosszabb ideig is elérhetők legyenek (akár 10 évig), konfigurálhat egy [hosszú távú adatmegőrzést](sql-database-long-term-retention.md) az önálló adatbázisokra és a rugalmas készletekre.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Mi az a SQL Database biztonsági másolat?

A SQL Database a SQL Server technológiával [teljes biztonsági mentést](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) készít hetente, a [különbözeti biztonsági mentést](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 12 óránként, a [tranzakciós napló biztonsági](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) mentését pedig 5-10 percenként. A biztonsági mentések az [ra-GRS Storage-blobokban](../storage/common/storage-redundancy.md) tárolódnak, amelyek egy adatközpont-kimaradás elleni védelem érdekében egy [párosított adatközpontba](../best-practices-availability-paired-regions.md) vannak replikálva. Adatbázis visszaállításakor a szolgáltatás kiszámítja, hogy a teljes, a különbözeti és a tranzakciónapló biztonsági mentéseket vissza kell-e állítani.

Ezeket a biztonsági másolatokat a következő célra használhatja:

- Egy meglévő adatbázis visszaállítása a Azure Portal, Azure PowerShell, Azure CLI vagy REST API használatával a megőrzési időszakon belül a **korábbi időpontra** . Az önálló adatbázisban és a rugalmas készletekben ez a művelet egy új adatbázist fog létrehozni az eredeti adatbázissal megegyező kiszolgálón. Felügyelt példányban ez a művelet létrehozhat egy másolatot az adatbázisról, vagy megegyező vagy eltérő felügyelt példányt ugyanahhoz az előfizetéshez.
- A **törölt adatbázis visszaállítása a törölt** vagy a megőrzési időn belül bármikor. A törölt adatbázis csak ugyanabban a logikai kiszolgálón vagy felügyelt példányban állítható vissza, amelyben az eredeti adatbázis létrejött.
- **Adatbázis visszaállítása egy másik földrajzi régióba**. A Geo-visszaállítás lehetővé teszi a helyreállítást földrajzi katasztrófa esetén, ha nem fér hozzá a kiszolgálóhoz és az adatbázishoz. Egy új adatbázist hoz létre bármely meglévő kiszolgálón, bárhol a világon.
- Egy **adott hosszú távú biztonsági másolatból származó adatbázis visszaállítása** önálló adatbázis vagy rugalmas készlet, ha az adatbázist hosszú távú adatmegőrzési HÁZIRENDDEL (ltr) konfigurálták. A LTR lehetővé teszi az adatbázis egy korábbi verziójának visszaállítását [a Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) vagy [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) segítségével, hogy megfeleljen a megfelelőségi kérésnek, vagy az alkalmazás egy régebbi verzióját futtassa. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).
- A visszaállítás végrehajtásához tekintse meg [az adatbázis biztonsági másolatokból való visszaállítását](sql-database-recovery-using-backups.md)ismertető témakört.

> [!NOTE]
> Az Azure Storage-ban a *replikálás* kifejezés arra utal, hogy fájlokat másol az egyik helyről a másikra. Az SQL *adatbázis-replikációja* arra utal, hogy több másodlagos adatbázis is szinkronizálva van egy elsődleges adatbázissal.

Az alábbi példákkal kipróbálhatja a következő műveleteket:

| | Az Azure Portal | Azure PowerShell |
|---|---|---|
| Biztonsági másolatok megőrzésének módosítása | [önálló adatbázis](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Felügyelt példány](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [önálló adatbázis](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| A biztonsági másolatok hosszú távú megőrzésének módosítása | [Önálló adatbázis](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Felügyelt példány – N/A  | [önálló adatbázis](sql-database-long-term-backup-retention-configure.md)<br/>Felügyelt példány – N/A  |
| Adatbázis visszaállítása az adott időpontban | [Önálló adatbázis](sql-database-recovery-using-backups.md#point-in-time-restore) | [Önálló adatbázis](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Törölt adatbázis visszaállítása | [Önálló adatbázis](sql-database-recovery-using-backups.md) | [Önálló adatbázis](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Adatbázis visszaállítása az Azure Blob Storage | Önálló adatbázis – N/A <br/>Felügyelt példány – N/A  | Önálló adatbázis – N/A <br/>[Felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

SQL Database támogatja az önkiszolgáló szolgáltatást az időponthoz tartozó visszaállításhoz (PITR) a teljes biztonsági mentés, a különbözeti biztonsági másolatok és a tranzakciónapló biztonsági másolatainak automatikus létrehozásával. A teljes adatbázis biztonsági mentése hetente, a különbözeti adatbázis biztonsági mentései általában 12 óránként jönnek létre, és a tranzakciónapló biztonsági mentései általában 5-10 percenként jönnek létre, a számítási méret és az adatbázis-tevékenység mennyisége alapján. Az első teljes biztonsági mentést az adatbázis létrehozása után azonnal ütemezi a rendszer. Általában 30 percen belül befejeződik, de hosszabb időt is igénybe vehet, ha az adatbázis jelentős méretű. A kezdeti biztonsági mentés például hosszabb időt vehet igénybe egy visszaállított adatbázison vagy adatbázis-másolaton. Az első teljes biztonsági mentés után a rendszer az összes további biztonsági mentést automatikusan ütemezi, és csendesen kezeli a háttérben. Az adatbázis biztonsági másolatainak pontos időzítését a SQL Database szolgáltatás határozza meg, mivel a teljes rendszerterhelést kiegyensúlyozza. A biztonsági mentési feladatok nem módosíthatók vagy letilthatók.

A PITR biztonsági mentései a földrajzilag redundáns tárolással védettek. További információ: [Azure Storage-redundancia](../storage/common/storage-redundancy.md).

További információ: [időponthoz való visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore) .

### <a name="long-term-retention"></a>Hosszú távú megőrzés

Az önálló és a készletezett adatbázisok lehetővé teszi a teljes biztonsági másolatok hosszú távú megőrzésének (LTR) konfigurálását akár 10 évig az Azure Blob Storage-ban. Ha a LTR szabályzat engedélyezve van, a rendszer automatikusan átmásolja a heti teljes biztonsági mentést egy másik RA-GRS tárolóba. A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy éves biztonsági mentések esetében eltérő megőrzési időt választhat. A tárterület-használat a biztonsági mentések és a megőrzési időtartam (ok) kiválasztott gyakoriságával függ. A [ltr díjszabási számológép](https://azure.microsoft.com/pricing/calculator/?service=sql-database) használatával megbecsülheti a ltr-tároló költségeit.

A PITR hasonlóan a LTR biztonsági mentések védettek a Geo-redundáns tárolással. További információ: [Azure Storage-redundancia](../storage/common/storage-redundancy.md).

További információ: a [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Biztonsági másolatok tárolásának felhasználása 

Az önálló adatbázisok esetében a biztonsági mentési tárterület teljes kihasználtsága a következőképpen számítható ki:   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`.

A rugalmas készletek esetében a teljes biztonsági mentési tárterület összesített mérete a készlet szintjén történik, és a következőképpen számítjuk ki:   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`. 

A megőrzési időtartamnál régebbi biztonsági mentések az időbélyegző alapján automatikusan törlődnek. Mivel a különbözeti biztonsági másolatok és a naplók biztonsági mentései egy korábbi teljes biztonsági mentést igényelnek, a rendszer hetente megtisztítja őket. 

Azure SQL Database a teljes adatmegőrzési biztonsági mentési tárterületet összesítő értékként számítja ki. Ezt az értéket óránként jelenteni kell az Azure számlázási folyamatának, amelynek feladata az óránkénti használat összesítése az egyes hónapok végén a felhasználás kiszámításához. Az adatbázis eldobása után a felhasználás a biztonsági mentések kora értékre csökken. Ha a biztonsági másolatok régebbiek lesznek a megőrzési időtartamnál, a számlázás leáll. 

   > [!IMPORTANT]
   > Az adatbázisok biztonsági másolatait a rendszer megőrzi a megadott megőrzési időtartamra, még akkor is, ha az adatbázis el lett dobva. Az adatbázis eldobása és újbóli létrehozása közben előfordulhat, hogy a tárolási és a számítási költségek miatt a biztonsági másolatok tárolási költségei megnövekednek, mivel a biztonsági mentést a megadott megőrzési időtartam (amely legalább 7 nap) minden egyes eldobott adatbázis esetében megőrzi. 



### <a name="monitor-consumption"></a>Használat figyelése

A biztonsági másolatok (teljes, különbözeti és napló) minden típusa külön metrikaként van jelenteni az adatbázis figyelési paneljén. Az alábbi ábra bemutatja, hogyan figyelheti meg a biztonsági másolatok tárolási felhasználását egy adott adatbázisra vonatkozóan. Ez a funkció jelenleg nem érhető el a felügyelt példányok számára.

![Az adatbázis biztonsági mentési felhasználásának figyelése a Azure Portal adatbázis-figyelési paneljén](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>A biztonsági mentési tárhely felhasználásának finomhangolása

A biztonsági másolatok tárolásának túlzott mérete az egyes adatbázisok munkaterhelésével és méretétől függ. A következő hangolási technikák bevezetését érdemes megfontolni a biztonsági másolatok tárolási felhasználásának további csökkentése érdekében:

* Csökkentse a [biztonsági másolatok megőrzési időtartamát](#change-pitr-backup-retention-period-using-azure-portal) az igényeinek megfelelő minimálisra.
* Kerülje a nagy írási műveletek a szükségesnél gyakrabban történő elvégzését, például az index újraépítését.
* A nagyméretű adatterhelési műveletek esetében érdemes [fürtözött oszlopcentrikus indexeket](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)használni, csökkenteni a nem fürtözött indexek számát, és a tömeges betöltési műveleteket is figyelembe kell venni a 1 000 000-es sorszámú sorok számával.
* Általános célú szolgáltatási szinten a kiépített adattároló kevésbé költséges, mint a felesleges biztonsági mentési tár díja, mivel a folyamatosan magas biztonsági mentési tárolási költségekkel rendelkező ügyfeleknek érdemes lehet növelni az adattárolást biztonsági mentési tár.
* A TempDB használata az ETL-logikában ideiglenes eredmények tárolásához állandó táblák helyett (csak felügyelt példány esetén alkalmazható).
* Érdemes lehet kikapcsolni a TDE titkosítást olyan adatbázisok esetén, amelyek nem tartalmaznak bizalmas adatokat (például fejlesztési vagy tesztelési adatbázisokat). A nem titkosított adatbázisok biztonsági másolatait általában nagyobb tömörítési aránnyal tömöríti a rendszer.

> [!IMPORTANT]
> Az analitikai, data mart \ adatraktár számítási feladataihoz erősen ajánlott [fürtözött oszlopcentrikus indexek](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)használata, a nem fürtözött indexek számának csökkentése, valamint a tömeges betöltési műveletek elvégzése az 1 000 000-es sorok számával, hogy csökkentse a biztonsági másolatok tárolásának felesleges mennyiségét.


## <a name="storage-costs"></a>Tárolási költségek

A tárolási díj akkor változik, ha a DTU modellt vagy a virtuális mag modellt használja. 

### <a name="dtu-model"></a>DTU modell

Az adatbázisok és a rugalmas készletek biztonsági mentési tárterülete a DTU-modell használatával nem számítható fel külön díj ellenében. 

### <a name="vcore-model"></a>Virtuálismag-alapú modell

Az önálló adatbázisok esetében az adatbázis méretének 100%-ának megfelelő minimális biztonsági mentési tárterület külön díj nélkül elérhető. A rugalmas készletek és a felügyelt példányok esetében a minimális biztonsági mentési tárterület a készlet vagy a példány méretének lefoglalt adattárolójának 100%-a, külön díj nélkül biztosítva. A biztonsági mentési tár további felhasználásának díja GB/hó. Ez a további felhasználás az egyes adatbázisok munkaterhelésével és méretétől függ.

Az Azure SQL DB összesített értékként számítja ki az összes megőrzési biztonsági mentési tárterületet. Ezt az értéket minden órában jelenteni kell az Azure számlázási folyamatnak, amelynek feladata az óránkénti használat összesítése, hogy minden hónap végén lekérje a felhasználást. Az adatbázis eldobása után csökkentjük a felhasználást a biztonsági mentések kora után. Ha a megőrzési időtartamnál régebbiek lesznek, a számlázás leáll. Mivel a napló összes biztonsági mentése és különbözeti biztonsági mentése megmarad a teljes megőrzési időszakra vonatkozóan, a nagy mértékben módosított adatbázisok magasabb biztonsági mentési költségekkel fognak rendelkezni. 

Tegyük fel, hogy az adatbázis 744 GB-nyi biztonsági mentési tárterülettel rendelkezik, és ez az összeg állandó marad egész hónapban. Ha ezt az összesített tárterület-használatot óránkénti használatra szeretné átalakítani, akkor a 744,0-es (havi 31 napos * napi 24 órás) felosztást választja. Az SQL DB tehát minden órában 1 GB PITR biztonsági mentést fog jelenteni az adatbázisban. Az Azure-számlázás ezt az értéket összesíti, és a teljes hónapra vonatkozóan 744 GB-ot jelenít meg, a költségeket pedig az adott régióban a $/GB/mo arány alapján számítjuk fel. 

Most egy összetettebb példa. Tegyük fel, hogy az adatbázis megőrzésének időtartama 14 nap a hónap közepén, és ez (elméletileg) a teljes biztonsági mentési tárterületet 1488 GB-ra megduplázza. Az SQL DB 1 GB-nyi használatot jelent a 1-372 órán belül, majd az 373-744-es órákon belül 2 GB-ot jelent a használatban. Ez a végleges számla 1116 GB/hó. 

### <a name="monitor-costs"></a>Költségek figyelése

A biztonsági másolatok tárolási költségeinek megismeréséhez lépjen a **Cost Management + számlázás** elemre a Azure Portal, válassza a **Cost Management**lehetőséget, majd válassza a **Cost Analysis**elemet. Válassza ki a kívánt előfizetést **hatókörként**, majd szűrje azt az időtartamot és szolgáltatást, amelyre kíváncsi. 

Adjon hozzá egy szűrőt a **szolgáltatás neveként**, majd válassza az **SQL Database** lehetőséget a legördülő listából. A **mérési alkategória** szűrővel válassza ki a szolgáltatás számlázási számlálóját. Egyetlen adatbázis vagy egy rugalmas készlet esetében válassza a **pitr egy-egy vagy rugalmas készletet**. Felügyelt példány esetén válassza a **mi pitr biztonsági mentési tár**lehetőséget. A **tárolási** és a **számítási** alkategóriák is hasznosak lehetnek, bár nincsenek a biztonsági mentési tárolási költségekhez társítva. 

![A biztonsági mentési tár költséghatékonyságának elemzése](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)


## <a name="backup-retention"></a>Biztonsági mentés megőrzése

Az összes Azure SQL-adatbázis (egy, készletezett és felügyelt példány-adatbázis) alapértelmezett biztonsági mentési megőrzési időtartama **hét** nap. [A biztonsági másolatok megőrzési időtartamát akár 35 napig is megváltoztathatja](#change-pitr-backup-retention-period).

Ha töröl egy adatbázist, SQL Database a biztonsági mentések ugyanúgy megmaradnak, mint az online adatbázishoz. Ha például olyan alapszintű adatbázist töröl, amely hét napos megőrzési időtartammal rendelkezik, akkor a négy napos biztonsági mentés még három napig elmenthető.

Ha a biztonsági mentéseket a maximális megőrzési időtartamnál hosszabb ideig kell megtartania, módosíthatja a biztonsági mentési tulajdonságokat úgy, hogy egy vagy több hosszú távú megőrzési időszakot adjon hozzá az adatbázishoz. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Ha törli az SQL-adatbázisokat futtató Azure SQL Servert, a kiszolgálóhoz tartozó összes rugalmas készlet és adatbázis is törlődik, és nem állítható helyre. Törölt kiszolgálót nem lehet visszaállítani. Ha azonban hosszú távú adatmegőrzést konfigurált, a LTR-adatbázisok biztonsági mentéseit nem törli a rendszer, és ezek az adatbázisok helyreállíthatók.

## <a name="encrypted-backups"></a>Titkosított biztonsági másolatok

Ha az adatbázis TDE van titkosítva, a biztonsági másolatok automatikusan titkosítva maradnak, beleértve a LTR biztonsági mentéseket is. Ha a TDE engedélyezve van egy Azure SQL Database-adatbázishoz, a biztonsági mentések is titkosítva lesznek. Az összes új Azure SQL-adatbázis alapértelmezés szerint engedélyezve van a TDE. További információ a TDE-ről: [transzparens adattitkosítás Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Biztonsági mentés integritása

A Azure SQL Database mérnöki csapat folyamatosan ellenőrzi a logikai kiszolgálókon és rugalmas készletekben elhelyezett adatbázisok automatizált adatbázis-biztonsági másolatának visszaállítását (ez a felügyelt példányban nem érhető el). Az időponthoz tartozó visszaállításkor az adatbázisok a DBCC CHECKDB UTASÍTÁST használatával is megkapják az integritási ellenőrzéseket.

A felügyelt példányok automatikus kezdeti biztonsági mentést végeznek `CHECKSUM` a natív `RESTORE` paranccsal vagy az áttelepítési szolgáltatással visszaállított adatbázisok esetében.

A sértetlenség-ellenőrzés során észlelt problémák miatt a mérnöki csapat riasztást küld. A Azure SQL Database adatintegritásával kapcsolatos további információkért lásd: az [adatintegritás a Azure SQL Databaseban](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Megfelelőség

Ha az adatbázist egy DTU-alapú szolgáltatási rétegből virtuális mag-alapú szolgáltatási rétegre telepíti át, a rendszer megőrzi a PITR megőrzését annak érdekében, hogy az alkalmazás adathelyreállítási házirendje ne legyen biztonságban. Ha az alapértelmezett megőrzés nem felel meg a megfelelőségi követelményeknek, megváltoztathatja a PITR megőrzési időtartamát a PowerShell vagy a REST API használatával. További információ: a [biztonsági másolatok megőrzési időszakának módosítása](#change-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>PITR biztonsági másolat megőrzési idejének módosítása

Az alapértelmezett PITR biztonsági mentési megőrzési időszakot a Azure Portal, a PowerShell vagy a REST API használatával módosíthatja. Az alábbi példák bemutatják, hogyan módosíthatja a PITR megőrzését 28 napra.

> [!WARNING]
> Ha csökkenti az aktuális megőrzési időt, az új megőrzési időtartamnál régebbi összes biztonsági mentés már nem érhető el. Ha növeli az aktuális megőrzési időt, SQL Database a hosszabb megőrzési időtartam eléréséig megtartja a meglévő biztonsági mentéseket.

> [!NOTE]
> Ezek az API-k csak a PITR megőrzési időtartamot érintik. Ha a LTR-t konfigurálta az adatbázishoz, az nem lesz hatással. A LTR megőrzési időtartam (ok) módosításával kapcsolatos további információkért lásd: [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>PITR biztonsági mentés megőrzési időszakának módosítása Azure Portal használatával

Ha módosítani szeretné a PITR biztonsági mentés megőrzési időszakát a Azure Portal használatával, Navigáljon arra a kiszolgáló-objektumra, amelynek megőrzési időszakát módosítani szeretné a portálon, majd válassza ki a megfelelő beállítást, amely alapján módosítani kívánja a kiszolgáló objektumát.

#### <a name="single-database--elastic-pools"></a>[Önálló adatbázis & rugalmas készletek](#tab/single-database)

Az Azure SQL Database-adatbázisok PITR biztonsági mentésének megőrzése a kiszolgáló szintjén történik. A kiszolgáló szintjén végzett módosítás az adott kiszolgálón lévő adatbázisokra vonatkozik. Ha Azure SQL Database-kiszolgáló PITR szeretné módosítani Azure Portalról, lépjen a kiszolgáló áttekintés paneljére, kattintson a biztonsági másolatok kezelése elemre a navigációs menüben, majd kattintson a megőrzés konfigurálása a navigációs sávon lehetőségre.

![PITR Azure Portal módosítása](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

SQL Database felügyelt példány PITR biztonsági másolatának megtartásának módosítása külön adatbázis szintjén történik. Ha módosítani szeretné a PITR biztonsági mentési megőrzését egy példány-adatbázisból a Azure Portalból, lépjen az egyes adatbázis-áttekintés panelre, majd kattintson a biztonsági mentés megőrzésének konfigurálása elemre a navigációs sávon.

![PITR Azure Portal módosítása](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PITR biztonsági mentés megőrzési időszakának módosítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>PITR megőrzési időszakának módosítása REST API használatával

#### <a name="sample-request"></a>Kérésminta

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>A kérelem törzse

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

## <a name="next-steps"></a>Következő lépések

- Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel az adatok véletlen sérüléstől vagy törléstől való védelme érdekében szükségesek. Azure SQL Database az üzletmenet-folytonossági megoldásokkal kapcsolatos további információkért lásd: az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).
- Ha a Azure Portal használatával szeretne visszaállítani egy adott időpontra, tekintse meg [az adatbázis visszaállítása egy időpontra a Azure Portal használatával](sql-database-recovery-using-backups.md)című témakört.
- Ha a PowerShell használatával szeretne visszaállítani egy adott időpontra, tekintse meg az [adatbázis visszaállítása egy időpontra a PowerShell használatával](scripts/sql-database-restore-database-powershell.md)című témakört.
- Az Azure Blob Storage-ban az Azure Portal használatával történő automatikus biztonsági mentések hosszú távú megőrzésének konfigurálásához, kezeléséhez és visszaállításához lásd: [a biztonsági másolatok hosszú távú megőrzésének kezelése a Azure Portal használatával](sql-database-long-term-backup-retention-configure.md).
- Az Azure Blob Storage-ban a PowerShell használatával történő automatikus biztonsági mentések hosszú távú megőrzésének konfigurálásához, kezeléséhez és visszaállításához lásd: a [biztonsági másolatok hosszú távú megőrzésének kezelése a PowerShell használatával](sql-database-long-term-backup-retention-configure.md).
