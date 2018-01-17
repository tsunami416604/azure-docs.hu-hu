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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: cf960ac827db94b9a43361288b1d0863dac86555
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>Adatok betöltése az Azure SQL Data Warehouse Azure Data Factory használatával

[Az SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy olyan felhőalapú, horizontálisan felskálázható adatbázis képes a nagy mennyiségű relációs és nem relációs adatok.  Nagymértékben párhuzamos feldolgozási (MPP) architektúrára épülő SQL Data Warehouse optimalizált vállalati adatok adatraktár munkaterhelések.  Felhő rugalmasság méretezni a tárolást, és egymástól függetlenül számítási rugalmasságot biztosít.

Ismerkedés az Azure SQL Data Warehouse mostantól egyszerűbb, mint minden eddiginél használatával **Azure Data Factory**.  Az Azure Data Factory egy teljes körűen felügyelt felhőalapú integrációs szolgáltatás, amely feltölti az SQL Data Warehouse az adatokat a rendszer, és értékes amivel időt takarít meg az SQL Data Warehouse értékelése és felépítése az elemzés közben használható megoldások. Az alábbiakban az adatok betöltése az Azure SQL Data Warehouse Azure Data Factory használatával legfontosabb előnyei:

* **Egyszerűen állíthat be**: 5. lépés intuitív varázsló nem parancsfájl-szükséges.
* **Gazdag adattároló támogatási**: széles választékának a helyszíni és felhőalapú adattároló beépített támogatása, tekintse meg a részletes listát [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute keresztül továbbított adatok, és a globális szolgáltatás meglétének biztosítja az adatok sosem hagyja el a földrajzi határ
* **A PolyBase használatával unparalleled teljesítmény**: adatok áthelyezése az Azure SQL Data Warehouse Polybase használatával a leghatékonyabb módja a. Az átmeneti tárolási blob szolgáltatást használja, minden típusú adattároló mellett az Azure Blob storage és a Data Lake Store, amely támogatja a Polybase az alapértelmezés szerint a nagy terhelés sebességű érhet el. További részletek a [a másolási tevékenység](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan használ az adatok a Data Factory másolása eszközzel **adatok betöltése az Azure SQL Database az Azure SQL Data Warehouse**. Adatok másolása egyéb típusú adattároló hasonló lépések követésével.

> [!NOTE]
>  Általános képességeivel kapcsolatos információk a Data Factory az adatok másolása az Azure SQL Data Warehouse, lásd: [másolása Azure Data Factory használatával adatok vagy az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) cikk.
>
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
* **Azure SQL Data Warehouse**. Ez az adattárház tárolja az SQL Database-ből átmásolt adatokat. Ha még nem rendelkezik Azure SQL Data Warehouse-zal, a létrehozás folyamatáért lásd az [SQL Data Warehouse létrehozásával](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) kapcsolatos cikket.
* **Azure SQL Database** Ebben az oktatóanyagban adatokat másol egy Azure SQL Database Adventure Works LT mintaadatokkal, létrehozhat egy következő a [hozzon létre egy Azure SQL-adatbázis](../sql-database/sql-database-get-started-portal.md) cikk. 
* **Azure Storage-fiók** Az Azure Storage használatos **átmeneti** blob a tömeges másolási műveletnél. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Kattintson a **NEW** (Új) lehetőségre a bal oldali menüben, kattintson az **Data + Analytics** (Adatok + analitika) pontra, majd a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** lapján adja meg az értékeket, az alábbi képernyőfelvételen látható módon:
      
     ![Új előállító lap](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **név.** Adjon meg egy globális egyedi nevet az adat-előállítóban. Ha a következő hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévADFTutorialDataFactory-ra), majd próbálkozzon újra a létrehozással. Lásd: [Data Factory - elnevezési szabályait](naming-rules.md) cikk az adat-előállító összetevők elnevezési szabályait.
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **Előfizetés.** Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
    * **Erőforráscsoport.** A legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válasszon **hozzon létre új** lehetőséget, majd írja be az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió.** Válassza ki **V2 (előzetes verzió)**.
    * **Hely.** Válassza ki az adat-előállító helyét. Csak a támogatott helyek a legördülő listában jelennek meg. Az adattároló (Azure Storage, Azure SQL Database stb.) adat-előállító által használt más helyek és régiókban is szerepelhet. 

3. Kattintson a **Create** (Létrehozás) gombra.
4. Miután létrehozása befejeződött, navigáljon a data factory, és megjelenik a **adat-előállító** lapon, az ábrán látható módon. Kattintson a **Szerző & figyelő** csempe az adatok integrációs alkalmazást egy külön lapján.
   
   ![Data factory kezdőlap](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse

1. A get elindított oldalon kattintson **adatok másolása** feliratú csempéjén, így az adatok másolása eszköz indítása. 

   ![Másolja az adatokat eszköz csempe](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. A a **tulajdonságok** lapon adja meg az adatok másolása eszköz **CopyFromSQLToSQLDW** a a **feladat neve**, és kattintson a **tovább**. 

    ![Másolja az adatokat eszköz-Tulajdonságok lap](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. Az a **forrás adattár** lapon jelölje be **Azure SQL Database**, és kattintson a **következő**.

    ![Forrás adatokat tároló lap](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Az a **adja meg az Azure SQL database** lapon esetén tegye a következőket: 
    1. Válassza ki az Azure SQL server a **kiszolgálónév**.
    2. Az Azure SQL-adatbázist a **adatbázisnév**.
    3. Adja meg annak a felhasználónak a nevére **felhasználónév**.
    4. Adja meg a jelszót a felhasználónak az **jelszó**.
    5. Kattintson a **Tovább** gombra. 

        ![Adja meg az Azure SQL-adatbázis](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. Az a **válassza ki azokat a táblákat, amelynek be kell másolnia az adatokat, vagy használjon egy egyéni lekérdezést** lapján táblákat szűrő megadásával **SalesLT** beviteli mezőbe, majd ellenőrizze a **(válassza ki az összes)** Jelölje ki minden olyan táblát, és kattintson a jelölőnégyzet **következő**. 

    ![Válassza ki a bemeneti fájl vagy mappa](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. Az a **adatok céltár** lapon jelölje be **Azure SQL Data Warehouse**, és kattintson a **tovább**. 

    ![A cél adatokat tároló lap](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. Az a **adja meg az Azure SQL Data Warehouse** lapon esetén tegye a következőket: 

    1. Válassza ki az Azure SQL server a **kiszolgálónév**.
    2. Válassza ki az Azure SQL Data Warehouse esetében a **adatbázisnév**.
    3. Adja meg annak a felhasználónak a nevére **felhasználónév**.
    4. Adja meg a jelszót a felhasználónak az **jelszó**.
    5. Kattintson a **Tovább** gombra. 

        ![Adja meg az Azure SQL-adatraktár](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. Az a **táblaleképezés** lapon tekintse át, és kattintson a **következő**. Egy intelligens táblaleképezés jelenik meg, hogy a maps forrás céltáblák táblanevek alapján. Ha a tábla nem létezik az a cél Azure Data Factory alapértelmezés szerint létrehoz egy azonos nevű. Választhatja azt is, egy meglévő táblára van leképezve. 

    > [!NOTE]
    > Automatikus tábla létrehozásához az SQL Data Warehouse fogadó érvényes SQL Server vagy az Azure SQL Database forrásaként szolgál. Ha más forrás adattárolóból adatok másolása kell lehet előre létrehozhat a séma az Azure SQL Data Warehouse fogadó adatmásolás előtt.

    ![Leképezési lapja](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. Az a **séma-hozzárendelése** lapon tekintse át, és kattintson a **következő**. Intelligens leképezési oszlopnév alapul. Lehetővé teszik a Data Factory automatikusan a táblák létrehozása mellett dönt, ha megfelelő adattípus konvertálása olyan esetben fordulhat elő, ha a kompatibilitási kijavításához forrás és a célkiszolgáló között. Ha egy nem támogatott típus a forrás és cél oszlop közötti átalakítás, lásd: hibaüzenetet a megfelelő tábla mellett.

    ![Séma leképezési lap](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. Az a **beállítások** lapon, válassza ki az Azure Storage-ban a **tárfióknév** legördülő listából. Az adatok átmeneti megelőzően az SQL Data Warehouse PolyBase használatával szolgál. Miután végzett a másolat, a program automatikusan kiüríti az átmeneti adatok. 

    A "Speciális beállításai" jelet az "alapértelmezett típusú használata" lehetőséget.

    ![Beállítások lap](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. Az a **összegzés** lapon tekintse át a beállításokat, és kattintson a **következő**.

    ![Összefoglaló lap](./media/load-azure-sql-data-warehouse/summary-page.png)
13. Az a **telepítési lap**, kattintson a **figyelő** figyelheti a folyamat (feladat).

    ![Telepítés lap](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Figyelje meg, hogy a **figyelő** a bal oldali lapon automatikusan ki van jelölve. Megjelenik a tevékenységfuttatási részletek megtekintése, és futtassa újra a kimenetátirányítási mechanizmusával hivatkozások a **műveletek** oszlop. 

    ![A figyelő folyamat fut](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. A Futtatás futószalag társított tevékenység fut megtekintéséhez kattintson **nézet tevékenység fut** hivatkozásra a **műveletek** oszlop. 10 másolási tevékenység az adatcsatorna, minden egyes másolja át az adatok egy tábla. Váltson vissza az a folyamat futó nézet, kattintson a **folyamatok** tetején. Kattintson a **frissítése** a listájának frissítése. 

    ![A figyelő tevékenység fut](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

## <a name="next-steps"></a>További lépések

Előzetes tudnivalók Azure SQL Data Warehouse támogatásáról a következő cikkben: 

> [!div class="nextstepaction"]
>[Az Azure SQL Data Warehouse-összekötő](connector-azure-sql-data-warehouse.md)