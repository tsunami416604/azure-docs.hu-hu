---
title: Automatikus, Geo-redundáns biztonsági másolatok
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: A Azure SQL Database és az Azure SQL felügyelt példánya néhány percenként automatikusan létrehoz egy helyi adatbázis biztonsági mentését, és az Azure olvasási hozzáférésű geo-redundáns tárterületet használja a Geo-redundancia érdekében.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 06/04/2020
ms.openlocfilehash: 340f4310da5131ea0d2576e7c77d8f6cd0a731b3
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983104"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatikus biztonsági mentések – Azure SQL Database & SQL felügyelt példánya

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Mi az adatbázis biztonsági mentése?

Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel ezek az adatok a sérüléstől vagy törléstől védve vannak.

Mind a SQL Database, mind az SQL felügyelt példánya SQL Server technológiával [teljes biztonsági mentést](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) készít minden héten, a [különbözeti biztonsági mentéseket](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 12-24 óránként, a [tranzakciós napló biztonsági](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) mentését pedig 5 – 10 percenként. A tranzakciónapló biztonsági mentései gyakorisága a számítási mérettől és az adatbázis-tevékenység mennyiségétől függ.

Adatbázis visszaállításakor a szolgáltatás határozza meg, hogy a rendszer melyik teljes, különbözeti és tranzakciónapló biztonsági másolatokat szeretné visszaállítani.

Ezek a biztonsági másolatok lehetővé teszik az adatbázis visszaállítását egy adott időpontra a beállított megőrzési időtartamon belül. A biztonsági másolatok olyan [ra-GRS tároló Blobok](../../storage/common/storage-redundancy.md) , amelyek egy [párosított régióba](../../best-practices-availability-paired-regions.md) replikálódnak az elsődleges régióban található biztonsági mentési tárterületet érintő kimaradások elleni védelem érdekében. 

Ha az adatvédelmi szabályok megkövetelik, hogy a biztonsági másolatok hosszabb ideig is elérhetők legyenek (akár 10 évig), konfigurálhatja a [hosszú távú adatmegőrzést](long-term-retention-overview.md) mind az önálló, mind a készletezett adatbázisokhoz.

A biztonsági másolatokat a következő célokra használhatja:

- Azure Portal, Azure PowerShell, Azure CLI vagy REST API használatával [visszaállíthat egy meglévő adatbázist egy korábbi időpontra](recovery-using-backups.md#point-in-time-restore) a megőrzési időszakon belül. Az önálló és a készletezett adatbázisok esetében a művelet egy új adatbázist hoz létre az eredeti adatbázissal megegyező kiszolgálón, de egy másik név alatt az eredeti adatbázis felülírásának elkerüléséhez. A visszaállítás befejeződése után törölheti vagy [átnevezheti](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database) az eredeti adatbázist, és átnevezheti a visszaállított adatbázist, hogy az eredeti adatbázis neve legyen. Felügyelt példány esetén a művelet hasonlóképpen létrehozhatja az adatbázis egy példányát ugyanazon az előfizetésben és ugyanabban a régióban, ugyanazon a másik felügyelt példányon is.
- A [törölt adatbázis visszaállítása a törlés](recovery-using-backups.md#deleted-database-restore) időpontjára vagy a megőrzési időtartamon belül bármely időpontra. A törölt adatbázist csak ugyanazon a kiszolgálón vagy felügyelt példányon lehet visszaállítani, ahol az eredeti adatbázis létrejött. Adatbázis törlésekor a szolgáltatás a törlés előtt elvégzi a tranzakciós napló utolsó biztonsági mentését, hogy megakadályozza az adatvesztést.
- [Adatbázis visszaállítása egy másik földrajzi régióba](recovery-using-backups.md#geo-restore). A Geo-visszaállítás lehetővé teszi, hogy egy földrajzi katasztrófa után helyreállítsa az adatbázist vagy a biztonsági mentéseket az elsődleges régióban. Egy új adatbázist hoz létre bármely meglévő kiszolgálón vagy felügyelt példányon bármely Azure-régióban.
- Egy adatbázis visszaállítása egy önálló adatbázis vagy készletezett adatbázis [adott hosszú távú biztonsági másolatából](long-term-retention-overview.md) , ha az adatbázis hosszú távú adatmegőrzési HÁZIRENDDEL (ltr) van konfigurálva. A LTR lehetővé teszi az adatbázis egy korábbi verziójának visszaállítását [a Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) vagy [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) segítségével, hogy megfeleljen a megfelelőségi kérésnek, vagy az alkalmazás egy régebbi verzióját futtassa. További információkért lásd: [Hosszú távú megőrzés](long-term-retention-overview.md).

A visszaállítás végrehajtásához tekintse meg [az adatbázis biztonsági másolatokból való visszaállítását](recovery-using-backups.md)ismertető témakört.

> [!NOTE]
> Az Azure Storage-ban a *replikálás* kifejezés a Blobok egyik helyről egy másikra történő másolását jelenti. Az SQL-ben az *adatbázis-replikáció* olyan különböző technológiákra utal, amelyek több másodlagos adatbázis szinkronizálására szolgálnak egy elsődleges adatbázissal.

Az alábbi példákkal kipróbálhatja a biztonsági mentési konfigurációt és a visszaállítási műveleteket:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| **Biztonsági másolatok megőrzésének módosítása** | [Önálló adatbázis](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Felügyelt példány](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Önálló adatbázis](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **A biztonsági másolatok hosszú távú megőrzésének módosítása** | [Önálló adatbázis](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Felügyelt példány – N/A  | [Önálló adatbázis](long-term-backup-retention-configure.md)<br/>Felügyelt példány – N/A  |
| **Adatbázis visszaállítása egy adott időpontban** | [Önálló adatbázis](recovery-using-backups.md#point-in-time-restore) | [Önálló adatbázis](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Törölt adatbázis visszaállítása** | [Önálló adatbázis](recovery-using-backups.md) | [Önálló adatbázis](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Felügyelt példány](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Adatbázis visszaállítása az Azure Blob Storage-ból** | Önálló adatbázis – N/A <br/>Felügyelt példány – N/A  | Önálló adatbázis – N/A <br/>[Felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>Biztonsági mentés ütemezése

Az első teljes biztonsági mentést az új adatbázis létrehozása vagy visszaállítása után azonnal ütemezi a rendszer. Ez a biztonsági mentés általában 30 percen belül elvégezhető, de hosszabb időt is igénybe vehet, ha az adatbázis nagy méretű. A kezdeti biztonsági mentés például hosszabb időt vehet igénybe egy visszaállított adatbázison vagy egy adatbázis-másolaton, ami általában nagyobb, mint egy új adatbázis. Az első teljes biztonsági mentés után a rendszer az összes további biztonsági mentést automatikusan ütemezi és kezeli. Az adatbázis biztonsági másolatának pontos időzítését az SQL Database vagy az SQL felügyelt példány-szolgáltatás határozza meg, mivel a rendszer a teljes rendszerterhelést kiegyensúlyozza. Nem módosíthatja a biztonsági mentési feladatok ütemtervét, vagy letilthatja azokat.

> [!IMPORTANT]
> Új, visszaállított vagy másolt adatbázis esetén az időponthoz tartozó visszaállítási funkció elérhetővé válik a kezdeti teljes biztonsági mentést követő kezdeti tranzakciós napló biztonsági mentésének idejétől kezdve.

## <a name="backup-storage-consumption"></a>Biztonsági másolatok tárolásának felhasználása

A SQL Server biztonsági mentési és visszaállítási technológiával az adatbázisok egy adott időpontra való visszaállításához egy teljes biztonsági mentésből, opcionálisan egy különbözeti biztonsági másolatból és egy vagy több tranzakciónapló biztonsági másolatából álló, megszakítás nélküli biztonsági mentési láncra van szükség. A SQL Database és az SQL felügyelt példányának biztonsági mentési ütemterve minden héten egy teljes biztonsági mentést tartalmaz. Ezért ahhoz, hogy a PITR a teljes megőrzési időtartamon belül engedélyezzék, a rendszernek további teljes, különbözeti és tranzakciónapló-biztonsági másolatokat kell tárolnia a beállított megőrzési időtartamnál hosszabb ideig. 

Más szóval, a megőrzési időszak során bármely időpontra vonatkozóan olyan teljes biztonsági mentésre van szükség, amely régebbi a megőrzési időszak legrégebbi időpontjánál, valamint a különbözeti és tranzakciós naplók teljes biztonsági mentésének megszakítás nélküli lánca egészen a következő teljes biztonsági mentésig.

> [!NOTE]
> A PITR engedélyezéséhez a további biztonsági mentéseket a beállított megőrzési időtartamnál hosszabb ideig tárolja a rendszer. A biztonsági mentési tár díját az összes biztonsági mentés esetében azonos díjszabással számoljuk el. 

A PITR funkcióinak megadásához már nem szükséges biztonsági másolatok automatikusan törlődnek. Mivel a differenciált biztonsági másolatok és a naplók biztonsági mentései egy korábbi teljes biztonsági mentést igényelnek, a rendszer mindhárom biztonsági mentési típust hetente kiüríti.

Az összes adatbázishoz, beleértve a [TDE titkosított](transparent-data-encryption-tde-overview.md) adatbázisokat is, a biztonsági másolatok tömörítve vannak, és csökkentik a biztonsági mentési tárolók tömörítését Az átlagos biztonsági mentési tömörítési arány 3-4 alkalommal, azonban az adattermészettől függően jelentősen alacsonyabb vagy magasabb lehet, valamint az, hogy a rendszer az adatbázisban használja-e az adattömörítést.

A SQL Database és az SQL felügyelt példánya összesített értékként számítja ki az összes felhasznált biztonsági mentési tárterületet. Ezt az értéket óránként jelenteni kell az Azure számlázási folyamatnak, amelynek feladata az óránkénti használat összesítése az egyes hónapok végén kiszámított felhasználás kiszámításához. Az adatbázis törlése után a felhasználás a biztonsági mentések életkora szerint csökken, és törlődik. Ha minden biztonsági mentés törölve lett, és a PITR már nem lehetséges, a számlázás leáll.
   
> [!IMPORTANT]
> Az adatbázisok biztonsági másolatait a rendszer a PITR engedélyezéséhez is megőrzi, még akkor is, ha az adatbázist törölték. Az adatbázisok törlése és újbóli létrehozása közben a tárolási és a számítási költségek is megadhatók, így a biztonsági másolatok tárolási költségei megnövelhető, mivel a szolgáltatás minden törléskor megőrzi a törölt adatbázisok biztonsági másolatait. 

### <a name="monitor-consumption"></a>Használat figyelése

A virtuális mag-adatbázisok esetében a biztonsági mentési típusok (teljes, különbözeti és napló) által felhasznált tárterületet külön metrikaként kell jelenteni az adatbázis figyelési paneljén. Az alábbi ábra bemutatja, hogyan figyelheti meg a biztonsági másolatok tárolási felhasználását egy adott adatbázisra vonatkozóan. Ez a funkció jelenleg nem érhető el a felügyelt példányok számára.

![Az adatbázis biztonsági mentési felhasználásának figyelése a Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>A biztonsági másolatok tárolásának finomhangolása

A biztonsági mentési tárterület-felhasználás legfeljebb az adatbázis maximális adatméretére vonatkozik. A biztonsági másolatok tárolásának túlzott mérete az egyes adatbázisok munkaterhelésével és maximális méretétől függ. A biztonsági mentési tár használatának csökkentése érdekében vegye figyelembe a következő hangolási technikák némelyikét:

- Csökkentse a [biztonsági másolatok megőrzési időtartamát](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) az igényeinek megfelelő minimálisra.
- Kerülje a nagyméretű írási műveleteket, például az index újraépítését, gyakrabban, mint amennyire szüksége van.
- Nagyméretű adatterhelési műveletek esetén érdemes lehet [fürtözött oszlopcentrikus indexeket](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes) használni, és a kapcsolódó [ajánlott eljárásokat](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)követni, és/vagy csökkenteni a nem fürtözött indexek számát.
- A általános célú szolgáltatási szinten a kiépített adattárolás olcsóbb, mint a biztonsági mentési tár ára. Ha folyamatosan nagy mennyiségű biztonsági mentési tárolási költséggel rendelkezik, érdemes lehet növelni az adattárolást a biztonsági mentési tárolón való mentéshez.
- Az ideiglenes eredmények és/vagy átmeneti adatmennyiségek tárolásához az alkalmazás logikájában állandó táblák helyett használjon TempDB.

## <a name="backup-retention"></a>Biztonsági mentés megőrzése

Az új, visszaállított és másolt adatbázisok esetében a Azure SQL Database és az Azure SQL felügyelt példánya megfelelő biztonsági másolatokat tart fenn, hogy alapértelmezés szerint az elmúlt 7 napban engedélyezze a PITR. A nagy kapacitású-adatbázisok kivételével a 1-35-es napon belül [megváltoztathatja az adatbázis biztonsági másolatának megőrzési időszakát](#change-the-pitr-backup-retention-period) . A [biztonsági másolatok tárolásának felhasználásáról](#backup-storage-consumption)a PITR engedélyezéséhez tárolt biztonsági másolatok a megőrzési időtartamnál régebbiek lehetnek.

Ha töröl egy adatbázist, a rendszer ugyanúgy tartja a biztonsági mentéseket, mint egy online adatbázis számára a megadott megőrzési időtartammal. Törölt adatbázis biztonsági másolatának megőrzési időszaka nem módosítható.

> [!IMPORTANT]
> Ha töröl egy kiszolgálót vagy felügyelt példányt, akkor a kiszolgálón vagy a felügyelt példányon lévő összes adatbázis is törlődik, és nem állítható helyre. A törölt kiszolgálók vagy a felügyelt példányok nem állíthatók vissza. Ha azonban beállította a hosszú távú adatmegőrzést (LTR) egy adatbázishoz vagy egy felügyelt példányhoz, a hosszú távú adatmegőrzési biztonsági másolatok nem törlődnek, és az adatbázisokat egy másik kiszolgálón vagy felügyelt példányon, ugyanabban az előfizetésben, a hosszú távú adatmegőrzési biztonsági mentés időpontjában lehet visszaállítani.

Az elmúlt 1-35 napon belüli PITR biztonsági mentését időnként rövid távú biztonsági másolatok megőrzésének is nevezik. Ha a biztonsági mentéseket továbbra is meg kell őriznie, mint a 35 napos maximális rövid távú megőrzési időszak, akkor a [hosszú távú adatmegőrzést](long-term-retention-overview.md)is engedélyezheti.

### <a name="long-term-retention"></a>Hosszú távú megőrzés

Az önálló és a készletezett adatbázisok és a felügyelt példányok esetében az Azure Blob Storage-ban akár 10 évig is beállíthatja a teljes biztonsági másolatok hosszú távú megőrzését (LTR). Ha engedélyezi a LTR szabályzatot, a hetente teljes biztonsági mentést a rendszer automatikusan átmásolja egy másik RA-GRS tárolóba. A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy évenkénti teljes biztonsági mentésekhez eltérő megőrzési időt választhat. A tárterület-felhasználás a LTR biztonsági mentések kiválasztott gyakoriságával, valamint a megőrzési időtartammal vagy időszakokkal függ. A [ltr díjszabási számológép](https://azure.microsoft.com/pricing/calculator/?service=sql-database) használatával megbecsülheti a ltr-tároló költségeit.

A PITR biztonsági mentésekhez hasonlóan a LTR biztonsági mentések a földrajzilag redundáns tárolással védettek. További információ: [Azure Storage-redundancia](../../storage/common/storage-redundancy.md).

További információ a LTR: a [biztonsági másolatok hosszú távú megőrzése](long-term-retention-overview.md).

## <a name="storage-costs"></a>Tárolási költségek

A Storage díjszabása attól függően változik, hogy a DTU modellt vagy a virtuális mag modellt használja-e.

### <a name="dtu-model"></a>DTU-modell

A DTU-modellben az adatbázisok és a rugalmas készletek biztonsági mentési tárterületére nem számítunk fel további díjat. A biztonsági mentési tár ára az adatbázis vagy a készlet árának részét képezi.

### <a name="vcore-model"></a>Virtuálismag-alapú modell

SQL Database önálló adatbázisai esetén a biztonsági mentési tár összege az adatbázis maximális adattárolási méretének 100%-a, külön díj nélkül biztosítjuk. Rugalmas készletek és felügyelt példányok esetén a biztonsági mentési tár összege a készlet maximális adattárolásának 100 százalékával vagy a példányok maximális tárolási méretével egyenlő, külön díj nélkül biztosítva. 

Az önálló adatbázisok esetében ez az egyenlet a teljes számlázható biztonsági mentési tár használatának kiszámítására szolgál:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

A készletezett adatbázisok esetében a teljes számlázható biztonsági mentési tárterület összesített mérete a készlet szintjén történik, és a következőképpen számítjuk ki:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Felügyelt példányok esetén a teljes számlázható biztonsági mentési tár mérete a példány szintjén összesítve történik, és a következőképpen számítható ki:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

A teljes számlázható biztonsági mentési tár (ha van ilyen) GB/hó-ban lesz felszámítva. Ez a biztonsági mentési tárterület az egyes adatbázisok, rugalmas készletek és felügyelt példányok munkaterhelésével és méretétől függ. A nagy mértékben módosított adatbázisok nagyobb különbözeti és naplózott biztonsági mentéssel rendelkeznek, mivel a biztonsági másolatok mérete az adatváltozások mennyiségével arányos. Ezért az ilyen adatbázisok esetében a biztonsági mentési költségek is magasabbak lesznek.

A SQL Database és az SQL felügyelt példánya a teljes számlázható biztonsági mentési tárterületet összesített értékként számítja ki az összes biztonsági mentési fájlban. Az Azure számlázási folyamat minden órában ezt az értéket jelenti, amely összesíti ezt az óránkénti használatot, hogy minden hónap végén lekérje a biztonsági mentési tárterület felhasználását. Ha töröl egy adatbázist, a biztonsági mentési tár fogyasztása fokozatosan csökken, mert a régebbi biztonsági mentések le lesznek törölve. Mivel a differenciált biztonsági másolatok és a naplók biztonsági mentései egy korábbi teljes biztonsági mentést igényelnek, a rendszer mindhárom biztonsági mentési típust hetente kiüríti. Az összes biztonsági másolat törlése után a számlázás leáll. 

Egyszerűsített Példaként tegyük fel, hogy egy adatbázis 744 GB-nyi biztonsági mentési tárterülettel rendelkezik, és hogy ez az összeg állandó marad az egész hónapban, mert az adatbázis teljesen inaktív. Ha át szeretné alakítani ezt az összesített tárterület-használatot óránkénti használatra, ossza fel 744,0-ra (havonta 31 nap * 24 óra/nap). SQL Database jelentést küld az Azure számlázási folyamatnak, amelyet az adatbázis 1 GB PITR biztonsági mentést használt óránként, állandó sebességgel. Az Azure-számlázás összesíti ezt a felhasználást, és az egész hónapra vonatkozóan 744 GB-ot fog megjeleníteni. A költségeket a régiójában lévő összeg/GB/hó arány alapján számítjuk fel.

Most egy összetettebb példa. Tegyük fel, hogy ugyanazt az üresjárati adatbázist a hónap közepétől számított 7 nap és 14 nap között is megnöveli. Ez a növekmény a teljes biztonsági mentési tárterület 1 488 GB-ra való megkettőzését eredményezi. SQL Database 1 GB-nyi használatot jelent az 1 – 372 (a hónap első felében). A használatot 2 GB-ra, 373 – 744 (a hónap második felében) fogja jelenteni. Ez a használat a 1 116 GB/hó utolsó számlára lesz összesítve.

A tényleges biztonsági mentési számlázási forgatókönyvek összetettebbek. Mivel az adatbázis változásainak aránya függ a munkaterheléstől, és az idő múlásával változik, az egyes különbözeti és naplózott biztonsági másolatok mérete is változhat, ami miatt az óránkénti biztonsági mentés a tárterület-felhasználásra megfelelően ingadozik. Továbbá minden különbözeti biztonsági mentés az adatbázisban az utolsó teljes biztonsági mentés óta végrehajtott összes változást tartalmazza, így az összes különbözeti biztonsági mentés teljes mérete fokozatosan nő a hét folyamán, majd a teljes, differenciált és naplózott biztonsági másolatok kiesése után meredeken csökken. Ha például egy nagy mennyiségű írási tevékenység, például az index újraépítése a teljes biztonsági mentés befejezése után futott le, akkor az index újraépítésének módosításait a rendszer az Újraépítés ideje alatt, a következő különbözeti biztonsági másolatban, valamint minden, a következő teljes biztonsági mentésig vett különbözeti biztonsági másolatban fogja tartalmazni. Az utóbbi forgatókönyvben a nagyobb adatbázisok esetében a szolgáltatás optimalizálása egy teljes biztonsági mentést készít, nem pedig különbözeti biztonsági mentést, ha a különbözeti biztonsági másolat túlzottan nagyméretű lenne. Ez csökkenti az összes különbözeti biztonsági mentés méretét a következő teljes biztonsági mentésig.

Az egyes biztonsági mentési típusok (teljes, különbözeti, tranzakciós napló) teljes biztonsági mentését a használat [monitorozása](#monitor-consumption)című témakörben leírtak szerint figyelheti.

### <a name="monitor-costs"></a>Költségek figyelése

A biztonsági másolatok tárolási költségeinek megismeréséhez lépjen a **Cost Management + számlázás** elemre a Azure Portalban, válassza a **Cost Management**lehetőséget, majd válassza a **Cost Analysis**elemet. Válassza ki a kívánt előfizetést **hatókörként**, majd szűrje azt az időszakot és szolgáltatást, amelyre kíváncsi.

Adjon hozzá egy szűrőt a **szolgáltatás neveként**, majd válassza az **SQL Database** elemet a legördülő listából. A **mérési alkategória** szűrővel válassza ki a szolgáltatás számlázási számlálóját. Önálló adatbázis vagy rugalmas adatbázis-készlet esetén válassza a **pitr egy-egy vagy rugalmas készletet**. Felügyelt példány esetén válassza a **mi pitr biztonsági mentési tár**lehetőséget. A **tárolási** és a **számítási** alkategóriák is hasznosak lehetnek, de nem a biztonsági másolatok tárolási költségeihez vannak társítva.

![A biztonsági mentési tár költséghatékonyságának elemzése](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="encrypted-backups"></a>Titkosított biztonsági másolatok

Ha az adatbázis TDE van titkosítva, a biztonsági másolatok automatikusan titkosítva maradnak, beleértve a LTR biztonsági mentéseket is. Az Azure SQL összes új adatbázisa alapértelmezés szerint engedélyezve van a TDE. További információ a TDE-ről: [transzparens adattitkosítás SQL Database & SQL felügyelt példányával](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Biztonsági mentés integritása

Az Azure SQL Engineering csapata folyamatosan teszteli az automatikus adatbázis-biztonsági mentések visszaállítását. (Ez a teszt jelenleg nem érhető el az SQL felügyelt példányain.) Az időponthoz tartozó visszaállításkor az adatbázisok a DBCC CHECKDB UTASÍTÁST is megkapják.

A sértetlenség-ellenőrzés során észlelt problémák miatt a mérnöki csapat riasztást küld. További információ: az [adatintegritás SQL Databaseban](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

A biztonsági másolatok integritásának biztosításához az összes adatbázis-biztonsági mentést az ELLENŐRZŐÖSSZEG beállítással végezheti el.

## <a name="compliance"></a>Megfelelőség

Ha az adatbázist egy DTU-alapú szolgáltatási rétegből virtuális mag-alapú szolgáltatási rétegre telepíti át, a rendszer megőrzi a PITR megőrzését annak érdekében, hogy az alkalmazás adathelyreállítási házirendje ne legyen veszélyeztetve. Ha az alapértelmezett megőrzés nem felel meg a megfelelőségi követelményeknek, módosíthatja a PITR megőrzési időtartamát. További információ: [a PITR biztonsági mentés megőrzési időtartamának módosítása](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>A PITR biztonsági mentés megőrzési idejének módosítása

Az alapértelmezett PITR biztonsági mentési megőrzési időszakot a Azure Portal, a PowerShell vagy a REST API használatával módosíthatja. Az alábbi példák bemutatják, hogyan módosíthatja a PITR megőrzését 28 napra.

> [!WARNING]
> Ha csökkenti az aktuális megőrzési időtartamot, akkor elveszíti az új megőrzési időtartamnál régebbi időpontokra való visszaállítás lehetőségét. Azok a biztonsági másolatok, amelyekre már nincs szükség az új megőrzési időtartamon belüli PITR biztosításához, törlődnek. Ha az aktuális megőrzési időtartamot emeli, akkor az új megőrzési időtartamon belül nem lehet azonnal visszaállítani a régebbi időpontokra. Az idő múlásával a rendszer megkezdi a biztonsági mentések megőrzését.

> [!NOTE]
> Ezek az API-k csak a PITR megőrzési időszakot érintik. Ha a LTR-t konfigurálta az adatbázishoz, az nem lesz hatással. További információ a LTR megőrzési időtartamának módosításáról: [hosszú távú adatmegőrzés](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a Azure Portal használatával

Ha módosítani szeretné a PITR biztonsági mentési megőrzési időtartamát a Azure Portal használatával, lépjen a kiszolgáló vagy a felügyelt példányra azokkal az adatbázisokkal, amelyek megőrzési időtartamát módosítani kívánja. 

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

A SQL Database PITR biztonsági mentési megőrzésének változásai a portál kiszolgáló lapján hajthatók végre. A kiszolgálón található adatbázisok PITR megtartásának módosításához lépjen a kiszolgáló áttekintés paneljére. Válassza a **biztonsági másolatok kezelése** lehetőséget a bal oldali ablaktáblán, válassza ki az adatbázisokat a módosítás hatókörében, majd válassza a **megőrzés konfigurálása** a képernyő tetején:

![PITR-megőrzés, kiszolgáló szintjének módosítása](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[Felügyelt SQL-példány](#tab/managed-instance)

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

## <a name="next-steps"></a>További lépések

- Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel az adatok véletlen sérüléstől vagy törléstől való védelme érdekében szükségesek. SQL Database az üzletmenet-folytonossági megoldásokkal kapcsolatos további információkért lásd: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontra a Azure Portal használatával](recovery-using-backups.md).
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontra a PowerShell használatával](scripts/restore-database-powershell.md).
- Az Azure Blob Storage-ban az Azure Portal használatával történő automatikus biztonsági mentések konfigurálásával, kezelésével és visszaállításával kapcsolatos további információkért lásd: [a biztonsági másolatok hosszú távú megőrzésének kezelése a Azure Portal használatával](long-term-backup-retention-configure.md).
- További információ arról, hogyan konfigurálható, kezelhető és állítható vissza az automatikus biztonsági mentések az Azure Blob Storage-ban a PowerShell használatával: a [biztonsági másolatok hosszú távú megőrzésének kezelése a PowerShell használatával](long-term-backup-retention-configure.md).
