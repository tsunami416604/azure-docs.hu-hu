---
title: "Adatok betöltése az Azure SQL Data Warehouse Azure Data Factory használatával |} Microsoft Docs"
description: "Adatok másolása az Azure SQL Data Warehouse az Azure Data Factory használatával"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: eec6eeb3419c5f5f4c8d22398051f7cf057ac980
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatok betöltése az Azure SQL Data Warehouse Azure Data Factory használatával

[Az SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, horizontálisan felskálázható adatbázis, amely képes a nagy mennyiségű relációs és nem relációs adatok. Az SQL Data Warehouse a nagymértékben párhuzamos feldolgozási (MPP) architektúra, amely a vállalati adatok adatraktár munkaterhelések optimalizált épül. Felhő rugalmasság méretezni a tárolást, és egymástól függetlenül számítási rugalmasságot biztosít.

Ismerkedés az Azure SQL Data Warehouse mostantól egyszerűbb, mint minden eddiginél Azure Data Factory használatakor. Az Azure Data Factory egy olyan felhőalapú teljes körűen felügyelt adatok integrációs szolgáltatás. A szolgáltatás segítségével egy SQL Data Warehouse adatok feltöltése a meglévő rendszerről és időt takaríthat meg az elemzési megoldásokat felépítése közben.

Az Azure Data Factory az adatok betöltése az Azure SQL Data Warehouse a következő előnyöket nyújtja:

* **Egyszerűen állíthat be**: egy egyszerűen elsajátítható 5. lépés varázsló nem parancsfájl-szükséges.
* **Gazdag adattároló támogatási**: a helyszíni és felhőalapú adattároló széles skáláját beépített támogatása. Részletes listájáért lásd: a tábla [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute keresztül továbbított adatok. A globális szolgáltatás meglétének biztosítja, hogy az adatok sosem hagyja el a földrajzi határ.
* **A PolyBase használatával unparalleled teljesítmény**: adatok áthelyezése az Azure SQL Data Warehouse Polybase a legegyszerűbb módja a. Az átmeneti blob szolgáltatás segítségével minden típusú adattároló, beleértve az Azure Blob storage és a Data Lake Store nagy terhelés sebesség elérése. (Polybase támogatja az Azure Blob storage és az Azure Data Lake Store alapértelmezés szerint.) További információkért lásd: [a másolási tevékenység](copy-activity-performance.md).

A cikkből megtudhatja, hogyan használható a Data Factory adatok másolása eszköz _adatok betöltése az Azure SQL Database az Azure SQL Data Warehouse_. Akkor is hasonló lépésekkel lehet adatok másolása az egyéb típusú adattároló.

> [!NOTE]
> További információkért lásd: [másolása Azure Data Factory használatával adatok vagy az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
>
> Ez a cikk az Azure Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használata lásd [másolási tevékenység során az Azure Data Factoryben az 1-es](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Az SQL Data Warehouse: Az adatraktár adatait tartalmazza, amely az SQL-adatbázis átmásolva keresztül. Ha nem rendelkezik Azure SQL Data Warehouse, lásd az útmutatást [SQL Data Warehouse létrehozása](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Az Azure SQL Database: Ez az oktatóanyag az Adventure Works LT mintaadatokkal Azure SQL-adatbázis adatok másolja át. A cikk utasításait követve létrehozhat SQL-adatbázis [hozzon létre egy Azure SQL-adatbázis](../sql-database/sql-database-get-started-portal.md). 
* Azure storage-fiókok: az Azure Storage használatos a _átmeneti_ blob a tömeges másolási műveletnél. Ha egy Azure storage-fiók nem rendelkezik, tekintse meg a utasításait [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza **új** > **adatok + analitika** > **adat-előállító**: 
   
   ![Hozzon létre egy új adat-előállító](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** lapján adja meg a mezőket, amelyeknek az alábbi ábrán látható:
      
   ![Új adat-előállító lap](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Név**: Adja meg az az Azure data factory globálisan egyedi nevet. Ha a hibaüzenet "adat-előállító \"LoadSQLDWDemo\" nem áll rendelkezésre," írjon be egy másik nevet az adat-előállítóban. Például használhatja a név  _**saját_név**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállítóban. Adat-előállító összetevők elnevezési szabályait, lásd: [Data Factory elnevezési szabályok](naming-rules.md).
    * **Előfizetés**: válassza ki az Azure-előfizetéshez használandó adat-előállító létrehozása. 
    * **Erőforráscsoport**: a legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válassza ki a **hozzon létre új** lehetőséget, majd írja be az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válasszon **V2 (előzetes verzió)**.
    * **Hely**: Jelölje ki az adat-előállítóban helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adattároló adat-előállító által használt más helyek és a régióban lehet. Ezekkel az áruházakkal adatok közé tartoznak a Azure Data Lake Store, Azure Storage, Azure SQL Database és így tovább.

3. Kattintson a **Létrehozás** gombra.
4. Létrehozásának befejezése után lépjen a data factory. Megjelenik a **adat-előállító** kezdőlapja a következő ábrán látható módon:
   
   ![Data factory kezdőlap](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Válassza ki a **Szerző & figyelő** csempe az adatok integrációs alkalmazást egy külön lapján.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse

1. Az a **Ismerkedés** lapon jelölje be a **adatok másolása** csempe az adatok másolása eszköz:

   ![Az Adatok másolása eszköz csempéje](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. Az a **tulajdonságok** adja meg azokat **CopyFromSQLToSQLDW** a a **feladatnév** mezőjét, majd válassza **következő**:

    ![Tulajdonságok lap](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. Az a **forrás adattár** lapon jelölje be **Azure SQL Database**, és válassza ki **következő**:

    ![Forrásadattár lap](./media/load-azure-sql-data-warehouse/specify-source.png)
4. **Az Azure SQL Database megadása** oldalon tegye a következőket: 
   1. A **Kiszolgáló neve** mezőnél válassza ki az Azure SQL Server-kiszolgálót.
   2. Az **Adatbázis neve** mezőnél válassza ki az Azure SQL Database-adatbázist.
   3. A **Felhasználónév** mezőben adja meg a felhasználó nevét.
   4. Adja meg a felhasználó jelszavát **jelszó**.
   5. Kattintson a **Tovább** gombra.
   
   ![Adja meg az Azure SQL-adatbázis](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. Az a **válassza ki azokat a táblákat, amelynek be kell másolnia az adatokat, vagy használjon egy egyéni lekérdezést** lapján adja meg **SalesLT** szűrése a táblázatokat. Válassza ki a **(válassza ki az összes)** használja az összes tábla a példányra a mezőbe, majd válassza ki **következő**: 

    ![Válassza ki a forrástábla](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. Az a **adatok céltár** lapon jelölje be **Azure SQL Data Warehouse**, és válassza ki **következő**:

    ![Céladattár lap](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. Az a **adja meg az Azure SQL Data Warehouse** lapon esetén tegye a következőket: 

   1. A **Kiszolgáló neve** mezőnél válassza ki az Azure SQL Server-kiszolgálót.
   2. Válassza ki az Azure SQL Data Warehouse esetében a **adatbázisnév**.
   3. A **Felhasználónév** mezőben adja meg a felhasználó nevét.
   4. Adja meg a felhasználó jelszavát **jelszó**.
   5. Kattintson a **Tovább** gombra.
   
   ![Adja meg az Azure SQL-adatraktár](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. Az a **táblaleképezés** lapon tekintse át a tartalmat, és válassza ki **következő**. Egy intelligens táblaleképezés jeleníti meg. A forrástáblákból a céltábla táblázat neve alapján van leképezve. Ha a forrástábla nem létezik a cél, Azure Data Factory táblát alapértelmezés szerint ugyanazzal a névvel létrehoz. Egy meglévő céltábla forrástábla is leképezheti. 

   > [!NOTE]
   > Automatikus tábla létrehozása az SQL Data Warehouse fogadó vonatkozik, ha SQL Server vagy az Azure SQL Database a forrás. Adatok másolása másik forrás-adattár, ha az adatok másolását végrehajtása előtt a sémának a fogadó Azure SQL Data Warehouse előzetes létrehozásához szüksége.

   ![Tábla hozzárendelése oldal](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. Az a **séma-hozzárendelése** lapon tekintse át a tartalmat, és válassza ki **következő**. Az intelligens táblaleképezés oszlopnév alapul. Ha engedélyezi a Data Factory automatikusan létrehozza a táblákat, adattípus konvertálása akkor fordulhat elő, ha nincsenek inkompatibilitási problémák között a forrás és cél tárolja. Ha a forrás és cél oszlopok közötti típusa nem támogatott adatok átalakításnál, lásd: hibaüzenetet a megfelelő táblanevek mellett látható.

    ![Séma hozzárendelése oldal](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. Az a **beállítások** lapon, válassza ki az Azure storage-fiókot a **tárfióknév** legördülő listából. A fiók szolgál az adatok átmeneti megelőzően az SQL Data Warehouse PolyBase használatával. A másolás után, a közbenső adatainak Azure Storage a program automatikusan kiüríti. A **speciális beállítások**, törölje a **típus alapértelmezett** lehetőséget:

    ![Beállítások lap](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. Az a **összegzés** lapon tekintse át a beállításokat, és válassza ki **következő**:

    ![Összefoglaló lap](./media/load-azure-sql-data-warehouse/summary-page.png)
13. Az a **telepítési lap**, jelölje be **figyelő** figyelheti a folyamat (feladat):

    ![Üzembe helyezés lap](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop ismerteti tevékenységfuttatási részletek megtekintése, és futtassa újra a folyamatot: 

    ![Folyamatfuttatások monitorozása](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Válassza ki, ha tevékenység fut, amely társul a folyamat, futtassa a **nézet tevékenység fut** hivatkozásra a **műveletek** oszlop. 10 másolási tevékenység az adatcsatorna és az egyes tevékenységek másolja át az adatok egy tábla. Váltson vissza az a folyamat futó nézet, válassza ki a **folyamatok** tetején. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Minden egyes másolási tevékenység végrehajtási adatainak figyelésére, válassza ki a **részletek** hivatkozásra **műveletek** figyelési nézet tevékenységben. Részletes adatai figyelhetők adatokat fogadó, adatátvitelt, megfelelő időtartamú támadáséhoz hasonlók a forráskiszolgálóról másolt, és konfigurációkat használják:

    ![A figyelő tevékenységfuttatási részletei](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>További lépések

Előzetes tudnivalók Azure SQL Data Warehouse támogatásáról a következő cikkben: 

> [!div class="nextstepaction"]
>[Az Azure SQL Data Warehouse-összekötő](connector-azure-sql-data-warehouse.md)