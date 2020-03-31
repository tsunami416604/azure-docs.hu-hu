---
title: Különbözeti másolás adatbázisból vezérlőtábla használatával
description: Megtudhatja, hogyan használhat megoldássablont új vagy frissített sorok csak az Azure Data Factory adatbázisból történő növekményes másolására.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 3c077e2c04cae94d2e1a2a84ccd7d09c7a0829b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439749"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Különbözeti másolás vezérlőtáblával rendelkező adatbázisból

Ez a cikk egy sablont, amely elérhető növekményesen betölteni az új vagy frissített sorokat egy adatbázistábla az Azure-ba egy külső vezérlőtábla, amely tárolja a magas vízjel érték.

Ez a sablon megköveteli, hogy a forrásadatbázis sémája tartalmazzon egy időbélyeg oszlopot vagy növekménykulcsot az új vagy frissített sorok azonosításához.

>[!NOTE]
> Ha rendelkezik egy időbélyeg oszlop a forrás adatbázisban az új vagy frissített sorok azonosítására, de nem szeretné, hogy hozzon létre egy külső vezérlőtáblát használni a különbözeti másolás, ehelyett használhatja az [Azure Data Factory Copy Data eszköz](copy-data-tool.md) egy folyamat lekérni. Ez az eszköz egy eseményindító ütemezett időt használ változóként az új sorok beolvasásához a forrásadatbázisból.

## <a name="about-this-solution-template"></a>A megoldássablon – kapcsolat

Ez a sablon először beolvassa a régi vízjel értékét, és összehasonlítja az aktuális vízjel értékével. Ezt követően csak a változásokat másolja át a forrásadatbázisból a két vízjel értékének összehasonlítása alapján. Végül az új magas vízjel értéket egy külső vezérlőtáblához tárolja a különbözeti adatok következő betöltéséhez.

A sablon négy tevékenységet tartalmaz:
- **A keresés** beolvassa a régi magas vízjel értéket, amely egy külső vezérlőtáblában van tárolva.
- Egy másik **keresési** tevékenység lekéri az aktuális magas vízjel értéket a forrásadatbázisból.
- **A** másolás csak a forrásadatbázisból a céltárolóba történő módosításokat másolja. A forrásadatbázis változásait azonosító lekérdezés hasonló a "SELECT * FROM DATA_SOURCE_TABLE WHERE TIMESTAMP_Column > "utolsó magas vízjel" és TIMESTAMP_Column <= "aktuális magas vízjel".The query that identifies the changes in the source database is similar to 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high-watermark" and TIMESTAMP_Column <= "current high-watermark".The query that identifies the changes in the source database is similar to 'SELECT * FROM DATA_SOURCE_TABLE WHERE TIMESTAMP_Column > "last high-watermark" and TIMESTAMP_Column <= "current high-watermark".The query that identifies the changes in the source database is
- **Az SqlServerStoredProcedure** az aktuális magas vízjel értéket írja egy külső vezérlőtáblába a különbözeti másoláshoz legközelebb.

A sablon a következő paramétereket határozza meg:
- *Data_Source_Table_Name* az a tábla a forrásadatbázisban, amelyből adatokat szeretne betölteni.
- *Data_Source_WaterMarkColumn* a forrástábla új vagy frissített sorainak azonosítására használt oszlop neve. Ennek az oszlopnak a típusa általában *datetime*, *INT*vagy hasonló.
- *Data_Destination_Container* annak a helynek a gyökérelérési útja, ahová az adatokat a céltárolóba másolja a rendszer.
- *Data_Destination_Directory* a könyvtár elérési útja annak a helynek a gyökere alatt, ahová az adatokat a céltárolóba másolják.
- *Data_Destination_Table_Name* az a hely, ahová az adatok másolása a céltárolóba történik (akkor alkalmazható, ha az "Azure Synapse Analytics (korábban SQL DW)" adatcélként van kiválasztva).
- *Data_Destination_Folder_Path* az a hely, ahová az adatokmásolása a céltárolóba történik (akkor alkalmazható, ha a "Fájlrendszer" vagy az "Azure Data Lake Storage Gen1" adatcélként van kiválasztva).
- *Control_Table_Table_Name* a külső vezérlőtábla, amely a magas vízjel értéket tárolja.
- *Control_Table_Column_Name* a külső vezérlőtábla azon oszlopa, amely a magas vízjel értéket tárolja.

