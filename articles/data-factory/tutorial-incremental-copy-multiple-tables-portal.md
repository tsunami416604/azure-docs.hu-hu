---
title: Több tábla növekményes másolása Azure Portal használatával
description: Az oktatóanyag során egy Azure Data Factory-folyamatot hoz létre, amely egy helyszíni SQL Server több táblájának módosított adatait másolja növekményesen egy Azure SQL-adatbázisba.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/20/2018
ms.openlocfilehash: 4649ac8bbef23711ed45baffa15bb9e8bff8daec
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119177"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Adatok növekményes betöltése az SQL Server több táblájából egy Azure SQL-adatbázisba

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az oktatóanyag során egy Azure-beli adat-előállítót hoz létre egy olyan folyamattal, amely változásadatokat tölt be egy helyszíni SQL Server több táblájából egy Azure SQL-adatbázisba.    

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

1. **Egy adatraktár előkészítése a küszöbértékek tárolására**.   
    
    Ebben az oktatóanyagban a küszöbértékeket egy SQL-adatbázisban tároljuk.

1. **Folyamat létrehozása a következő tevékenységekkel**: 
    
    a. Egy ForEach tevékenység létrehozása, amely végighalad a forrástáblanevek listáján, amelyet a rendszer paraméterként ad át a folyamatnak. Minden forrástáblához elindítja a következő tevékenységeket a változásadatok betöltéséhez az adott tábla esetében.

    b. Két keresési tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek.

    c. Egy másolási tevékenység létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként az Azure Blob Storage-ba másolja.

    d. Egy StoredProcedure tevékenység létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához. 

    Itt látható a megoldás összefoglaló jellegű ábrája: 

    ![Adatok növekményes betöltése](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **SQL Server**. Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használ forrásadattárként. 
* **Azure SQL Database**. Egy SQL-adatbázist használ fogadóadattárként. Ha még nem rendelkezik SQL-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../azure-sql/database/single-database-create-quickstart.md) ismertető cikket. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Forrástáblák létrehozása az SQL Server-adatbázisban

1. Nyissa meg az SQL Server Management Studiót, és csatlakozzon a helyszíni SQL Server-adatbázishoz.

1. A **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

1. Futtassa a következő SQL-parancsot az adatbázison a `customer_table` és a `project_table` nevű tábla létrehozásához:

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

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Céltáblák létrehozása az Azure SQL-adatbázisban
1. Nyissa meg az SQL Server Management Studio alkalmazást, és csatlakozzon az Azure SQL-adatbázishoz.

1. A **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

1. Futtassa a következő SQL-parancsot az Azure SQL Database-ben a és a nevű tábla létrehozásához `customer_table` `project_table` :  
    
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

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Egy másik tábla létrehozása az Azure SQL-adatbázisban a felső küszöbértékek tárolására
1. Futtassa a következő SQL-parancsot az Azure SQL Database-ben egy nevű tábla létrehozásához `watermarktable` a vízjel értékének tárolásához: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Szúrja be a két forrástábla kezdeti küszöbértékeit a küszöbértékek táblájába.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Tárolt eljárás létrehozása az Azure SQL-adatbázisban 

Az alábbi parancs futtatásával hozzon létre egy tárolt eljárást az Azure SQL-adatbázisban. Ez a tárolt eljárás minden folyamatfuttatás után frissíti a küszöbértéket. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Adattípusok és további tárolt eljárások létrehozása az Azure SQL-adatbázisban
A következő lekérdezés futtatásával hozzon létre két tárolt eljárást és két adattípust az Azure SQL Database-ben. Ezek összevonják a forrástáblák adatait a céltáblákba.

Ahhoz, hogy az utazást könnyen el lehessen kezdeni, közvetlenül használjuk ezeket a tárolt eljárásokat egy tábla változón keresztül, majd egyesítjük őket a célhelyek tárolójába. Legyen óvatos, ha nem vár "nagy" számú különbözeti sort (több mint 100) a Table változóban.  

