---
title: Adatok betöltése az Azure SQL Data Warehouse-ba
description: Az Adatmásolás Azure Data Factory használatával Azure SQL Data Warehouse
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/22/2018
ms.openlocfilehash: 7815a99d4521e6797b4095a38fcfce50ac29a2b8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981192"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Betöltés az Azure SQL Data Warehouseba Azure Data Factory használatával

A [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, kibővíthető adatbázis, amely képes nagy mennyiségű, egymással rokon és nem rokon adatmennyiség feldolgozására. A SQL Data Warehouse a nagyvállalati adattárház számítási feladataihoz optimalizált, nagymértékben párhuzamos feldolgozási (MPP) architektúrára épül. A felhő rugalmasságának köszönhetően rugalmasan méretezheti a tárolást és a számításokat egymástól függetlenül.

A Azure SQL Data Warehouse használatának első lépései mostantól minden eddiginél könnyebben használhatók Azure Data Factory használatakor. A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatással feltöltheti a meglévő rendszerből származó adatokkal rendelkező SQL Data Warehouseokat, és időt takaríthat meg az elemzési megoldások kiépítésekor.

Azure Data Factory a következő előnyöket biztosítja az adatAzure SQL Data Warehouseba való betöltéshez:

* **Egyszerűen beállítható**: egy intuitív 5 lépésből álló varázsló, amely nem igényel parancsfájlt.
* **Gazdag adattár-támogatás**: beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez. Részletes listát a [támogatott adattárakkal](copy-activity-overview.md#supported-data-stores-and-formats)foglalkozó táblázatban talál.
* **Biztonságos és megfelelő**: az adatátvitel HTTPS-vagy ExpressRoute-kapcsolaton keresztül történik. A globális szolgáltatás jelenléte biztosítja, hogy az adatai soha nem hagyják el a földrajzi határt.
* **Páratlan teljesítmény**a következő használatával: a "Base" a leghatékonyabb módszer az adatok Azure SQL Data Warehouseba való áthelyezésére. Az átmeneti blob funkcióval nagy terhelési sebességet érhet el minden típusú adattárból, beleértve az Azure Blob Storage-t és a Data Lake Storet is. (A Base alapértelmezés szerint támogatja az Azure Blob Storage-t és a Azure Data Lake Store.) Részletekért lásd: [másolási tevékenység teljesítménye](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan tölthetők be a Data Factory Adatok másolása eszköz az _adatok Azure SQL Databaseból Azure SQL Data Warehouseba való betöltéséhez_. Az adatok más típusú adattárakból történő másolásához hasonló lépéseket kell követnie.

> [!NOTE]
> További információ: [adatok másolása Azure SQL Data Warehouseba vagy a Azure Data Factory használatával](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Azure SQL Data Warehouse: az adatraktár tartalmazza az SQL-adatbázisból másolt adatok tárolását. Ha nem rendelkezik Azure SQL Data Warehouseval, tekintse meg a [SQL Data Warehouse létrehozása](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)című témakör utasításait.
* Azure SQL Database: ez az oktatóanyag az Azure SQL Database-ből származó adatok másolását végzi az Adventure Works LT mintaadatok alapján. SQL-adatbázis létrehozásához kövesse az [Azure SQL Database létrehozása](../sql-database/sql-database-get-started-portal.md)című témakör utasításait.
* Azure Storage-fiók: az Azure Storage a tömeges másolási művelet során _átmeneti_ blobként szolgál. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-account-create.md) ismertető cikkben találja.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **erőforrás létrehozása** > **adatok és Analitika** > **Data Factory**:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **új adat-előállító** lapon adja meg az alábbi képen látható mezők értékét:

   ![Új adat-előállító lap](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)

    * **Név**: adjon meg egy globálisan egyedi nevet az Azure-beli adatgyár számára. Ha "az adatfeldolgozó neve \"LoadSQLDWDemo\" nem érhető el" hibaüzenet jelenik meg, adjon meg egy másik nevet az adatelőállító számára. Használhatja például a _**sajátneve**_ **ADFTutorialDataFactory**nevet. Próbálkozzon újra az adatelőállító létrehozásával. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. 
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válassza a **v2**elemet.
    * **Hely**: válassza ki az adatelőállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Refactory által használt adattárak más helyszíneken és régiókban is lehetnek. Ezek az adattárak a következők: Azure Data Lake Store, Azure Storage, Azure SQL Database stb.

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatait a gyárban. Megjelenik a **Data Factory** kezdőlapja, ahogy az a következő képen látható:

   ![Data factory kezdőlap](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse-ba

1. Az **első lépések** lapon válassza ki a **adatok másolása** csempét a adatok másolása eszköz elindításához:

   ![Az Adatok másolása eszköz csempéje](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. A **Tulajdonságok** lapon adja meg a **CopyFromSQLToSQLDW** a **feladat neve** mezőben, majd válassza a **Next (tovább**) gombot:

    ![Tulajdonságok lap](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. A **forrás adattár** oldalon hajtsa végre a következő lépéseket:

    a. kattintson az **+ új kapcsolatok létrehozása**lehetőségre:

    ![Forrásadattár lap](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Válassza ki **Azure SQL Database** a katalógusból, és válassza a **Folytatás**lehetőséget. Az összekötők szűréséhez a keresőmezőbe írja be az "SQL" kifejezést.

    ![Azure SQL-adatbázis kiválasztása](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Az **új társított szolgáltatás** oldalon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, majd adja meg a felhasználónevet és a jelszót. Kattintson a **Kapcsolat tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Befejezés** lehetőséget.

    ![Azure SQL-adatbázis konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Forráshoz társított szolgáltatás kiválasztása](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. A táblázatok **kiválasztásához, amelyből másolni kívánja az adatait, vagy használjon egyéni lekérdezési** oldalt, írja be a **SalesLT** a táblák szűréséhez. A másoláshoz használandó összes tábla használatához válassza az **összes kijelölése** lehetőséget, majd válassza a **Next (tovább**) gombot:

    ![Forrástábla kiválasztása](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. A **cél adattár** oldalon hajtsa végre a következő lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    ![Fogadó adattár lapja](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Válassza ki **Azure SQL Data Warehouse** a katalógusból, majd kattintson a **tovább**gombra.

    ![Azure SQL DW kiválasztása](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Az **új társított szolgáltatás** oldalon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, majd adja meg a felhasználónevet és a jelszót. Kattintson a **Kapcsolat tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Befejezés** lehetőséget.

    ![Az Azure SQL DW konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Válassza ki fogadóként az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Fogadó társított szolgáltatás kiválasztása](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. A **tábla-hozzárendelés** lapon tekintse át a tartalmat, és kattintson a **Tovább gombra**. Megjelenik egy intelligens táblázat-hozzárendelés. A forrástábla a táblák nevei alapján van leképezve a céltábla számára. Ha a forrás tábla nem létezik a célhelyen, Azure Data Factory alapértelmezés szerint ugyanazzal a névvel hozza létre a célhelyet. A forrástábla egy meglévő céltábla számára is képezhető le.

   > [!NOTE]
   > A SQL Data Warehouse fogadó automatikus táblájának létrehozása akkor érvényes, ha SQL Server vagy Azure SQL Database a forrás. Ha más forrás-adattárból másol be egy másikat, előbb létre kell hoznia a sémát a fogadó Azure SQL Data Warehouse az adatok másolásának végrehajtása előtt.

   ![Tábla hozzárendelése oldal](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. A **séma-hozzárendelés** lapon tekintse át a tartalmat, és kattintson a **Tovább gombra**. Az intelligens tábla leképezése az oszlop nevén alapul. Ha lehetővé teszi, hogy Data Factory automatikusan létrehozza a táblákat, az adattípus-konverzió akkor fordulhat elő, ha a forrás-és a célhelyek között inkompatibilitás van. Ha a forrás és a cél oszlop között nem támogatott adattípusú átalakítás van, a megfelelő tábla mellett hibaüzenet jelenik meg.

    ![Séma hozzárendelése oldal](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. A **Beállítások** lapon végezze el a következő lépéseket:

    a. Az **előkészítési beállítások** szakaszban kattintson az **+ új** elemre az átmeneti tárolók új létrehozásához. A tárterület az adatok átmeneti tárolásához használatos, mielőtt a rendszer betölti a SQL Data Warehouset a Base használatával. A másolás befejezése után a rendszer automatikusan kitakarítja az Azure Storage-ban lévő ideiglenes adatkészletet.

    ![Átmeneti beállítás konfigurálása](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Az **új társított szolgáltatás** oldalon válassza ki a Storage-fiókját, és kattintson a **Befejezés gombra**.

    ![Az Azure Storage konfigurálása](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. A **Speciális beállítások** szakaszban válassza a **típus alapértelmezett használata** lehetőséget, majd kattintson a **tovább**gombra.

    ![A bázisterület konfigurálása](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Next (tovább) gombra**:

    ![Összefoglaló lap](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Az **üzembe helyezés lapon**válassza a **figyelő** lehetőséget a folyamat figyeléséhez (feladat):

    ![Üzembe helyezés lap](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop a tevékenység futtatási részleteinek megtekintésére és a folyamat újrafuttatására mutató hivatkozásokat tartalmaz:

    ![Folyamatfuttatások monitorozása](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Ha meg szeretné tekinteni a folyamat futtatásához társított tevékenység-futtatásokat, válassza a **műveletek** oszlop **Megtekintés tevékenység futtatása** hivatkozását. Ha vissza szeretne váltani a folyamat futási nézetére, válassza a felül található **folyamatok** hivatkozást. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenységfuttatások monitorozása](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a tevékenységek figyelése nézet **műveletek** területén található **részletek** hivatkozást. A forrásról a fogadóra másolt adatok mennyiségét, az adatátvitelt, a végrehajtási lépéseket és a megfelelő időtartamot, valamint a használt konfigurációkat is figyelheti:

    ![Tevékenység-futtatási részletek figyelése](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Következő lépések

A következő cikkből megismerheti a Azure SQL Data Warehouse támogatását:

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse-összekötő](connector-azure-sql-data-warehouse.md)
