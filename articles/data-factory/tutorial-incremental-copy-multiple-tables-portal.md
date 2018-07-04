---
title: Több tábla növekményes másolása az Azure Data Factory használatával | Microsoft Docs
description: Az oktatóanyag során egy Azure Data Factory-folyamatot hoz létre, amely egy helyszíni SQL Server több táblájának módosított adatait másolja növekményesen egy Azure SQL Database-be.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: c35d267acfd1778e80605cdfe9eec0edbb18a281
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052844"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Adatok növekményes betöltése az SQL Server több táblájából egy Azure SQL-adatbázisba
Az oktatóanyag során egy Azure-beli adat-előállítót hoz létre egy olyan folyamattal, amely változásadatokat tölt be egy helyszíni SQL Server több táblájából egy Azure SQL Database-be.    

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Forrás- és céladattárak előkészítése.
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * Az Integration Runtime telepítése. 
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása, futtatása és figyelése.
> * Itt tekintheti meg a művelet eredményét.
> * Adatok hozzáadása vagy frissítése a forrástáblákban.
> * A folyamat újrafuttatása és monitorozása.
> * A végső eredmények áttekintése.

## <a name="overview"></a>Áttekintés
Az alábbiak a megoldás kialakításának leglényegesebb lépései: 

1. **A küszöb oszlopának kiválasztása**.
    
    Jelölje ki az adatforrás egyes táblázatainak egy-egy oszlopát, amely alapján az új és a frissített rekordok minden egyes futtatáskor azonosíthatóak. Normális esetben az ebben a kiválasztott oszlopban (például: last_modify_time vagy ID) lévő adatok a sorok létrehozásával vagy frissítésével folyamatosan növekednek. Az ebben az oszlopban lévő legnagyobb érték szolgál a küszöbként.

2. **Egy adatraktár előkészítése a küszöbértékek tárolására**.   
    
    Ebben az oktatóanyagban a küszöbértékeket egy SQL-adatbázisban tároljuk.

3. **Egy folyamat létrehozása a következő tevékenységekkel**: 
    
    a. Egy ForEach tevékenység létrehozása, amely végighalad a forrástáblanevek listáján, amelyet a rendszer paraméterként ad át a folyamatnak. Minden forrástáblához elindítja a következő tevékenységeket a változásadatok betöltéséhez az adott tábla esetében.

    b. Két keresési tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek.

    c. Egy másolási tevékenység létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként az Azure Blob Storage-ba másolja.

    d. Egy StoredProcedure tevékenység létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához. 

    Itt látható a megoldás összefoglaló jellegű ábrája: 

    ![Adatok növekményes betöltése](media\tutorial-incremental-copy-multiple-tables-portal\high-level-solution-diagram.png)


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Egy SQL Server**. Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használ forrásadattárként. 
* **Azure SQL Database** Egy SQL-adatbázist használ fogadóadattárként. Ha még nem rendelkezik SQL-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Forrástáblák létrehozása az SQL Server-adatbázisban

1. Nyissa meg az SQL Server Management Studiót, és csatlakozzon a helyszíni SQL Server-adatbázishoz.

2. A **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

3. Futtassa a következő SQL-parancsot az adatbázison a `customer_table` és a `project_table` nevű tábla létrehozásához:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Céltáblák létrehozása az Azure SQL Database-ben
1. Nyissa meg az SQL Server Management Studio alkalmazást, és csatlakozzon az Azure SQL Database-hez.

2. A **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

3. Futtassa a következő SQL-parancsot az SQL-adatbázison a `customer_table` és a `project_table` nevű tábla létrehozásához:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Egy másik tábla létrehozása az Azure SQL Database-ben a felső küszöbértékek tárolására
1. Futtassa a következő SQL-parancsot az SQL-adatbázison egy `watermarktable` nevű, a küszöbértékek tárolására szolgáló tábla létrehozásához: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Szúrja be a két forrástábla kezdeti küszöbértékeit a küszöbértékek táblájába.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Tárolt eljárás létrehozása az Azure SQL Database-ben 

