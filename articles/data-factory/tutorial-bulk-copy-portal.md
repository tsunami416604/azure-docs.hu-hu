---
title: Adatok tömeges másolása az Azure Data Factory használatával | Microsoft Docs
description: Megismerheti, hogyan másolhat tömegesen adatokat egy forrásadattárból a céladattárba az Azure Data Factory és a másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: b6d7b926a414c95d4e05834bafc91a2aa9c047fe
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "41919412"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Táblák tömeges másolása az Azure Data Factory használatával
Ez az oktatóanyag azt mutatja be, hogyan lehet **táblákat másolni az Azure SQL Database-ből az Azure SQL Data Warehouse-ba**. A minta egyéb másolási forgatókönyvek esetén is alkalmazható. Például táblák másolására az SQL Serverről/Oracle-ből az Azure SQL Database-be/Data Warehouse-ba/Azure Blobba, vagy különböző elérési utak másolására a Blobból Azure SQL Database-táblákba.

> [!NOTE]
> - Ha még csak ismerkedik az Azure Data Factory szolgáltatással: [Bevezetés az Azure Data Factory használatába](introduction.md).

Az oktatóanyag a következő főbb lépésekből áll:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SQL Database, Azure SQL Data Warehouse és Azure Storage-beli társított szolgáltatások létrehozása
> * Azure SQL Database- és Azure SQL Data Warehouse-adatkészletek létrehozása.
> * Egy folyamat létrehozása a másolni kívánt táblák megkeresésére, és egy másik folyamat létrehozása a tényleges másolási művelet elvégzésére 
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag az Azure Portalt használja. Az adat-előállítók egyéb eszközökkel/SDK-kkal való létrehozásával kapcsolatban lásd [rövid útmutatóinkat](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat
Ebben a forgatókönyvben az Azure SQL Database-ben lévő egyes táblákat szeretné átmásolni az SQL Data Warehouse-ba. A következők a folyamatokban végbemenő munkafolyamat lépései logikai sorrendben:

![Munkafolyamat](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Az első folyamat megkeresi azoknak a tábláknak a listáját, amelyeket át kell másolni a fogadó adattárakba.  Másik megoldásként fenntarthat egy metaadattáblát, amely felsorolja az összes, a fogadó adattárba másolandó táblát. A folyamat ezután elindít egy másik folyamatot, amely végigiterál az adatbázis összes tábláján, és elvégzi az adatmásolási műveletet.
* A második folyamat hajtja végre a tényleges másolást. A táblák listáját használja paraméterként. A legjobb teljesítmény érdekében a lista minden egyes Azure SQL Database-tábláját másolja át az SQL Data Warehouse megfelelő táblájába [szakaszos másolással a Blob Storage és a PolyBase segítségével](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Ebben a példában az első folyamat a táblák listáját adja át a paraméter értékeként. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Azure Storage-fiók** Az Azure Storage-fiók a tömeges másolási műveletben átmeneti blobtárolóként működik. 
* **Azure SQL Database** Ez az adatbázis tartalmazza a forrásadatokat. 
* **Azure SQL Data Warehouse**. Ez az adattárház tárolja az SQL Database-ből átmásolt adatokat. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Az SQL Database és az SQL Data Warehouse előkészítése

**A forrás Azure SQL Database előkészítése**:

Hozzon létre egy Azure SQL Database-adatbázist az Adventure Works LT mintaadataival az [Azure SQL Database létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikk alapján. Ez az oktatóanyag a mintaadatbázisban található összes táblát átmásolja egy SQL Data Warehouse-ba.

**A fogadó Azure SQL Data Warehouse előkészítése**:

1. Ha még nem rendelkezik Azure SQL Data Warehouse-zal, a létrehozás folyamatáért lásd az [SQL Data Warehouse létrehozásával](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) kapcsolatos cikket.

1. Hozzon létre megfelelő táblasémákat az SQL Data Warehouse-ban. Az [áttelepítési segédprogrammal](https://www.microsoft.com/download/details.aspx?id=49100) **áttelepítheti a sémákat** az Azure SQL Database-ből az Azure SQL Data Warehouse-ba. A későbbiekben az Azure Data Factory segítségével fogja áttelepíteni/másolni az adatokat.

## <a name="azure-services-to-access-sql-server"></a>Az SQL Server elérésének engedélyezése az Azure-szolgáltatások számára

Az SQL Database és az SQL Data Warehouse esetében is engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás **BE** van kapcsolva az Azure SQL Serverhez. Ez a beállítás lehetővé teszi a Data Factory szolgáltatás számára, hogy adatokat olvasson be az Azure SQL Database-ből, és adatokat írjon az Azure SQL Data Warehouse-ba. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

1. Kattintson a **További szolgáltatások** hubra a bal oldalon, majd az **SQL-kiszolgálók** elemre.
1. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
1. A **Tűzfalbeállítások** lapon kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.

## <a name="create-a-data-factory"></a>Data factory létrehozása
1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
1. Az **Új data factory** lapon **névként** adja meg az **ADFTutorialBulkCopyDF** értéket. 
      
     ![Új adat-előállító lap](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a névmezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialBulkCopyDF). A Data Factory-összetevők elnevezésére vonatkozó részleteket a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
1. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
      Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
1. A **Verzió** résznél válassza a **V2** értéket.
1. Válassza ki a Data Factory **helyét**. Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
1. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
1. Kattintson a **Create** (Létrehozás) gombra.
1. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
1. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
1. A Data Factory felhasználóifelület-alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.
1. Az **első lépéseket bemutató** lapon váltson a **Szerkesztés** lapra a bal oldali panelen, ahogy az az alábbi képen látható:  

    ![Első lépések lap](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre az adattárak és a számítási erőforrások adat-előállítóval történő társításához. A társított szolgáltatás rendelkezik azon kapcsolatadatokkal, amelyeket a Data Factory szolgáltatás futtatáskor az adattárhoz való kapcsolódáshoz használ. 

Ebben az oktatóanyagban társítani fogja az Azure SQL Database-t, az Azure SQL Data Warehouse-t és az Azure Blob Storage-ot az adat-előállítóhoz. Az Azure SQL Database a forrásadattár. Az Azure SQL Data Warehouse a cél-/fogadóadattár. Az Azure Blob Storage feladata az adatok előkészítése, mielőtt az adatokat a rendszer betöltené az SQL Data Warehouse-ba a PolyBase segítségével. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>A forrás Azure SQL Database-beli társított szolgáltatás létrehozása
Ebben a lépésben létrehoz egy társított szolgáltatást, hogy az Azure SQL Database-t az adat-előállítóhoz kapcsolja. 

1. Kattintson az ablak alján látható **Kapcsolatok** elemre, majd kattintson az eszköztáron az **+ Új** lehetőségre. 

    ![Új társított szolgáltatás gomb](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
1. Az **Új társított szolgáltatás** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** elemre. 

    ![Az Azure SQL Database kiválasztása](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
1. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    1. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet. 
    1. A **Kiszolgáló neve** mezőnél válassza ki az Azure SQL Server kiszolgálóját.
    1. Az **Adatbázis neve** mezőnél válassza ki az Azure SQL Database adatbázisát. 
    1. Adja meg az Azure SQL Database-hez csatlakoztatni kívánt **felhasználó nevét**. 
    1. Adja meg a felhasználóhoz tartozó **jelszót**. 
    1. Ha tesztelni szeretné az Azure SQL Database-zel létrejövő kapcsolatot a megadott adatok használatával, kattintson a **Kapcsolat tesztelése** elemre.
    1. Kattintson a **Save** (Mentés) gombra.

        ![Azure SQL Database beállításai](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>A fogadó Azure SQL Data Warehouse-beli társított szolgáltatás létrehozása

1. A **Kapcsolatok** lapon kattintson ismét az **+ Új** elemre az eszköztáron. 
1. Az **Új társított szolgáltatás** ablakban válassza az **Azure SQL Data Warehouse** lehetőséget, majd kattintson a **Folytatás** elemre. 
1. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    1. A **Név** mezőbe írja be az **AzureSqlDWLinkedService** nevet. 
    1. A **Kiszolgáló neve** mezőnél válassza ki az Azure SQL Server kiszolgálóját.
    1. Az **Adatbázis neve** mezőnél válassza ki az Azure SQL Database adatbázisát. 
    1. Adja meg az Azure SQL Database-hez csatlakoztatni kívánt **felhasználó nevét**. 
    1. Adja meg a felhasználóhoz tartozó **jelszót**. 
    1. Ha tesztelni szeretné az Azure SQL Database-zel létrejövő kapcsolatot a megadott adatok használatával, kattintson a **Kapcsolat tesztelése** elemre.
    1. Kattintson a **Save** (Mentés) gombra.

### <a name="create-the-staging-azure-storage-linked-service"></a>Az átmeneti Azure Storage-beli társított szolgáltatás létrehozása
Ebben az oktatóanyagban a jobb másolási teljesítmény érdekében az Azure Blob Storage-ot átmeneti területként használja a PolyBase engedélyezéséhez.

1. A **Kapcsolatok** lapon kattintson ismét az **+ Új** elemre az eszköztáron. 
1. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Continue** (Folytatás) elemre. 
1. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    1. A **Név** mezőbe írja be az **AzureStorageLinkedService** nevet. 
    1. A **Storage-fiók neve** elemnél válassza ki saját **Azure Storage-fiókját**.
    1. Kattintson a **Save** (Mentés) gombra.


## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben az oktatóanyagban létrehozza a forrás- és fogadó-adatkészletet, amelyek meghatározzák az adatok tárolásának helyét. 

Az **AzureSqlDatabaseDataset** bemeneti adatkészlet az **AzureSqlDatabaseLinkedService** szolgáltatásra hivatkozik. A társított szolgáltatás megadja a kapcsolati sztringet az adatbázishoz való csatlakozáshoz. Az adatkészlet megadja a forrásadatokat tartalmazó adatbázis és tábla nevét. 

Az **AzureSqlDWDataset** kimeneti adatkészlet az **AzureSqlDWLinkedService** szolgáltatásra hivatkozik. A társított szolgáltatás megadja a kapcsolati sztringet az adattárházhoz való csatlakozáshoz. Az adatkészlet meghatározza az adatbázist és táblát, ahova a rendszer az adatokat másolja. 

Ebben az oktatóanyagban a forrás és cél SQL-táblái nincsenek fixen rögzítve az adatkészlet-definíciókban. Ehelyett a ForEach tevékenység továbbítja futtatáskor a tábla nevét a másolási tevékenységnek. 

### <a name="create-a-dataset-for-source-sql-database"></a>Adatkészlet létrehozása a forrás SQL-adatbázishoz

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson az **Adatkészlet** elemre. 

    ![Új adatkészlet menü](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Az **Új adatkészlet** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Befejezés** gombra. Meg kell jelennie egy új, **AzureSqlTable1** nevű lapnak. 
    
    ![Az Azure SQL Database kiválasztása](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
1. Az alul található Tulajdonságok ablakban a **Név** mezőbe írja be az **AzureSqlDatabaseDataset** nevet.

1. Váltson a **Kapcsolat** lapra, és végezze el az alábbi lépéseket: 

    1. A **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.
    1. Válasszon egy tetszőleges táblát a **Tábla** elemnél. Ez a tábla egy helyőrző tábla. Egy folyamat létrehozásakor meghatározhat egy lekérdezést a forrásadatkészlethez. A lekérdezés segítségével adatok nyerhetők ki az Azure SQL Database-ből. Másik lehetőségként kattinthat a **Szerkesztés** jelölőnégyzetbe, és megadhatja a **dummyName** nevet a tábla neveként. 

    ![Forrásadatkészlet kapcsolata lap](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Adatkészlet létrehozása a fogadó SQL Data Warehouse-hoz

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson az **Adatkészlet** elemre. 
1. Az **Új adatkészlet** ablakban válassza az **Azure SQL Data Warehouse** lehetőséget, majd kattintson a **Befejezés** gombra. Meg kell jelennie egy új, **AzureSqlDWTable1** nevű lapnak. 
1. Az alul található Tulajdonságok ablakban a **Név** mezőbe írja be az **AzureSqlDWDataset** nevet.
1. Váltson a **Paraméterek** lapra, és kattintson az **+ Új** elemre, majd adja meg a **DWTableName** értéket a paraméter neveként. Ha másolja/beilleszti ezt a nevet az oldalról, gondoskodjon arról, hogy ne legyen **szóköz karakter** a **DWTableName** név végén. 

    ![Forrásadatkészlet kapcsolata lap](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter.png)

1. Lépjen a **Kapcsolat** lapra. 

    a. A **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.

    b. A **Tábla** esetében ellenőrizze a **Szerkesztés** lehetőséget, kattintson a táblanév beviteli mezőjére, majd a lenti **Dinamikus tartalom hozzáadása** hivatkozásra. 
    
    ![Paraméter neve](./media/tutorial-bulk-copy-portal/table-name-parameter.png)

    c. A **Dinamikus tartalom hozzáadása** lapon, a **Paraméterek** területen kattintson a **DWTAbleName** elemre, ami automatikusan kitölti a felső `@dataset().DWTableName` kifejezés-szövegmezőt, majd kattintson a **Befejezés** lehetőségre. Az adatkészlet **tableName** tulajdonságához beállított értéket továbbítja a rendszer argumentumként a **DWTableName** paraméterhez. A ForEach tevékenység végighalad egy táblalistán, és egyesével továbbítja azokat a másolási tevékenységnek. 

    ![Adatkészlet paramétereinek szerkesztője](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)

## <a name="create-pipelines"></a>Folyamatok létrehozása
Ebben az oktatóanyagban a következő két folyamatot hozza létre: **IterateAndCopySQLTables** és **GetTableListAndTriggerCopyData**. 

A **GetTableListAndTriggerCopyData** folyamat két lépést hajt végre:

* Megkeresi az Azure SQL Database rendszertábláját, és lekéri a másolni kívánt táblák listáját.
* Elindítja az **IterateAndCopySQLTables** folyamatot, amely elvégezi a tényleges adatmásolást.

A **GetTableListAndTriggerCopyData** a táblák listáját használja paraméterként. A lista minden egyes Azure SQL Database-táblájának adatait átmásolja az Azure SQL Data Warehouse-ba a szakaszos másolás és a PolyBase segítségével.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Az IterateAndCopySQLTables folyamat létrehozása

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson a **Folyamat** elemre.

    ![Új folyamat menü](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Az **Általános** lapon adja meg az **IterateAndCopySQLTables** nevet. 

1. Váltson a **Paraméterek** lapra, és hajtsa végre a következőket: 

    1. Kattintson az **+ Új** elemre. 
    1. A paraméter **nevénél** adja meg a **tableList** nevet.
    1. A **Típus** részben válassza a **Tömb** lehetőséget.

        ![Folyamat paramétere](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
1. A **Tevékenységek** eszközkészletben bontsa ki az **Ismétlés és feltételek** elemet, és húzza a **ForEach** tevékenységet a folyamat tervezőfelületére. A **Tevékenységek** eszközkészletben kereshet is az egyes tevékenységek között. 

    a. Az alul található **Általános** lapon a **Név** mezőbe írja be az **IterateSQLTables** nevet. 

    b. Lépjen a **Beállítások** lapra, kattintson az **Elemek** beviteli mezőjére, majd kattintson a lenti **Dinamikus tartalom hozzáadása** hivatkozásra. 

    ![ForEach tevékenység beállításai](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)

    c. A **Dinamikus tartalom hozzáadása** lapon csukja össze a Rendszerváltozók és függvények szakaszt, majd kattintson a **Paraméterek** alatti **tableList** elemre, ami automatikusan kitölti a felső kifejezés-szövegmezőt a `@pipeline().parameter.tableList` értékkel, majd kattintson a **Befejezés** lehetőségre. 

    ![Foreach paramétereinek szerkesztője](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Lépjen a **Tevékenységek** lapra, kattintson a **Tevékenység hozzáadása** gombra egy gyermektevékenység a **ForEach** tevékenységhez adásához.

1. A **Tevékenységek** eszközkészletben bontsa ki a **DataFlow** elemet, és húzza át a **Másolás** tevékenységet a folyamat tervezőfelületére. Figyelje meg felül a navigációs menüt. Az IterateAndCopySQLTable a folyamat neve, az IterateSQLTables pedig a ForEach tevékenység neve. A tervező a tevékenység hatókörében van. Ha vissza szeretne váltani a folyamatszerkesztőre a ForEach szerkesztőjéből, kattintson a navigációs menüben található hivatkozásra. 

    ![Másolás a ForEach tevékenységbe](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
1. Váltson a **Forrás** lapra, és végezze el az alábbi lépéseket:

    1. **Forrásadatkészletnek** válassza a következőt: **AzureSqlDatabaseDataset**. 
    1. A **Felhasználói lekérdezésnél** válassza a **Lekérdezés** lehetőséget. 
    1. Kattintson a **Lekérdezés** beviteli mezőre, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, adja meg a következő kifejezést a **Lekérdezés** értékeként, majd kattintson a **Befejezés** gombra.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Forrásbeállítások másolása](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
1. Váltson a **Fogadó** lapra, és végezze el az alábbi lépéseket: 

    1. **Fogadó-adatkészletnek** válassza a következőt: **AzureSqlDWDataset**.
    1. Kattintson a DWTableName paraméter VALUE beviteli mezőjére, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, írja be szkriptként a `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` kifejezést, majd kattintson a **Befejezés** gombra.
    1. Bontsa ki a **PolyBase-beállítások** elemet, és jelölje be a **PolyBase engedélyezése** lehetőséget. 
    1. Törölje az **Alapértelmezett használati típus** jelölőnégyzet jelölését. 
    1. Kattintson a **Másolás előtti szkript** beviteli mezőre, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, adja meg szkriptként a következő kifejezést, majd kattintson a **Befejezés** gombra. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Fogadóbeállítások másolása](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. Váltson a **Beállítások** lapra, és végezze el az alábbi lépéseket: 

    1. Az **Előkészítés engedélyezése** értéke legyen **Igaz**.
    1. A **Tárfiók társított szolgáltatásánál** válassza az **AzureStorageLinkedService** elemet.

        ![Előkészítés engedélyezése](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)

1. A folyamat beállításainak érvényesítéséhez kattintson az **Érvényesítés** gombra a felső folyamateszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>A GetTableListAndTriggerCopyData folyamat létrehozása

Ez a folyamat két lépést hajt végre:

* Megkeresi az Azure SQL Database rendszertábláját, és lekéri a másolni kívánt táblák listáját.
* Elindítja az „IterateAndCopySQLTables” folyamatot, amely elvégezi a tényleges adatmásolást.

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson a **Folyamat** elemre.

    ![Új folyamat menü](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. A Tulajdonságok ablakban módosítsa a folyamat nevét a következőre a **GetTableListAndTriggerCopyData** értékre. 

1. A **Tevékenységek** eszközkészletben bontsa ki az **Általános** elemet, húzza a **Keresés** tevékenységet a folyamat tervezőfelületére, és tegye a következőket:

    1. A **Név** mezőbe írja be a **LookupTableList** nevet. 
    1. A **Leírásnál** adja meg a következőt: **A táblalista lekérése az Azure SQL Database-ből**.

        ![Keresési tevékenység – általános lap](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
1. Váltson a **Beállítások** lapra, és végezze el az alábbi lépéseket:

    1. **Forrásadatkészletnek** válassza a következőt: **AzureSqlDatabaseDataset**. 
    1. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget. 
    1. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Törölje a **Csak az első sor** mező jelölőnégyzetének jelölését.

        ![Keresési tevékenység – beállítások lap](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Húzza a **Folyamat végrehajtása** tevékenységet a Tevékenységek eszközkészletből a folyamat tervezőfelületére, és adja neki a **TriggerCopy** nevet.

    ![Folyamat végrehajtása tevékenység – általános lap](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
1. Váltson a **Beállítások** lapra, és végezze el az alábbi lépéseket: 

    1. A **Meghívott folyamat** elemnél válassza az **IterateAndCopySQLTables** lehetőséget. 
    1. Bontsa ki a **Speciális** szakaszt. 
    1. Kattintson az **+ Új** elemre a **Paraméterek** szakaszban. 
    1. A **név** paraméternél adja meg a **tableList** értéket.
    1. Kattintson a VALUE beviteli mezőre, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, adja meg a `@activity('LookupTableList').output.value` értéket a táblanév értékeként, majd kattintson a **Befejezés** gombra. A keresési tevékenység eredménylistáját a második folyamat bemeneteként állítja be. Az eredménylista tartalmazza azokat a táblákat, amelyeknek az adatait a célhelyre kell másolni. 

        ![Folyamat végrehajtása tevékenység – beállítások lap](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
1. A keresési tevékenységhez tartozó **zöld mezőt** a Folyamat végrehajtása tevékenység bal oldalára húzva **csatlakoztassa** a **keresési** tevékenységet a **Folyamat végrehajtása** tevékenységhez.

    ![A keresési és a Folyamat végrehajtása tevékenység csatlakoztatása](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
1. A folyamat érvényesítéséhez kattintson az **Érvényesítés** elemre az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

1. Ha közzé szeretne tenni entitásokat (adathalmazokat, folyamatokat stb.) a Data Factory szolgáltatásban, kattintson az **Összes közzététele** elemre az ablak tetején. Várja meg, amíg befejeződik a közzétételi folyamat. 

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása

Lépjen a **GetTableListAndTriggerCopyData** folyamathoz, kattintson az **Aktiválás**, majd az **Aktiválás most** elemre. 

![Aktiválás most](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a **Monitorozás** lapra. Kattintson a **Frissítés** elemre, amíg meg nem jelennek a futtatások a megoldásában mindkét folyamathoz. Folytassa mindaddig a lista frissítését, amíg meg nem jelenik a **Sikeres** állapot. 

    ![Folyamatfuttatások](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
1. Ha meg szeretné tekinteni a GetTableListAndTriggerCopyData folyamathoz tartozó tevékenységfuttatásokat, kattintson a folyamat Tevékenységek hivatkozásának első hivatkozására. Ehhez a folyamatfuttatáshoz két tevékenységfuttatást kell látnia. 

    ![Tevékenységfuttatások](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
1. A **keresési** tevékenység eredményeinek megtekintéséhez kattintson a tevékenység **Kimenet** oszlopában található hivatkozásra. A **Kimenet** ablakot teljes méretre állíthatja és visszaállíthatja. Áttekintés után kattintson az **X** elemre a **Kimenet** ablak bezárásához.

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
1. A **Folyamatfuttatások** nézetre való visszaváltáshoz kattintson a fent található **Folyamatok** hivatkozásra. Kattintson az **IterateAndCopySQLTables** folyamat **Tevékenységfuttatások megtekintése** hivatkozására (a **Tevékenységek** oszlop első hivatkozása). A kimenetnek az alábbi képen látható módon kell megjelennie. Vegye figyelembe, hogy a **keresési** tevékenység kimenetében minden táblához tartozik egy **másolási** tevékenységfuttatás. 

    ![Tevékenységfuttatások](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
1. Ellenőrizze, hogy a rendszer átmásolta-e az adatokat az oktatóanyagban használt SQL Data Warehouse-célra. 

## <a name="next-steps"></a>További lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SQL Database, Azure SQL Data Warehouse és Azure Storage-beli társított szolgáltatások létrehozása
> * Azure SQL Database- és Azure SQL Data Warehouse-adatkészletek létrehozása.
> * Egy folyamat létrehozása a másolni kívánt táblák megkeresésére, és egy másik folyamat létrehozása a tényleges másolási művelet elvégzésére 
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Folytassa a következő oktatóanyaggal, amelyben az adatok egy forrásból egy célhelyre történő fokozatos másolását ismerheti meg:
> [!div class="nextstepaction"]
>[Adatok fokozatos másolása](tutorial-incremental-copy-portal.md)