Ha nagy számú különbözeti sort kell egyesíteni a célhelyen, javasoljuk, hogy a másolási tevékenység használatával másolja át az összes különbözeti értéket egy ideiglenes "előkészítési" táblába a cél tárolóban, majd a saját tárolt eljárást a Table változó használata nélkül egyesítse az "átmeneti" táblából a "végleges" táblába. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
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

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
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
2. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Data Factory**: 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Az **Új data factory** lapon, a **Név** mezőben adja meg a következőt: **ADFMultiIncCopyTutorialDF**. 
 
   Az Azure-beli adatgyár nevének **globálisan egyedinek**kell lennie. Ha egy piros felkiáltójelet lát a következő hibaüzenettel, változtassa meg az adat-előállító nevét (például a következőre: sajátneveADFIncCopyTutorialDF), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
5. Az **erőforráscsoport**esetében hajtsa végre az alábbi lépések egyikét:
     
    - Válassza a **meglévő használata**lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából. 
    - Válassza az **új létrehozása**lehetőséget, és adja meg az erőforráscsoport nevét.   
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
6. A **Verzió** résznél válassza a **V2** értéket.
7. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
8. Kattintson a **Létrehozás**gombra.      
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

## <a name="create-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul létrehozása
Mialatt adatokat helyez át egy magánhálózaton (helyszínen) lévő adattárból egy Azure-adattárba, telepítsen egy saját üzemeltetésű integrációs modult (IR) a helyszíni környezetben. A saját üzemeltetésű integrációs modul adatokat helyez át a magánhálózat és az Azure között. 

1. Kattintson a bal oldali ablak alján látható **Connections** (Kapcsolatok) elemre, majd váltson át az **Integration Runtimes** (Integrációs modulok) lapra a **Connections** (Kapcsolatok) ablakban. 

1. Az **Integration Runtimes** (Integrációs modulok) lapon kattintson a **+ New** (Új) elemre. 

1. A **Integration Runtime telepítő** ablakban jelölje be az **adatáthelyezési és-küldési tevékenységek végrehajtása külső számításokhoz**lehetőséget, majd kattintson a **Folytatás**gombra. 

1. Válassza a **saját**üzemeltetésű lehetőséget, majd kattintson a **Folytatás**gombra. 
1. Adja meg a **MySelfHostedIR** **nevet**, majd kattintson a **Létrehozás**gombra. 

