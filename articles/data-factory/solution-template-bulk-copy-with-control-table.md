---
title: A tömeges másolás az Azure Data Factoryvel vezérlő táblával adatbázisból |} A Microsoft Docs
description: Ismerje meg, hogyan megoldássablon használatával teljes mértékben adatok tömeges másolása az Azure Data Factoryvel forrás táblák partíció listáját tárolja egy külső vezérlőelem tábla használatával.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967335"
---
# <a name="bulk-copy-from-database-with-control-table"></a>A tömeges másolás vezérlő táblával adatbázisból

Ha meg szeretné adatok másolása az Oracle-kiszolgálóhoz, Netezza kiszolgáló, Teradata-kiszolgáló vagy az SQL Server data warehouse-ból az Azure-ba, hatalmas mennyiségű adatokat betölteni az adatforrások több táblájából rendelkezik. A legtöbb esetben az adatok további particionálandó mindegyik táblában úgy, hogy egyetlen táblából betöltheti a párhuzamosan több szálon sorok rendelkezik. A jelen sablon adott esetben lett tervezve. 

Ha azt szeretné, az adatok másolása az adatok kis méretű kis számú táblában, sokkal hatékonyabban, hogy nyissa meg az "adatok másolása eszköz" szeretné, hogy egy egyetlen másolja, vagy foreach tevékenység + másolási tevékenység a folyamatban. Ezzel a sablonnal a több mint meg kell ezt az egyszerű használati eset.

## <a name="about-this-solution-template"></a>Ez a megoldássablon kapcsolatban

Ez a sablon táblából forrásadatbázis partícióinak listáját egy külső vezérlőelem, amelyet át kell másolni a céltár ismétel mindegyik partíció az adatforrás-adatbázis és az adatmásolási műveletet hajt végre.

A sablon a három tevékenységet tartalmaz:
-   A **keresési** tevékenység dpmvolume partíció a forrásadatbázis egy külső vezérlőelem táblából.
-   A **ForEach** tevékenység, keresési tevékenység az a partíció-lista lekérése és azok a másolási tevékenység, majd újrafuttathatja.
-   A **másolási** tevékenység, amely a cél másolhatja az egyes partíciók adatbázis-tárolóból.

A sablon meghatározza öt:
-   A paraméter *Control_Table_Name* a tábla neve a külső vezérlőelem táblázat. A vezérlő tábla a forrásadatbázis Partíciólista tárolására szolgál.
-   A paraméter *Control_Table_Schema_PartitionID* az oszlopnév a külső vezérlőelem táblában tárolni minden egyes partíción azonosítóját. Ellenőrizze, hogy a Partícióazonosító a forrásadatbázisban mindegyik partíció esetében egyedi legyen.
-   A paraméter *Control_Table_Schema_SourceTableName* a külső vezérlőelem táblában tárolni a forrásadatbázisból minden táblanévhez oszlop neve.
-   A paraméter *Control_Table_Schema_FilterQuery* a külső vezérlőelem táblában tárolni az adatokat kíván gyűjteni a forrásadatbázisban mindegyik partíció Szűrőlekérdezés oszlop neve. Például, ha az adatok dokumentumtárolási évente, a lekérdezés minden egyes sorban tárolt lehet hasonló, "Válassza ki * az adatforrás ahol LastModifytime > =" 2015-01-01 00:00:00: "% és LastModifytime < ="2015-12-31 23:59:59.999: "%"
-   A paraméter *Data_Destination_Folder_Path* ahol az adatokat másolja be a céltároló elérési útja.  Ez a paraméter csak akkor látható, ha az Ön által kiválasztott célhely egy fájlalapú tárolón tárolóban.  Ha úgy dönt, hogy az SQL Data Warehouse a cél tárolóként, nem nem szükséges itt lehet képkockák paraméter. De a táblanevek, az SQL data warehouse a séma ugyanaz, mint a forrás-adatbázisban kell lennie.

## <a name="how-to-use-this-solution-template"></a>Ez a megoldássablon használata

1. Az SQL-kiszolgáló vagy a tömeges másolási forrásadatbázis partíció listájának tárolása az SQL Azure vezérlő tábla létrehozása.  Az alábbi példában tekintheti meg a forrásadatbázis öt partíciók találhatók hol három partíció egy táblában találhatók:*datasource_table* és a két partíció egy másik táblához:*project_table*. Az oszlop *LastModifytime* van tábla particionálásához használt *datasource_table* forrásadatbázisból. A lekérdezés az első partíció olvasásához használt "Válassza ki * datasource_table a ahol LastModifytime > =" 2015-01-01 00:00:00: "% és LastModifytime < ="2015-12-31 23:59:59.999: "%".  Megtekintheti a hasonló lekérdezés adatokat olvasni a többi partíció is. 

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

2. Lépjen a sablon **tömeges másolási adatbázisból**, és hozzon létre egy **új kapcsolat** külső vezérlőelem-táblázathoz.  Ehhez a kapcsolathoz csatlakozni az adatbázishoz, hozta létre a vezérlő tábla #1. lépésben.

    ![A vezérlő táblához új kapcsolat létrehozása](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Hozzon létre egy **új kapcsolat** a forráshely adatbázisára, ahol az adatok másolása.

     ![Hozzon létre egy új kapcsolatot a forráshely adatbázisára](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Hozzon létre egy **új kapcsolat** a céladattárba, ahol az adatokat másolja.

    ![Hozzon létre egy új kapcsolatot tárolási célhelye](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Kattintson a **ezzel a sablonnal**.

    ![Sablon használata](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. A folyamat a panelen érhető el az alábbi példában látható módon jelenik meg:

    ![A folyamat áttekintése](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Kattintson a **Debug**, adjon meg a paramétereket, és kattintson a **Befejezés**

    ![Kattintson a hibakereséshez](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Az eredmény panelen érhető el az alábbi példában látható módon jelenik meg:

    ![Tekintse át az eredmény](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Nem kötelező) Ha az SQL Data Warehouse adatok célhelyeként, is adjon meg egy átmeneti, amely az SQL Data Warehouse Polybase van szüksége egy Azure blob Storage a kapcsolat szükséges.  Ellenőrizze, hogy már létrehozta a blob storage-tárolóba.  
    
    ![A Polybase-beállítás](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)