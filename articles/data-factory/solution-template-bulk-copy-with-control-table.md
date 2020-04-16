---
title: Adatbázisból származó tömeges másolás vezérlőtáblával
description: Megtudhatja, hogyan használhat megoldássablont tömeges adatok másolására egy adatbázisból egy külső vezérlőtábla használatával a forrástáblák partíciólistájának tárolására az Azure Data Factory használatával.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: d6d634d9a32ae1728e1122d863ddabd94f73ee27
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414829"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Vezérlőtáblával rendelkező adatbázis ból történő tömeges másolás
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha adatokat szeretne másolni az Oracle Server, netezza, Teradata vagy SQL Server adattárházából az Azure SQL Data Warehouse-ba, hatalmas mennyiségű adatot kell betöltenie több táblából. Az adatokat általában minden táblában particionálni kell, hogy egyetlen táblából párhuzamosan több szálat tartalmazó sorokat tölthessen be. Ez a cikk az ilyen esetekben használható sablont ismerteti.

 >! MEGJEGYZÉS: Ha viszonylag kis mennyiségű adatkötettel rendelkező táblából szeretne adatokat másolni az SQL Data Warehouse-ba, hatékonyabb az [Azure Data Factory Copy Data eszköz](copy-data-tool.md)használata. A sablon, amely a cikkben ismertetjük, több, mint amire az adott forgatókönyvhöz szüksége van.

## <a name="about-this-solution-template"></a>A megoldássablon – kapcsolat

Ez a sablon lekéri a külső vezérlőtáblából másolandó forrásadatbázis-partíciók listáját. Ezután a forrásadatbázis minden partícióján átitatja, és másolja az adatokat a célhelyre.

A sablon három tevékenységet tartalmaz:
- **A keresés** lekéri az biztos adatbázispartíciók listáját egy külső vezérlőtáblából.
- **ForEach** lekéri a partíciólistát a lookup tevékenységből, és minden partíciót a Másolás tevékenységhez.
- **Másolja** az egyes partíciókat a forrásadatbázis-tárolóból a céltárolóba.

A sablon a következő paramétereket határozza meg:
- *Control_Table_Name* a külső vezérlőtábla, amely a forrásadatbázis partíciólistáját tárolja.
- *Control_Table_Schema_PartitionID* az egyes partícióazonosítókat tároló külső vezérlőtáblában szereplő oszlopnév neve. Győződjön meg arról, hogy a partícióazonosító egyedi a forrásadatbázis minden partíciójához.
- *Control_Table_Schema_SourceTableName* a külső vezérlőtábla, amely a forrásadatbázisból származó minden tábla nevét tárolja.
- *Control_Table_Schema_FilterQuery* a külső vezérlőtábla azon oszlopának a neve, amely a szűrőlekérdezést tárolja, hogy a forrásadatbázis egyes partícióiból adatokat kapjon. Ha például az adatokat év szerint particionálta, az egyes sorokban tárolt lekérdezés hasonló lehet a "select * adatforrásból, ahol a LastModifytime >= ''2015-01-01 00:00:00'" és lastmodifytime <= ''2015-12-31 23:59:59.999''".
- *Data_Destination_Folder_Path* az az elérési út, ahol az adatok at a céltárolóba másolja a rendszer (akkor alkalmazható, ha a választott cél a "Fájlrendszer" vagy az "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* a gyökérmappa elérési útja, ahová az adatok másolása a céltárolóba történik. 
- *Data_Destination_Directory* a gyökér könyvtárelérési útja, ahol az adatok at a céltárolóba másolják. 

Az utolsó három paraméter, amely meghatározza az elérési utat a céltárolóban, csak akkor látható, ha a választott cél a fájlalapú tároló. Ha az "Azure Synapse Analytics (korábban SQL DW)" lehetőséget választja céltárolóként, ezek a paraméterek nem szükségesek. De a tábla nevek és a séma az SQL Data Warehouse meg kell egyeznie a forrásadatbázisban szereplő.

## <a name="how-to-use-this-solution-template"></a>A megoldássablon használata

1. Hozzon létre egy vezérlőtáblát az SQL Server vagy az Azure SQL Database rendszerben a forrásadatbázis partíciólistájának tárolására a tömeges másoláshoz. A következő példában öt partíció található a forrásadatbázisban. Három partíció a *datasource_table*, és kettő a *project_table*. A *LastModifytime* oszlop a *datasource_table* tábla adatainak a forrásadatbázisból történő particionálására szolgál. Az első partíció olvasásához használt lekérdezés a "select * datasource_table ahonnan LastModifytime >= ''2015-01-01 00:00:00'' és LastModifytime <= ''2015-12-31 23:59:59.999'''. Hasonló lekérdezéssel más partíciók adatainak olvasására is használható.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Nyissa meg a **Tömeges másolás adatbázisból** sablont. **Új** kapcsolat létrehozása az 1.

    ![Új kapcsolat létrehozása a vezérlőtáblához](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. **Hozzon** létre egy új kapcsolatot azzal a forrásadatbázissal, amelyből adatokat másol.

    ![Új kapcsolat létrehozása a forrásadatbázissal](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. **Hozzon** létre egy új kapcsolatot a céladattárhoz, amelybe az adatokat másolja.

    ![Új kapcsolat létrehozása a céltárhoz](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Válassza **a Sablon használata lehetőséget.**

6. A folyamat a következő példában látható módon jelenik meg:

    ![A folyamat áttekintése](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Válassza **a Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd a **Befejezés**lehetőséget.

    ![Kattintson a **Debug**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. A következő példához hasonló eredmények jelennek meg:

    ![Az eredmény áttekintése](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Nem kötelező) Ha az "Azure Synapse Analytics (korábban SQL DW)" adatcélként való választása, meg kell adnia egy kapcsolatot az Azure Blob storage-hoz az SQL Data Warehouse Polybase által megkövetelt módon. A sablon automatikusan létrehoz egy tároló elérési útját a Blob storage.The template will automatically generate a container path for your Blob storage. Ellenőrizze, hogy a tároló a folyamat futtatása után lett-e létrehozva.
    
    ![Polibázis beállítása](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)
