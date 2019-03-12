---
title: Változásadatok másolásához az adatbázisból vezérlő táblázat az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan növekményes másolása az új vagy frissített sorok csak az adatbázisból az Azure Data Factory megoldás sablon használatával.
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
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771157"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Egy vezérlő táblával adatbázisból származó változásadatok másolásához

Ez a cikk ismerteti egy sablont, amely növekményes betöltése új vagy frissített sorok egy adatbázis-táblából az Azure-bA egy külső vezérlőelem tábla tárolja a magas vízjelének használatával érhető el.

Ehhez a sablonhoz szükséges, hogy a forrásadatbázis sémája új vagy frissített sorainak azonosításához időbélyeg oszlop, vagy növekvő kulcsot tartalmaz.

>[!NOTE]
> Ha van időbélyegző-oszlopot a forrásadatbázisban új vagy frissített sorainak azonosításához, de nem kívánja használni a változásadatok másolásához egy külső vezérlőelem tábla létrehozásához, használhatja a [Azure Data Factory az adatok másolása eszköz](copy-data-tool.md) beolvasni a folyamatot. Az eszköz egy eseményindító ütemezett idő változóként új sorok olvasása a forrásadatbázisból használja.

## <a name="about-this-solution-template"></a>Ez a megoldássablon kapcsolatban

Ez a sablon először lekéri a régi küszöbértéket, és összehasonlítja az aktuális küszöbértéket. Ezt követően a módosítások csak a forrásadatbázisból, két küszöbértékei összehasonlítását alapján másolja. Végül tárolja egy külső vezérlőelem változásadatok betöltése a következő táblázat tartalmazza az új felső küszöbbel rendelkező érték.

A sablon négy tevékenységet tartalmaz:
- **Keresési** kérdezi le a régi felső küszöbbel rendelkező értékhez, amely egy külső vezérlőelem tábla tárolja.
- Egy másik **keresési** tevékenység lekéri a jelenlegi felső küszöbbel rendelkező érték a forrásadatbázisból.
- **Másolás** csak a változásokat másolja a forrásadatbázisból tárolási célhelye. A lekérdezés, amely azonosítja a módosításokat a forrásadatbázis hasonlít a "kiválasztása * ahol TIMESTAMP_Column a következő: Data_Source_Table >"legutóbbi magas vízjel"és a TIMESTAMP_Column < ="jelenlegi felső küszöbbel rendelkező"".
- **SqlServerStoredProcedure** legközelebb ír a jelenlegi felső küszöbbel rendelkező érték egy külső vezérlőelem táblázat változásadatok másolásához.

A sablon meghatározza öt:
- *Data_Source_Table_Name* a forrásadatbázis adatokat letölteni kívánt tábla.
- *Data_Source_WaterMarkColumn* a forrástábla, amely azonosítja az új vagy frissített sorok az oszlop neve. Ez az oszlop típus általában *datetime*, *INT*, vagy hasonló.
- *Data_Destination_Folder_Path* vagy *Data_Destination_Table_Name* az a hely, ahol az adatokat másolja a cél Store.
- *Control_Table_Table_Name* külső vezérlőelem tábla tárolja a magas vízjelének van.
- *Control_Table_Column_Name* külső vezérlőelem tábla tárolja a magas vízjelének oszlopa.

## <a name="how-to-use-this-solution-template"></a>Ez a megoldássablon használata

1. Ismerje meg a forrás betölteni kívánt táblát, és adja meg az új vagy frissített sorainak azonosításához használható felső küszöbbel rendelkező oszlop. Lehet, hogy ez az oszlop típusát *datetime*, *INT*, vagy hasonló. Az oszlop értéke növeli a új sorok hozzáadása során. A következő minta forrástáblában (data_source_table), a használhatjuk a *LastModifytime* oszlop felső küszöbbel rendelkező oszlopot.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Vezérlő tábla létrehozása az SQL Server vagy az Azure SQL Database változásadatok betöltése nagy küszöbértékek tárolására. A következő példában a vezérlőelem táblázat neve nem *watermarktable*. Ebben a táblázatban *WatermarkValue* az oszlop, amely a magas vízjelének tárolja, és a típusa *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Hozzon létre egy tárolt eljárást az azonos SQL Server vagy az Azure SQL Database-példányhoz, a vezérlő tábla létrehozásához használt. A tárolt eljárás segítségével új felső küszöbbel rendelkező érték írása a külső vezérlőelem változásadatok betöltése a következő táblázat.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Nyissa meg a **adatbázisból származó Változásadatok másolásához** sablont. Hozzon létre egy **új** az adatok másolása a kívánt forrás-adatbázishoz való csatlakozáshoz.

    ![Hozzon létre egy új kapcsolatot a forrástáblához](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Hozzon létre egy **új** a célként megadott adattárba, hogy az adatokat másolni kívánt kapcsolatot.

    ![Hozzon létre egy új kapcsolatot a céltábla](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Hozzon létre egy **új** a vezérlőelem külső tábla és a tárolt eljárást, amely a 2. és 3 lépésben létrehozott kapcsolat.

    ![A vezérlő tábla adattárba új kapcsolat létrehozása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Válassza ki **ezzel a sablonnal**.

     ![Sablon használata](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Az alábbi példában látható módon jelenik meg a rendelkezésre álló folyamat:

     ![A folyamat áttekintése](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Válassza ki **tárolt eljárás**. A **tárolt eljárás neveként**, válassza a **[update_watermark]**. Válassza ki **importálási paraméter**, majd válassza ki **dinamikus tartalom hozzáadása**.  

     ![Állítsa be a tárolt eljárási tevékenység](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. A tartalmat  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**, majd válassza ki **Befejezés**.  

     ![A tárolt eljárás paraméterei tartalmának írása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Válassza ki **Debug**, adja meg a **paraméterek**, majd válassza ki **Befejezés**.

    ![Select **Debug**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Az alábbi példához hasonló eredmény jelenik meg:

    ![Tekintse át az eredmény](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Létrehozhat új sort a forrástáblában. A következő minta SQL-nyelv új sorok létrehozásához:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Válassza a folyamat újbóli futtatása **Debug**, adja meg a **paraméterek**, majd válassza ki **Befejezés**.

    ![Select **Debug**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Láthatja, hogy csak az új sorok át lettek másolva a célhelyen.

15. (Nem kötelező:) Ha az SQL Data warehouse-ba, az adat célhelyét, meg kell adni az Azure Blob storage-kapcsolat az átmeneti, amelyek szükségesek az SQL Data Warehouse Polybase. Győződjön meg arról, hogy a tároló már létrejött a Blob storage-ban.
    
    ![A Polybase konfigurálása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>További lépések

- [Tömegesen másolni egy adatbázisból vezérlő táblázat az Azure Data Factory használatával](solution-template-bulk-copy-with-control-table.md)
- [Fájlok másolása az Azure Data Factory használatával több tároló](solution-template-copy-files-multiple-containers.md)
