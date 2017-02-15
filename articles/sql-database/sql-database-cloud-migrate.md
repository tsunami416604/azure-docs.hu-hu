---
title: "SQL Server-adatbázis áttelepítése az Azure SQL Database-be | Microsoft Docs"
description: "Útmutató a helyszíni SQL Server-adatbázisok a felhőbeli Azure SQL Database-be történő áttelepítéséhez. Az adatbázis-áttelepítési eszközökkel tesztelheti a kompatibilitást az adatbázis áttelepítése előtt."
keywords: "adatbázis-áttelepítés,sql server-adatbázis áttelepítése,adatbázis-áttelepítési eszközök,adatbázis áttelepítése,sql database áttelepítése"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 61fb027dfdd5830d87fe4fcfff57f685db71475e


---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>SQL Server-adatbázis áttelepítése a felhőbeli SQL Database-be
Ebben a cikkben a helyszíni SQL Server 2005 vagy újabb adatbázisok Azure SQL Database-be való áttelepítésének módját ismerheti meg. Ebben az adatbázis-áttelepítési folyamatban az aktuális környezetében megtalálható SQL Server-adatbázisból az SQL Database-be telepíti át a sémáját és az adatait. A művelet sikeres elvégzéséhez a meglévő adatbázisnak először meg kell felelnie egy kompatibilitási teszten. Az SQL Database 12-es verzióval a kiszolgálószintű és adatbázisok közötti műveletekkel kapcsolatos probléma helyett [szolgáltatásparitást](sql-database-features.md) próbálunk elérni. A [részben támogatott vagy nem támogatott funkciókra](sql-database-transact-sql-information.md) támaszkodó adatbázisokat és alkalmazásokat némileg át kell alakítani ezen inkompatibilitások kijavítása érdekében, még mielőtt áttelepíthetné az SQL Server-adatbázist.

Az áttelepítéshez a következő lépéseket kell elvégeznie:

* **Kompatibilitási teszt:** Az adatbázis és az SQL Database kompatibilitásának ellenőrzése. 
* **A kompatibilitási problémák megoldása, ha vannak ilyenek:** Ha az ellenőrzés meghiúsul, ki kell javítania a kompatibilitási hibákat.  
* **Az áttelepítés elvégzése:** Ha az adatbázis kompatibilis, egy vagy több módszer is a rendelkezésére áll az áttelepítéshez. 

