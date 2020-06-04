---
title: Automatikus, Geo-redundáns biztonsági másolatok
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: A Azure SQL Database és az Azure SQL felügyelt példánya néhány percenként automatikusan létrehoz egy helyi adatbázis biztonsági mentését, és az Azure olvasási hozzáférésű geo-redundáns tárterületet használja a Geo-redundancia érdekében.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 62e8bbbc3781c7e27671e8cb303ef2dcad2a30f3
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324334"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatikus biztonsági mentések – Azure SQL Database & SQL felügyelt példánya

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az Azure SQL felügyelt példánya is létrehozza az adatbázis biztonsági másolatait, amelyeket a rendszer a konfigurált megőrzési időtartamig tart. Az Azure [olvasási hozzáférésű geo-redundáns tárolást (ra-GRS)](../../storage/common/storage-redundancy.md) használnak a biztonsági másolatok megőrzésének biztosításához, még akkor is, ha az adatközpont nem érhető el.

Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel az adatok véletlen sérüléstől vagy törléstől való védelme érdekében szükségesek. Ha a biztonsági szabályok megkövetelik, hogy a biztonsági másolatok hosszabb ideig is elérhetők legyenek (akár 10 évig), konfigurálhatja a [hosszú távú adatmegőrzést](long-term-retention-overview.md) mind az önálló, mind a készletezett adatbázisokhoz.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Mi az SQL Database biztonsági másolat?

