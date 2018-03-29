---
title: Azure SQL adatbázis felügyelt példány SQL Server-példány áttelepítése |} Microsoft Docs
description: Ismerje meg, hogyan telepítheti át az SQL Server-példányt Azure SQL adatbázis felügyelt példányra.
keywords: adatbázis-áttelepítés,sql server-adatbázis áttelepítése,adatbázis-áttelepítési eszközök,adatbázis áttelepítése,sql database áttelepítése
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: bonova
ms.openlocfilehash: 4546f03294ea8ab01ecb2b2777c5b92dbc5a7f4a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Azure SQL adatbázis felügyelt példány SQL Server példány áttelepítés

Ebből a cikkből megismerheti az Azure SQL Database felügyelt-példányt (előzetes verzió) történő áttelepítéséhez egy SQL Server 2005 vagy újabb verzió példány módszerekkel kapcsolatos. 

> [!NOTE]
> Egy önálló adatbázis vagy a rugalmas készlet egy önálló adatbázis áttelepítéséhez lásd: [egy SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-cloud-migrate.md).

Az SQL Database felügyelt példánya a meglévő SQL Database szolgáltatás kiegészítése, amely a különálló adatbázisok és a rugalmas adatbáziskészletek mellett egy harmadik üzembe helyezési lehetőséget is elérhetővé tesz.  Adatbázis növekedési-és-shift billentyű egy teljes körűen felügyelt PaaS engedélyezése nélkül az alkalmazás újratervezése tervezték. A felügyelt példány az SQL Server programozási modell funkcióival nagy mértékben kompatibilis, és emellett beépítetten támogatja az SQL Server legtöbb funkcióját is, valamint a hozzájuk kapcsolódó eszközöket és szolgáltatásokat.

A magas szintű áttelepítési folyamatának néz a következő diagramon:

![áttelepítési folyamat](./media/sql-database-managed-instance-migration/migration-process.png)

## <a name="assess-managed-instance-compatibility"></a>Felügyelt példány kompatibilitási felmérése

Először határozza meg, hogy-e az adatbázis-követelmények az alkalmazás kompatibilis felügyelt példány. Felügyelt példány egyszerű növekedési és shift áttelepítési, a legtöbb meglévő alkalmazások használják a helyszíni SQL Server vagy a virtuális gépek számára készült. Azonban néha szolgáltatásokat vagy funkciókat nyújtanak, amelyek még nem támogatott és a megoldás megvalósításának költségét túl magas. 

