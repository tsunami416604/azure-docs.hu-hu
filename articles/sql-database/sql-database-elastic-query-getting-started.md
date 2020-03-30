---
title: Jelentés a kibővített felhőalapú adatbázisok között
description: Adatbázisközi adatbázis-lekérdezések használatával több adatbázisban is jelentést készít.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: bad52b364dc83994e7985fc80b1b9f9e7f50481e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823773"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Jelentés a kibővített felhőalapú adatbázisok között (előzetes verzió)

Rugalmas [lekérdezéssel](sql-database-elastic-query-overview.md)egyetlen csatlakozási pontról több Azure SQL-adatbázisból is létrehozhat jelentéseket. Az adatbázisokat vízszintesen particionálni kell (más néven "szilánkos").

Ha már rendelkezik adatbázissal, olvassa el a [Meglévő adatbázisok áttelepítése kicsinyített adatbázisokba (Áttelepítése kicsinyített adatbázisokba) témakört.](sql-database-elastic-convert-to-use-elastic-tools.md)

A lekérdezéshez szükséges SQL-objektumok megértéséhez olvassa el a [Lekérdezés vízszintesen particionált adatbázisok között című témakört.](sql-database-elastic-query-horizontal-partitioning.md)

## <a name="prerequisites"></a>Előfeltételek

Töltse le és futtassa az [Első lépések a rugalmas adatbázis-eszközökkel című mintát.](sql-database-elastic-scale-get-started.md)

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Shard térképkezelő létrehozása a mintaalkalmazás használatával
Itt hozzon létre egy shard térképkezelőt több szegmenssel együtt, majd adatokat szúrjon be a szegmensekbe. Ha történetesen már rendelkezik szilánkok beállítása szilánkos adatokkal, kihagyhatja a következő lépéseket, és lépjen a következő szakaszba.

1. Az Első **lépések rugalmas adatbázis-eszközökkel** mintaalkalmazás a cikkben található [A mintaalkalmazás letöltése és futtatása](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app-1)című szakaszlépéseit követve hozhat létre és futtatható. Miután befejezte az összes lépést, a következő parancssor jelenik meg:

    ![Parancssorba][1]