Mind a SQL Database, mind az SQL felügyelt példánya SQL Server technológiával hozza létre a [teljes biztonsági mentést](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) hetente, a [különbözeti biztonsági mentéseket](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 12 óránként, a [tranzakciós napló biztonsági](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) mentése pedig 5 – 10 percenként. A biztonsági mentések az [ra-GRS Storage-blobokban](../../storage/common/storage-redundancy.md) vannak tárolva, amelyeket egy adatközpont-kimaradás elleni védelem érdekében egy [párosított adatközpontba](../../best-practices-availability-paired-regions.md) replikáltak. Adatbázis visszaállításakor a szolgáltatás határozza meg, hogy a rendszer melyik teljes, különbözeti és tranzakciónapló biztonsági másolatokat szeretné visszaállítani.

A biztonsági másolatokat a következő célokra használhatja:

- A Azure Portal, Azure PowerShell, az Azure CLI vagy a REST API használatával **állítsa vissza a meglévő adatbázist egy korábbi időpontra** a megőrzési időszakon belül. Az önálló és a készletezett adatbázisok esetében a művelet egy új adatbázist hoz létre ugyanazon a kiszolgálón, amely ugyanabban az előfizetésben található, mint az eredeti adatbázis. Felügyelt példányokban ez a művelet létrehozhat egy másolatot az adatbázisról, vagy egy másik felügyelt példányt is ugyanazzal az előfizetéssel.
- A **törölt adatbázis visszaállítása a törlés időpontjára** vagy a megőrzési időtartamon belül bármikor. A törölt adatbázist csak ugyanazon a kiszolgálón vagy felügyelt példányon lehet visszaállítani, és ugyanabban az előfizetésben, ahol az eredeti adatbázist létrehozták.
- **Adatbázis visszaállítása egy másik földrajzi régióba**. A Geo-visszaállítás lehetővé teszi a helyreállítást földrajzi katasztrófa esetén, amikor nem fér hozzá a kiszolgálóhoz és az adatbázishoz. Egy új adatbázist hoz létre bármely meglévő kiszolgálón, bárhol a világon.
- Egy adatbázis **visszaállítása egy adott hosszú távú biztonsági másolatból** egy adatbázis vagy készletezett adatbázis esetében, ha az adatbázis hosszú távú adatmegőrzési HÁZIRENDDEL (ltr) van konfigurálva. A LTR lehetővé teszi az adatbázis egy korábbi verziójának visszaállítását [a Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) vagy [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) segítségével, hogy megfeleljen a megfelelőségi kérésnek, vagy az alkalmazás egy régebbi verzióját futtassa. További információkért lásd: [Hosszú távú megőrzés](long-term-retention-overview.md).

A visszaállítás végrehajtásához tekintse meg [az adatbázis biztonsági másolatokból való visszaállítását](recovery-using-backups.md)ismertető témakört.

> [!NOTE]
> Az Azure Storage-ban a *replikálás* kifejezés arra utal, hogy fájlokat másol az egyik helyről a másikra. A Azure SQL Database és az SQL felügyelt példányában az *adatbázis-replikáció* arra utal, hogy több másodlagos adatbázis is szinkronizálva van egy elsődleges adatbázissal.

Az alábbi példákkal kipróbálhatja a következő műveleteket:

| | Az Azure Portal | Azure PowerShell |
|---|---|---|
| Biztonsági másolatok megőrzésének módosítása | [Önálló adatbázis](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Felügyelt példány](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Önálló adatbázis](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| A biztonsági másolatok hosszú távú megőrzésének módosítása | [Önálló adatbázis](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Felügyelt példány – N/A  | [Önálló adatbázis](long-term-backup-retention-configure.md)<br/>Felügyelt példány – N/A  |
| Adatbázis visszaállítása egy adott időpontban | [Önálló adatbázis](recovery-using-backups.md#point-in-time-restore) | [Önálló adatbázis](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Törölt adatbázis visszaállítása | [Önálló adatbázis](recovery-using-backups.md) | [Önálló adatbázis](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Adatbázis visszaállítása az Azure Blob Storage-ból | Önálló adatbázis – N/A <br/>Felügyelt példány – N/A  | Önálló adatbázis – N/A <br/>[Felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A SQL Database és az SQL felügyelt példánya támogatja az önkiszolgáló visszaállítást (PITR) a teljes biztonsági másolatok, a különbözeti biztonsági másolatok és a tranzakciónapló biztonsági mentéseinak automatikus létrehozásával. A teljes adatbázis biztonsági mentése hetente jön létre, és a különbözeti adatbázis biztonsági mentései általában 12 óránként jönnek létre. A tranzakciónaplók biztonsági mentései általában 5 – 10 percenként jönnek létre. A tranzakciónapló biztonsági mentései gyakorisága a számítási mérettől és az adatbázis-tevékenység mennyiségétől függ.

Az első teljes biztonsági mentést az adatbázis létrehozása után azonnal ütemezi a rendszer. Ez a biztonsági mentés általában 30 percen belül elvégezhető, de hosszabb időt is igénybe vehet, ha az adatbázis nagy méretű. A kezdeti biztonsági mentés például hosszabb időt vehet igénybe egy visszaállított adatbázison vagy adatbázis-másolaton. Az első teljes biztonsági mentés után a további mentések felhasználói beavatkozás nélkül, a háttérben lesznek automatikusan ütemezve és felügyelve. Az adatbázis biztonsági mentésének pontos időzítését SQL Database vagy SQL felügyelt példány határozza meg, mivel a rendszer a teljes rendszerterhelést kiegyensúlyozza. A biztonsági mentési feladatok nem módosíthatók és nem tilthatók le.

### <a name="default-backup-retention-period"></a>Alapértelmezett biztonsági mentés megőrzési ideje

A PITR biztonsági mentések földrajzilag redundáns tárolással védettek. További információ: [Azure Storage-redundancia](../../storage/common/storage-redundancy.md).

További információ a PITR: [időponthoz való visszaállítás](recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Hosszú távú megőrzés

Az önálló és a készletezett adatbázisok esetében a teljes biztonsági másolatok hosszú távú megőrzését (LTR) akár 10 évig is beállíthatja az Azure Blob Storage-ban. Ha engedélyezi a LTR szabályzatot, a hetente teljes biztonsági mentést a rendszer automatikusan átmásolja egy másik RA-GRS tárolóba. A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy éves biztonsági mentések esetében eltérő megőrzési időt választhat. A tárterület-használat a biztonsági mentések és a megőrzési időszak vagy időszakok kiválasztott gyakoriságával függ. A [ltr díjszabási számológép](https://azure.microsoft.com/pricing/calculator/?service=sql-database) használatával megbecsülheti a ltr-tároló költségeit.

A PITR biztonsági mentésekhez hasonlóan a LTR biztonsági mentések a földrajzilag redundáns tárolással védettek. További információ: [Azure Storage-redundancia](../../storage/common/storage-redundancy.md).

További információ a LTR: a [biztonsági másolatok hosszú távú megőrzése](long-term-retention-overview.md).

## <a name="backup-storage-consumption"></a>Biztonsági másolatok tárolásának felhasználása

Az önálló adatbázisok és a felügyelt példányok esetében ez az egyenlet a teljes biztonsági mentési tár használatának kiszámítására szolgál:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

A készletezett adatbázisok esetében a teljes biztonsági mentési tárterület összesített mérete a készlet szintjén történik, és a következőképpen számítjuk ki:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Azok a biztonsági másolatok, amelyek a megőrzési időszak előtt történnek, az időbélyegző alapján automatikusan törlődnek. Mivel a differenciált biztonsági másolatok és a naplók biztonsági mentései egy korábbi teljes biztonsági mentést igényelnek, a rendszer hetente kiüríti őket.

A SQL Database és az SQL felügyelt példánya összesített értékként számítja ki az összes megőrzési biztonságimásolat-tárolót. Ezt az értéket óránként jelenteni kell az Azure számlázási folyamatnak, amelynek feladata az óránkénti használat összesítése az egyes hónapok végén kiszámított felhasználás kiszámításához. Az adatbázis eldobása után a felhasználás a biztonsági mentések kora értékre csökken. Ha a biztonsági másolatok régebbiek lesznek a megőrzési időtartamnál, a számlázás leáll.
   
   > [!IMPORTANT]
   > Az adatbázisok biztonsági másolatait a rendszer megőrzi a megadott megőrzési időtartamra, még akkor is, ha az adatbázis el lett dobva. Az adatbázisok eldobása és újbóli létrehozása közben a tárolási és a számítási költségek is menthetők, így növelheti a biztonsági másolatok tárolási költségeit, mivel a Microsoft minden eldobott adatbázis esetében megőrzi a biztonsági mentést az egyes eldobott adatbázisok számára. 

### <a name="monitor-consumption"></a>Használat figyelése

A biztonsági másolatok (teljes, különbözeti és napló) mindegyik típusa külön metrikaként szerepel az adatbázis-figyelés panelen. Az alábbi ábra bemutatja, hogyan figyelheti meg a biztonsági másolatok tárolási felhasználását egy adott adatbázisra vonatkozóan. Ez a funkció jelenleg nem érhető el a felügyelt példányok számára.

![Az adatbázis biztonsági mentési felhasználásának figyelése a Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>A biztonsági másolatok tárolásának finomhangolása

A biztonsági másolatok tárolásának túlzott mérete a munkaterheléstől és az egyes adatbázisok méretétől függ. A biztonsági mentési tár használatának csökkentése érdekében vegye figyelembe a következő hangolási technikák némelyikét:

- Csökkentse a [biztonsági másolatok megőrzési időtartamát](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) az igényeinek megfelelő minimálisra.
- Kerülje a nagyméretű írási műveleteket, például az index újraépítését, gyakrabban, mint amennyire szüksége van.
- A nagyméretű adatterhelési műveletek esetében érdemes [fürtözött oszlopcentrikus indexeket](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)használni, csökkenteni a nem fürtözött indexek számát, és figyelembe venni a tömeges betöltési műveleteket a 1 000 000 körüli sorok száma alapján.
- Az általános célú szolgáltatási szinten a kiépített adattárolás kevésbé költséges, mint a felesleges biztonsági mentési tár díja. Ha folyamatosan nagy mennyiségű biztonsági mentési tárolási költséggel rendelkezik, érdemes lehet növelni az adattárolót, hogy mentse a biztonsági mentési tárolót.
- Ideiglenes eredmények tárolására az ETL-logikában található állandó táblák helyett használjon TempDB. (Csak az SQL felügyelt példányára vonatkozik.)
- Érdemes lehet kikapcsolni a TDE titkosítást olyan adatbázisok esetén, amelyek nem tartalmaznak bizalmas adatokat (például fejlesztési vagy tesztelési adatbázisokat). A nem titkosított adatbázisok biztonsági másolatait általában nagyobb tömörítési aránnyal tömöríti a rendszer.

> [!IMPORTANT]
> Az analitikai data mart \ adatraktár számítási feladataihoz erősen ajánlott [fürtözött oszlopcentrikus indexeket](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)használni, csökkenteni a nem fürtözött indexek számát, és figyelembe venni a tömeges betöltési műveleteket a 1 000 000-es számú sorok száma alapján a felesleges biztonsági másolatok tárolásának csökkentése érdekében.

## <a name="storage-costs"></a>Tárolási költségek

A Storage díjszabása attól függően változik, hogy a DTU modellt vagy a virtuális mag modellt használja-e.

### <a name="dtu-model"></a>DTU-modell

A DTU-modell használata esetén nem számítunk fel további díjat az adatbázisok és a rugalmas adatbázis-készletek biztonsági mentési tárhelyéről.

### <a name="vcore-model"></a>Virtuálismag-alapú modell

SQL Database önálló adatbázisai esetében az adatbázis méretének 100%-ának megfelelő minimális biztonsági mentési tárterület külön díj nélkül elérhető. A SQL Database és az önálló példányok és a példányok SQL felügyelt példányokban lévő rugalmas készletei esetében a minimális biztonsági mentési tár a készlethez vagy a példány méretéhez lefoglalt tárterület 100 százalékával egyenlő, külön díj nélkül biztosítva. A biztonsági mentési tár további felhasználása GB/hó alapon van felszámítva. Ez a további felhasználás az egyes adatbázisok munkaterhelésével és méretétől függ.

A SQL Database és az SQL felügyelt példánya összesített értékként fogja kiszámítani az összes megőrzési biztonsági mentési tárolót. Ezt az értéket minden órában jelenteni kell az Azure számlázási folyamatnak, amelynek feladata az óránkénti használat összesítése, hogy az egyes hónapok végén felhasználja a felhasználást. Az adatbázis eldobása után a Microsoft a biztonsági mentések kora után csökkenti a felhasználást. Ha a biztonsági másolatok régebbiek lesznek a megőrzési időtartamnál, a számlázás leáll. Mivel az összes napló biztonsági mentése és különbözeti biztonsági mentése megmarad a teljes megőrzési időszakra vonatkozóan, a nagy mértékben módosított adatbázisok magasabb biztonsági mentési költségekkel fognak rendelkezni.

Tegyük fel, hogy egy adatbázis 744 GB-nyi biztonsági mentési tárterülettel rendelkezik, és hogy ez az összeg állandó marad egész hónapban. Ha át szeretné alakítani ezt az összesített tárterület-használatot óránkénti használatra, ossza fel 744,0-ra (havonta 31 nap * 24 óra/nap). Így SQL Database fogja jelenteni, hogy az adatbázis óránként 1 GB PITR biztonsági mentést használ. Az Azure-számlázás összesíti ezt a felhasználást, és az egész hónapra vonatkozóan 744 GB-ot fog megjeleníteni. A díj a régió $/GB/month arányán alapul.

Most egy összetettebb példa. Tegyük fel, hogy az adatbázis megőrzése a hónap közepén 14 napig nő. Tegyük fel, hogy ez a növekedés (elméletileg) a teljes biztonsági mentési tárterület 1 488 GB-ra való megkettőzését eredményezi. A SQL Database 1 GB-nyi használatot jelent az 1 – 372. órában. A 373-es és a 744-os óra közötti időszakban 2 GB-ot jelent a használatban. Ez a használat a 1 116 GB/hó utolsó számlára lesz összesítve.

### <a name="monitor-costs"></a>Költségek figyelése

A biztonsági másolatok tárolási költségeinek megismeréséhez lépjen a **Cost Management + számlázás** elemre a Azure Portalban, válassza a **Cost Management**lehetőséget, majd válassza a **Cost Analysis**elemet. Válassza ki a kívánt előfizetést **hatókörként**, majd szűrje azt az időszakot és szolgáltatást, amelyre kíváncsi.

Adjon hozzá egy szűrőt a **szolgáltatás neveként**, majd válassza az **SQL Database** elemet a legördülő listából. A **mérési alkategória** szűrővel válassza ki a szolgáltatás számlázási számlálóját. Önálló adatbázis vagy rugalmas adatbázis-készlet esetén válassza a **pitr egy-egy vagy rugalmas készletet**. Felügyelt példány esetén válassza a **mi pitr biztonsági mentési tár**lehetőséget. A **tárolási** és a **számítási** alkategóriák is hasznosak lehetnek, de nem a biztonsági másolatok tárolási költségeihez vannak társítva.

![A biztonsági mentési tár költséghatékonyságának elemzése](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Biztonsági mentés megőrzése

A SQL Database és az SQL felügyelt példányában található összes adatbázisnak van egy 7 napos alapértelmezett biztonsági mentési megőrzési időtartama. [A biztonsági másolatok megőrzési időszaka](#change-the-pitr-backup-retention-period) a 35 napnál hosszabb ideig is megváltoztatható.

Ha töröl egy adatbázist, az Azure ugyanúgy megőrzi a biztonsági mentéseket, mint az online adatbázisban. Ha például olyan alapszintű adatbázist töröl, amely hét napos megőrzési időtartammal rendelkezik, akkor a négy napos biztonsági mentés még három napig is elmenthető.

Ha a biztonsági mentéseket a maximális megőrzési időtartamnál hosszabb ideig kell megtartania, módosíthatja a biztonsági mentési tulajdonságokat úgy, hogy egy vagy több hosszú távú megőrzési időszakot adjon hozzá az adatbázishoz. További információkért lásd: [Hosszú távú megőrzés](long-term-retention-overview.md).

> [!IMPORTANT]
> A biztonsági másolatok megőrzési időtartamának 1 napra (vagy 1-7 közötti értékre) való beállítása csak a PowerShell vagy a REST API használatával támogatott. Az az. SQL modul minimálisan szükséges verziója: v 2.6.0, vagy a Cloudshellben-on keresztül is végrehajtható, amely mindig a legújabb az. SQL-verzió.

> [!IMPORTANT]
> Ha törli a kiszolgálót vagy a felügyelt példányt, a rendszer a kiszolgáló vagy a felügyelt példány által kezelt összes adatbázist is törli. Nem állíthatók helyre. A törölt kiszolgálók vagy a felügyelt példányok nem állíthatók vissza. Ha azonban hosszú távú adatmegőrzést állított be SQL Database vagy többtényezős példány esetében, a LTR-adatbázisok biztonsági másolatait nem törli a rendszer, és ezek az adatbázisok visszaállíthatók.

## <a name="encrypted-backups"></a>Titkosított biztonsági másolatok

Ha az adatbázis TDE van titkosítva, a biztonsági másolatok automatikusan titkosítva maradnak, beleértve a LTR biztonsági mentéseket is. Ha a TDE engedélyezve van SQL Database vagy SQL felügyelt példányhoz, a biztonsági mentések is titkosítva lesznek. A SQL Database és az SQL felügyelt példány összes új adatbázisa alapértelmezés szerint engedélyezve van a TDE. További információ a TDE-ről: [transzparens adattitkosítás SQL Database & SQL felügyelt példányával](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Biztonsági mentés integritása

Az Azure SQL Engineering csapata folyamatosan ellenőrzi a SQL Database-adatbázisokban található adatbázisok automatikus biztonsági másolatainak visszaállítását. (Ez a teszt nem érhető el az SQL felügyelt példányában.) Az időponthoz tartozó visszaállításkor az adatbázisok a DBCC CHECKDB UTASÍTÁST is megkapják.

Az SQL felügyelt példánya automatikus kezdeti biztonsági mentést `CHECKSUM` végez a natív `RESTORE` paranccsal vagy az Azure adatáttelepítési szolgáltatással visszaállított adatbázisok esetében az áttelepítés befejeződése után.

A sértetlenség-ellenőrzés során észlelt problémák miatt a mérnöki csapat riasztást küld. További információ: az [adatintegritás SQL Databaseban](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Megfelelőség

Ha az adatbázist egy DTU-alapú szolgáltatási rétegből virtuális mag-alapú szolgáltatási rétegre telepíti át, a rendszer megőrzi a PITR megőrzését annak érdekében, hogy az alkalmazás adathelyreállítási házirendje ne legyen veszélyeztetve. Ha az alapértelmezett megőrzés nem felel meg a megfelelőségi követelményeknek, akkor a PowerShell vagy a REST API használatával módosíthatja a PITR megőrzési időtartamát. További információ: [a PITR biztonsági mentés megőrzési időtartamának módosítása](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>A PITR biztonsági mentés megőrzési idejének módosítása

Az alapértelmezett PITR biztonsági mentési megőrzési időszakot a Azure Portal, a PowerShell vagy a REST API használatával módosíthatja. Az alábbi példák bemutatják, hogyan módosíthatja a PITR megőrzését 28 napra.

> [!WARNING]
> Ha csökkenti az aktuális megőrzési időtartamot, az új megőrzési időtartamnál régebbi összes meglévő biztonsági mentés már nem érhető el. Ha az aktuális megőrzési időt emeli, az Azure a hosszabb megőrzési időtartam végéig megőrzi a meglévő biztonsági mentéseket.

> [!NOTE]
> Ezek az API-k csak a PITR megőrzési időszakot érintik. Ha a LTR-t konfigurálta az adatbázishoz, az nem lesz hatással. További információ a LTR megőrzési időtartamának módosításáról: [hosszú távú adatmegőrzés](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a Azure Portal használatával

Ha módosítani szeretné a PITR biztonsági mentési megőrzési időtartamát a Azure Portal használatával, ugorjon arra a kiszolgálói objektumra, amelynek megőrzési időszakát módosítani kívánja a portálon. Ezután válassza ki a megfelelő beállítást a módosítani kívánt kiszolgálói objektum alapján.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

A SQL Database PITR biztonsági mentési megőrzésének módosításai a kiszolgálói szinten hajthatók végre. A kiszolgáló szintjén végrehajtott módosítások a kiszolgálón lévő adatbázisokra vonatkoznak. Ha módosítani szeretné egy kiszolgáló PITR-megőrzését a Azure Portal, lépjen a kiszolgáló áttekintés paneljére. Válassza a **biztonsági másolatok kezelése** lehetőséget a bal oldali ablaktáblán, majd válassza a **megőrzés beállítása** a képernyő tetején:

![PITR-megőrzés, kiszolgáló szintjének módosítása](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Az SQL felügyelt példány PITR biztonsági mentési megőrzésének módosításai egyedi adatbázis-szinten hajthatók végre. Ha módosítani szeretné a PITR biztonsági mentési megőrzését egy példány-adatbázishoz a Azure Portal, lépjen az egyes adatbázisok áttekintés paneljére. Ezután válassza a **biztonsági másolatok megőrzésének konfigurálása** lehetőséget a képernyő tetején:

![PITR-megőrzés, felügyelt példány módosítása](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell-AzureRM modult továbbra is támogatja a SQL Database és az SQL felügyelt példánya, de az az. SQL modul jövőbeli fejlesztése is. További információ: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul parancsaihoz tartozó argumentumok lényegében azonosak a AzureRm-modulokban szereplő parancsokkal.

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

## <a name="next-steps"></a>Következő lépések

- Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel az adatok véletlen sérüléstől vagy törléstől való védelme érdekében szükségesek. SQL Database az üzletmenet-folytonossági megoldásokkal kapcsolatos további információkért lásd: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontra a Azure Portal használatával](recovery-using-backups.md).
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontra a PowerShell használatával](scripts/restore-database-powershell.md).
- Az Azure Blob Storage-ban az Azure Portal használatával történő automatikus biztonsági mentések konfigurálásával, kezelésével és visszaállításával kapcsolatos további információkért lásd: [a biztonsági másolatok hosszú távú megőrzésének kezelése a Azure Portal használatával](long-term-backup-retention-configure.md).
- További információ arról, hogyan konfigurálható, kezelhető és állítható vissza az automatikus biztonsági mentések az Azure Blob Storage-ban a PowerShell használatával: a [biztonsági másolatok hosszú távú megőrzésének kezelése a PowerShell használatával](long-term-backup-retention-configure.md).
