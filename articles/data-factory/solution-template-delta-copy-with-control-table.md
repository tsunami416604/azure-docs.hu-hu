---
title: Változásadatok másolásához adatbázisból vezérlő táblával, az Azure Data Factoryvel |} A Microsoft Docs
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
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967293"
---
# <a name="delta-copy-from-database-with-control-table"></a>Vezérlő táblával adatbázisból a változásadatok másolásához

Ha szeretne növekményes betöltése módosításokat (új vagy frissített sorok) csak egy adatbázis-táblából az Azure-bA egy külső vezérlőelem táblával, a nagy küszöbértékek tárolására.  A jelen sablon adott esetben lett tervezve. 

A sablon használatához a forrásadatbázis séma kell tartalmazó időbélyeg oszlop, vagy növekvő kulcsot az új vagy frissített sorainak azonosításához.

Ha már van időbélyegző-oszlopot a forrásadatbázisban az új vagy frissített sorainak azonosításához, de nem szeretné a változásadatok másolásához engedélyezése egy külső vezérlőelem tábla létrehozásához, használhatja egy folyamatot, amely egy eseményindító ütemezett időpontot használja változóként olvassa be az adatok másolása eszközzel az új sorok csak a forrásadatbázisból.

## <a name="about-this-solution-template"></a>Ez a megoldássablon kapcsolatban

Ez a sablon minden esetben először kéri le a régi küszöbértéket, és összehasonlítja az aktuális küszöbértéket. Ezt követően csak másolja át a módosításokat a forrásadatbázis 2 küszöbértékei összehasonlítása alapján.  Amikor végzett, egy külső vezérlőelem változásadatok betöltése a következő táblázat tartalmazza az új felső küszöbbel rendelkező érték tárolja.

A sablon négy tevékenységet tartalmaz:
-   A **keresési** tevékenység küszöbértéket kéri le a régi nagy – egy vezérlőelem külső tábla tárolja.
-   A **keresési** tevékenység, amely az aktuális felső küszöbbel rendelkező értéket lekérheti a forrásadatbázisban.
-   A **másolási** tevékenységet, hogy a módosítások csak a forrásadatbázisból a cél a másolhatja. A lekérdezést a forrásadatbázisból a másolási tevékenység módosítások azonosításához használt hasonló, az "VÁLASSZA * ahol TIMESTAMP_Column a következő: Data_Source_Table >"legutóbbi magas vízjel"és a TIMESTAMP_Column < ="jelenlegi felső küszöbbel rendelkező"".
-   A **SqlServerStoredProcedure** tevékenység az aktuális magas vízjelének írni egy külső vezérlőelem tábla változásadatok másolásához a következő alkalommal.

A sablon meghatározza öt:
-   A paraméter *Data_Source_Table_Name* kívánja betölteni az adatokat a forrásadatbázisból a táblázat neve van.
-   A paraméter *Data_Source_WaterMarkColumn* a forrástábla, amely az új vagy frissített sorainak azonosításához használható oszlop neve. Ez az oszlop típusa általában lehet datetime vagy INT stb.
-   A paraméter *Data_Destination_Folder_Path* vagy *Data_Destination_Table_Name* az a hely, ahol az adatokat a cél tárolóba másolja.
-   A paraméter *Control_Table_Table_Name* nagy küszöbértékek tárolására külső vezérlőelem tábla neve.
-   A paraméter *Control_Table_Column_Name* az oszlopnév a vezérlőelem külső tábla nagy küszöbértékek tárolására.

## <a name="how-to-use-this-solution-template"></a>Ez a megoldássablon használata

1. Fedezze fel a forrástábla kívánja betölteni, és adja meg a felső küszöbbel rendelkező oszlop, amely az új vagy frissített sorok szeletelni használható. Ez az oszlop típusa általában lehet datetime vagy INT stb., és gondoskodik a növelése, ha új sorok hozzáadni kívánt adatokat.  A mintául szolgáló forrásból táblából (tábla neve: data_source_table) oszlop használatával, az alábbiakban *LastModifytime* felső küszöbbel rendelkező oszlopot.

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
    
2. Vezérlő tábla létrehozása az SQL-kiszolgáló vagy az SQL Azure változásadatok betöltése nagy küszöbértékek tárolására. Az alábbi példában láthatja a vezérlő tábla neve *watermarktable*. Az oszlop neve, a nagy küszöbértékek tárolására van benne található *WatermarkValue* és a típusa *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Hozzon létre egy tárolt eljárást ugyanazt az SQL server vagy a vezérlő tábla létrehozásához használt SQL Azure. A tárolt eljárás segítségével új felső küszöbbel rendelkező érték írása a változásadatok betöltése a következő alkalommal a külső vezérlőelem táblába.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Lépjen a sablon **adatbázisból származó Változásadatok másolásához**, és hozzon létre egy **új kapcsolat** a forráshely adatbázisára, ahol az adatok másolása.

    ![Hozzon létre egy új kapcsolatot a forrástáblához](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Hozzon létre egy **új kapcsolat** a céladattárba, ahol az adatokat másolja.

    ![Hozzon létre egy új kapcsolatot a céltábla](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Hozzon létre egy **új kapcsolat** a vezérlőelem külső tábla és a tárolt eljárást.  Csatlakozás az adatbázishoz, amelyen korábban a vezérlő táblában létrehozott és tárolt eljárás #2 és a #3. lépésében.

    ![A vezérlő tábla adattárba új kapcsolat létrehozása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Kattintson a **ezzel a sablonnal**.

     ![Sablon használata](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. A folyamat a panelen érhető el az alábbi példában látható módon jelenik meg:

     ![Folyamat áttekintése](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Kattintson a tárolt eljárási tevékenység, válassza ki **tárolt eljárás neveként**, kattintson a **importálási paraméter** kattintson **dinamikus tartalom hozzáadása**.  

     ![Állítsa be a tárolt eljárási tevékenység](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. A tartalmat **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** kattintson **Befejezés**.  

     ![Tárolt eljárás paraméter tartalmának írása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Kattintson a **Debug**, adjon meg a paramétereket, és kattintson a **Befejezés**.

    ![Kattintson a hibakereséshez](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Az eredmény panelen érhető el az alábbi példában látható módon jelenik meg:

    ![Tekintse át az eredmény](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Létrehozhat új sort a forrástáblában.  A minta sql hozhat létre új sor lehet, a következőket:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. A folyamat újbóli futtatása kattintva **Debug**, adjon meg a paramétereket, és kattintson a **Befejezés**.

    ![Kattintson a hibakereséshez](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. Láthatja, hogy csak az új sorok át lettek másolva a célra.

15. (Nem kötelező) Ha az SQL Data Warehouse adatok célhelyeként, is adjon meg egy átmeneti, amely az SQL Data Warehouse Polybase van szüksége egy Azure blob Storage a kapcsolat szükséges.  Ellenőrizze, hogy már létrehozta a blob storage-tárolóba.  
    
    ![A Polybase konfigurálása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)