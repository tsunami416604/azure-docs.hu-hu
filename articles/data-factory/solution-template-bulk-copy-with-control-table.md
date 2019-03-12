---
title: Tömegesen másolni egy adatbázisból vezérlő táblázat az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használható a megoldássablon tömeges adatokat másol egy adatbázist egy külső vezérlőelem tábla használatával a forrás-táblák partíció listájának tárolása az Azure Data Factory használatával.
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
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530569"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>A tömeges másolás az ellenőrzési tábla adatbázisból

Adatok másolása az adattárház az Oracle-kiszolgálóhoz, Netezza, Teradata vagy SQL Server az Azure SQL Data Warehouse, hatalmas mennyiségű adat betöltése több táblájából rendelkezik. Az adatok általában úgy, hogy párhuzamosan több szálon rendelkező sor betöltése egy egyetlen táblázatból származó particionálandó mindegyik tábla rendelkezik. Ez a cikk ismerteti egy ezekben a forgatókönyvekben használandó sablont.

 >! Vegye figyelembe, ha szeretne egy kis számú táblában viszonylag kis adatkötettel adatokat másol az SQL Data Warehouse, hatékonyabb, ha használja a [Azure Data Factory az adatok másolása eszköz](copy-data-tool.md). A sablon (ebben a cikkben ismertetett) több mint kell ehhez a forgatókönyvhöz.

## <a name="about-this-solution-template"></a>Ez a megoldássablon kapcsolatban

Ez a sablon a forráspartíciók adatbázis másolása egy külső vezérlőelem táblából listájának beolvasása. Ezután mindegyik partíció a forrásadatbázis ismétel, és átmásolja az adatokat a célhelyre.

A sablon a három tevékenységet tartalmaz:
- **Keresési** meg arról, hogy adatbázis-partíciók listájának beolvasása egy külső vezérlőelem táblából.
- **ForEach** partíció listájának beolvasása a keresési tevékenység eredménylistáját és végiglépkedve mindegyik partíció a másolási tevékenységnek.
- **Másolás** mindegyik partíció másol a forrásadattárba a adatbázis tárolási célhelye.

A sablon meghatározza öt:
- *Control_Table_Name* a külső vezérlőelem tábla, amely tárolja a Partíciólista a forrásadatbázishoz.
- *Control_Table_Schema_PartitionID* neve, a vezérlőelem külső tábla, amely tárolja az egyes partícióazonosító. az oszlop neve Győződjön meg arról, hogy a forrásadatbázis minden partíció esetében egyedi legyen-e a Partícióazonosító.
- *Control_Table_Schema_SourceTableName* a vezérlőelem külső tábla, amely tárolja a forrásadatbázisból minden táblanévhez.
- *Control_Table_Schema_FilterQuery* a külső vezérlőelem táblában, amely a szűrő-lekérdezés az adatok bekérése mindegyik partíció az adatforrás-adatbázisban tárolja az oszlop neve. Például, ha az adatok dokumentumtárolási év, a lekérdezés minden egyes sorban tárolt lehet hasonló "Válassza ki * az adatforrás ahol LastModifytime > =" 2015-01-01 00:00:00: "% és LastModifytime < ="2015-12-31 23:59:59.999: "%".
- *Data_Destination_Folder_Path* ahol az adatokat másolja be a céltároló elérési útja. Ez a paraméter csak akkor látható, ha a cél, Ön által választott fájlalapú tárolást. Ha úgy dönt, hogy az SQL Data Warehouse a cél tárolóként, ezt a paramétert, nem szükséges. De a táblanevek, az SQL Data Warehouse a séma megegyezik a forrásadatbázis rétegében kell lennie.

## <a name="how-to-use-this-solution-template"></a>Ez a megoldássablon használata

1. Vezérlő tábla létrehozása az SQL Server vagy az Azure SQL Database tömeges másolására szolgáló forrásból adatbázis Partíciólista tárolásához. Az alábbi példában nincsenek öt partíciókat a forrásadatbázisban. A rendszer három partíció a *datasource_table*, és a kettő a *project_table*. Az oszlop *LastModifytime* van tábla particionálásához használt *datasource_table* a forrásadatbázisból. A lekérdezés, amellyel az első partíció beolvasása "Válassza ki * datasource_table a ahol LastModifytime > =" 2015-01-01 00:00:00: "% és LastModifytime < ="2015-12-31 23:59:59.999: "%". Egy hasonló lekérdezés segítségével adatok olvasása a többi partíciótól.

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

2. Nyissa meg a **tömeges másolási adatbázisból** sablont. Hozzon létre egy **új** a külső vezérlőelem táblában az 1. lépésben létrehozott kapcsolat.

    ![A vezérlő táblához új kapcsolat létrehozása](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Hozzon létre egy **új** , másolunk adatokat az adatforrás-adatbázishoz való csatlakozáshoz.

     ![Hozzon létre egy új kapcsolatot a forráshely adatbázisára](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Hozzon létre egy **új** kapcsolatot a cél-adatok tárolására, hogy másolunk adatokat.

    ![Hozzon létre egy új kapcsolatot tárolási célhelye](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Válassza ki **ezzel a sablonnal**.

    ![Sablon használata](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. A folyamat az alábbi példában látható módon jelenik meg:

    ![A folyamat áttekintése](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Válassza ki **Debug**, adja meg a **paraméterek**, majd válassza ki **Befejezés**.

    ![Kattintson a ** hibakeresési **](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Az alábbi példához hasonló eredmény jelenik meg:

    ![Tekintse át az eredmény](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Nem kötelező) Ha az SQL Data Warehouse, az adat célhelyét, meg kell adnia egy kapcsolatot az Azure Blob storage, az SQL Data Warehouse Polybase által előírt, előkészítési. Győződjön meg arról, hogy a Blob storage-tárolóba már létrejött.
    
    ![A Polybase-beállítás](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)
