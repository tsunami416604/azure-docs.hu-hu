---
title: Adatok tömeges másolása az Azure Portal használatával
description: Megismerheti, hogyan másolhat tömegesen adatokat egy forrásadattárból a céladattárba az Azure Data Factory és a másolási tevékenység használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/27/2020
ms.openlocfilehash: 04469fa1bd0473710d9fa0bf0190c6459f1f8a07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418779"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Táblák tömeges másolása az Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez az oktatóanyag **bemutatja, hogy több táblát másol az Azure SQL Database-ből az Azure Synapse Analytics (korábban SQL DW) rendszerbe.** A minta egyéb másolási forgatókönyvek esetén is alkalmazható. Például táblák másolása az SQL Server/Oracle az Azure SQL Database/Azure Synapse Analytics (korábban SQL DW)/Azure Blob, másolása különböző elérési utak blob az Azure SQL Database-táblák.

> [!NOTE]
> - Ha még csak ismerkedik az Azure Data Factory szolgáltatással: [Bevezetés az Azure Data Factory használatába](introduction.md).

Az oktatóanyag a következő főbb lépésekből áll:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre Azure SQL Database, Azure Synapse Analytics (korábban SQL DW) és az Azure Storage kapcsolódó szolgáltatások.
> * Hozzon létre Azure SQL Database és Azure Synapse Analytics (korábban SQL DW) adatkészleteket.
> * Egy folyamat létrehozása a másolni kívánt táblák megkeresésére, és egy másik folyamat létrehozása a tényleges másolási művelet elvégzésére 
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag az Azure Portalt használja. Az adat-előállítók egyéb eszközökkel/SDK-kkal való létrehozásával kapcsolatban lásd [rövid útmutatóinkat](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat
Ebben a forgatókönyvben számos táblát az Azure SQL Database, amely az Azure Synapse Analytics (korábban SQL DW) szeretne másolni. A következők a folyamatokban végbemenő munkafolyamat lépései logikai sorrendben:

![Munkafolyamat](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Az első folyamat megkeresi azoknak a tábláknak a listáját, amelyeket át kell másolni a fogadó adattárakba.  Másik megoldásként fenntarthat egy metaadattáblát, amely felsorolja az összes, a fogadó adattárba másolandó táblát. A folyamat ezután elindít egy másik folyamatot, amely végigiterál az adatbázis összes tábláján, és elvégzi az adatmásolási műveletet.
* A második folyamat hajtja végre a tényleges másolást. A táblák listáját használja paraméterként. A lista minden egyes táblája, másolja az adott táblát az Azure SQL Database a megfelelő táblát az Azure Synapse Analytics (korábban SQL DW) [használatával szakaszos másolás blob storage és polybase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) a legjobb teljesítmény érdekében. Ebben a példában az első folyamat a táblák listáját adja át a paraméter értékeként. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek
* **Az Azure Storage-fiók.** Az Azure Storage-fiók a tömeges másolási műveletben átmeneti blobtárolóként működik. 
* **Az Azure SQL Database .** Ez az adatbázis tartalmazza a forrásadatokat. 
* **Az Azure Synapse Analytics (korábban SQL DW).** Ez az adattárház tárolja az SQL Database-ből átmásolt adatokat. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>SQL-adatbázis és Az Azure Synapse Analytics (korábban SQL DW) előkészítése

**A forrás Azure SQL Database előkészítése**:

Hozzon létre egy Azure SQL-adatbázist az Adventure Works LT mintaadataival az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikk alapján. Ez az oktatóanyag az összes táblát átmásolja ebből a mintaadatbázisból egy Azure Synapse Analytics (korábban SQL DW) rendszerbe.

**Készítse elő a mosogató Azure Synapse Analytics (korábban SQL DW)**:

1. Ha nem rendelkezik egy Azure Synapse Analytics (korábban SQL DW), tekintse meg az [SQL Data Warehouse létrehozása](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) cikket a lépéseket, hogy hozzon létre egyet.

1. Hozzon létre megfelelő táblasémákat az Azure Synapse Analytics (korábban SQL DW) rendszerben. A későbbiekben az Azure Data Factory segítségével fogja áttelepíteni/másolni az adatokat.

## <a name="azure-services-to-access-sql-server"></a>Az SQL Server elérésének engedélyezése az Azure-szolgáltatások számára

Az SQL Database és az Azure Synapse Analytics (korábban SQL DW) esetén engedélyezze az Azure-szolgáltatások nak az SQL-kiszolgáló elérését. Győződjön meg arról, hogy **az Azure-szolgáltatások és -erőforrások hozzáférhetnek ehhez** a kiszolgálói beállításhoz be van **kapcsolva** az Azure SQL-kiszolgálón. Ez a beállítás lehetővé teszi, hogy a Data Factory szolgáltatás adatokat olvasson be az Azure SQL Database-ből, és adatokat írjon az Azure Synapse Analytics (korábban SQL DW) számára. 

A beállítás ellenőrzéséhez és bekapcsolásához nyissa meg az Azure SQL-kiszolgálót > biztonsággal > tűzfalakat és virtuális hálózatokat, > állítsa be az **Azure-szolgáltatások és -erőforrások hozzáférésének engedélyezése a kiszolgálót** **BE beállításra.**

## <a name="create-a-data-factory"></a>Data factory létrehozása
1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) 
1. Az Azure Portal menübal válassza az > **Erőforrás-elemzési** > **adatgyár** **létrehozása lehetőséget.** 
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Az **Új adatgyár** lapon adja meg az **ADFTutorialBulkCopyDF** **nevet.** 
 
   Az Azure-adat-előállító nevének **globálisan egyedinek**kell lennie. Ha a névmezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialBulkCopyDF). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
       `Data factory name "ADFTutorialBulkCopyDF" is not available`
