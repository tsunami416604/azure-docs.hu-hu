---
title: Adatok betöltése az Azure SQL Data Warehouse-ba
description: Adatok másolása az Azure SQL Data Warehouse-ba az Azure Data Factory használatával
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
ms.openlocfilehash: dc6d8c9da749e7ee60713f4357dc6d46051d89f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131278"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatok betöltése az SQL Data Warehouse-ba Azure Data Factory használatával

[Az Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, kibővített adatbázis, amely képes nagy mennyiségű adat feldolgozására, relációs és nem relációs. Az SQL Data Warehouse a nagyvállalati adattárház-munkaterhelésekre optimalizált masszívan párhuzamos feldolgozási (MPP) architektúrára épül. Felhőrugalmasságot kínál a tárhely és az önálló számítási kapacitás méretezésének rugalmasságával.

Az Azure SQL Data Warehouse használatának első lépései mostantól egyszerűbbek, mint valaha, amikor az Azure Data Factoryt használja. Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatás segítségével feltöltheti az SQL Data Warehouse-t a meglévő rendszerből származó adatokkal, és időt takaríthat meg az elemzési megoldások létrehozásakor.

Az Azure Data Factory a következő előnyöket kínálja az Azure SQL Data Warehouse-ba való adatbetöltéshez:

* **Könnyen beállítható:** Intuitív, 5 lépéses varázsló parancsfájlok nélkül.
* **Bővített adattár-támogatás:** Beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez. A részletes listát a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)táblázatában található.
* **Biztonságos és megfelelő**: Az adatok átvitele HTTPS-en vagy ExpressRoute-on keresztül történik. A globális szolgáltatásjelenlét biztosítja, hogy az adatok soha nem hagyja el a földrajzi határt.
* **Páratlan teljesítmény a PolyBase használatával:** A Polybase a leghatékonyabb módja az adatok Azure SQL Data Warehouse-ba való áthelyezésének. Az átmeneti blob funkcióval nagy terhelési sebességet érhet el minden típusú adattárból, beleértve az Azure Blob storage-ot és a Data Lake Store-t. (A Polybase alapértelmezés szerint támogatja az Azure Blob storage-t és az Azure Data Lake Store-t.) További információt a [Tevékenységteljesítmény másolása](copy-activity-performance.md)című témakörben talál.

Ez a cikk bemutatja, hogyan tölthet _be adatokat az Azure SQL Database-ből az Azure SQL Data Warehouse-ba_a Data Factory Copy Data eszközzel. Hasonló lépéseket követve más típusú adattárakból is másolhat adatokat.

