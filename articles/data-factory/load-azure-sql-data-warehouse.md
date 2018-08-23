---
title: Adatok betöltése az Azure SQL Data Warehouse-bA az Azure Data Factory használatával |} A Microsoft Docs
description: Adatok másolása az Azure SQL Data Warehouse-bA az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 8525dd443e80bb7d67bc48cc007ab1632ee3e611
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42054523"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatok betöltése az Azure SQL Data Warehouse-bA az Azure Data Factory használatával

[Az Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, horizontálisan felskálázható adatbázis, amely nagy mennyiségű relációs és nem relációs adatok feldolgozására alkalmas állapotban van. Az SQL Data Warehouse a nagymértékben párhuzamos feldolgozási (MPP) architektúra, amely nagyvállalati számítási feladatokhoz van optimalizálva épül. Felhőalapú rugalmasság és számítási egymástól függetlenül méretezheti a storage is kínál.

Ismerkedés az Azure SQL Data Warehouse mostantól egyszerűbb, mint valaha Azure Data Factory használatakor. Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás az. A szolgáltatás segítségével egy SQL Data Warehouse-adatok feltöltése a meglévő rendszerről és időt takaríthat meg az elemzési megoldások készítése során.

Az Azure Data Factory az adatok betöltéséhez az Azure SQL Data Warehouse-bA az alábbi előnyöket biztosítja:

* **Könnyű beállítás**: 5-lépés egy intuitív varázslóval telepítheti nem parancsfájl-szükséges.
* **Sokrétű támogatás adattároló**: a helyszíni és felhőalapú adattárak számos beépített támogatása. Részletes listáját, tekintse meg a fenti táblázat [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute keresztül továbbított adatok. A szolgáltatás globális jelenlét biztosítja, hogy az adatok a földrajzi határ sosem hagyja el.
* **A PolyBase használatával páratlan teljesítményt**: adatok áthelyezése az Azure SQL Data Warehouse-bA a Polybase a leghatékonyabb módja a. Az előkészítési blob szolgáltatás használatával nagy terhelés megbízhatóbbak minden típusú adattárakban, többek között az Azure Blob storage és Data Lake Store elérése. (A Polybase támogatja az Azure Blob storage és az Azure Data Lake Store alapértelmezés szerint.) További információkért lásd: [másolási tevékenység](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan használható a Data Factory az adatok másolása eszköz _adatok betöltése az Azure SQL Database az Azure SQL Data Warehouse_. Adatok másolása a más típusú adattárakban hasonló lépéseket követheti.

> [!NOTE]
> További információkért lásd: [másolhat az Azure SQL Data Warehouse-ból az Azure Data Factory használatával](connector-azure-sql-data-warehouse.md).
## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Az Azure SQL Data Warehouse: Az adattárház tárolja az SQL database-ből átmásolt adatokat. Ha nem rendelkezik az Azure SQL Data Warehouse, tekintse meg a következő témakör utasításait [hozzon létre egy SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Az Azure SQL Database: Ebben az oktatóanyagban adatokat másol egy Azure SQL database, az Adventure Works LT mintaadatokat. Létrehozhat egy SQL-adatbázis található utasításokat követve [hozzon létre egy Azure SQL database](../sql-database/sql-database-get-started-portal.md). 
* Az Azure storage-fiók: az Azure Storage szolgál a _átmeneti_ blob a tömeges másolási műveletben. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikkben találja.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza ki a **új** > **adatok + analitika** > **adat-előállító**: 
   
   ![Új adat-előállító létrehozása](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
1. Az a **új adat-előállító** lap, adja meg a mezőket az alábbi képen látható:
      
   ![Új adat-előállító lap](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Név**: Adja meg az Azure data factory egy globálisan egyedi nevet. Ha a hibaüzenetet kapja "adat-előállító nevét \"LoadSQLDWDemo\" nem érhető el" adja meg a data Factory egy másik nevet. Például használhatja a név  _**sajátneve**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállító. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki az Azure-előfizetés, amelyben az adat-előállító létrehozásához. 
    * **Erőforráscsoport**: a legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válassza ki a **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válasszon **V2**.
    * **Hely**: válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak más helyeken / régiókban is lehetnek. Ezek adattárak közé tartozik az Azure Data Lake Store, az Azure Storage, Azure SQL Database és így tovább.

1. Kattintson a **Létrehozás** gombra.
1. Létrehozás befejezése után nyissa meg az adat-előállítóhoz. Megjelenik a **adat-előállító** kezdőlapja a következő képen látható módon:
   
   ![Data factory kezdőlap](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Válassza ki a **létrehozás és Monitorozás** csempére kattintva indítsa el az adatintegrációs alkalmazás külön lapon.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse-ba

1. Az a **Ismerkedés** lapon válassza ki a **adatok másolása** csempére, hogy az adatok másolása eszköz elindításához:

   ![Az Adatok másolása eszköz csempéje](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Az a **tulajdonságok** adja meg azokat **CopyFromSQLToSQLDW** számára a **feladat neve** mezőt, és válassza **tovább**:

    ![Tulajdonságok lap](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Az a **forrásadattár** lapon, a következő lépéseket:

    a. Kattintson a **+ új kapcsolat létrehozása**:

    ![Forrásadattár lap](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Válassza ki **Azure SQL Database** a katalógusban, és válassza a **Folytatás**. Az összekötők a keresőmezőbe írja be "az SQL".

    ![Azure SQL-adatbázis kiválasztása](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Az a **új társított szolgáltatás** lapon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, és adja meg a felhasználónevet és a passworkd. Kattintson a **kapcsolat tesztelése** ellenőrzése a beállításokat, majd válassza ki **Befejezés**.
   
    ![Azure SQL-adatbázis konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Forráshoz társított szolgáltatás kiválasztása](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. Az a **másolja az adatokat, vagy egyéni lekérdezés használata tartalmazó táblák kiválasztása** lap, adja meg **SalesLT** szűrése a táblákat. Válassza ki a **(az összes kijelölése)** a példány összes tábla használandó mezőt, és válassza **tovább**: 

    ![Válassza ki a forrástáblákban](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Az a **célként megadott adattárba** lapon, a következő lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    ![Fogadó lap](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Válassza ki **Azure SQL Data Warehouse** a katalógusban, és válassza a **tovább**.

    ![Válassza ki az Azure SQL dw-vel](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Az a **új társított szolgáltatás** lapon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, és adja meg a felhasználónevet és a passworkd. Kattintson a **kapcsolat tesztelése** ellenőrzése a beállításokat, majd válassza ki **Befejezés**.
   
    ![Az Azure SQL DW konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Válassza ki fogadóként az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Fogadó társított szolgáltatás kiválasztása](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Az a **tábla hozzárendelése** lapon tekintse át a tartalmat, és válassza ki **tovább**. Egy intelligens táblaleképezés jeleníti meg. A forrástáblákból a céltáblákba táblázat neve alapján vannak leképezve. Ha a forrástábla nem létezik a célhelyen, Azure Data Factory céltáblában alapértelmezés szerint ugyanazzal a névvel létrehoz. A meglévő céloldali táblához is leképezheti a forrástábla. 

   > [!NOTE]
   > Automatikus táblázat létrehozása az SQL Data Warehouse fogadó vonatkozik, ha SQL Server vagy az Azure SQL Database a forrás. Ha egy másik forrásadattárból másolt adatok szüksége előzetes létrehozása a fogadó Azure SQL Data Warehouse a sémát az adatmásolás végrehajtása előtt.

   ![Tábla hozzárendelése oldal](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Az a **séma-hozzárendelés** lapon tekintse át a tartalmat, és válassza ki **tovább**. Az intelligens tábla hozzárendelése az oszlop neve alapján. Ha engedélyezi a Data Factory automatikusan létrehozza a táblákat, írja be az adatkonvertálás előfordulhatnak, ha a forrás- és tárolók között inkompatibilitás merül fel. Ha egy nem támogatott típus a forrás és cél oszlop közötti átalakítás, egy hibaüzenetet a megfelelő tábla mellett láthatja.

    ![Séma hozzárendelése oldal](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Az a **beállítások** lapon, a következő lépéseket:

    a. A **előkészítési beállítások** területén kattintson **+ új** új átmeneti tárolóba. A storage használható az adatok átmeneti megelőzően az SQL Data Warehouse-bA a PolyBase használatával. A Másolás befejezése után az ideiglenes adatokat az Azure Storage automatikusan törlődnek. 

    ![Átmeneti konfigurálása](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Az a **új társított szolgáltatás** lapon válassza ki a tárfiókját, és válassza ki **Befejezés**.
   
    ![Az Azure Storage konfigurálása](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. Az a **speciális beállítások** területen törölje a **típus alapértelmezett** lehetőséget, majd **tovább**.

    ![A PolyBase konfigurálása](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Az a **összefoglalás** lapon tekintse át a beállításokat, és válassza ki **tovább**:

    ![Összefoglaló lap](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Az a **üzembe helyezés lap**válassza **figyelő** a folyamat (feladat) figyeléséhez:

    ![Üzembe helyezés lap](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop tartalmazza a tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások: 

    ![Folyamatfuttatások monitorozása](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. A folyamat futásához társított tevékenységfuttatások megtekintéséhez jelölje ki a **Tevékenységfuttatások megtekintése** hivatkozásra a **műveletek** oszlop. Válassza ki, váltson vissza a folyamatfuttatások nézetre a **folyamatok** a fenti hivatkozásra. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Figyelheti a minden egyes másolási tevékenység-végrehajtási részleteit, válassza ki a **részletei** mellett kapcsolni **műveletek** a figyelési nézet tevékenység. Adatait is figyelheti, például a fogadó, a fájlmegosztásra, a megfelelő időtartamot a végrehajtási lépések a forráskiszolgálóról másolt adatok mennyiségét, és a használt konfigurációk:

    ![A figyelő tevékenységfuttatás részletei](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>További lépések

Lépjen az Azure SQL Data Warehouse-támogatással kapcsolatos további információt a következő cikket: 

> [!div class="nextstepaction"]
>[Az Azure SQL Data Warehouse-összekötő](connector-azure-sql-data-warehouse.md)