Használjon [adatok áttelepítési Segéd (DMA-)](https://docs.microsoft.com/sql/dma/dma-overview) észleli a potenciális kompatibilitási problémák kiható az Azure SQL Database adatbázisokkal kapcsolatos funkciók. DMA egyelőre nem támogatják a felügyelt példány áttelepítési céljaként, de javasoljuk, hogy assessment futtassa az Azure SQL Database, és gondosan tekintse át a listában, a paritásos jelentett funkciót és a kompatibilitási problémák elleni termék dokumentációját. A legtöbb a akadályozza meg, hogy az Azure SQL Database-áttelepítés problémák elhárítását, felügyelt példánya el lettek távolítva. Példány, szolgáltatások, mint az adatbázisok közötti lekérdezéseket, a példányt, a csatolt kiszolgáló más SQL-forrás CLR, globális ideiglenes táblák, adatbázisok közötti tranzakciójának példány szintű nézetek, a Service Broker és a hasonló találhatók felügyelt példányok. 

Van azonban néhány esetben figyelembe kell vennie, mint egy másik lehetőség, ha [SQL Server Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/sql-server/). Néhány példa:

- Ha az operációs rendszer vagy a fájlrendszerben, például a telepítés harmadik féltől származó és egyéni ügynökökkel azonos virtuális gépen futó SQL Server közvetlen hozzáférést igényel.
- Ha még nem támogatott funkciók, például a FileStream rendelkezik szigorú függőségi / filetable objektum, a PolyBase és a kereszt-példány tranzakciók.
- Ha feltétlenül szükség maradjon egy SQL Server adott verziójának (2012, példány).
- Ha a számítási követelményei sokkal alacsonyabb felügyelt példánynak kínál a nyilvános előzetes verziójában (egy vCore példányhoz) és az adatbázis összevonása nem elfogadható lehetőség.

## <a name="choose-app-connectivity-option"></a>Válassza ki az alkalmazás kapcsolódási beállítást választja

Felügyelt példány teljesen akkor szerepel a virtuális hálózat, így a végső szintű elkülönítési és biztonsági biztosít az adatok. Az alábbi ábrán látható a különböző alkalmazás-topológiák teljesen az Azure-ban vagy hibrid környezetben, függetlenül attól, hogy egy teljes körűen felügyelt szolgáltatás telepítése különböző lehetőséget is kínál, vagy a központi modell az előtér-alkalmazások.

![alkalmazás üzembe helyezési topológiák](./media/sql-database-managed-instance-migration/application-deployment-topologies.png)

A kiválasztott beállításokat engedélyezi a csatlakozást egy SQL végpont csak a magánhálózati IP-címét, amely biztosítja, hogy az adatok elkülönítését optimális szintjét. <!--- For more information, see How to connect your application to Managed Instance.--->

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Központi telepítése optimálisan méretezett felügyelt példányhoz

Felügyelt példány megfelelő helyszíni munkaterhelések esetén, amelyek tervezi, hogy helyezze át a felhőbe. Egy új alapjául szolgáló vásárlási modell nagyobb rugalmasságot biztosít a munkaterhelések erőforrások a megfelelő szintű kiválasztásával okozna. A helyszíni világ általában az ilyen terhelések méretezése fizikai mag használatával. Az új alapjául szolgáló vásárlási modell felügyelt példány alapul virtuális mag, vagy "vCores," a további tárhely és a rendelkezésre álló IO külön-külön. A vCore modell egy egyszerűbb megérthetik a valóban használt funkciókért és felhőbeli számítási igényeinek helyszíni ma. Az új modell lehetővé teszi a megfelelő méretének a cél környezetet a felhőben.

Kiválaszthatja a számítási és tárolási erőforrások telepítési idő, és módosítsa, ezt követően az alkalmazás leállása nélkül, anélkül.

![felügyelt példány méretezése](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Hozza létre a virtuális hálózat infrastruktúra és a felügyelt példány - és adatbázis visszaállítása egy biztonságimásolat-fájlból, lásd: [hozzon létre egy felügyelt példányt](sql-database-managed-instance-tutorial-portal.md).

> [!IMPORTANT]
> Fontos, hogy a cél VNet és alhálózat mindig összhangban tartsa [felügyelt példány VNET követelmények](sql-database-managed-instance-vnet-configuration.md#requirements). Bármely alkalmazással való inkompatibilitás előfordulhat, hogy új példányok létrehozása vagy a már létrehozott használatával.

## <a name="select-migration-method-and-migrate"></a>Áttelepítési módszer kiválasztása és áttelepítése

A felügyelt helyszíni vagy infrastruktúra-szolgáltatási adatbázis megvalósítások tömeges adatbázis áttelepítés igénylő példány célok felhasználói forgatókönyvek. Amikor a növekedési és az eltolás mértékét megadó rendszeresen példányszintű és / vagy funkciók kereszt-adatbázis alkalmazások háttér kell azok optimális választás. Ha ez a forgatókönyv, áthelyezheti teljes példánya nem rearchitecture kell az Azure-ban a megfelelő környezet az alkalmazások. 

Helyezze át az SQL Server-példányok, tervezze meg gondosan kell:

-   Az áttelepítés összes adatbázis elhelyezésű (ők egyazon példányán fut) van szüksége
-   Az alkalmazás függ, többek között a bejelentkezéseket, hitelesítő adatokat, az SQL Agent-feladatok és operátorok és kiszolgálói szintű eseményindítók példányszintű objektumok áttelepítése. 

Felügyelt példány egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, és delegálja a platform rendszeres DBA tevékenységekről, azok be vannak építve. Ezért néhány szintű példányadatokat nem kell áttelepíteni, például a karbantartási feladatok rendszeres biztonsági mentést vagy mindig a konfigurációhoz, mint [magas rendelkezésre állású](sql-database-high-availability.md) részét.

Felügyelt példányát támogatja a következő adatbázis áttelepítési lehetőségek (ezek jelenleg az egyetlen támogatott áttelepítési módszereinek):

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

A [Azure adatbázis áttelepítési szolgáltatás (DMS)](../dms/dms-overview.md) lehetővé minimális állásidővel adatok Azure platformon több adatbázis forrásból zökkenőmentes áttelepítés egy teljes körűen felügyelt szolgáltatás. Ez a szolgáltatás leegyszerűsíti a meglévő harmadik féltől származó és az SQL Server-adatbázisok áthelyezése az Azure-bA szükséges feladatok. A nyilvános előzetes verzió központi telepítési lehetőségek között az Azure SQL Database, a példány által felügyelt és az SQL Server egy Azure virtuális gépen. DMS az ajánlott módszer az áttelepítés a vállalati alkalmazások. 

![DMS](./media/sql-database-managed-instance-migration/dms.png)

További információt ebben a forgatókönyvben és konfigurációs lépéseket a DMS, lásd: [áttelepítése a helyi adatbázis felügyelt példányhoz DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>URL-címről natív VISSZAÁLLÍTÁSA

Natív biztonsági másolatokat (.bak fájlok) a helyszíni SQL Server helyreállítása vagy [SQL Server virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/), elérhető [Azure Storage](https://azure.microsoft.com/services/storage/), egy SQL-példányon DB felügyelt főbb képességek, amelyek lehetővé teszi, hogy gyors és egyszerű offline áttelepítés adatbázis. 

Az alábbi ábra a magas szintű folyamatát ismerteti:

![áttelepítési folyamat](./media/sql-database-managed-instance-migration/migration-flow.png)

A következő táblázat futtatja a forrás SQL Server verziójától függően módszerrel kapcsolatos további információk:

|Lépés|SQL-motor és verzió|Biztonsági mentése / visszaállítása módszer|
|---|---|---|
|Helyezze a biztonsági mentési Azure Storage|Előzetes SQL 2012 SP1 CU2|Közvetlenül az Azure storage a .bak-fájl feltöltése|
||2012 SP1 CU2 - 2016|Közvetlen biztonsági mentés használata elavult [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) szintaxis|
||2016 vagy újabb verzió|Biztonsági másolat használatával közvetlen [az SAS-hitelesítő adatok](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Állítsa vissza az Azure Storage felügyelt példányon|[Állítsa vissza az URL-cím elé SAS hitelesítő adatok](sql-database-managed-instance-tutorial-portal.md#restore-the-wide-world-importers-database-from-a-backup-file)|

> [!IMPORTANT]
> A rendszer-adatbázisokat pRestore nem támogatott. Példány szolgáltatásszint-objektumokhoz (master vagy msdb adatbázis tárolt) áttelepítéséhez ajánlott parancsfájl ki őket, és T-SQL-parancsfájlok a cél-példányon futnak.

A teljes oktatóanyag, amely tartalmazza az adatbázis biztonsági másolatának visszaállítása egy felügyelt példányhoz SAS hitelesítő adatok használatával, lásd: [hozzon létre egy felügyelt példányt](sql-database-managed-instance-tutorial-portal.md).

### <a name="migrate-using-bacpac-file"></a>Telepítse át a BACPAC fájl használata

Importálhatja az Azure SQL Database és a felügyelt példány egy hozza létre az eredeti adatbázist, az adatokat egy BACPAC fájl másolatát. Lásd: [egy új Azure SQL-adatbázis BACPAC fájl importálása](sql-database-import.md).

## <a name="monitor-applications"></a>Alkalmazások figyelése

Alkalmazás viselkedését és áttelepítés után a teljesítmény nyomon követése Felügyelt példányában módosításai csak akkor engedélyezett, egyszer a [adatbázis-kompatibilitási szint módosítása](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Az adatbázis áttelepítése az Azure SQL Database eredeti kompatibilitási szintje végleg a legtöbb esetben a. Ha egy felhasználói adatbázis kompatibilitási szintjének 100- nál nagyobb az áttelepítés előtt, akkor változatlan marad az áttelepítés után. Ha egy felhasználói adatbázis kompatibilitási szintje 90 áttelepítés előtt, a frissített adatbázis kompatibilitási szintjének értéke 100, amelyhez a legalacsonyabb támogatott kompatibilitási szintje felügyelt példányban. Rendszer-adatbázisokat kompatibilitási szintje nem 140.

Áttelepítési kockázatok csökkentése érdekében módosítsa az adatbázis kompatibilitási szintje csak a teljesítmény figyelése után. Optimális eszközként előtt és után az adatbázis kompatibilitási szint módosítása, alkalmazások és szolgáltatások teljesítményével kapcsolatos információk kapcsolódnak a leírtak szerint használja a Lekérdezéstár [teljesítmény stabilitását tartsa a frissítés során újabb SQL Server-verzióra](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Miután a teljes körűen felügyelt, tegye meg a előnyeit, az SQL Database szolgáltatás részeként automatikusan biztosított. Például nem kell létrehoznia a biztonsági mentések felügyelt példányon, mert a szolgáltatás végez biztonsági mentést meg automatikusan. Ön már nem kell foglalkoznia az ütemezés, véve, és a biztonsági mentések kezelése. Felügyelt példány biztosít a megőrzési időszak használatával belül bármely visszaállításának [idő-helyreállítás (PITR) pont](sql-database-recovery-using-backups.md#point-in-time-restore). Nyilvános előzetes a megőrzési időtartam rögzített hét nap.
Ezenkívül nem kell foglalkoznia beállítása a magas rendelkezésre állás mint [magas rendelkezésre állású](sql-database-high-availability.md) részét.

Biztonsági megerősítésére, fontolja meg a rendelkezésre álló funkciók némelyike:
- Az Azure Active Directory-hitelesítés az adatbázis szintjén
- Naplózás és Fenyegetésészlelés tevékenységek figyelése
- Adatok bizalmas és rendszerjogosultságú hozzáférés szabályozása ([sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) és [dinamikus Adatmaszkolási](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)).

## <a name="next-steps"></a>További lépések

- Felügyelt példány kapcsolatos információkért lásd: [Mi az, hogy a felügyelt példánya?](sql-database-managed-instance.md)
- Ez az oktatóanyag magában foglalja a biztonsági másolat visszaállítása, lásd: [hozzon létre egy felügyelt példányt](sql-database-managed-instance-tutorial-portal.md).
- Az oktatóanyag ábrázoló áttelepítéshez DMS használatával, lásd: [áttelepítése a helyi adatbázis felügyelt példányhoz DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).  