## <a name="how-to-use-this-solution-template"></a>A megoldássablon használata

1. Fedezze fel a betölteni kívánt forrástáblát, és határozza meg az új vagy frissített sorok azonosítására használható magas vízjeloszlopot. Ennek az oszlopnak a típusa lehet *datetime,* *INT*vagy hasonló. Az oszlop értéke az új sorok hozzáadásával növekszik. A következő mintaforrás-táblából (data_source_table) a *LastModifytime* oszlopot használhatjuk magas vízjeloszlopként.

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
    
2. Hozzon létre egy vezérlőtáblát az SQL Server vagy az Azure SQL Database rendszerben a különbözeti adatok betöltésének magas vízjelértékének tárolásához. A következő példában a vezérlőtábla neve *vízjelként jelenik meg.* Ebben a táblában a *Vízjelérték* az az oszlop, amely a magas vízjel értéket tárolja, és típusa *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Hozzon létre egy tárolt eljárást ugyanabban az SQL Server vagy Azure SQL Database-példányban, amelyet a vezérlőtábla létrehozásához használt. A tárolt eljárás segítségével írhatja az új magas vízjel értéket a külső vezérlőtáblába a különbözeti adatok következő betöltéséhez.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Nyissa meg a **Különbözet idomát az Adatbázis sablonból.** **Hozzon** létre egy új kapcsolatot azzal a forrásadatbázissal, amelyből adatokat szeretne másolni.

    ![Új kapcsolat létrehozása a forrástáblával](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. **Hozzon** létre egy új kapcsolatot a céladattárhoz, amelybe az adatokat másolni szeretné.

    ![Új kapcsolat létrehozása a céltáblához](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. **Új** kapcsolat létrehozása a külső vezérlőtáblához és a 2.

    ![Új kapcsolat létrehozása a vezérlőtábla adattárával](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Válassza **a Sablon használata lehetőséget.**
    
8. A rendelkezésre álló folyamat a következő példában látható módon jelenik meg:
  
    ![A folyamat áttekintése](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Válassza **a Tárolt eljárás lehetőséget.** A **Tárolt eljárás neve mezőbe**válassza a **[dbo].[ update_watermark]** című. Válassza **a Paraméter importálása**lehetőséget, majd a **Dinamikus tartalom hozzáadása**lehetőséget.  

    ![A tárolt eljárási tevékenység beállítása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Írja be ** \@a(z) {activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue} (Befejezés)** tartalmat, majd válassza **a Befejezés gombot.**  

    ![A tárolt eljárás paramétereinek tartalmának beírása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Válassza **a Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd a **Befejezés**lehetőséget.

    ![**Debug**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Az alábbi példához hasonló eredmények jelennek meg:

    ![Az eredmény áttekintése](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Új sorokat hozhat létre a forrástáblában. Itt van minta SQL nyelv új sorok létrehozásához:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. A folyamat ismételt futtatásához válassza a **Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd kattintson a **Befejezés gombra.**

    Látni fogja, hogy csak új sorokat másolt a célhelyre.

15. (Nem kötelező:) Ha az Azure Synapse Analytics (korábban SQL DW) adatcélként való kiválasztását választja, az SQL Data Warehouse Polybase által megkövetelt Azure Blob storage-tárolóhoz is létre kell adnia egy kapcsolatot. A sablon létrehoz egy tároló elérési útját. A folyamat futtatása után ellenőrizze, hogy a tároló létrejött-e a Blob storage-ban.
    
    ![A polibázis konfigurálása](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>További lépések

- [Tömeges másolás egy adatbázisból egy vezérlőtábla használatával az Azure Data Factory-val](solution-template-bulk-copy-with-control-table.md)
- [Fájlok másolása több tárolóból az Azure Data Factory segítségével](solution-template-copy-files-multiple-containers.md)
