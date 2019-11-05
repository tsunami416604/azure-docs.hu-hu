---
title: Adatok szinkronizálása Azure SQL Database Edge-ből Azure Data Factory használatával | Microsoft Docs
description: Tudnivalók az Azure SQL Database Edge és az Azure Blob Storage közötti adatszinkronizálásról
keywords: SQL Database Edge, adatok szinkronizálása az SQL Database Edge-ből, az SQL Database Edge-adatgyárból
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501322"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Oktatóanyag: adatok szinkronizálása SQL Database Edge-ből az Azure Blob Storage-ba a Azure Data Factory használatával

Ebben az oktatóanyagban a Azure Data Factory segítségével az adatok növekményes szinkronizálása egy Azure SQL Database Edge-példányból az Azure Blob Storage-ba.

## <a name="before-you-begin"></a>Előzetes teendők

Ha még nem hozott létre adatbázist vagy táblát a Azure SQL Database Edge-telepítésben, akkor az alábbi módszerek egyikével hozhat létre egyet:

* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) vagy [Azure Data Studio](/sql/azure-data-studio/download/) használatával csatlakozhat az SQL Database Edge-hez, és végrehajthat egy SQL-parancsfájlt az adatbázis és a tábla létrehozásához.
* Hozzon létre egy SQL-adatbázist és-táblázatot a [Sqlcmd](/sql/tools/sqlcmd-utility/) használatával az SQL Database Edge-modulhoz való közvetlen csatlakozással. További információ: [Kapcsolódás az adatbázis-kezelőhöz a Sqlcmd használatával](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* A SQLPackage. exe használatával helyezzen üzembe egy dacpac-fájlt az SQL Database Edge-tárolón. Ez automatizálható úgy, hogy a SQLPackage fájl URI-JÁT a kívánt tulajdonságok konfigurációjának részeként, vagy közvetlenül a SqlPackage. exe ügyfélalkalmazás használatával helyezi üzembe a dacpac a SQL Database Edge-ben.

    A sqlpackage letöltéséhez tekintse meg a [Sqlpackage letöltése és telepítése](/sql/tools/sqlpackage-download/)című témakört. A SqlPackage. exe következő mintáit kell megadnia, de további információért olvassa el a SqlPackage dokumentációját.

    **Dacpac létrehozása**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Dacpac alkalmazása**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>SQL-tábla és-eljárás létrehozása a vízjel szintjeinek tárolására és frissítésére

A vízjel tábla azon utolsó időbélyeg tárolására szolgál, amelybe az adatokat már szinkronizálták az Azure Storage szolgáltatással. A Transact-SQL (T-SQL) tárolt eljárás használatával a rendszer minden szinkronizálás után frissíti a vízjel-táblázatot. 

Hajtsa végre a következő parancsokat az SQL Database Edge-példányon:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>Data Factory munkafolyamat létrehozása

Ebben a szakaszban egy Azure Data Factory folyamatot hoz létre az adatok Azure SQL Database Edge-ből az Azure Blob Storage-ba való szinkronizálásához.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Data Factory létrehozása a Data Factory felhasználói felület használatával

Hozzon létre egy Data Factory az [oktatóanyag](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)utasításai alapján.

### <a name="create-a-data-factory-pipeline"></a>Data Factory folyamat létrehozása

1. A Data Factory felhasználói felületének **első lépések** lapján kattintson a **folyamat létrehozása** csempére.

    ![Data Factory – folyamat létrehozása](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. A folyamat **Tulajdonságok** ablakának **általános** lapján adja meg a **PeriodicSync** nevét.

3. Adja hozzá a **keresési** tevékenységet a régi vízjel értékének lekéréséhez. A **tevékenységek eszközkészletben**bontsa ki az **általános**elemet, és húzza & a **keresési** tevékenység eldobása a folyamat tervező felületére. Módosítsa a tevékenység nevét a *OldWatermark*értékre.

    ![régi vízjel keresése](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Váltson a **Beállítások** lapra, és válassza az **+ új** lehetőséget a **forrás adatkészlethez**. Ebben a lépésben létrehoz egy adatkészletet, amely a watermarktable lévő adatokat jelöli. Ez a tábla tartalmazza az előző másolási művelet során használt régi küszöbértéket.

5. Az **új adatkészlet** ablakban válassza az **Azure SQL Server**lehetőséget, majd válassza a **Folytatás**lehetőséget.  

6. Az adatkészlet **tulajdonságok beállítása** ablakában adja meg a *WatermarkDataset* nevet.

7. A **társított szolgáltatás**esetében válassza az **új**lehetőséget, majd hajtsa végre a következő lépéseket:

    1. Adja meg a *SQLDBEdgeLinkedService* **nevet**.

    2. Adja meg a **kiszolgáló neve**SQL Database Edge-kiszolgálójának adatait.

    3. Adja meg az **adatbázis nevét** a legördülő listából.

    4. Adja meg a **felhasználónevét** és a **jelszavát**.

    5. Az SQL Database Edge-példányhoz való kapcsolódás teszteléséhez válassza a **Kapcsolódás tesztelése**lehetőséget.

    6. Kattintson a **Létrehozás** gombra.

    ![társított szolgáltatás létrehozása](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Kattintson **az OK gombra**

8. A **Beállítások** lapon válassza a **Szerkesztés**lehetőséget.

9. A **kapcsolatok** lapon válassza a *[dbo] lehetőséget. [ watermarktable]* a **táblához**. Ha meg szeretné tekinteni az adatmegjelenítést a táblázatban, válassza az **előnézet**-adatelemet.

10. Váltson a folyamat-szerkesztőre a felső részen található folyamat lapon, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával. A **keresési tevékenység**tulajdonságok ablakában ellenőrizze, hogy a **WatermarkDataset** van-e kiválasztva a **forrás adatkészlet** mezőhöz.

11. A **tevékenységek** eszközkészletben bontsa ki az **általános**elemet, húzzon át egy másik **keresési** tevékenységet a folyamat tervező felületére, és állítsa a nevet a **NewWatermark** értékre a Tulajdonságok ablak **általános** lapján. Ez a keresési tevékenység a célhelyre átmásolandó forrásadatokat tartalmazó táblából kap új küszöbértéket.

12. A második **keresési** tevékenység tulajdonságok ablakában váltson a **Beállítások** lapra, majd az **új** elemre kattintva hozzon létre egy adatkészletet, amely az új küszöbértéket tartalmazó forrás táblára mutat.

13. Az **új adatkészlet** ablakban válassza ki SQL Database Edge-példányt, majd válassza a **Folytatás**lehetőséget.

    1. A **készlet tulajdonságai** ablakban adja meg a **SourceDataset** **nevet**. Válassza a *SQLDBEdgeLinkedService* elemet a társított szolgáltatáshoz.

    2. Válassza ki azt ***a táblázatot, amelyet szinkronizálni szeretne*** a táblával. Ehhez az adatkészlethez is megadhat egy lekérdezést az oktatóanyag későbbi részében leírtak szerint. A lekérdezés elsőbbséget élvez az ebben a lépésben megadott táblával szemben.

    3. Kattintson az **OK** gombra.

14. Váltson a folyamat-szerkesztőre a felső részen található folyamat lapon, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával. A **keresési** tevékenység tulajdonságainak lapján ellenőrizze, hogy a **SourceDataset** lehetőség van-e kiválasztva a **Forrásadatkészlet** mezőnél.

15. Válassza a lekérdezés lehetőséget a lekérdezés **használata** mezőnél, és adja meg a következő lekérdezést a tábla nevének frissítése után a lekérdezésben: csak az időbélyeg maximális értékét **választja ki a** táblából. Győződjön meg arról, hogy csak az **első sort**jelölte be.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![lekérdezés kiválasztása](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. A **tevékenységek** eszközkészletben bontsa ki az **Áthelyezés & átalakítás**elemet, húzza át a **Másolás** tevékenységet a tevékenységek eszközkészletből, és állítsa a nevet **IncrementalCopy**értékre.

17. **A keresési tevékenységhez** csatolt **zöld gombot** a **másolási** tevékenységhez húzva mindkét **keresési** tevékenységet a **másolási** tevékenységhez csatlakoztathatja. Ha a másolási tevékenység szegélyének színe kékre változik, engedje el az egérgombot.

18. Válassza ki a **másolási** tevékenységet, és ellenőrizze, hogy megjelenik-e a tevékenység tulajdonságai a **Tulajdonságok** ablakban.

19. Váltson a **Forrás** lapra a **tulajdonságok** ablakában, és hajtsa végre a következő lépéseket:

    1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél.

    2. A **Lekérdezés használata** mezőnél válassza a **Lekérdezés** lehetőséget.

    3. Adja meg a **lekérdezési** mezőhöz tartozó SQL-lekérdezést. Példa az alábbi lekérdezésre

    4. SQL-lekérdezés:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Váltson a fogadó **lapra,** és válassza a **+ új** lehetőséget a fogadó **adatkészlet** mezőben.

21. Ebben az oktatóanyagban a fogadó adattár típusa **Azure Blob Storage**. Válassza az **Azure Blob Storage**lehetőséget, majd válassza a **Folytatás** lehetőséget az **új adatkészlet** ablakban.

22. A **formátum kiválasztása** ablakban válassza ki az adatai formátumának típusát, majd kattintson a **Folytatás**gombra.

23. A **készlet tulajdonságai** ablakban adja meg a **SinkDataset** nevet. A társított szolgáltatás esetében válassza az **+ új**lehetőséget. Ebben a lépésben létrehoz egy kapcsolatot (társított szolgáltatást) az **Azure Blob-tárolóhoz**.

24. Az **új társított szolgáltatás (Azure Blob Storage)** ablakban végezze el a következő lépéseket:

    1. Adja meg a *AzureStorageLinkedService* nevet.

    2. Az Azure-előfizetéséhez válassza ki az Azure Storage-fiókját a **Storage-fiók nevéhez** .

    3. Tesztelje a **kapcsolatokat** , majd válassza a **Befejezés**lehetőséget.

25. A **készlet tulajdonságai** ablakban ellenőrizze, hogy a *AzureStorageLinkedService* van-e kiválasztva a **társított szolgáltatáshoz**. Ezután válassza a **Létrehozás** és **az OK gombot**.

26. A **fogadó** lapon válassza a **Szerkesztés**lehetőséget.

27. Nyissa meg a *SinkDataset* **Kapcsolódás** lapját, és hajtsa végre a következő lépéseket:

    1. A **fájl elérési útja** mezőbe írja be a következőt: *asdedatasync/incrementalcopy*, ahol a **adftutorial** a blob-tároló neve, a **incrementalcopy** pedig a mappa neve. Ha még nem létezik, hozza létre a tárolót, vagy állítsa be egy meglévő tároló nevét. Az Azure Data Factory automatikusan létrehozza az *incrementalcopy* kimeneti mappát, ha az még nem létezik. A **fájl elérési útjánál** a **Tallózás** gombot is használhatja a blobtárolóban található mappák megkereséséhez.

    2. A **fájl elérési útja** mező **fájljának** részeként válassza a **dinamikus tartalom hozzáadása [ALT + P]** lehetőséget, majd *írja be a @CONCAT(növekményes, folyamat () értéket. RunId, '. txt ')* a megnyitott ablakban. Ezután kattintson a **Befejezés** gombra. A fájl neve dinamikusan jön létre a kifejezés használatával. A folyamat minden futtatásához tartozik egy egyedi azonosító. A másolási tevékenység a futtatási azonosítót használja a fájlnév létrehozásához.

28. Váltson a **folyamat** -szerkesztőre a felső részen található folyamat lapon, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával.

29. A **tevékenységek** eszközkészletében bontsa ki az **Általános** elemet, és húzza a **tárolt eljárási** tevékenységet a **tevékenységek** eszközkészletéből a folyamat tervezőfelületére. **Kapcsolja** a **másolási** tevékenység zöld (sikeres) kimenetét a **tárolt eljárási** tevékenységhez.

30. Válassza a **tárolt eljárási tevékenység** lehetőséget a folyamat-tervezőben, módosítsa a nevét a *SPtoUpdateWatermarkActivity*értékre.

31. Váltson az **SQL-fiók** lapra, és válassza a *SQLDBEdgeLinkedService* lehetőséget a **társított szolgáltatáshoz**.

32. Váltson a **Tárolt eljárás** lapra, és végezze el az alábbi lépéseket:

    1. A **tárolt eljárás neve**mezőben válassza a *[dbo] lehetőséget. [ usp_write_watermark]* .

    2. A tárolt eljárás paraméterei értékének megadásához válassza a paraméter importálása lehetőséget, majd adja meg a következő értékeket a paraméterekhez:

    |Name (Név)|Típus|Érték|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity ("NewWaterMark"). output. firstRow. NewWatermarkvalue}|
    |TableName|Sztring|@ {Activity ("OldWaterMark"). output. firstRow. Táblanév}|

33. A folyamat beállításainak érvényesítéséhez kattintson az **Érvényesítés** elemre az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. **A folyamat-ellenőrzési jelentés** ablakának bezárásához válassza a **>>** lehetőséget.

34. Az entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) az **Összes közzététele** elem kiválasztásával teheti közzé az Azure Data Factory szolgáltatásban. Várjon, amíg megjelenik a sikeres közzétételt jelző üzenet.

## <a name="trigger-a-pipeline-on-schedule"></a>Folyamat elindítása az ütemterv szerint

1. A folyamat eszköztárán válassza az **aktiválás hozzáadása**, majd az **új/szerkesztés**lehetőséget, és válassza az **+ új**lehetőséget.

2. Nevezze el az eseményindítót a *HourlySync*, válassza a **típus** ütemezése lehetőséget, és állítsa be az **ismétlődést** 1 óránként.

3. Kattintson az **OK** gombra.

4. Kattintson **Az összes közzététele** gombra.

5. Válassza az **aktiválás most**lehetőséget.

6. Váltson a bal oldali **Monitorozás** lapra. Láthatja a manuális eseményindítás által elindított folyamatfuttatás állapotát. A lista frissítéséhez kattintson a **frissítés** gombra.

## <a name="next-steps"></a>További lépések

Az oktatóanyagban szereplő Azure Data Factory folyamat az SQL Database Edge-példány egyik táblájából az Azure Blob Storage-ba egy óránként másolja az adatait. Ha többet szeretne megtudni a Data Factory használatáról további forgatókönyvekben, folytassa ezeket az [oktatóanyagokat](../data-factory/tutorial-copy-data-portal.md).
