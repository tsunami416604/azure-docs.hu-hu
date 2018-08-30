---
title: Tábla növekményes másolása az Azure Data Factory használatával | Microsoft Docs
description: Az oktatóanyag során egy Azure Data Factory-folyamatot hoz majd létre, amely adatokat másol be növekményesen egy Azure SQL-adatbázisból egy Azure Blob Storage-tárolóba.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: yexu
ms.openlocfilehash: 342fdce9a0e9b47380a8d8c975703ebb7f57e3b6
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43087129"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Adatok növekményes betöltése egy Azure SQL Database-adatbázisból egy Azure Blob Storage-tárolóba
Az oktatóanyag során egy Azure adat-előállítót hoz majd létre egy olyan folyamattal, amely módosított adatokat tölt be egy Azure SQL-adatbázisban lévő táblából egy Azure Blob Storage-tárolóba. 

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az adatraktár előkészítése a küszöbértékek tárolására.
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása.
> * A folyamat futtatása.
> * A folyamat futásának monitorozása. 
> * Eredmények áttekintése
> * További adatok hozzáadása a forráshoz.
> * A folyamat újbóli futtatása.
> * A folyamat második futtatásának monitorozása.
> * A második futtatás eredményeinek ellenőrzése.


## <a name="overview"></a>Áttekintés
Itt látható a megoldás összefoglaló jellegű ábrája: 

![Adatok növekményes betöltése](media\tutorial-Incremental-copy-portal\incrementally-load.png)

Az alábbiak a megoldás kialakításának leglényegesebb lépései: 

1. **A küszöb oszlopának kiválasztása**.
    Jelölje ki az adatforrás egyik oszlopát, amelynek alapján az új és a frissített rekordok minden egyes futtatáskor leválogathatók. Normális esetben az ebben a kiválasztott oszlopban (például: last_modify_time vagy ID) lévő adatok a sorok létrehozásával vagy frissítésével folyamatosan növekednek. Az ebben az oszlopban lévő legnagyobb érték szolgál a küszöbként.

2. **Egy adatraktár előkészítése a küszöbértékek tárolására**. Ebben az oktatóanyagban a küszöbértékeket egy SQL-adatbázisban tároljuk.
    
3. **Egy folyamat létrehozása a következő munkafolyamattal**: 
    
    Ebben a megoldásban a folyamat a következő tevékenységeket tartalmazza:
  
    * Két keresési tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek. 
    * Egy másolási tevékenység létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként egy Blob Storage-tárolóba másolja. 
    * Egy StoredProcedure tevékenység létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához. 


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Azure SQL Database** Ezt az adatbázist használjuk forrásadattárként. Ha még nem rendelkezik SQL-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.
* **Azure Storage**. A blobtárolót használjuk majd fogadóadattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg a [tárfiók létrehozásának](../storage/common/storage-quickstart-create-account.md) lépéseit ismertető cikket. Hozzon létre egy tárolót adftutorial néven. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Adatforrástábla létrehozása az SQL-adatbázisban
1. Nyissa meg az SQL Server Management Studiót. A **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