> [!NOTE]
> További információ: [Adatok másolása az Azure SQL Data Warehouse-ba vagy onnan az Azure Data Factory használatával.](connector-azure-sql-data-warehouse.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Azure SQL Data Warehouse: Az adattárház az SQL-adatbázisból átmásolt adatokat tartalmazza. Ha nem rendelkezik Azure SQL Data Warehouse-val, olvassa el az [SQL Data Warehouse létrehozása](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)című témakörutasításait.
* Azure SQL-adatbázis: Ez az oktatóanyag adatokat másol egy Azure SQL-adatbázisból a Adventure Works LT mintaadatokkal. SQL-adatbázist az [Azure SQL-adatbázis létrehozása](../sql-database/sql-database-get-started-portal.md)című részben található utasításokat követve hozhat létre.
* Azure storage-fiók: Az Azure Storage átmeneti _blobként_ használatos a tömeges másolási műveletben. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-account-create.md) ismertető cikkben találja.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az > **Erőforrás-adatok létrehozása + Analytics** > **Data Factory** **lehetőséget:**

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lapon adja meg az alábbi képen látható mezők értékeit:

   ![Új adat-előállító lap](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)

    * **Név**: Adjon meg egy globálisan egyedi nevet az Azure-adat-előállító. Ha a következő hibaüzenet jelenik meg: "Data factory name LoadSQLDWDemo is not available" (A Data factory name LoadSQLDWDemo(Data factory name LoadSQLDWDemo) (Data factory name LoadSQLDWDemo) (Data factory name LoadSQLDWDemo) (Data factory name LoadSQLDWDemo) (Data factory name \"LoadSQLDWDemo)\" (Data Például használhatja a _**nevét a nevét**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállítót. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetést, amelyben létre szeretné hozni az adat-előállító. 
    * **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza a **V2**lehetőséget.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállító által használt adattárak más helyeken és régiókban is lehetnek. Ezek az adattárak közé tartozik az Azure Data Lake Store, az Azure Storage, az Azure SQL Database és így tovább.

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatgyárat. A **Data Factory** kezdőlapja az alábbi képen látható módon jelenik meg:

   ![Data factory kezdőlap](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse-ba

1. Az **Első lépések** lapon válassza az **Adatok másolása** csempét az Adatok másolása eszköz elindításához:

   ![Az Adatok másolása eszköz csempéje](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. A **Tulajdonságok** lapon adja meg a **CopyFromSQLToSQLDW** értéket a **Tevékenység neve** mezőhöz, és válassza a **Tovább**gombot:

    ![Tulajdonságok lap](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. A **Forrás adattár** lapon hajtsa végre a következő lépéseket:

    a. kattintson **a + Új kapcsolat létrehozása**:

    ![Forrásadattár lap](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Válassza az **Azure SQL Database elemet** a gyűjteményből, és válassza a **Folytatás**gombot. Az összekötők szűréséhez írja be az "SQL" szót a keresőmezőbe.

    ![Azure SQL-adatbázis kiválasztása](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Az **Új csatolt szolgáltatás** lapon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, és adja meg a felhasználónevet és a jelszót. Kattintson a **Kapcsolat tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Befejezés** lehetőséget.

    ![Azure SQL-adatbázis konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Forráshoz társított szolgáltatás kiválasztása](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. A **Táblák kiválasztása, amelyekből az adatokat másolni szeretné, vagy egyéni lekérdezési** lapot használ, írja be a **SalesLT** értéket a táblák szűréséhez. A másoláshoz tartozó összes tábla használatához válassza a **(Az összes kijelölése)** mezőt, majd válassza a **Tovább**gombot:

    ![Forrástáblák kijelölése](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. A **Cél adattár** lapon hajtsa végre a következő lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    ![Az adattár lap elsüllyesztése](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Válassza ki az **Azure SQL Data Warehouse** elemet a gyűjteményből, és válassza a **Tovább**gombot.

    ![Válassza az Azure SQL DW-t](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Az **Új csatolt szolgáltatás** lapon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, és adja meg a felhasználónevet és a jelszót. Kattintson a **Kapcsolat tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Befejezés** lehetőséget.

    ![Az Azure SQL DW konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Válassza ki fogadóként az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Fogadó társított szolgáltatás kiválasztása](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. A **Táblázatleképezés** lapon tekintse át a tartalmat, és válassza a **Tovább**gombot. Intelligens táblaleképezés jelenik meg. A forrástáblák a táblanevek alapján vannak leképezve a céltáblákra. Ha a célban nem létezik forrástábla, az Azure Data Factory alapértelmezés szerint létrehoz egy azonos nevű céltáblát. Forrástáblát egy meglévő céltáblához is leképezhet.

   > [!NOTE]
   > Az SQL Data Warehouse-fogadó automatikus táblalétrehozása akkor érvényes, ha az SQL Server vagy az Azure SQL Database a forrás. Ha adatokat másol egy másik forrás-adattárból, elő kell hoznia a sémát a fogadóban az Azure SQL Data Warehouse az adatmásolás végrehajtása előtt.

   ![Tábla hozzárendelése oldal](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. A **Sémaleképezés** lapon tekintse át a tartalmat, és válassza a **Tovább**gombot. Az intelligens táblaleképezés az oszlop névén alapul. Ha hagyja, hogy a Data Factory automatikusan létrehozza a táblákat, adattípus-átalakítás ra konfektúra léphet fel, ha inkompatibilitás van a forrás- és a céltárolók között. Ha a forrás- és a céloszlop között nem támogatott adattípus-átalakítás történik, a megfelelő tábla mellett hibaüzenet jelenik meg.

    ![Séma hozzárendelése oldal](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. A **Beállítások** lapon hajtsa végre az alábbi lépéseket:

    a. Az **Előkészítési beállítások csoportban** kattintson a **+ Új** az új átmeneti tárolóhoz elemre. A tároló az adatok előkészítéséhez használatos, mielőtt azok betöltődnek az SQL Data Warehouse-ba a PolyBase használatával. A másolás befejezése után az Azure Storage-ban a köztes adatok automatikusan törlődnek.

    ![Előkészítés konfigurálása](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Az **Új csatolt szolgáltatás** lapon jelölje ki a tárfiókot, és válassza a **Befejezés gombot.**

    ![Az Azure Storage konfigurálása](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. A **Speciális beállítások** csoportban törölje a jelet a **Típus alapértelmezett használata** jelölőnégyzetből, majd válassza a **Tovább**gombot.

    ![A PolyBase konfigurálása](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Az **Összegzés** lapon tekintse át a beállításokat, és válassza a **Tovább**gombot:

    ![Összefoglaló lap](./media/load-azure-sql-data-warehouse/summary-page.png)
1. A **Telepítés lapon**válassza a **Figyelő** lehetőséget a folyamat (feladat) figyeléséhez:

    ![Üzembe helyezés lap](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **Műveletek** oszlop hivatkozásokat tartalmaz a tevékenységfuttatás részleteinek megtekintéséhez és a folyamat újbóli futtatásához:

    ![Folyamatfuttatások monitorozása](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez válassza a Műveletek **oszlop** **Tevékenységfuttatások megtekintése** hivatkozását. Ha vissza szeretne váltani a folyamatfuttatási nézetre, válassza a **folyamatkapcsolatok** közötti kapcsolatot a tetején. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenységfuttatások monitorozása](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a **Részletek hivatkozást** a **műveletek** a tevékenységfigyelési nézetben. Figyelheti a részleteket, például a forrásból a fogadóba másolt adatok mennyiségét, az adatátviteli áteresztőát, a megfelelő időtartamú végrehajtási lépéseket és a használt konfigurációkat:

    ![Tevékenységfuttatás részleteinek figyelése](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>További lépések

Az Azure SQL Data Warehouse támogatásáról az alábbi cikkhez továbbkell lépnie:

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse összekötő](connector-azure-sql-data-warehouse.md)
