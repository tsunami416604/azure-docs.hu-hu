---
title: "A jelentés (vízszintes particionálás) kiterjesztett felhő az adatbázisok közötti |} Microsoft Docs"
description: "több adatbázis adatbázis-lekérdezések használata"
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: c81ef5e3-41e9-4fd2-8631-868f2e168147
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: mlandzic
ms.openlocfilehash: 8eb56d44c3a261f6325d4fc91f169d09bf108160
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Jelentés közötti kiterjesztett felhő (előzetes verzió)
A több Azure SQL-adatbázisok egyetlen kapcsolódási pont használatával jelentéseket hozhat létre egy [rugalmas lekérdezési](sql-database-elastic-query-overview.md). Az adatbázisok vízszintesen kell particionálni (más néven "szilánkos").

Ha egy meglévő adatbázist, olvassa el [kiterjesztett adatbázisok áttelepítése a meglévő adatbázisok](sql-database-elastic-convert-to-use-elastic-tools.md).

A lekérdezni szükséges SQL objektumok ismertetése: [vízszintesen particionált az adatbázisok közötti lekérdezés](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Előfeltételek
Töltse le és futtassa a [Ismerkedés a rugalmas adatbázis eszközök minta](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Hozzon létre egy shard mintaalkalmazás térkép-kezelőt
Itt hoz létre a shard térképre manager több szegmensben osztják, az adatok beszúrását követi azokat a szilánkok együtt. Ha már rendelkezik a bennük foglalt horizontálisan skálázott adatok szilánkok telepítése történhet meg, hagyja ki a következő lépéseket, és helyezze át a következő szakaszban.

1. Hozza létre, és futtassa a **Ismerkedés a rugalmas adatbáziseszközöket** mintaalkalmazást. Kövesse a lépéseket, amíg a szakasz a 7. lépés [töltse le és futtassa a mintaalkalmazást](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). 7. lépés végén jelenik meg a következő parancssort:

    ![parancssor][1]
2. A parancsablakban írja be a "1", és nyomja le az ENTER **Enter**. Létrehozza a shard térkép manager, és két szilánkok hozzáadása a kiszolgálóhoz. Ezután írja be a "3", és nyomja le az ENTER **Enter**; a művelet megismétlése négy alkalommal. Ez a szilánkok minta adatsorok szúrja be.
3. A [Azure-portálon](https://portal.azure.com) jelenítsen meg három új adatbázist a kiszolgálón:

   ![A Visual Studio-jóváhagyás][2]

   Ezen a ponton adatbázisok közötti-lekérdezések használata támogatott a Elastic Database ügyféloldali kódtáraknál keresztül. Például használja a 4. lehetőséget a parancsablakban. A lekérdezés eredményeként előálló egy több shard a rendszer mindig egy **UNION ALL** az összes szilánkok az eredményeket.

   A következő szakaszban létrehozhatunk egy minta adatbázis végponttal, amely támogatja a gazdagabb lekérdezése az adatok között szilánkok.

## <a name="create-an-elastic-query-database"></a>A lekérdezés rugalmas adatbázis létrehozása
1. Nyissa meg a [Azure-portálon](https://portal.azure.com) , és jelentkezzen be.
2. Új Azure SQL-adatbázis létrehozása a shard beállításai ugyanarra a kiszolgálóra. Neve az adatbázis "ElasticDBQuery."

    ![Azure-portál és a tarifacsomag][3]

    > [!NOTE]
    > használhat egy meglévő adatbázist. Ha így tesz, nem lehet egy a szilánkok, amelyeket szeretne, a-lekérdezéseket hajt végre. Ezt az adatbázist a metaadat-objektumok egy rugalmas adatbázis-lekérdezés létrehozásához használható.
    >

## <a name="create-database-objects"></a>Adatbázis-objektumok létrehozása
### <a name="database-scoped-master-key-and-credentials"></a>Adatbázis-hatókörű főkulcs és a hitelesítő adatok
Ezek használhatók a shard térkép manager és a szilánkok való csatlakozáshoz:

1. Nyissa meg az SQL Server Management Studio vagy Visual Studio SQL Server Data Tools összetevővel.
2. Csatlakozzon az ElasticDBQuery adatbázisához, és a következő T-SQL-parancsok:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "felhasználónév" és a "password" legyen ugyanaz, mint a 6. lépésében használt bejelentkezési adatok [töltse le és futtassa a mintaalkalmazást](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) a [Ismerkedés a rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Külső adatforrások
A külső létrehozásához a ElasticDBQuery adatbázison végre az alábbi parancsot:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" shard leképezés neve esetén a shard térkép és shard térkép-kezelőt a rugalmas adatbázis eszközök minta létrehozott. Azonban ha az egyéni telepítő ezt a mintát használja, majd azt névnek kell lennie a shard térkép választja, az alkalmazás.

### <a name="external-tables"></a>Külső táblák
Hozzon létre egy külső táblát, amely megfelel a szilánkok ügyfelek tábla a következő ElasticDBQuery adatbázis a következő parancs futtatásával:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Egy minta rugalmas adatbázis T-SQL-lekérdezés végrehajtása
A külső adatforrást és a külső táblák meghatározása után a külső táblákon végrehajtott most már használhatja a teljes T-SQL.

Hajtsa végre a lekérdezést a ElasticDBQuery adatbázison:

    select count(CustomerId) from [dbo].[Customers]

Megfigyelheti, hogy a lekérdezés eredményeit összesíti a szilánkok a, és lehetőséget ad a következő kimeneti:

![Kimeneti részletei][4]

## <a name="import-elastic-database-query-results-to-excel"></a>A rugalmas adatbázis-lekérdezések eredményének Excel importálása
 Importálhatja az eredményeket a lekérdezés egy Excel-fájlhoz.

1. Indítsa el az Excel 2013.
2. Keresse meg a **adatok** menüszalagján.
3. Kattintson a **egyéb forrásokból származó** kattintson **az SQL Server**.

   ![Excel importálása más forrásokból][5]
4. Az a **Adatkapcsolat varázsló** írja be a kiszolgáló nevét és a bejelentkezési hitelesítő adatokat. Ezután kattintson a **Next** (Tovább) gombra.
5. A párbeszédpanelen **válassza ki a kívánt adatokat tartalmazó adatbázis**, jelölje be a **ElasticDBQuery** adatbázis.
6. Válassza ki a **ügyfelek** táblázatban a lista nézetben, majd kattintson **következő**. Kattintson a **Befejezés**.
7. Az a **és adatokat importálhat** űrlap **válassza ki, hogy az adatok megtekintéséhez a munkafüzet**, jelölje be **tábla** kattintson **OK**.

Összes sorát **ügyfelek** tábla különböző szilánkok tárolt feltölti az Excel-táblában.

Most már használhatja az Excel hatékony képi megjelenítés funkciók. A kiszolgáló nevét, az adatbázis neve és a hitelesítő adatok adatbázishoz való kapcsolódáshoz a BI és az integráció eszközök a Rugalmas lekérdezési használhatja a kapcsolati karakterláncot. Győződjön meg arról, hogy az SQL Server támogatja-e az eszköz adatforrásként. A Rugalmas lekérdezési adatbázis és a külső táblák csakúgy, mint bármely más SQL Server-adatbázis és SQL Server-táblázatot, amely kíván csatlakozni, a eszközzel is hivatkozik.

### <a name="cost"></a>Költségek
Nem kell külön fizetni a rugalmas adatbázis-lekérdezés szolgáltatás használatára vonatkozó van.

Díjszabási információkért lásd: [SQL adatbázis díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Következő lépések

* Rugalmas lekérdezési áttekintését lásd: [rugalmas lekérdezési áttekintése](sql-database-elastic-query-overview.md).
* Függőleges particionálási oktatóanyagért lásd a [első lépések (a vertikális particionálás) közötti adatbázis-lekérdezés](sql-database-elastic-query-getting-started-vertical.md).
* A szintaxis és a minta lekérdezések függőleges particionált adatok, lásd: [adatok lekérdezése függőleges particionálva)](sql-database-elastic-query-vertical-partitioning.md)
* A szintaxis és a minta lekérdezések vízszintesen particionált adatok, lásd: [adatok vízszintesen lekérdezése particionálva)](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_hajtható végre \_távoli](https://msdn.microsoft.com/library/mt703714) tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy egyetlen távoli Azure SQL Database vagy az adatbázisok egy vízszintes particionálási sémát a szilánkok szolgál.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