2. Futtassa a következő SQL-parancsot az SQL-adatbázison egy tábla `data_source_table` néven, adatforrástárként történő létrehozásához: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Az oktatóanyagban a LastModifytime oszlopot használjuk küszöboszlopként. Az adatforrástár adatai az alábbi táblázatban láthatók:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Egy másik tábla létrehozása az SQL-adatbázisban a felső küszöbértékek tárolására
1. Futtassa a következő SQL-parancsot az SQL-adatbázison egy `watermarktable` nevű, a küszöbértékek tárolására szolgáló tábla létrehozásához:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Állítsa be a felső küszöb alapértelmezett értékét a forrásadattár táblanevével. Ebben az oktatóanyagban a tábla neve a következő: data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Tekintse át a következő tábla adatait: `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Kimenet: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Tárolt eljárás létrehozása az SQL-adatbázisban 

Az alábbi parancs futtatásával hozzon létre egy tárolt eljárást az SQL-adatbázisban:

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-incremental-copy-portal/new-azure-data-factory-menu.png)
2. Az **Új data factory** lapon, a **Név** mezőben adja meg a következőt: **ADFIncCopyTutorialDF**. 
      
     ![Új adat-előállító lap](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha egy piros felkiáltójelet lát a következő hibaüzenettel, változtassa meg az adat-előállító nevét (például a következőre: sajátneveADFIncCopyTutorialDF), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezésére vonatkozó részleteket a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
        Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. A **Verzió** résznél válassza a **V2** értéket.
5. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.      
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Az oktatóanyag során egy olyan folyamatot fogunk létrehozni, amelyben két keresési, egy másolási és egy StoredProcedure (tárolt eljárás) tevékenység van összefűzve. 

1. A Data Factory felhasználói felületének **első lépéseket ismertető** oldalán kattintson a **Folyamat létrehozása** csempére. 

   ![A Data Factory felhasználói felületének első lépéseket ismertető oldala](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. A folyamat **tulajdonságait** tartalmazó ablak **általános** lapján adja meg az **IncrementalCopyPipeline** nevet. 

   ![Folyamat neve](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. Adja meg az első keresési tevékenységet a régi küszöbérték lekéréséhez. A **Tevékenységek** eszközkészletben bontsa ki az **Általános** elemet, és húzza a **Keresés** tevékenységet a folyamat tervezőfelületére. Változtassa a tevékenység nevét a következőre: **LookupOldWaterMarkActivity**.

   ![Első keresési tevékenység – név](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Váltson a **Beállítások** lapra, és a **Forrásadatkészlet** elemnél kattintson az **+ Új** gombra. Ebben a lépésben egy olyan adatkészletet hoz létre, amely a **küszöbértékek táblájában** található adatokat jelöli. Ez a tábla tartalmazza az előző másolási művelet során használt régi küszöbértéket. 

   ![Új adatkészlet menü – régi küszöbérték](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. Az **Új adatkészlet** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Befejezés** gombra. Egy új lap nyílik meg az adatkészlethez. 

   ![Az Azure SQL Database kiválasztása](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. Az adatkészlet tulajdonságainak ablakában a **Watermarkdataset** értéket adja meg **névként**.

   ![Küszöbérték-adatkészlet – név](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. Váltson a **Kapcsolat** lapra, majd kattintson az **+ Új** gombra, hogy kapcsolatot létesítsen az Azure SQL Database-adatbázissal (létrehozzon egy társított szolgáltatást). 

   ![Új társított szolgáltatás gomb](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:

    1. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet. 
    2. A **kiszolgáló nevénél** válassza ki az Azure SQL Servert.
    3. Adja meg a **felhasználó a nevét**az Azure SQL Serverhez való hozzáféréshez. 
    4. Adja meg a felhasználónévhez tartozó **jelszót**. 
    5. Az Azure SQL Database-adatbázis kapcsolatának teszteléséhez kattintson a **Kapcsolat tesztelése** elemre.
    6. Kattintson a **Save** (Mentés) gombra.
    7. A **Kapcsolat** lapon ellenőrizze, hogy az **AzureSqlDatabaseLinkedService** lehetőség van-e kiválasztva **társított szolgáltatásként**.
       
        ![Új társított szolgáltatás ablak](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. A **Tábla** mezőnél válassza a **[dbo].[watermarktable]** lehetőséget. A táblában található adatok előnézetének megtekintéséhez kattintson az **Adatok előnézete** elemre.

    ![Küszöbérték-adatkészlet – kapcsolat beállításai](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. A folyamatszerkesztőt úgy érheti el, ha a fenti folyamat fülre vagy a bal oldali fanézetben a folyamat nevére kattint. A **keresési** tevékenység tulajdonságainak lapján ellenőrizze, hogy a **WatermarkDataset** lehetőség van-e kiválasztva a **Forrásadatkészlet** mezőnél. 

    ![Folyamat – régi küszöbérték adatkészlete](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. A **tevékenységek** eszközkészletében bontsa ki az **Általános** elemet, húzzon egy másik **keresési** tevékenységet a folyamat tervezőfelületére, és a tulajdonságok ablakának **Általános** lapján állítsa a nevet a következőre: **LookupNewWaterMarkActivity**. Ez a keresési tevékenység a célhelyre átmásolandó forrásadatokat tartalmazó táblából kap új küszöbértéket. 

    ![Második keresési tevékenység – név](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. A második **keresési** tevékenység tulajdonságainak ablakában váltson a **Beállítások** lapra, majd kattintson az **Új** gombra. Így létrehoz egy, az új küszöbértéket (LastModifyTime maximális értéke) tartalmazó táblára mutató adatkészletet. 

    ![Második keresési tevékenység – új adatkészlet](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. Az **Új adatkészlet** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Befejezés** gombra. Egy új lap nyílik meg ehhez az adatkészlethez. Az adatkészlet fanézetben is megjelenik. 
15. A tulajdonságok ablakának **általános** lapján a **SourceData** értéket adja meg a **Név** mezőben. 

    ![Forrásadatkészlet – név](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. Váltson a **Kapcsolat** lapra, és végezze el az alábbi lépéseket: 

    1. A **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.
    2. A Tábla elemhez válassza a **[dbo].[data_source_table]** lehetőséget. Az oktatóanyag során később megadunk egy olyan lekérdezést, amely az adatkészletre vonatkozik. A lekérdezés elsőbbséget élvez az ebben a lépésben megadott táblával szemben. 

        ![Második keresési tevékenység – új adatkészlet](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. A folyamatszerkesztőt úgy érheti el, ha a fenti folyamat fülre vagy a bal oldali fanézetben a folyamat nevére kattint. A **keresési** tevékenység tulajdonságainak lapján ellenőrizze, hogy a **SourceDataset** lehetőség van-e kiválasztva a **Forrásadatkészlet** mezőnél. 
18. A **Lekérdezés használata** mezőben válassza a **Lekérdezés** lehetőséget, majd írja be a következő lekérdezést: Ön csak a **LastModifyTime** érték maximális értékét választja ki a **data_source_table** táblából. Ha nem rendelkezik ezzel a lekérdezéssel, az adatkészlet minden sorát a táblából kapja, mivel a tábla neve (data_source_table) lett megadva az adatkészlet meghatározásában.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Második keresési tevékenység – lekérdezés](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. A **Tevékenységek** eszközkészletben bontsa ki az **Adatfolyam** elemet, húzza át a **Másolás** tevékenységet az eszközkészletből, és állítsa be az **IncrementalCopyActivity** nevet. 

    ![Másolási tevékenység – név](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. A keresési tevékenységhez csatolt **zöld gombot** a másolási tevékenységhez húzva **kapcsolja mindkét keresési tevékenységet a másolási tevékenységhez**. Amikor a másolási tevékenység szegélyének színe kékre vált, engedje el az egér gombját. 

    ![Keresési tevékenységek hozzákapcsolása egy másolási tevékenységhez](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Válassza ki a **másolási tevékenységet**, és győződjön meg arról, hogy a **tulajdonságok** ablakában megjelennek a tevékenység tulajdonságai. 

    ![Másolási tevékenység tulajdonságai](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. Váltson a **Forrás** lapra a **tulajdonságok** ablakában, és hajtsa végre a következő lépéseket:

    1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél. 
    2. A **Lekérdezés használata** mezőnél válassza a **Lekérdezés** lehetőséget. 
    3. Írja be a következő SQL-lekérdezést a **Lekérdezés** mezőbe. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Másolási tevékenység – forrás](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Váltson a **Fogadó** lapra, és kattintson az **+ Új** lehetőségre a **Fogadó adatkészlet** mezőben. 

    ![Új adatkészlet gomb](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. Ebben az oktatóanyagban a fogadó adattár típusa Azure Blob Storage. Ezért az **Új adatkészlet** ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Befejezés** gombra. 

    ![Select Azure Blob Storage](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. Az adatkészlet tulajdonságainak ablakában, az **általános** lapon, a **SinkDataset** értéket adja meg **névként**. 

    ![Fogadó adatkészlet – név](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. Váltson a **Kapcsolat** lapra, és kattintson az **+ Új** gombra. Ebben a lépésben létrehoz egy kapcsolatot (társított szolgáltatást) az **Azure Blob-tárolóhoz**.

    ![Fogadó adatkészlet – új kapcsolat](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    1. A **Név** mezőbe írja az **AzureStorageLinkedService** nevet. 
    2. A **Storage-fiók neve** elemnél válassza ki saját Azure Storage-fiókját.
    3. Kattintson a **Save** (Mentés) gombra. 

        ![Azure Storage-beli társított szolgáltatás – beállítások](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. A **Kapcsolat** lapon végezze el a következő lépéseket:

    1. Győződjön meg róla, hogy az **AzureStorageLinkedService** lehetőség van kiválasztva **társított szolgáltatásként**. 
    2. A **Fájl elérési útja** mező **mappa** részéhez írja be az **adftutorial/incrementalcopy** értéket. Az **adftutorial** a blobtároló, az **incrementalcopy** pedig a mappa neve. Ez a kódtöredék azt feltételezi, hogy a Blob Storage-ban rendelkezik egy adftutorial nevű blobtárolóval. Ha még nem létezik, hozza létre a tárolót, vagy állítsa be egy meglévő tároló nevét. Az Azure Data Factory automatikusan létrehozza az **incrementalcopy** kimeneti mappát, ha az még nem létezik. A **fájl elérési útjánál** a **Tallózás** gombot is használhatja a blobtárolóban található mappák megkereséséhez. .RunId, '.txt')`.
    3. A **Fájl elérési útja** mező **mappa** részéhez írja be a következőt: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`. A fájl neve dinamikusan jön létre a kifejezés használatával. A folyamat minden futtatásához tartozik egy egyedi azonosító. A másolási tevékenység a futtatási azonosítót használja a fájlnév létrehozásához. 

        ![Fogadó adatkészlet – kapcsolat beállításai](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. A **folyamatszerkesztőt** úgy érheti el, ha a fenti folyamat fülre vagy a bal oldali fanézetben a folyamat nevére kattint. 
29. A **tevékenységek** eszközkészletében bontsa ki az **Általános** elemet, és húzza a **tárolt eljárási** tevékenységet a **tevékenységek** eszközkészletéből a folyamat tervezőfelületére. **Kapcsolja** a **másolási** tevékenység zöld (sikeres) kimenetét a **tárolt eljárási** tevékenységhez. 
    
    ![Másolási tevékenység – forrás](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. Válassza ki a **tárolt eljárási tevékenységet** a folyamattervezőben, és módosítsa a nevét a következőre: **StoredProceduretoWriteWatermarkActivity**. 

    ![Tárolt eljárási tevékenység – név](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. Váltson az **SQL-fiók** lapra, majd a **Társított szolgáltatás** elemnél válassza az *AzureSqlDatabaseLinkedService** lehetőséget. 

    ![Tárolt eljárási tevékenység – SQL-fiók](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. Váltson a **Tárolt eljárás** lapra, és végezze el az alábbi lépéseket: 

    1. A **tárolt eljárás neveként** válassza az **sp_write_watermark** lehetőséget. 
    2. A tárolt eljárás paraméterértékeinek megadásához kattintson a **Paraméter importálása** gombra, és adja meg az alábbi értékeket a paraméterekhez: 

        | Name (Név) | Típus | Érték | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Sztring | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Tárolt eljárási tevékenység – tárolt eljárás beállításai](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. A folyamat beállításainak érvényesítéséhez kattintson az **Érvényesítés** gombra az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **folyamatérvényesítési jelentés** ablakának bezárásához kattintson a >> gombra.   

    ![Folyamat érvényesítése](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. Az entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) az **Összes közzététele** elem kiválasztásával teheti közzé az Azure Data Factory szolgáltatásban. Várjon, amíg megjelenik a sikeres közzétételt jelző üzenet. 

    ![Közzététel gomb](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása
1. Kattintson az **Aktiválás** gombra az eszköztáron, majd az **Aktiválás most** elemre. 

    ![Aktiválás most gomb](./media/tutorial-incremental-copy-portal/trigger-now.png)
2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a manuális eseményindítás által elindított folyamatfuttatás állapotát. A lista frissítéséhez kattintson a **Frissítés** gombra. 
    
    ![Folyamatfuttatások](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez kattintson az első hivatkozásra (**Tevékenységfuttatások megtekintése**) a **Műveletek** oszlopban. A fenti **Folyamatok** elemre kattintva visszaválthat az előző nézetre. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenységfuttatások](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>Az eredmények áttekintése
1. Kapcsolódjon Azure Storage-fiókjához az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) vagy valamilyen hozzá hasonló eszközzel. Ellenőrizze, hogy létrejött-e egy kimeneti fájl az **adftutorial** tároló **incrementalcopy** nevű mappájában.

    ![Első kimeneti fájl](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Ha megnyitja a kimeneti fájlt, láthatja, hogy a **data_source_table** minden adata át lett másolva a blobfájlba. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Ellenőrizze a `watermarktable` legutolsó értékét. Láthatja, hogy a küszöbérték frissült.

    ```sql
    Select * from watermarktable
    ```
    
    A kimenet itt látható:
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>További adatok hozzáadása a forráshoz

Szúrjon be új adatokat az SQL-adatbázisba (forrásadattár).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

Az SQL-adatbázis a következő frissített adatokat tartalmazza:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Még egy folyamatfuttatás aktiválása
1. Váltson a **Szerkesztés** lapra. Kattintson a folyamatra a fanézetben, ha a tervezőben nem nyílik meg. 

    ![Aktiválás most gomb](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. Kattintson az **Aktiválás** gombra az eszköztáron, majd az **Aktiválás most** elemre. 

    ![Aktiválás most gomb](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>A folyamat második futtatásának monitorozása.

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a manuális eseményindítás által elindított folyamatfuttatás állapotát. A lista frissítéséhez kattintson a **Frissítés** gombra. 
    
    ![Folyamatfuttatások](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez kattintson az első hivatkozásra (**Tevékenységfuttatások megtekintése**) a **Műveletek** oszlopban. A fenti **Folyamatok** elemre kattintva visszaválthat az előző nézetre. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenységfuttatások](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>A második kimenet ellenőrzése

1. A blobtárolóban azt láthatja, hogy egy újabb fájl jött létre. Ebben az oktatóanyagban az új fájl neve a következő: `Incremental-<GUID>.txt`. Nyissa meg ezt a fájlt, és láthatja, hogy két sornyi rekordot tartalmaz.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Ellenőrizze a `watermarktable` legutolsó értékét. Láthatja, hogy a küszöbérték ismét frissült.

    ```sql
    Select * from watermarktable
    ```
    Példa a kimenetre: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>További lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Az adatraktár előkészítése a küszöbértékek tárolására.
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása.
> * A folyamat futtatása.
> * A folyamat futásának monitorozása. 
> * Eredmények áttekintése
> * További adatok hozzáadása a forráshoz.
> * A folyamat újbóli futtatása.
> * A folyamat második futtatásának monitorozása.
> * A második futtatás eredményeinek ellenőrzése.

Ebben az oktatóanyagban a folyamat egy SQL-adatbázisban lévő egyetlen táblából másolt adatokat egy blobtárolóba. Folytassa a következő oktatóanyaggal, amelyben azzal ismerkedhet meg, hogyan másolhat adatokat egy helyszíni SQL Server adatbázis több táblájából egy SQL-adatbázisba. 

> [!div class="nextstepaction"]
>[Adatok növekményes betöltése az SQL Server több táblájából az Azure SQL Database adatbázisba](tutorial-incremental-copy-multiple-tables-portal.md)



