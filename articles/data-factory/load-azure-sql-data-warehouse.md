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
ms.date: 04/16/2020
ms.openlocfilehash: 1a764f392402acf9aa405468470d0fb6f680d755
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461108"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatok betöltése az SQL Data Warehouse-ba Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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

2. Az **Új adatgyár** lapon adja meg a következő elemek értékeit:

    * **Név**: Írja be *a LoadSQLDWDemo* nevet a névhez. Az adat-előállító nevének *globálisan egyedinek kell lennie. Ha a "Data factory name 'LoadSQLDWDemo" (Data factory name'LoadSQLDWDemo' (Data factory name'loadSQLDWDemo' is not available) hibaüzenet jelenik meg, adjon meg egy másik nevet az adatgyárnak. Például használhatja a _**nevét a nevét**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállítót. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetést, amelyben létre szeretné hozni az adat-előállító. 
    * **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza a **V2**lehetőséget.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállító által használt adattárak más helyeken és régiókban is lehetnek. Ezek az adattárak közé tartozik az Azure Data Lake Store, az Azure Storage, az Azure SQL Database és így tovább.

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatgyárat. A **Data Factory** kezdőlapja az alábbi képen látható módon jelenik meg:

   ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse-ba

1. Az Adatok másolása eszköz elindításához az **Első lépések** oldalon kattintson az **Adatok másolása** csempére.

1. A **Tulajdonságok** lapon adja meg a **CopyFromSQLToSQLDW** értéket a **Tevékenység neve** mezőhöz, és válassza a **Tovább**gombot.

    ![Tulajdonságok lap](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. A **Forrás adattár** lapon hajtsa végre a következő lépéseket:
    >[!TIP]
    >Ebben az oktatóanyagban az *SQL-hitelesítést* használja a forrás-adattár hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat:*egyszerű szolgáltatás* és *felügyelt identitás,* ha szükséges. A részleteket a [cikk](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) megfelelő szakaszaiban találja.
    >Az adattárak titkos kulcsainak biztonságos tárolásához is ajánlott egy Azure Key Vault használata. A részletes illusztrációkat ebben a [cikkben](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) olvashatja.

    a. kattintson **a + Új kapcsolat létrehozása gombra.**

    b. Válassza az **Azure SQL Database elemet** a gyűjteményből, és válassza a **Folytatás**gombot. Az összekötők szűréséhez írja be az "SQL" szót a keresőmezőbe.

    ![Azure SQL-adatbázis kiválasztása](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Az **Új csatolt szolgáltatás** lapon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, és adja meg a felhasználónevet és a jelszót. A beállítások érvényesítéséhez kattintson a **Kapcsolat tesztelése** gombra, majd a **Létrehozás gombra.**

    ![Azure SQL-adatbázis konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

1. A **Táblák kiválasztása, amelyekből az adatokat másolni szeretné, vagy egyéni lekérdezési** lapot használ, írja be a **SalesLT** értéket a táblák szűréséhez. A másoláshoz tartozó összes tábla használatához válassza a **(Az összes kijelölése)** mezőt, majd kattintson a **Tovább**gombra.

    ![Forrástáblák kijelölése](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. A **Szűrő alkalmazása** lapon adja meg a beállításokat, vagy válassza a **Tovább**gombot.

1. A **Cél adattár** lapon hajtsa végre a következő lépéseket:
    >[!TIP]
    >Ebben az oktatóanyagban az *SQL-hitelesítést* használja a céladattár hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat:*egyszerű szolgáltatás* és *felügyelt identitás,* ha szükséges. A részleteket a [cikk](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#linked-service-properties) megfelelő szakaszaiban találja.
    >Az adattárak titkos kulcsainak biztonságos tárolásához is ajánlott egy Azure Key Vault használata. A részletes illusztrációkat ebben a [cikkben](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) olvashatja.

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    b. Válassza ki az **Azure Synapse Analytics (korábban SQL DW)** a katalógusban, és válassza a **Folytatás**lehetőséget. Az összekötők szűréséhez írja be az "SQL" szót a keresőmezőbe.

    ![Válassza az Azure SQL DW-t](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Az **Új csatolt szolgáltatás** lapon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, és adja meg a felhasználónevet és a jelszót. A beállítások érvényesítéséhez kattintson a **Kapcsolat tesztelése** gombra, majd a **Létrehozás gombra.**

    ![Az Azure SQL DW konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Válassza ki fogadóként az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

1. A **Táblázatleképezés** lapon tekintse át a tartalmat, és válassza a **Tovább**gombot. Intelligens táblaleképezés jelenik meg. A forrástáblák a táblanevek alapján vannak leképezve a céltáblákra. Ha a célban nem létezik forrástábla, az Azure Data Factory alapértelmezés szerint létrehoz egy azonos nevű céltáblát. Forrástáblát egy meglévő céltáblához is leképezhet.

   > [!NOTE]
   > Az SQL Data Warehouse-fogadó automatikus táblalétrehozása akkor érvényes, ha az SQL Server vagy az Azure SQL Database a forrás. Ha adatokat másol egy másik forrás-adattárból, elő kell hoznia a sémát a fogadóban az Azure SQL Data Warehouse az adatmásolás végrehajtása előtt.

   ![Tábla hozzárendelése oldal](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Az **Oszlopleképezés** lapon tekintse át a tartalmat, és válassza a **Tovább**gombot. Az intelligens táblaleképezés az oszlop névén alapul. Ha hagyja, hogy a Data Factory automatikusan létrehozza a táblákat, adattípus-átalakítás ra konfektúra léphet fel, ha inkompatibilitás van a forrás- és a céltárolók között. Ha a forrás- és a céloszlop között nem támogatott adattípus-átalakítás történik, a megfelelő tábla mellett hibaüzenet jelenik meg.

    ![Oszlopleképezési lap](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. A **Beállítások** lapon hajtsa végre az alábbi lépéseket:

    a. Az **Előkészítési beállítások csoportban** kattintson a **+ Új** az új átmeneti tárolóhoz elemre. A tároló az adatok előkészítéséhez használatos, mielőtt azok betöltődnek az SQL Data Warehouse-ba a PolyBase használatával. A másolás befejezése után az Azure Blob Storage-ban a köztes adatok automatikusan törlődnek.

    b. Az **Új csatolt szolgáltatás** lapon jelölje ki a tárfiókot, és válassza a **Létrehozás** lehetőséget a csatolt szolgáltatás üzembe helyezéséhez.

    c. A **Speciális beállítások** csoportban törölje a jelet a **Típus alapértelmezett használata** jelölőnégyzetből, majd válassza a **Tovább**gombot.

    ![A PolyBase konfigurálása](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Az **Összegzés** lapon tekintse át a beállításokat, és válassza a **Tovább gombot.**

    ![Összefoglaló lap](./media/load-azure-sql-data-warehouse/summary-page.png)
1. A **Központi telepítés lapon**válassza a **Figyelő** lehetőséget a folyamat (feladat) figyeléséhez.

1. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. Ha a folyamat sikeresen befejeződött, válassza a **CopyFromSQLToSQLDW** kapcsolatot a **PIPELINE NAME** oszlopban a tevékenységfuttatás részleteinek megtekintéséhez és a folyamat újbóli futtatásához.

    [![Folyamatfuttatások monitorozása](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)
1. Ha vissza szeretne váltani a folyamatfuttatási nézetre, válassza a minden **folyamat futtatása** hivatkozást a tetején. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenységfuttatások monitorozása](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a **Részletek** hivatkozást (szemüveg ikon) a **TEVÉKENYSÉG NEVE** csoportban a tevékenységfuttatások nézetben. Figyelheti a részleteket, például a forrásból a fogadóba másolt adatok mennyiségét, az adatátviteli áteresztőképest, a megfelelő időtartamú végrehajtási lépéseket és a használt konfigurációkat.
    ![Tevékenységfuttatás részleteinek figyelése](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Tevékenységfuttatás részleteinek figyelése](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>További lépések

Az Azure SQL Data Warehouse támogatásáról az alábbi cikkhez továbbkell lépnie:

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse összekötő](connector-azure-sql-data-warehouse.md)