1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
1. Az **erőforráscsoport esetében**tegye az alábbi lépések egyikét:
     
   - Válassza **a Meglévő használata**lehetőséget, és válasszon ki egy meglévő erőforráscsoportot a legördülő listából. 
   - Válassza **az Új létrehozása**lehetőséget, és írja be egy erőforráscsoport nevét.   
         
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
1. A **Verzió** résznél válassza a **V2** értéket.
1. Válassza ki a Data Factory **helyét**. Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
1. Kattintson **a Létrehozás gombra.**
1. A létrehozás befejezése után válassza az **Ugrás az erőforrásra** lehetőséget a **Data Factory** lapra való navigáláshoz. 
   
1. A Data Factory felhasználóifelület-alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.
1. A **Let's get started (Kezdjük)** lapon váltson a bal oldali panel **Szerző** lapjára az alábbi képen látható módon:

     ![Első lépések lap](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre az adattárak és a számítási erőforrások adat-előállítóval történő társításához. A társított szolgáltatás rendelkezik azon kapcsolatadatokkal, amelyeket a Data Factory szolgáltatás futtatáskor az adattárhoz való kapcsolódáshoz használ. 

Ebben az oktatóanyagban az Azure SQL Database, az Azure Synapse Analytics (korábban SQL DW) és az Azure Blob Storage adattárak az adat-előállító. Az Azure SQL Database a forrásadattár. Az Azure Synapse Analytics (korábban SQL DW) a fogadó/cél adattár. Az Azure Blob Storage az adatok színpadra, mielőtt az adatok betöltődnek az Azure Synapse Analytics (korábban SQL DW) a PolyBase használatával. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>A forrás Azure SQL Database-beli társított szolgáltatás létrehozása
Ebben a lépésben létrehoz egy társított szolgáltatást, hogy az Azure SQL-adatbázist az adat-előállítóhoz kapcsolja. 

1. Kattintson az ablak alján található **Kapcsolatok** gombra, majd kattintson az **eszköztáron** a + Új gombra (**A Kapcsolatok** gomb a **gyári erőforrások**csoport bal oldali oszlopának alján található ). 

1. Az **Új társított szolgáltatás** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** elemre. 
1. Az **Új csatolt szolgáltatás (Azure SQL Database)** ablakban tegye a következő lépéseket: 

    a. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.
    
    b. A **Kiszolgáló neve** mezőnél válassza ki az Azure SQL Server kiszolgálóját.
    
    c. Az **Adatbázis neve** mezőnél válassza ki az Azure SQL-adatbázisát. 
    
    d. Adja meg az Azure SQL-adatbázishoz csatlakoztatni kívánt **felhasználó nevét**. 
    
    e. Adja meg a felhasználóhoz tartozó **jelszót**. 

    f. Ha tesztelni szeretné az Azure SQL-adatbázissal létrejövő kapcsolatot a megadott adatok használatával, kattintson a **Kapcsolat tesztelése** elemre.
  
    g. A csatolt szolgáltatás mentéséhez kattintson a **Létrehozás** gombra.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>Hozza létre a fogadó azure Synapse Analytics (korábban SQL DW) kapcsolt szolgáltatás

1. A **Kapcsolatok** lapon kattintson ismét az **+ Új** elemre az eszköztáron. 
1. Az **Új csatolt szolgáltatás** ablakban válassza az **Azure Synapse Analytics (korábbi SQL DW)** lehetőséget, majd kattintson a **Folytatás**gombra. 
1. Az **új csatolt szolgáltatás (Azure Synapse Analytics (korábban SQL DW))** ablakban tegye a következő lépéseket: 
   
    a. A **Név** mezőbe írja be az **AzureSqlDWLinkedService** nevet.
     
    b. A **Kiszolgáló neve** mezőnél válassza ki az Azure SQL Server kiszolgálóját.
     
    c. Az **Adatbázis neve** mezőnél válassza ki az Azure SQL-adatbázisát. 
     
    d. Adja meg **a felhasználónevet** az Azure SQL-adatbázishoz való csatlakozáshoz. 
     
    e. Adja meg a felhasználó **jelszavát.** 
     
    f. Ha tesztelni szeretné az Azure SQL-adatbázissal létrejövő kapcsolatot a megadott adatok használatával, kattintson a **Kapcsolat tesztelése** elemre.
     
    g. Kattintson **a Létrehozás gombra.**

### <a name="create-the-staging-azure-storage-linked-service"></a>Az átmeneti Azure Storage-beli társított szolgáltatás létrehozása
Ebben az oktatóanyagban a jobb másolási teljesítmény érdekében az Azure Blob Storage-ot átmeneti területként használja a PolyBase engedélyezéséhez.

1. A **Kapcsolatok** lapon kattintson ismét az **+ Új** elemre az eszköztáron. 
1. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Continue** (Folytatás) elemre. 
1. Az **Új csatolt szolgáltatás (Azure Blob Storage)** ablakban tegye a következő lépéseket: 

    a. A **Name** (Név) mezőbe írja az **AzureStorageLinkedService** nevet.                                                 
    b. A **Storage-fiók neve** elemnél válassza ki saját **Azure Storage-fiókját**.
    
    c. Kattintson **a Létrehozás gombra.**


## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben az oktatóanyagban létrehozza a forrás- és fogadó-adatkészletet, amelyek meghatározzák az adatok tárolásának helyét. 

Az **AzureSqlDatabaseDataset** bemeneti adatkészlet az **AzureSqlDatabaseLinkedService** szolgáltatásra hivatkozik. A társított szolgáltatás megadja a kapcsolati sztringet az adatbázishoz való csatlakozáshoz. Az adatkészlet megadja a forrásadatokat tartalmazó adatbázis és tábla nevét. 

Az **AzureSqlDWDataset** kimeneti adatkészlet az **AzureSqlDWLinkedService** szolgáltatásra hivatkozik. A csatolt szolgáltatás határozza meg a kapcsolati karakterláncot az Azure Synapse Analytics (korábban SQL DW) csatlakozni. Az adatkészlet meghatározza az adatbázist és táblát, ahova a rendszer az adatokat másolja. 

Ebben az oktatóanyagban a forrás és cél SQL-táblái nincsenek fixen rögzítve az adatkészlet-definíciókban. Ehelyett a ForEach tevékenység továbbítja futtatáskor a tábla nevét a másolási tevékenységnek. 

### <a name="create-a-dataset-for-source-sql-database"></a>Adatkészlet létrehozása a forrás SQL-adatbázishoz

1. Kattintson **a + (plusz)** elemre a bal oldali ablaktáblában, majd kattintson az **Adatkészlet gombra.** 

    ![Új adatkészlet menü](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Az **Új adatkészlet** ablakban válassza az **Azure SQL Database**lehetőséget, majd kattintson a **Folytatás**gombra. 
    
1. A **Tulajdonságok beállítása** ablak **Név területén**írja be az **AzureSqlDatabaseDataset parancsot.** A **Csatolt szolgáltatás csoportban**válassza az **AzureSqlDatabaseLinkedService lehetőséget.** Ezt követően kattintson az **OK** gombra.

1. Váltson a **Kapcsolat** lapra, és jelölje ki a **Táblázat**táblázatot. Ez a tábla egy helyőrző tábla. Egy folyamat létrehozásakor meghatározhat egy lekérdezést a forrásadatkészlethez. A lekérdezés segítségével adatok nyerhetők ki az Azure SQL Database-ből. Másik lehetőségként kattintson a **Szerkesztés** jelölőnégyzetre, és írja be a **dbo.dummyName fájlt** táblanévként. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Adatkészlet létrehozása az Azure Synapse Analytics (korábbi ideig SQL DW) fogadóhoz

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson az **Adatkészlet** elemre. 
1. Az **Új adatkészlet** ablakban válassza az **Azure Synapse Analytics (korábban SQL DW)** lehetőséget, majd kattintson a **Folytatás**gombra.
1. A **Tulajdonságok beállítása** ablakNév **területén**adja meg az **AzureSqlDWDataset parancsot.** A **Csatolt szolgáltatás csoportban**válassza az **AzureSqlDWLinkedService lehetőséget.** Ezt követően kattintson az **OK** gombra.
1. Váltson a **Paraméterek** lapra, és kattintson az **+ Új** elemre, majd adja meg a **DWTableName** értéket a paraméter neveként. Ha ezt a nevet másolja/beilleszti az oldalról, győződjön meg arról, hogy a **DWTableName**végén nincs **záró szóköz karakter.**
1. Lépjen a **Kapcsolat** lapra. 

    a. **A Táblázat beállításban**jelölje be a **Szerkesztés jelölőnégyzetet.** Írja be a **dbo** értéket az első táblanév beviteli mezőbe. Ezután jelölje ki a második beviteli mezőbe, és kattintson az alábbi **Dinamikus tartalom hozzáadása** hivatkozásra. 

    ![Adatkészlet-kapcsolat táblaneve](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. A **Dinamikus tartalom hozzáadása** lapon kattintson a **DWTAbleName (DWTAbleName)** `@dataset().DWTableName`elemre a Paraméterek **csoportban,** amely automatikusan feltölti a felső kifejezés szövegmezőjét, majd kattintson a **Befejezés gombra.** Az adatkészlet **tableName** tulajdonságához beállított értéket továbbítja a rendszer argumentumként a **DWTableName** paraméterhez. A ForEach tevékenység végighalad egy táblalistán, és egyesével továbbítja azokat a másolási tevékenységnek. 

    ![Adatkészlet paramétereinek szerkesztője](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Folyamatok létrehozása
Ebben az oktatóanyagban a következő két folyamatot hozza létre: **IterateAndCopySQLTables** és **GetTableListAndTriggerCopyData**. 

A **GetTableListAndTriggerCopyData** folyamat két műveletet hajt végre:

* Megkeresi az Azure SQL Database rendszertábláját, és lekéri a másolni kívánt táblák listáját.
* Elindítja a folyamat **IterateAndCopySQLTables** a tényleges adatok másolása.

Az **IterateAndCopySQLTables** folyamat paraméterként a táblák listáját veszi fel. A lista minden egyes táblája az Azure SQL Database táblájából másolja az adatokat az Azure Synapse Analytics (korábban SQL DW) szakaszos másolással és PolyBase használatával.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Az IterateAndCopySQLTables folyamat létrehozása

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson a **Folyamat** elemre.

    ![Új folyamat menü](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Az **Általános** lapon adja meg az **IterateAndCopySQLTables** nevet. 

1. Váltson a **Paraméterek** lapra, és hajtsa végre a következőket: 

    a. Kattintson az **+ Új** elemre. 
    
    b. Adja meg a **tableList** paraméter **nevét**.
    
    c. A **Típus** részben válassza a **Tömb** lehetőséget.

1. A **Tevékenységek** eszközkészletben bontsa ki az **Ismétlés és feltételek** elemet, és húzza a **ForEach** tevékenységet a folyamat tervezőfelületére. A **Tevékenységek** eszközkészletben kereshet is az egyes tevékenységek között. 

    a. Az alul található **Általános** lapon a **Név** mezőbe írja be az **IterateSQLTables** nevet. 

    b. Váltson a **Beállítások** fülre, kattintson az **Elemek**beviteli mezőjére, majd az alábbi **Dinamikus tartalom hozzáadása** hivatkozásra. 

    c. A **Dinamikus tartalom hozzáadása** lapon csukja össze a **Rendszerváltozók** és **függvények** szakaszt, kattintson a **TableList (Paraméterek)** csoportban, amely automatikusan feltölti a felső kifejezés szövegmezőjét `@pipeline().parameter.tableList`. **tableList** Ezután kattintson a **Befejezés** gombra. 

    ![Foreach paramétereinek szerkesztője](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Váltson **a Tevékenységek** lapra, kattintson a **ceruza ikonra,** ha gyermektevékenységet szeretne hozzáadni a **ForEach** tevékenységhez.
    ![Foreach tevékenység építője](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. A **Tevékenységek** eszközkészletben bontsa ki **& Áthelyezése & átvitele**elemet, és húzza az **adatmásolási tevékenységet** a folyamattervező felületére. Figyelje meg felül a navigációs menüt. Az **IterateAndCopySQLTable** a folyamat neve, az **IterateSQLTables** pedig a ForEach tevékenység neve. A tervező a tevékenység hatókörében van. Ha vissza szeretne váltani a Folyamatszerkesztőre a ForEach szerkesztőből, kattintson a hivatkozásra a zsemlemorzsa menüben. 

    ![Másolás a ForEach tevékenységbe](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Váltson a **Forrás** lapra, és végezze el az alábbi lépéseket:

    1. **Forrásadatkészletnek** válassza a következőt: **AzureSqlDatabaseDataset**. 
    1. Válassza a **Lekérdezés** lehetőséget a **Lekérdezés használata mezőben.** 
    1. Kattintson a **Lekérdezés** beviteli mezőre, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, adja meg a következő kifejezést a **Lekérdezés** értékeként, majd kattintson a **Befejezés** gombra.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Váltson a **Fogadó** lapra, és végezze el az alábbi lépéseket: 

    1. **Fogadó-adatkészletnek** válassza a következőt: **AzureSqlDWDataset**.
    1. Kattintson a DWTableName paraméter ÉRTÉKE beviteli mezőjére -> `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` válassza ki az alábbi Dinamikus tartalom **hozzáadása** lehetőséget, írja be a kifejezést parancsfájlként, > válassza a Befejezés **lehetőséget.**
    1. A Másolás módszer ben válassza a **PolyBase**lehetőséget. 
    1. Törölje a törlést a **Típus alapértelmezett használata jelölőnégyzetből.** 
    1. Kattintson a **Másolás előtti szkript** beviteli mezőre, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, adja meg szkriptként a következő kifejezést, majd kattintson a **Befejezés** gombra. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Fogadóbeállítások másolása](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Váltson a **Beállítások** lapra, és végezze el az alábbi lépéseket: 

    1. Jelölje be az **Előkészítés engedélyezése**jelölőnégyzetet.
    1. A **Tárfiók társított szolgáltatásánál** válassza az **AzureStorageLinkedService** elemet.

1. A folyamat beállításainak érvényesítéséhez kattintson az **Érvényesítés** gombra a felső folyamateszköztáron. Győződjön meg arról, hogy nincs érvényesítési hiba. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>A GetTableListAndTriggerCopyData folyamat létrehozása

Ez a folyamat két műveletet végez:

* Megkeresi az Azure SQL Database rendszertábláját, és lekéri a másolni kívánt táblák listáját.
* Elindítja az „IterateAndCopySQLTables” folyamatot, amely elvégezi a tényleges adatmásolást.

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson a **Folyamat** elemre.
1. Az **Általános** lapon módosítsa a folyamat nevét **GetTableListAndTriggerCopyData névre.** 

1. A **Tevékenységek** eszközkészletben bontsa ki az **Általános**és a húzásos **kitekintési folyamattal kapcsolatos keresgélési** tevékenységet a folyamattervező felületére, és tegye a következő lépéseket:

    1. A **Név** mezőbe írja be a **LookupTableList** nevet. 
    1. A **Leírásnál** adja meg a következőt: **A táblalista lekérése az Azure SQL-adatbázisból**.

1. Váltson a **Beállítások** lapra, és végezze el az alábbi lépéseket:

    1. **Forrásadatkészletnek** válassza a következőt: **AzureSqlDatabaseDataset**. 
    1. Válassza a **Lekérdezés** **a használt lekérdezéshez lehetőséget**. 
    1. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Törölje a **Csak az első sor** mező jelölőnégyzetének jelölését.

        ![Keresési tevékenység – beállítások lap](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Húzással végezze el a **Folyamat végrehajtása** tevékenységet a Tevékenységek eszközkészletből a folyamattervező felületére, és állítsa a nevet **TriggerCopy**névre.

1. Ha a **lookup** tevékenységet a **Folyamat végrehajtása tevékenységhez** szeretné **kapcsolni,** húzza a lookup tevékenységhez csatolt **zöld mezőt** a Folyamat végrehajtása tevékenység végrehajtásától balra.

    ![A keresési és a Folyamat végrehajtása tevékenység csatlakoztatása](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Váltson a **Folyamatfuttatás végrehajtása** **tevékenység Beállítások** lapjára, és hajtsa végre az alábbi lépéseket: 

    1. A **Meghívott folyamat** elemnél válassza az **IterateAndCopySQLTables** lehetőséget. 
    1. Bontsa ki a **Speciális** szakaszt, és törölje a jelet a **Várakozás a befejezéskor jelölőnégyzetből.**
    1. Kattintson az **+ Új** elemre a **Paraméterek** szakaszban. 
    1. Adja meg a **tableList** **paraméternevét**.
    1. Kattintson a VALUE beviteli mezőre, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, adja meg a `@activity('LookupTableList').output.value` értéket a táblanév értékeként, majd kattintson a **Befejezés** gombra. A lookup tevékenység eredménylistáját a második folyamat bemeneteként állítja be. Az eredménylista tartalmazza azokat a táblákat, amelyeknek az adatait a célhelyre kell másolni. 

        ![Folyamat végrehajtása tevékenység – beállítások lap](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. A folyamat érvényesítéséhez kattintson az **Érvényesítés** elemre az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

1. Ha entitásokat (adatkészleteket, folyamatokat stb.) szeretne közzétenni a Data Factory szolgáltatásban, kattintson az ablak tetején az **összes közzététele** elemre. Várja meg, amíg befejeződik a közzétételi folyamat. 

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása

1. Nyissa meg a **GetTableListAndTriggerCopyData**folyamatot, kattintson az **Eseményindító hozzáadása** gombra a felső csővezeték eszköztárán , majd kattintson az **Eseményindító gombra.** 

1. Erősítse meg a futtatást a **Folyamat futtatása** lapon, majd kattintson a **Befejezés gombra.**

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a **Figyelő** **lapra.** Folytassa mindaddig a lista frissítését, amíg meg nem jelenik a **Sikeres** állapot. 

1. A **GetTableListAndTriggerCopyData** folyamathoz társított tevékenységfuttatások megtekintéséhez kattintson a folyamat névkapcsolatára. Ehhez a folyamatfuttatáshoz két tevékenységfuttatást kell látnia. 
    ![Folyamat futásának figyelése](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. A **keresőtevékenység** kimenetének megtekintéséhez kattintson a **TEVÉKENYSÉG NEVE** oszlopban a tevékenység melletti **Kimenet** hivatkozásra. A **Kimenet** ablakot teljes méretre állíthatja és visszaállíthatja. Áttekintés után kattintson az **X** elemre a **Kimenet** ablak bezárásához.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Ha vissza szeretne váltani a **Folyamatfuttatások** nézetre, kattintson a navigációs menü tetején található **Minden folyamat futtatása** hivatkozás elemre. Kattintson **az IterateAndCopySQLTables** hivatkozásra (a **PIPELINE NAME** oszlopban) a folyamat tevékenységfuttatásainak megtekintéséhez. Figyelje meg, hogy a **lookup** tevékenység kimenetében minden táblához egy **másolási** tevékenység fut. 

1. Győződjön meg arról, hogy az adatok másolása a cél Azure Synapse Analytics (korábban SQL DW) ebben az oktatóanyagban használt. 

## <a name="next-steps"></a>További lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre Azure SQL Database, Azure Synapse Analytics (korábban SQL DW) és az Azure Storage kapcsolódó szolgáltatások.
> * Hozzon létre Azure SQL Database és Azure Synapse Analytics (korábban SQL DW) adatkészleteket.
> * Egy folyamat létrehozása a másolni kívánt táblák megkeresésére, és egy másik folyamat létrehozása a tényleges másolási művelet elvégzésére 
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Folytassa a következő oktatóanyaggal, amelyben az adatok egy forrásból egy célhelyre történő fokozatos másolását ismerheti meg:
> [!div class="nextstepaction"]
>[Adatok fokozatos másolása](tutorial-incremental-copy-portal.md)