Az alábbi parancs futtatásával hozzon létre egy tárolt eljárást az SQL-adatbázisban. Ez a tárolt eljárás minden folyamatfuttatás után frissíti a küszöbértéket. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Adattípusok és további tárolt eljárások létrehozása az Azure SQL Database-ben
Az alábbi lekérdezés futtatásával hozzon létre két tárolt eljárást és két adattípust az SQL-adatbázisban. Ezek összevonják a forrástáblák adatait a céltáblákba.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
2. Az **Új data factory** lapon, a **Név** mezőben adja meg a következőt: **ADFMultiIncCopyTutorialDF**. 
      
     ![Új adat-előállító lap](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, módosítsa az adat-előállító nevét (például sajátneveADFMultiIncCopyTutorialDF értékre), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezésére vonatkozó részleteket a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
        Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.      
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson az **Author & Monitor** (Létrehozás és monitorozás) csempére.
11. Az Azure Data Factory felhasználói felületének első lépéseket ismertető oldalán kattintson a **Create pipeline** (Folyamat létrehozása) csempére (vagy) váltson az **Edit** (Szerkesztés) lapra. 

   ![Első lépések lap](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul létrehozása
Mialatt adatokat helyez át egy magánhálózaton (helyszínen) lévő adattárból egy Azure-adattárba, telepítsen egy saját üzemeltetésű integrációs modult (IR) a helyszíni környezetben. A saját üzemeltetésű integrációs modul adatokat helyez át a magánhálózat és az Azure között. 

1. Kattintson a bal oldali ablak alján látható **Connections** (Kapcsolatok) elemre, majd váltson át az **Integration Runtimes** (Integrációs modulok) lapra a **Connections** (Kapcsolatok) ablakban. 

   ![Connections (Kapcsolatok) lap](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
2. Az **Integration Runtimes** (Integrációs modulok) lapon kattintson a **+ New** (Új) elemre. 

   ![Új integrációs modul – gomb](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
3. Az **Integration Runtime Setup** (Integrációs modul beállítása) ablakban válassza a **Perform data movement and dispatch activities to external computes** (Adatáthelyezés és tevékenységek kiosztása külső számításokhoz) lehetőséget, majd kattintson a **Next** (Tovább) gombra. 

   ![Az integrációsmodul-típus kiválasztása](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
4. Válassza a ** Private Network ** (Magánhálózat) lehetőséget, majd kattintson a **Next** (Tovább) gombra. 

   ![Magánhálózat kiválasztása](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
5. Írja be a **MySelfHostedIR** nevet a **név** mezőjébe, és kattintson a **Next** (Tovább) gombra. 

   ![Saját üzemeltetésű integrációs modul neve](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
10. Kattintson a **Kattintson ide a számítógépen történő expressz telepítés indításához** elemre az **1. lehetőség: Expressz telepítés** szakaszban. 

   ![Kattintás az Expressz telepítés hivatkozásra](./media/tutorial-incremental-copy-multiple-tables-portal/click-exress-setup.png)
11. Az **Integrációs modul (Saját üzemeltetésű) – Expressz telepítés** ablakban kattintson a **Bezárás** elemre. 

   ![Integrációs modul telepítése – sikeres](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
12. A webböngészőben kattintson a **Befejezés** gombra az **Integrációs modul telepítése** ablakban. 

   ![Integrációs modul telepítése – befejezés](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
17. Győződjön meg róla, hogy a **MySelfHostedIR** szerepel az integrációs modulok listájában.

       ![Integrációs modulok – lista](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban a helyszíni SQL Server-adatbázissal és az SQL-adatbázissal társított szolgáltatásokat hoz létre. 

### <a name="create-the-sql-server-linked-service"></a>Az SQL Server társított szolgáltatásának létrehozása
Ebben a lépésben a helyszíni SQL Server-adatbázist társítja az adat-előállítóval.

1. A **Connections** (Kapcsolatok) ablakban váltson az **Integration Runtimes** (Integrációs modulok) lapról a **Linked Services** (Társított szolgáltatások) lapra, és kattintson a **+ New** (+ Új) elemre.

    ![Új társított szolgáltatás gomb](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **SQL Server** lehetőséget, majd kattintson a **Continue** (Folytatás) gombra. 

    ![Select SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
3. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:

    1. A **Név** mezőben adja meg az **SqlServerLinkedService** értéket. 
    2. Válassza a **MySelfHostedIR** elemet a **Connect via integration runtime** (Csatlakozás integrációs modulon keresztül) lehetőségnél. Ez egy **fontos** lépés. Az alapértelmezett integrációs modul nem tud csatlakozni a helyszíni adattárakhoz. Használja a korábban létrehozott saját üzemeltetésű integrációs modult. 
    3. A **Kiszolgáló neve** mezőben adja meg az SQL Server-adatbázist tartalmazó számítógép nevét.
    4. Az **Adatbázis neve** mezőben adja meg a forrásadatokkal rendelkező SQL Server-adatbázis nevét. Az előfeltételek részeként létrehozott egy táblát, és adatokat szúrt be ebbe az adatbázisba. 
    5. Az **Authentication type**(Hitelesítési típus) mezőben válassza ki **a hitelesítés típusát**, amellyel az adatbázishoz kíván csatlakozni. 
    6. A **User name** (Felhasználónév) mezőben adja meg az SQL Server-adatbázishoz hozzáféréssel rendelkező felhasználó nevét. Ha perjel karaktert (`\`) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja az escape-karaktert (`\`). Például: `mydomain\\myuser`.
    7. A **Password** (Jelszó) mezőben adja meg a felhasználónévhez tartozó **jelszót**. 
    8. Ha tesztelni szeretné, hogy a Data Factory csatlakozni tud-e az SQL Server-adatbázishoz, kattintson a **Test connection** (Kapcsolat tesztelése) gombra. Javítson ki minden hibát, amíg nem sikerül a kapcsolódás. 
    9. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra.

        ![SQL Server-beli társított szolgáltatás – beállítások](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Az Azure SQL Database társított szolgáltatás létrehozása
Az utolsó lépésben létrehoz egy társított szolgáltatást, amely összekapcsolja az SQL Server-adatbázist az adat-előállítóval. Ebben a lépésben a cél/fogadó Azure SQL Database-t az adat-előállítóhoz kapcsolja. 

1. A **Connections** (Kapcsolatok) ablakban váltson az **Integration Runtimes** (Integrációs modulok) lapról a **Linked Services** (Társított szolgáltatások) lapra, és kattintson a **+ New** (+ Új) elemre.

    ![Új társított szolgáltatás gomb](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. Az **Új társított szolgáltatás** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** elemre. 
3. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:

    1. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet. 
    3. A **Server name** (Kiszolgáló neve) mezőben válassza ki az Azure SQL Server-kiszolgáló nevét a legördülő listából. 
    4. A **Database name** (Adatbázis neve) mezőben válassza ki azt az Azure SQL-adatbázist, amelyben az előfeltételek részeként létrehozta a customer_table és a project_table táblákat. 
    6. A **User name** (Felhasználónév) mezőben adja meg a Azure SQL-adatbázishoz hozzáféréssel rendelkező felhasználó nevét. 
    7. A **Password** (Jelszó) mezőben adja meg a felhasználónévhez tartozó **jelszót**. 
    8. Ha tesztelni szeretné, hogy a Data Factory csatlakozni tud-e az SQL Server-adatbázishoz, kattintson a **Test connection** (Kapcsolat tesztelése) gombra. Javítson ki minden hibát, amíg nem sikerül a kapcsolódás. 
    9. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra.

        ![Azure SQL társított szolgáltatás – beállítások](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
10. Győződjön meg róla, hogy a két társított szolgáltatás szerepel a listában. 
   
    ![Két társított szolgáltatás](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan adatkészleteket hoz létre, amelyek az adatforrást, az adat célhelyét és a küszöbérték tárolási helyét jelölik.

### <a name="create-a-source-dataset"></a>Forrásadatkészlet létrehozása

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd a **Dataset** (Adatkészlet) elemre.

   ![Új adatkészlet menü](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. A **New Dataset** (Új adatkészlet) ablakban válassza az **SQL Server** lehetőséget, majd kattintson a **Finish** (Befejezés) elemre. 

   ![Select SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
3. A webböngészőben megjelenik egy új lap, amely az adatkészlet konfigurálására szolgál. Az adatkészlet fanézetben is megjelenik. A Properties (Tulajdonságok) ablak **General** (Általános) lapjának alján a **SourceDataset** értéket adja meg a **Name** (Név) mezőben. 

   ![Forrásadatkészlet – név](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
4. Váltson a **Connection** (Kapcsolat) lapra a Tulajdonságok ablakban, majd a **Linked service** (Társított szolgáltatás) mezőben válassza az **SqlServerLinkedService** elemet. Itt ne válasszon táblát. A teljes tábla betöltése helyett a folyamat másolási tevékenysége egy SQL-lekérdezést használ az adatok betöltéséhez.

   ![Forrásadatkészlet – kapcsolat](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Fogadó adatkészlet létrehozása
1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd a **Dataset** (Adatkészlet) elemre.

   ![Új adatkészlet menü](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. Az **Új adatkészlet** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Befejezés** gombra. 

   ![Az Azure SQL Database kiválasztása](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. A webböngészőben megjelenik egy új lap, amely az adatkészlet konfigurálására szolgál. Az adatkészlet fanézetben is megjelenik. A Properties (Tulajdonságok) ablak **General** (Általános) lapjának alján a **SinkDataset** értéket adja meg a **Name** (Név) mezőben.

   ![Fogadó adatkészlet – általános](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
4. Váltson a **Parameters** (Paraméterek) lapra a tulajdonságok ablakában, és hajtsa végre a következő lépéseket: 

    1. Kattintson a **New** (Új) elemre a **Create/update parameters** (Paraméterek létrehozása/frissítése) szakaszban. 
    2. Adja meg a **SinkTableName****nevet** és a **String** (Sztring) **típust**. Ez az adatkészlet a **SinkTableName** paramétert használja. A SinkTableName paramétert a folyamat állítja be dinamikusan, futásidőben. A folyamat ForEach tevékenysége végighalad a táblanevek listáján, és minden egyes ismétléskor átadja a táblanevet ennek az adatkészletnek.
   
       ![Fogadó adatkészlet – tulajdonságok](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
5. Váltson a **Connection** (Kapcsolat) lapra a Tulajdonságok ablakban, majd a **Linked service** (Társított szolgáltatás) mezőben válassza az **AzureSqlLinkedService** elemet. A **Table** (Tábla) tulajdonsághoz kattintson az **Add dynamic content** (Dinamikus tartalom hozzáadása) lehetőségre. 

   ![Fogadó adatkészlet – kapcsolat](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
    
    
6. Válassza a **SinkTableName** elemet a **Parameters** (Paraméterek) szakaszban
   
   ![Fogadó adatkészlet – kapcsolat](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-dynamicContent.png)

   
 7. Miután a **Finish** (Befejezés) gombra kattint, a **@dataset().SinkTableName** táblanévként fog megjelenni.
   
   ![Fogadó adatkészlet – kapcsolat](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Adatkészlet létrehozása a küszöbhöz
Ebben a lépésben egy adatkészletet hozunk létre a felső küszöbértékek tárolására. 

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd a **Dataset** (Adatkészlet) elemre.

   ![Új adatkészlet menü](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. Az **Új adatkészlet** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Befejezés** gombra. 

   ![Az Azure SQL Database kiválasztása](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. A Properties (Tulajdonságok) ablak **General** (Általános) lapjának alján a **WatermarkDataset** értéket adja meg a **Name** (Név) mezőben.
4. Váltson a **Kapcsolat** lapra, és végezze el az alábbi lépéseket: 

    1. A **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.
    2. A **Tábla** mezőnél válassza a **[dbo].[watermarktable]** lehetőséget.

       ![Küszöbérték-adatkészlet – kapcsolat](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Folyamat létrehozása
A folyamat táblanevek listáját használja paraméterként. A ForEach tevékenység végighalad a táblanevek listáján, és elvégzi a következő műveleteket: 

1. A keresési tevékenység lekéri a régi küszöbértéket (a kezdeti értéket, vagy a legutóbbi ismétlés során használt értéket).

2. A keresési tevékenység lekéri az új küszöbértéket (a forrástábla küszöbértéket tartalmazó oszlopának legnagyobb értékét).

3. A másolási tevékenység az előbbi két küszöbérték közötti adatokat másolja a forrásadatbázisból a céladatbázisba.

4. A StoredProcedure (tárolt eljárás) tevékenység frissíti a régi küszöbértéket, hogy a következő ismétlés első lépése azt használja. 

### <a name="create-the-pipeline"></a>A folyamat létrehozása

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson a **Folyamat** elemre.

    ![Új folyamat – menü](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
2. A **Properties** (Tulajdonságok) ablak **General** (Általános) lapján az **IncrementalCopyPipeline** értéket adja meg a **Név** mezőben. 

    ![Folyamat neve](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
3. A **Properties** (Tulajdonságok) ablakban hajtsa végre a következő lépéseket: 

    1. Kattintson az **+ Új** elemre. 
    2. A paraméter **nevénél** adja meg a **tableList** nevet. 
    3. A paraméter **típusaként** válassza az **Object** (Objektum) lehetőséget.

    ![Folyamat paraméterei](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
4. A **tevékenységek** eszközkészletében bontsa ki az **Ismétlés és feltételek** elemet, és húzza a **ForEach** tevékenységet a folyamat tervezőfelületére. A **Properties** (Tulajdonságok) ablak **General** (Általános) lapján az **IterateSQLTables** értéket adja meg. 

    ![ForEach tevékenység – név](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
5. Váltson a **Settings** (Beállítások) lapra a **Properties** (Tulajdonságok) ablakban és adja meg a `@pipeline().parameters.tableList` értéket az **Items** (Elemek) értékeként. A ForEach tevékenység végighalad egy táblalistán, és végrehajtja a növekményes másolási műveletet. 

    ![ForEach tevékenység – beállítások](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
6. Válassza ki a **ForEach** tevékenységet a folyamatban, ha még nincs kiválasztva. Kattintson az **Edit** (Szerkesztés) gombra (ceruza ikon).

    ![ForEach tevékenység – szerkesztés](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
7. A **tevékenységek** eszközkészletében bontsa ki az **Általános** elemet, húzza a **keresési** tevékenységet a folyamat tervezőfelületére, és a **Név** mezőbe írja be a **LookupOldWaterMarkActivity** kifejezést.

    ![Első keresési tevékenység – név](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
8. Váltson a **Beállítások** lapra a **tulajdonságok** ablakában, és hajtsa végre a következő lépéseket: 

    1. Válassza ki a **WatermarkDataset** elemet a **Source Dataset** (Forrásadatkészlet) mezőben.
    2. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget. 
    3. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Első keresési tevékenység – beállítások](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
9. Húzza a **Lookup** (Keresés) tevékenységet az **Activities** (Tevékenységek) eszközkészletből, és írja be a **LookupNewWaterMarkActivity** **nevet**.
        
    ![Második keresési tevékenység – név](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
10. Váltson a **Settings** (Beállítások) lapra.

    1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél. 
    2. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget.
    3. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Második keresési tevékenység – beállítások](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
11. Húzza a **Copy** (Másolás) tevékenységet az **Activities** (Tevékenységek) eszközkészletből, és írja be az **IncrementalCopyActivity** **nevet**. 

    ![Másolási tevékenység – név](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
12. Kapcsolja egymás után a **Lookup** (Keresés) tevékenységeket a **Copy** (Másolás) tevékenységhez. A csatlakozáshoz húzza a **Lookup** (Keresés) tevékenységhez tartozó **zöld** gombot a **Copy** (Másolás) tevékenységre. Amikor a másolási tevékenység szegélyének színe **kékre** vált, engedje el az egér gombját.

    ![Keresési tevékenységek hozzákapcsolása egy másolási tevékenységhez](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
13. Válassza ki a folyamatban a **Copy** (Másolás) tevékenységet. Váltson a **Source** (Forrás) lapra a **Properties** (Tulajdonságok) ablakban. 

    1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél. 
    2. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget. 
    3. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Másolási tevékenység – forrás beállításai](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
14. Váltson a **Sink** (Fogadó) lapra, és válassza a **SinkDataset** lehetőséget a **Sink Dataset** (Fogadó adatkészlet) mezőnél. 
        
    ![Másolási tevékenység – fogadóbeállítások](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
15. Váltson a **Parameters** (Paraméterek) lapra, és hajtsa végre a következő lépéseket:

    1. A **Sink Stored Procedure Name** (Fogadó tárolt eljárás neve) tulajdonsághoz írja be a következőt: `@{item().StoredProcedureNameForMergeOperation}`.
    2. A **Sink Table Type** (Fogadó táblatípusa) tulajdonsághoz írja be a következőt: `@{item().TableType}`.
    3. A **Sink Dataset** (Fogadó adatkészlet) szakasz **SinkTableName** paraméteréhez írja be a következőt: `@{item().TABLE_NAME}`.

        ![Másolási tevékenység – paraméterek](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
16. Húzza át a **Tárolt eljárás** tevékenységet a **tevékenységek** eszközkészletéből a folyamat tervezőfelületére. Kapcsolja a **Copy** (Másolás) tevékenységet a **Stored Procedure** (Tárolt eljárás) tevékenységhez. 

    ![Másolási tevékenység – paraméterek](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
17. Válassza ki a **Stored Procedure** (Tárolt eljárás) tevékenységet a folyamatban, és írja be a **StoredProceduretoWriteWatermarkActivity** **nevet** a **Properties** (Tulajdonságok) ablak **General** (Általános) lapján. 

    ![Tárolt eljárási tevékenység – név](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
18. Váltson az **SQL Account** (SQL-fiók) lapra, majd a **Linked Service** (Társított szolgáltatás) elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.

    ![Tárolt eljárási tevékenység – SQL-fiók](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
19. Váltson a **Tárolt eljárás** lapra, és végezze el az alábbi lépéseket:

    1. A **tárolt eljárás neveként** válassza az `sp_write_watermark` lehetőséget. 
    2. Válassza az **Importálási paraméter** lehetőséget. 
    3. Adja meg a következő értékeket a paraméterekhez: 

        | Name (Név) | Típus | Érték | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Sztring | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Tárolt eljárási tevékenység – tárolt eljárás beállításai](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
20. A bal oldali panelen kattintson a **Publish** (Közzététel) elemre. Ez a művelet közzéteszi a Data Factory szolgáltatásban a létrehozott entitásokat. 

    ![Közzététel gomb](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
21. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítések megtekintéséhez kattintson a **Show Notifications** (Értesítések megjelenítése) hivatkozásra. Az **X** gombra kattintva zárja be az értesítések ablakát.

    ![Értesítések megjelenítése](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>A folyamat futtatása

1. A folyamat eszköztárán kattintson a **Trigger** (Aktiválás) gombra, majd a **Trigger Now** (Aktiválás most) elemre.     

    ![Aktiválás most](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
2. A **Pipeline Run** (Folyamatfuttatás) ablakban írja be a következő értéket a **tableList** paraméterhez, és kattintson a **Finish** (Befejezés) gombra. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

    ![Folyamatfuttatás argumentumai](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a **manuális eseményindítás** által elindított folyamatfuttatást. A lista frissítéséhez kattintson a **Frissítés** gombra. Az **Actions** (Műveletek) oszlop hivatkozásaival megtekintheti a folyamat futásához társított tevékenységfuttatásokat, illetve újra futtathatja a folyamatot. 

    ![Folyamatfuttatások](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Itt a kiválasztott folyamatfuttatásához kapcsolódó összes tevékenységfuttatás látható. 

    ![Tevékenységfuttatások](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Az eredmények áttekintése
Az SQL Server Management Studióban futtassa a következő lekérdezéseket a cél SQL-adatbázison annak ellenőrzéséhez, hogy a rendszer átmásolta-e az adatokat a forrástáblákból a céltáblákba: 

**Lekérdezés** 
```sql
select * from customer_table
```

**Kimenet**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Lekérdezés**

```sql
select * from project_table
```

**Kimenet**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Lekérdezés**

```sql
select * from watermarktable
```

**Kimenet**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Megfigyelhető, hogy mindkét tábla küszöbértékei frissültek. 

## <a name="add-more-data-to-the-source-tables"></a>További adatok hozzáadása a forrástáblákhoz

Futtassa a következő lekérdezést a forrás SQL Server-adatbázison a customer_table meglévő sorának frissítéséhez. Szúrjon be egy új sort a project_table táblába. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>A folyamat újbóli futtatása
1. A webböngésző ablakában váltson a bal oldalon található **Edit** (Szerkesztés) lapra. 
2. A folyamat eszköztárán kattintson a **Trigger** (Aktiválás) gombra, majd a **Trigger Now** (Aktiválás most) elemre.   

    ![Aktiválás most](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. A **Pipeline Run** (Folyamatfuttatás) ablakban írja be a következő értéket a **tableList** paraméterhez, és kattintson a **Finish** (Befejezés) gombra. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>A folyamat ismételt monitorozása

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a **manuális eseményindítás** által elindított folyamatfuttatást. A lista frissítéséhez kattintson a **Frissítés** gombra. Az **Actions** (Műveletek) oszlop hivatkozásaival megtekintheti a folyamat futásához társított tevékenységfuttatásokat, illetve újra futtathatja a folyamatot. 

    ![Folyamatfuttatások](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Itt a kiválasztott folyamatfuttatásához kapcsolódó összes tevékenységfuttatás látható. 

    ![Tevékenységfuttatások](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>A végső eredmények áttekintése
Az SQL Server Management Studióban futtassa a következő lekérdezéseket a céladatbázison annak ellenőrzéséhez, hogy a rendszer átmásolta-e a frissített/új adatokat a forrástáblákból a céltáblákba. 

**Lekérdezés** 
```sql
select * from customer_table
```

**Kimenet**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

A 3. számú **PersonID** új **Name** és **LastModifytime** értékkel rendelkezik. 

**Lekérdezés**

```sql
select * from project_table
```

**Kimenet**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

A project_table táblához hozzá lett adva a **NewProject** bejegyzés. 

**Lekérdezés**

```sql
select * from watermarktable
```

**Kimenet**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Megfigyelhető, hogy mindkét tábla küszöbértékei frissültek.
     
## <a name="next-steps"></a>További lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Forrás- és céladattárak előkészítése.
> * Adat-előállító létrehozása
> * Helyi Integration Runtime (IR) létrehozása.
> * Az Integration Runtime telepítése.
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása, futtatása és figyelése.
> * Itt tekintheti meg a művelet eredményét.
> * Adatok hozzáadása vagy frissítése a forrástáblákban.
> * A folyamat újrafuttatása és monitorozása.
> * A végső eredmények áttekintése.

Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok növekményes betöltése az Azure SQL Database-ből az Azure Blob Storage-ba a változáskövetési technológia használatával](tutorial-incremental-copy-change-tracking-feature-portal.md)