2. A parancsablakba írja be az "1" értéket, és nyomja **le az Enter billentyűt.** Ez létrehozza a shard map manager, és két szilánkot ad hozzá a kiszolgálóhoz. Ezután írja be a "3" szót, és nyomja **le az Enter billentyűt.** ismételje meg a műveletet négyszer. Ez mintaadatsorokat szúr be a szegmensekbe.
3. Az [Azure Portalon](https://portal.azure.com) három új adatbázisnak kell megjelennie a kiszolgálón:

   ![Visual Studio megerősítése][2]

   Ezen a ponton az adatbázis közi lekérdezések a rugalmas adatbázis-ügyfélkódtárak on keresztül támogatott. Használja például a 4- es lehetőséget a parancsablakban. A többshard lekérdezés eredményei mindig az összes szegmensek összes eredménye **union.**

   A következő szakaszban létrehozunk egy minta-adatbázis végpontot, amely támogatja az adatok richerésa szegmensek közötti lekérdezését.

## <a name="create-an-elastic-query-database"></a>Rugalmas lekérdezési adatbázis létrehozása
1. Nyissa meg az [Azure Portalt,](https://portal.azure.com) és jelentkezzen be.
2. Hozzon létre egy új Azure SQL-adatbázist ugyanabban a kiszolgálón, mint a shard telepítő. Nevezze el az adatbázist "ElasticDBQuery" néven.

    ![Az Azure Portal és a tarifacsomag][3]

    > [!NOTE]
    > meglévő adatbázist is használhatunk. Ha ezt megteheti, nem lehet az egyik szilánkok, amelyeken szeretné végrehajtani a lekérdezéseket. Ez az adatbázis lesz használva a metaadat-objektumok egy rugalmas adatbázis-lekérdezés létrehozásához.
    >

## <a name="create-database-objects"></a>Adatbázis-objektumok létrehozása
### <a name="database-scoped-master-key-and-credentials"></a>Adatbázis-hatókörű főkulcs és hitelesítő adatok
Ezek a shard térképkezelőhöz és a szegmensekhez való csatlakozáshoz használhatók:

1. Nyissa meg az SQL Server Management Studio vagy az SQL Server Data Tools alkalmazást a Visual Studióban.
2. Csatlakozzon az ElasticDBQuery adatbázishoz, és hajtsa végre a következő T-SQL parancsokat:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    A "felhasználónév" és a "jelszó" megegyezik a 3. [Download and run the sample app](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) **Getting started with Elastic Database tools**

### <a name="external-data-sources"></a>Külső adatforrások
Külső adatforrás létrehozásához hajtsa végre a következő parancsot az ElasticDBQuery adatbázisban:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" a shard térkép neve, ha a rugalmas adatbázis-eszközök minta használatával hozta létre a shard térkép és a shard térképkezelő. Azonban ha az egyéni beállítás ebben a mintában, majd meg kell a shard térkép nevét választotta az alkalmazásban.

### <a name="external-tables"></a>Külső táblák
Hozzon létre egy külső táblát, amely megfelel a vevők táblának a szegmenseken az ElasticDBQuery adatbázis következő parancsának végrehajtásával:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Rugalmas adatbázis-minta végrehajtása T-SQL lekérdezés
Miután definiálta a külső adatforrást és a külső táblákat, most már teljes T-SQL-t használhat a külső táblákon.

A lekérdezés végrehajtása az ElasticDBQuery adatbázisban:

    select count(CustomerId) from [dbo].[Customers]

Észre fogja venni, hogy a lekérdezés összesíti az összes szegmensek eredményeit, és adja meg a következő kimenetet:

![Kimenet részletei][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Rugalmas adatbázis-lekérdezéseredményeinek importálása az Excelbe
 A lekérdezés eredményeit Excel-fájlba importálhatja.

1. Indítsa el az Excel 2013-at.
2. Nyissa meg az **Adatok** menüszalagot.
3. Kattintson **az Egyéb forrásokból,** majd az **SQL Server kiszolgálóról**parancsra.

   ![Excel importálása más forrásokból][5]
4. Az **Adatkapcsolat varázslómezőbe** írja be a kiszolgáló nevét és bejelentkezési adatait. Kattintson a **Tovább** gombra.
5. A **párbeszédpanelen Jelölje ki a kívánt adatokat tartalmazó adatbázist,** és jelölje ki az **ElasticDBQuery** adatbázist.
6. Jelölje ki a **Vevők** táblát a listanézetben, és kattintson a **Tovább**gombra. Ezután kattintson a **Befejezés** gombra.
7. Az **Adatok importálása** űrlapon válassza **ki, hogyan szeretné megtekinteni ezeket az adatokat a munkafüzetben,** válassza a **Táblázat** lehetőséget, és kattintson **az OK**gombra.

**A Vevők** tábla összes sora, amely különböző szilánkokban van tárolva, feltölti az Excel-lapot.

Most már használhatja az Excel hatékony adatvizualizációs funkcióit. A kapcsolati karakterlánc ot a kiszolgáló nevével, az adatbázis nevével és hitelesítő adataival együtt összekapcsolhatja az üzletiintelligencia- és adatintegrációs eszközökkel a rugalmas lekérdezési adatbázissal. Győződjön meg arról, hogy az SQL Server támogatja az eszköz adatforrásaként. A rugalmas lekérdezési adatbázisra és a külső táblákra ugyanúgy hivatkozhat, mint bármely más SQL Server adatbázisra és SQL Server-táblára, amelyhez az eszközhöz csatlakozna.

### <a name="cost"></a>Költségek
A rugalmas adatbázis-lekérdezés funkció használatáért nem számítunk fel további díjat.

Az árképzési információkért lásd az [SQL Database díjszabási részletei című témakört.](https://azure.microsoft.com/pricing/details/sql-database/)

## <a name="next-steps"></a>További lépések

* A rugalmas lekérdezés áttekintését a [Rugalmas lekérdezés áttekintése című témakörben találja.](sql-database-elastic-query-overview.md)
* Függőleges particionálási oktatóanyag: [Első lépések adatbázisközi lekérdezéssel (függőleges particionálás)](sql-database-elastic-query-getting-started-vertical.md).
* A vertikálisan particionált adatok szintaxisát és mintalekérdezéseit a [Vertikálisan particionált adatok lekérdezése) témakörben találja.](sql-database-elastic-query-vertical-partitioning.md)
* A vízszintesen particionált adatok szintaxisát és mintalekérdezéseit [lásd: Horizontálisan particionált adatok lekérdezése)](sql-database-elastic-query-horizontal-partitioning.md)
* [Lásd:\_ \_sp távoli végrehajtása](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL utasításegyetlen távoli Azure SQL-adatbázis vagy egy horizontális particionálási séma szegmensként szolgáló adatbázisok készlete.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