1. Kattintson a **Kattintson ide a számítógépen történő expressz telepítés indításához** elemre az **1. lehetőség: Expressz telepítés** szakaszban. 

   ![Kattintás az Expressz telepítés hivatkozásra](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. Az **Integrációs modul (Saját üzemeltetésű) – Expressz telepítés** ablakban kattintson a **Bezárás** elemre. 

   ![Integrációs modul telepítése – sikeres](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. A webböngészőben kattintson a **Befejezés** gombra az **Integrációs modul telepítése** ablakban. 

 
1. Győződjön meg róla, hogy a **MySelfHostedIR** szerepel az integrációs modulok listájában.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban a helyszíni SQL Server-adatbázissal és az Azure SQL-adatbázissal társított szolgáltatásokat hoz létre. 

### <a name="create-the-sql-server-linked-service"></a>Az SQL Server társított szolgáltatásának létrehozása
Ebben a lépésben a helyszíni SQL Server-adatbázist társítja az adat-előállítóval.

1. A **Connections** (Kapcsolatok) ablakban váltson az **Integration Runtimes** (Integrációs modulok) lapról a **Linked Services** (Társított szolgáltatások) lapra, és kattintson a **+ New** (+ Új) elemre.

1. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **SQL Server** lehetőséget, majd kattintson a **Continue** (Folytatás) gombra. 

1. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket:

    1. A **Név** mezőben adja meg az **SqlServerLinkedService** értéket. 
    1. Válassza a **MySelfHostedIR** elemet a **Connect via integration runtime** (Csatlakozás integrációs modulon keresztül) lehetőségnél. Ez egy **fontos** lépés. Az alapértelmezett integrációs modul nem tud csatlakozni a helyszíni adattárakhoz. Használja a korábban létrehozott saját üzemeltetésű integrációs modult. 
    1. A **Kiszolgáló neve** mezőben adja meg az SQL Server-adatbázist tartalmazó számítógép nevét.
    1. Az **Adatbázis neve** mezőben adja meg a forrásadatokkal rendelkező SQL Server-adatbázis nevét. Az előfeltételek részeként létrehozott egy táblát, és adatokat szúrt be ebbe az adatbázisba. 
    1. Az **Authentication type**(Hitelesítési típus) mezőben válassza ki **a hitelesítés típusát**, amellyel az adatbázishoz kíván csatlakozni. 
    1. A **User name** (Felhasználónév) mezőben adja meg az SQL Server-adatbázishoz hozzáféréssel rendelkező felhasználó nevét. Ha perjel karaktert (`\`) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja az escape-karaktert (`\`). Például: `mydomain\\myuser`.
    1. A **Password** (Jelszó) mezőben adja meg a felhasználónévhez tartozó **jelszót**. 
    1. Ha tesztelni szeretné, hogy a Data Factory csatlakozni tud-e az SQL Server-adatbázishoz, kattintson a **Test connection** (Kapcsolat tesztelése) gombra. Javítson ki minden hibát, amíg nem sikerül a kapcsolódás. 
    1. A társított szolgáltatás mentéséhez kattintson a **Befejezés**gombra.

### <a name="create-the-azure-sql-database-linked-service"></a>Az Azure SQL Database társított szolgáltatás létrehozása
Az utolsó lépésben létrehoz egy társított szolgáltatást, amely összekapcsolja az SQL Server-adatbázist az adat-előállítóval. Ebben a lépésben a cél/fogadó Azure SQL-adatbázist az adat-előállítóhoz kapcsolja. 

1. A **Connections** (Kapcsolatok) ablakban váltson az **Integration Runtimes** (Integrációs modulok) lapról a **Linked Services** (Társított szolgáltatások) lapra, és kattintson a **+ New** (+ Új) elemre.
1. Az **Új társított szolgáltatás** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** elemre. 
1. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket:

    1. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet. 
    1. A **kiszolgáló neve**mezőben válassza ki a kiszolgáló nevét a legördülő listából. 
    1. A **Database name** (Adatbázis neve) mezőben válassza ki azt az Azure SQL-adatbázist, amelyben az előfeltételek részeként létrehozta a customer_table és a project_table táblákat. 
    1. A **User name** (Felhasználónév) mezőben adja meg a Azure SQL-adatbázishoz hozzáféréssel rendelkező felhasználó nevét. 
    1. A **Password** (Jelszó) mezőben adja meg a felhasználónévhez tartozó **jelszót**. 
    1. Ha tesztelni szeretné, hogy a Data Factory csatlakozni tud-e az SQL Server-adatbázishoz, kattintson a **Test connection** (Kapcsolat tesztelése) gombra. Javítson ki minden hibát, amíg nem sikerül a kapcsolódás. 
    1. A társított szolgáltatás mentéséhez kattintson a **Befejezés**gombra.

1. Győződjön meg róla, hogy a két társított szolgáltatás szerepel a listában. 
   
    ![Két társított szolgáltatás](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan adatkészleteket hoz létre, amelyek az adatforrást, az adat célhelyét és a küszöbérték tárolási helyét jelölik.

### <a name="create-a-source-dataset"></a>Forrásadatkészlet létrehozása

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd a **Dataset** (Adatkészlet) elemre.

1. Az **új adatkészlet** ablakban válassza a **SQL Server**lehetőséget, majd kattintson a **Folytatás**gombra. 

1. A webböngészőben megjelenik egy új lap, amely az adatkészlet konfigurálására szolgál. Az adatkészletet a fanézetben is láthatja. A Properties (Tulajdonságok) ablak **General** (Általános) lapjának alján a **SourceDataset** értéket adja meg a **Name** (Név) mezőben. 

1. Váltson a **Connection** (Kapcsolat) lapra a Tulajdonságok ablakban, majd a **Linked service** (Társított szolgáltatás) mezőben válassza az **SqlServerLinkedService** elemet. Itt ne válasszon táblát. A teljes tábla betöltése helyett a folyamat másolási tevékenysége egy SQL-lekérdezést használ az adatok betöltéséhez.

   ![Forrásadatkészlet – kapcsolat](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Fogadó adatkészlet létrehozása
1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd a **Dataset** (Adatkészlet) elemre.

1. Az **új adatkészlet** ablakban válassza a **Azure SQL Database**lehetőséget, majd kattintson a **Folytatás**gombra. 

1. A webböngészőben megjelenik egy új lap, amely az adatkészlet konfigurálására szolgál. Az adatkészletet a fanézetben is láthatja. A Properties (Tulajdonságok) ablak **General** (Általános) lapjának alján a **SinkDataset** értéket adja meg a **Name** (Név) mezőben.

1. Váltson a **Parameters** (Paraméterek) lapra a tulajdonságok ablakában, és hajtsa végre a következő lépéseket: 

    1. Kattintson a **New** (Új) elemre a **Create/update parameters** (Paraméterek létrehozása/frissítése) szakaszban. 
    1. Adja meg a **SinkTableName****nevet** és a **String** (Sztring) **típust**. Ez az adatkészlet a **SinkTableName** paramétert használja. A SinkTableName paramétert a folyamat állítja be dinamikusan, futásidőben. A folyamat ForEach tevékenysége végighalad a táblanevek listáján, és minden egyes ismétléskor átadja a táblanevet ennek az adatkészletnek.
   
    ![Fogadó adatkészlet – tulajdonságok](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Váltson a Tulajdonságok ablak **kapcsolat** fülére, és válassza a **AzureSqlDatabaseLinkedService** lehetőséget a **társított szolgáltatáshoz**. A **Table** (Tábla) tulajdonsághoz kattintson az **Add dynamic content** (Dinamikus tartalom hozzáadása) lehetőségre.   
    
1. A **dinamikus tartalom hozzáadása** ablakban válassza a **SinkTableName** elemet a **Parameters (paraméterek** ) szakaszban. 
 
1. A **Befejezés**gombra kattintva megtekintheti a következőt: " @dataset (). SinkTableName "a tábla neve.

   ![Fogadó adatkészlet – kapcsolat](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Adatkészlet létrehozása a küszöbhöz
Ebben a lépésben egy adatkészletet hozunk létre a felső küszöbértékek tárolására. 

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd a **Dataset** (Adatkészlet) elemre.

1. Az **új adatkészlet** ablakban válassza a **Azure SQL Database**lehetőséget, majd kattintson a **Folytatás**gombra. 

1. A Properties (Tulajdonságok) ablak **General** (Általános) lapjának alján a **WatermarkDataset** értéket adja meg a **Name** (Név) mezőben.
1. Váltson a **Kapcsolat** lapra, és végezze el az alábbi lépéseket: 

    1. A **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.
    1. A **Tábla** mezőnél válassza a **[dbo].[watermarktable]** lehetőséget.

    ![Küszöbérték-adatkészlet – kapcsolat](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Folyamat létrehozása
A folyamat táblanevek listáját használja paraméterként. A ForEach tevékenység végighalad a táblanevek listáján, és elvégzi a következő műveleteket: 

1. A keresési tevékenység lekéri a régi küszöbértéket (a kezdeti értéket, vagy a legutóbbi ismétlés során használt értéket).

1. A keresési tevékenység lekéri az új küszöbértéket (a forrástábla küszöbértéket tartalmazó oszlopának legnagyobb értékét).

1. A másolási tevékenység az előbbi két küszöbérték közötti adatokat másolja a forrásadatbázisból a céladatbázisba.

1. A StoredProcedure (tárolt eljárás) tevékenység frissíti a régi küszöbértéket, hogy a következő ismétlés első lépése azt használja. 

### <a name="create-the-pipeline"></a>A folyamat létrehozása

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson a **Folyamat** elemre.

1. Az **általános** lapon adja meg a **IncrementalCopyPipeline** **nevet**. 

1. A **Parameters (paraméterek** ) lapon hajtsa végre a következő lépéseket: 

    1. Kattintson az **+ Új** elemre. 
    1. A paraméter **nevénél** adja meg a **tableList** nevet. 
    1. Válassza a **tömb** lehetőséget a paraméter **típusaként**.

1. A **tevékenységek** eszközkészletében bontsa ki az **Ismétlés és feltételek** elemet, és húzza a **ForEach** tevékenységet a folyamat tervezőfelületére. A **Properties** (Tulajdonságok) ablak **General** (Általános) lapján az **IterateSQLTables** értéket adja meg. 

1. Váltson a **Beállítások** lapra, majd az **Elemek** értékeként adja meg a következőt: `@pipeline().parameters.tableList`. A ForEach tevékenység végighalad egy táblalistán, és végrehajtja a növekményes másolási műveletet. 

    ![ForEach tevékenység – beállítások](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)

1. Válassza ki a **ForEach** tevékenységet a folyamatban, ha még nincs kiválasztva. Kattintson az **Edit** (Szerkesztés) gombra (ceruza ikon).

1. A **tevékenységek** eszközkészletében bontsa ki az **Általános** elemet, húzza a **keresési** tevékenységet a folyamat tervezőfelületére, és a **Név** mezőbe írja be a **LookupOldWaterMarkActivity** kifejezést.

1. Váltson a **Beállítások** lapra a **tulajdonságok** ablakában, és hajtsa végre a következő lépéseket: 

    1. Válassza ki a **WatermarkDataset** elemet a **Source Dataset** (Forrásadatkészlet) mezőben.
    1. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget. 
    1. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Első keresési tevékenység – beállítások](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Húzza a **Lookup** (Keresés) tevékenységet az **Activities** (Tevékenységek) eszközkészletből, és írja be a **LookupNewWaterMarkActivity****nevet**.
        
1. Váltson a **Beállítások** lapra.

    1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél. 
    1. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget.
    1. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Második keresési tevékenység – beállítások](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Húzza a **Copy** (Másolás) tevékenységet az **Activities** (Tevékenységek) eszközkészletből, és írja be az **IncrementalCopyActivity****nevet**. 

1. Kapcsolja egymás után a **Lookup** (Keresés) tevékenységeket a **Copy** (Másolás) tevékenységhez. A csatlakozáshoz húzza a **Lookup** (Keresés) tevékenységhez tartozó **zöld** gombot a **Copy** (Másolás) tevékenységre. Amikor a másolási tevékenység szegélyének színe **kékre** vált, engedje el az egér gombját.

    ![Keresési tevékenységek hozzákapcsolása egy másolási tevékenységhez](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Válassza ki a folyamatban a **Copy** (Másolás) tevékenységet. Váltson a **Source** (Forrás) lapra a **Properties** (Tulajdonságok) ablakban. 

    1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél. 
    1. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget. 
    1. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Másolási tevékenység – forrás beállításai](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Váltson a **Sink** (Fogadó) lapra, és válassza a **SinkDataset** lehetőséget a **Sink Dataset** (Fogadó adatkészlet) mezőnél. 
        
1. Hajtsa végre a következő lépéseket:

    1. Az **adatkészlet tulajdonságai** **SinkTableName** paraméternél adja meg a következőt: `@{item().TABLE_NAME}` .
    1. A **tárolt eljárás neve** tulajdonságnál adja meg a következőt: `@{item().StoredProcedureNameForMergeOperation}` .
    1. A **Table Type** tulajdonságnál adja meg a következőt: `@{item().TableType}` .
    1. A **Table Type paraméter neve**mezőbe írja be a következőt: `@{item().TABLE_NAME}` .

    ![Másolási tevékenység – paraméterek](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Húzza át a **Tárolt eljárás** tevékenységet a **tevékenységek** eszközkészletéből a folyamat tervezőfelületére. Kapcsolja a **Copy** (Másolás) tevékenységet a **Stored Procedure** (Tárolt eljárás) tevékenységhez. 

1. Válassza ki a **Stored Procedure** (Tárolt eljárás) tevékenységet a folyamatban, és írja be a **StoredProceduretoWriteWatermarkActivity****nevet** a **Properties** (Tulajdonságok) ablak **General** (Általános) lapján. 

1. Váltson az **SQL-fiók** lapra, és válassza a **AzureSqlDatabaseLinkedService** lehetőséget a **társított szolgáltatáshoz**.

    ![Tárolt eljárási tevékenység – SQL-fiók](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Váltson a **Tárolt eljárás** lapra, és végezze el az alábbi lépéseket:

    1. A **tárolt eljárás neveként** válassza az `[dbo].[usp_write_watermark]` lehetőséget. 
    1. Válassza az **Importálási paraméter** lehetőséget. 
    1. Adja meg a következő értékeket a paraméterekhez: 

        | Name | Típus | Érték | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Sztring | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Tárolt eljárási tevékenység – tárolt eljárás beállításai](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Válassza az **összes közzététele** lehetőséget a létrehozott entitások közzétételéhez a Data Factory szolgáltatásban. 

1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítések megtekintéséhez kattintson a **Show Notifications** (Értesítések megjelenítése) hivatkozásra. Az **X** gombra kattintva zárja be az értesítések ablakát.

 
## <a name="run-the-pipeline"></a>A folyamat futtatása

1. A folyamat eszköztárán kattintson az **trigger hozzáadása**lehetőségre, majd az **aktiválás most**elemre.     

1. A **Pipeline Run** (Folyamatfuttatás) ablakban írja be a következő értéket a **tableList** paraméterhez, és kattintson a **Finish** (Befejezés) gombra. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Folyamatfuttatás argumentumai](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a **manuális eseményindítás** által elindított folyamatfuttatást. A lista frissítéséhez kattintson a **Frissítés** gombra. Az **Actions** (Műveletek) oszlop hivatkozásaival megtekintheti a folyamat futásához társított tevékenységfuttatásokat, illetve újra futtathatja a folyamatot. 

    ![Folyamatfuttatások](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Itt a kiválasztott folyamatfuttatásához kapcsolódó összes tevékenységfuttatás látható. 

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
1. A folyamat eszköztárán kattintson az **trigger hozzáadása**lehetőségre, majd az **aktiválás most**elemre.   
1. A **Pipeline Run** (Folyamatfuttatás) ablakban írja be a következő értéket a **tableList** paraméterhez, és kattintson a **Finish** (Befejezés) gombra. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>A folyamat ismételt monitorozása

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a **manuális eseményindítás** által elindított folyamatfuttatást. A lista frissítéséhez kattintson a **Frissítés** gombra. Az **Actions** (Műveletek) oszlop hivatkozásaival megtekintheti a folyamat futásához társított tevékenységfuttatásokat, illetve újra futtathatja a folyamatot. 

1. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Itt a kiválasztott folyamatfuttatásához kapcsolódó összes tevékenységfuttatás látható. 

## <a name="review-the-final-results"></a>A végső eredmények áttekintése
A SQL Server Management Studioban futtassa a következő lekérdezéseket a cél SQL-adatbázison annak ellenőrzéséhez, hogy a frissített/új adatok át lettek-e másolva a forrás tábláiból a célhelyek táblájába. 

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


