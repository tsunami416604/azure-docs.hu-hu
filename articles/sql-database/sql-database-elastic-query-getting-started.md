---
title: (Horizontális partíciók) kiterjesztett felhőalapú adatbázisok közötti jelentés |} A Microsoft Docs
description: Használja az adatbázis-lekérdezések adatbázisközi jelentéshez több adatbázis között.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 9eabdae9b884b7c55d410100193c8c5a41243e09
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864590"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Jelentéskészítés több kiterjesztett felhőalapú adatbázisok (előzetes verzió)
Jelentéseket hozhat létre egy egyetlen kapcsolódási pont használatával több Azure SQL-adatbázis egy [rugalmas lekérdezés](sql-database-elastic-query-overview.md). Az adatbázisok horizontális kell particionálni a (más néven "horizontálisan skálázott").

Ha rendelkezik egy meglévő adatbázist, lásd: [horizontálisan felskálázott adatbázisok áttelepítése meglévő adatbázisok](sql-database-elastic-convert-to-use-elastic-tools.md).

A lekérdezéséhez szükséges SQL objektumok ismertetése: [horizontálisan particionált adatbázisok közötti lekérdezési](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Előfeltételek
Töltse le és futtassa a [Ismerkedés az Elastic Database-eszközök minta](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Hozzon létre egy szegmens a mintaalkalmazással kezelő
Itt létrehozhat horizontálispartíció-térkép manager több szegmensben, majd az adatok beszúrását a szegmensekre együtt. Ha már rendelkezik a horizontálisan skálázott adatok szegmensek telepítő őket, az alábbi lépéseket kihagyhatja, és helyezze át a következő szakaszra.

1. Hozza létre és futtassa a **Ismerkedés az Elastic Database-eszközök** mintaalkalmazást. Kövesse a lépéseket, amíg a szakasz a 7. lépés [töltse le és futtassa a mintaalkalmazást](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). 7. lépés végén jelenik meg a következő parancssort:

    ![parancssor][1]
2. A parancssori ablakba írja be a "1", és nyomja le az **Enter**. Ez létrehozza a szegmens kezelő, és két szegmensek hozzáadása a kiszolgálóhoz. Ezután írja be a "3", és nyomja le az **Enter**; négyszer ismételje meg a műveletet. Ez a szegmensek minta adatsor szúr be.
3. A [az Azure portal](https://portal.azure.com) jelenítsen meg három új adatbázist a kiszolgálón:

   ![A Visual Studio megerősítése][2]

   Adatbázisközi lekérdezések ezen a ponton az Elastic Database-ügyfélkönyvtárak támogatja. Például a parancsablakban használja a 4. lehetőséget. Többszegmenses lekérdezés eredményeinek mindig van egy **UNION ALL** minden szegmensre eredményét.

   A következő szakaszban létrehozunk egy minta adatbázis végpontot, amely támogatja a gazdagabb lekérdezése az adatokat a szegmensek között.

## <a name="create-an-elastic-query-database"></a>Rugalmas lekérdezés-adatbázis létrehozása
1. Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be.
2. Hozzon létre egy új Azure SQL-adatbázis ugyanazon a kiszolgálón, mint a szilánkleképezés-telepítés. Nevezze el az adatbázis "ElasticDBQuery."

    ![Az Azure portal és a tarifacsomag kiválasztása][3]

    > [!NOTE]
    > használhat egy meglévő adatbázist. Ha így tesz, akkor nem egyikének kell lennie a szegmensek szeretné hajthat végre a lekérdezéseket. Ezt az adatbázist a rugalmas lekérdezés metaadat-objektumok létrehozásához használható.
    >

## <a name="create-database-objects"></a>Adatbázis-objektumok létrehozása
### <a name="database-scoped-master-key-and-credentials"></a>Adatbázis-hatáskörű főkulcs és a hitelesítő adatok
Ezek használhatók a szilánkleképezés-kezelővel és a szegmensek csatlakozni:

1. Nyissa meg az SQL Server Management Studio vagy SQL Server Data Tools a Visual Studióban.
2. Csatlakozás ElasticDBQuery adatbázishoz, és hajtsa végre a következő T-SQL-parancsokat:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" és "password" legyen ugyanaz, mint a 6. lépésben használt bejelentkezési adatok [töltse le és futtassa a mintaalkalmazást](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) a [Ismerkedés az elastic database-eszközök](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Külső adatforrások
Hozzon létre egy külső adatforrást, az ElasticDBQuery adatbázison hajtsa végre a következő parancsot:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" a szegmenstérkép neve esetén a szegmenstérkép és a szilánkleképezés-kezelővel a rugalmas adatbázis-eszközök minta használatával hozott létre. Azonban ha az egyéni telepítés ezt a mintát használja, majd kell a szegmensek leképezésnév az alkalmazás a választott.

### <a name="external-tables"></a>Külső táblák
Hozzon létre egy külső táblát, amely megfelel a szegmensek a Customers tábla ElasticDBQuery adatbázisban a következő parancs végrehajtásával:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Egy mintául szolgáló elastic database-T-SQL-lekérdezés végrehajtása
A külső adatforrásban és a külső táblák meghatározása után a külső táblákon végrehajtott mostantól használhatja a teljes T-SQL.

Hajtsa végre a lekérdezést az ElasticDBQuery adatbázison:

    select count(CustomerId) from [dbo].[Customers]

Láthatja, hogy a lekérdezés összesíti az eredményeket a szegmensből, és lehetővé teszi a következő kimenet:

![Kimenet részletei][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Rugalmas adatbázis-lekérdezés eredményei importálása Excelbe
 A lekérdezés eredményeit, egy Excel-fájlba importálhatja.

1. Indítsa el az Excel 2013-hoz.
2. Keresse meg a **adatok** menüszalagon.
3. Kattintson a **egyéb forrásokból származó** kattintson **az SQL Server**.

   ![Az Excel import más forrásokból][5]
4. Az a **Adatkapcsolat varázsló** írja be a kiszolgáló nevét és bejelentkezési hitelesítő adatokat. Ezután kattintson a **Next** (Tovább) gombra.
5. A párbeszédpanel **válassza ki a kívánt adatokat tartalmazó adatbázisban**, jelölje be a **ElasticDBQuery** adatbázis.
6. Válassza ki a **ügyfelek** a listanézet táblát, és kattintson a **tovább**. Kattintson a **Befejezés**.
7. Az a **adatok importálása** űrlap **válassza ki, hogyan szeretné az adatok megtekintéséhez a munkafüzetet a**válassza **tábla** kattintson **OK**.

Az összes sort **ügyfelek** a különböző szegmensekben tárolt tábla, töltse fel az Excel-munkalapot.

Mostantól használhatja az Excel hatékony vizualizációs funkciókat. Használhatja a kapcsolati karakterláncot a kiszolgáló nevét, az adatbázis nevét és a hitelesítő adatok az adatbázishoz való csatlakozáshoz a BI-ban és integrációs eszközök a rugalmas lekérdezés. Győződjön meg arról, hogy az SQL Server támogatott-e az eszköz adatforrásként. A rugalmas lekérdezés adatbázis és a külső táblák csakúgy, mint bármely más SQL Server-adatbázis és kíván csatlakozni, az eszköz az SQL Server-táblákra is hivatkozik.

### <a name="cost"></a>Költségek
Nem jár további költségekkel az Elastic Database-lekérdezés funkciójával.

Díjszabási információkért tekintse meg a [SQL Database szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>További lépések

* Rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés – áttekintés](sql-database-elastic-query-overview.md).
* Függőleges particionálási oktatóanyagért lásd: [adatbázisközi lekérdezések (vertikális partíciók) – első lépések](sql-database-elastic-query-getting-started-vertical.md).
* Függőlegesen particionált adatok szintaxis és a minta lekérdezéseket, lásd: [lekérdezése függőlegesen particionált adatok)](sql-database-elastic-query-vertical-partitioning.md)
* A szintaxist és a minta lekérdezésekhez vízszintesen particionált adatok, lásd: [lekérdezése vízszintesen particionált adatok)](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_végrehajtása \_távoli](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy távoli Azure SQL Database vagy adatbázisok horizontális particionálási séma kidolgozásához szegmensek szolgáló készletét.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