Az SQL Server több módszert nyújt ezen feladatok elvégzéséhez. Ez a cikk az egyes feladatokhoz elérhető módszerek áttekintését tartalmazza. A következő ábra a lépéseket és a módszereket mutatja be.

  ![VSSSDT áttelepítési ábra](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> Nem SQL Server-adatbázis (beleértve a Microsoft Access-, Sybase-, MySQL Oracle- és DB2-adatbázisokat) az Azure SQL Database-be történő áttelepítéséhez lásd [az SQL Server áttelepítési segédet](http://blogs.msdn.com/b/ssma/) ismertető cikket.
> 
> 

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Az adatbázis-áttelepítési eszközök tesztelik az SQL Server-adatbázis és az SQL Database kompatibilitását
Az adatbázis-áttelepítési folyamat elindítása előtt az SQL Database kompatibilitási problémáinak teszteléséhez használja a következő módszerek egyikét:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* [SQL Server Data Tools for Visual Studio („SSDT”):](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) Az SSDT a legújabb kompatibilitási szabályokkal észleli az SQL Database V12 inkompatibilitásait. Ha inkompatibilitást észlel, ebben az eszközben közvetlenül kijavíthatja az észlelt problémákat. Ez a módszer az SQL Database V12 kompatibilitási problémáinak teszteléséhez és javításához javasolt módszer. 
* [SqlPackage:](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) Az SqlPackage egy parancssori segédprogram, amely teszteli a kompatibilitási hibákat, és létrehozza az észlelt kompatibilitási hibákat tartalmazó jelentést. Ha ezt az eszközt használja, győződjön meg arról, hogy a legújabb verziót használja, amely tartalmazza a legújabb kompatibilitási szabályokat. Ha hibák észlelhetők, egy másik eszközzel kell kijavítania az észlelt kompatibilitási hibákat – javasolt az SSDT használata.  
* [Az SQL Server Management Studio Adatrétegbeli alkalmazás exportálása varázslója:](sql-database-cloud-migrate-determine-compatibility-ssms.md) Ez a varázsló észleli és a képernyőn jelenti a hibákat. Ha nem észlel hibákat, folytathatja az eljárást és elvégezheti az SQL Database-be való áttelepítést. Ha hibák észlelhetők, egy másik eszközzel kell kijavítania az észlelt kompatibilitási hibákat – javasolt az SSDT használata.
* [SQL Azure Migration Wizard („SAMW”):](sql-database-cloud-migrate-fix-compatibility-issues.md) A SAMW egy CodePlex-eszköz, amely az Azure SQL Database V11 kompatibilitási szabályaival észleli az Azure SQL Database V12-vel kapcsolatos inkompatibilitásokat. Ha inkompatibilitást észlel, ebben az eszközben közvetlenül kijavíthatja a problémák egy részét. Ez az eszköz olyan inkompatibilitásokat is észlelhet, amelyeket nem szükséges kijavítani. Ez volt az első elérhető Azure SQL Database-áttelepítési segédeszköz, és a fejlesztését az SQL Server-közösség aktívan támogatja. Ezenkívül az eszköz használatával magából az eszközből elvégezheti az áttelepítést. 

## <a name="fix-database-migration-compatibility-issues"></a>Adatbázis-áttelepítés kompatibilitási problémáinak megoldása
Ha kompatibilitási problémák észlelhetők, ki kell javítania azokat az SQL Server-adatbázis áttelepítésének folytatása előtt. Sokféle kompatibilitási problémával találkozhat a forrásadatbázis SQL Server-verziójától és az áttelepített adatbázis összetettségétől függően. Az SQL Server korábbi verziói több kompatibilitási problémával rendelkeznek. A következő forrásanyagokat érdemes használnia a keresőmotorokban végzett internetes keresés mellett:

* [Az Azure SQL Database-ben nem támogatott SQL Server-adatbázisfunkciók](sql-database-transact-sql-information.md)
* [Megszűnt adatbázismotor-funkció az SQL Server 2016-ban](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Megszűnt adatbázismotor-funkció az SQL Server 2014-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Megszűnt adatbázismotor-funkció az SQL Server 2012-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Megszűnt adatbázismotor-funkció az SQL Server 2008 R2-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Megszűnt adatbázismotor-funkció az SQL Server 2005-ben](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Az internetes keresés és a forrásanyagok használata mellett érdemes felkeresnie az [MSDN SQL Server közösségi fórumait](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) vagy a [StackOverflow](http://stackoverflow.com/) oldalt.

Használja a következő adatbázis-áttelepítési eszközök egyikét az észlelt problémák megoldásához:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* Az [SQL Server Data Tools for Visual Studio („SSDT”)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) használata: Az SSDT használatához beimportálja az adatbázissémát az SQL Server Data Tools for Visual Studióba („SSDT”), majd összeállít egy projektet egy SQL Database V12-környezethez. Ezután kijavíthatja az összes észlelt kompatibilitási problémát az SSDT-ben. Amikor elkészült, visszaszinkronizálja a módosításokat a forrásadatbázisba (vagy a forrásadatbázis egy másolatába). Jelenleg az SSDT az SQL Database V12-vel kapcsolatos kompatibilitási problémák tesztelésének és javításának javasolt módszere. Lásd az [SSDT használatának útmutatóját](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
* Az [SQL Server Management Studio („SSMS”)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) használata: Az SSMS használatához Transact-SQL-parancsokat futtat egy másik eszközzel észlelt hibák kijavítása érdekében. Ez a módszer elsődlegesen a tapasztalt felhasználóknak lehet hasznos, akik közvetlenül a forrásadatbázisban módosítják az adatbázissémát. 
* Az [SQL Azure Migration Wizard („SAMW”)](sql-database-cloud-migrate-fix-compatibility-issues.md) használata: A SAMW használatához létrehoz egy Transact-SQL-szkriptet a forrásadatbázisból. Amikor lehetséges, a varázsló átalakítja a szkriptet, hogy a séma kompatibilis legyen az SQL Database V12-vel. Amikor elkészült, a SAMW az SQL Database V12-höz csatlakozhat a szkript futtatásához. Ez az eszköz elemzi a profilelemzési fájlokat a kompatibilitási problémák meghatározása érdekében. A szkript létrehozható csak sémával, vagy BCP formátumú adatokat is tartalmazhat.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Kompatibilis SQL Server-adatbázis áttelepítése SQL Database-be
Kompatibilis SQL Server-adatbázis áttelepítéséhez a Microsoft több áttelepítési módszert biztosít a különböző alkalmazási helyzetekre. A választott módszer az állásidővel kapcsolatos elvárásoktól, az SQL Server-adatbázis méretétől és összetettségétől, valamint a Microsoft Azure-felhőhöz rendelkezésre álló csatlakozási lehetőségektől függ.  

> [!div class="op_single_selector"]
> * [SSMS áttelepítési varázsló](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exportálás BACPAC-fájlba](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importálás BACPAC-fájlból](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Tranzakciós replikáció](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Az áttelepítési módszer kiválasztásakor az első kérdés, hogy megengedheti-e, hogy kivegye az adatbázist az éles környezetből az áttelepítés során. Ha az adatbázisokat tranzakciók végrehajtása közben telepíti át, az az adatbázis inkonzisztens állapotát és esetleges sérülését okozhatja. Sokféleképpen elérheti az adatbázisok fokozatos leválasztását, az ügyfélkapcsolat letiltásától [adatbázis-pillanatfelvételének](https://msdn.microsoft.com/library/ms175876.aspx) létrehozásáig.

Ha minimális állásidővel szeretné elvégezni az áttelepítést, használja az [SQL Server tranzakciós replikációját](sql-database-cloud-migrate-compatible-using-transactional-replication.md), ha az adatbázis megfelel a tranzakciós replikáció követelményeinek. Ha megengedhet valamennyi állásidőt vagy ha egy éles adatbázis tesztáttelepítését végzi a későbbi áttelepítéshez, érdemes lehet a következő három módszert használni:

* [SSMS áttelepítési varázsló:](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) Kis és közepes méretű adatbázisok esetén a kompatibilis SQL Server 2005- vagy újabb adatbázisok áttelepítése egyszerűen elvégezhető az [Adatbázis telepítése Microsoft Azure Database-be varázsló](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) futtatásával az SQL Server Management Studióban.
* [Exportálás BACPAC-fájlba](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md), majd [Importálás BACPAC-fájlból:](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) Ha problémái vannak a kapcsolattal (nincs kapcsolat, alacsony sávszélesség, vagy problémák az időtúllépéssel), illetve közepes és nagy adatbázisok esetén használjon egy [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-fájlt. Ezzel a módszerrel az SQL Server-sémát és az adatokat egy BACPAC-fájlba exportálja. Ezután a BACPAC-fájlt az SQL Database-be importálja az SQL Server Management Studio Adatrétegbeli alkalmazás exportálása varázslója vagy az [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) parancssori segédprogram használatával.
* A BACPAC és a BCP együttes használata: Sokkal nagyobb adatbázisok esetén egy [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-fájlt és a [BCP](https://msdn.microsoft.com/library/ms162802.aspx)-t használhatja a nagyobb párhuzamosság elérése érdekében, ami növeli ugyan a teljesítményt, de nagyobb összetettséget jelent. Ezzel a módszerrel külön telepítheti át a sémát és az adatokat.
  
  * [Exportálja csak a sémát egy BACPAC-fájlba](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
  * [Importálja csak a sémát a BACPAC-fájlból](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) az SQL Database-be.
  * A [BCP](https://msdn.microsoft.com/library/ms162802.aspx)-vel másolja át az adatokat egybesimított fájlokba, majd [párhuzamosan töltse be](https://technet.microsoft.com/library/dd425070.aspx) ezeket a fájlokat az Azure SQL Database-be.
    
     ![SQL Server-adatbázis áttelepítése – az SQL Database áttelepítése a felhőbe.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Következő lépések
* [Az SSDT legújabb verziója](https://msdn.microsoft.com/library/mt204009.aspx)
* [Az SQL Server Management Studio legújabb verziója](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>További források
* [SQL Database-funkciók](sql-database-features.md)
  [A Transact-SQL részben támogatott vagy nem támogatott funkciói](sql-database-transact-sql-information.md)
* [Nem SQL Server-adatbázisok áttelepítése az SQL Server áttelepítési segéddel](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


