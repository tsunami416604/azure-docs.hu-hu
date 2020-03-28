---
title: Adatok szinkronizálása az Azure SQL Database Edge-ből az Azure Data Factory használatával | Microsoft dokumentumok
description: További információ az Azure SQL Database Edge és az Azure Blob storage közötti adatok szinkronizálásáról
keywords: SQL adatbázis széle,adatok szinkronizálása az SQL adatbázis pereméről, sql adatbázis peremhálózati adatgyár
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851689"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Oktatóanyag: Adatok szinkronizálása az SQL Database Edge-ből az Azure Blob storage-ba az Azure Data Factory használatával

Ebben az oktatóanyagban az Azure Data Factory használatával növekményesen szinkronizálhatja az adatokat az Azure Blob storage-ba az Azure SQL Database Edge egy példányának egy táblájából.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem hozott létre adatbázist vagy táblát az Azure SQL Database Edge központi telepítésében, az alábbi módszerek egyikével hozzon létre egyet:

* Az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) vagy az Azure Data [Studio](/sql/azure-data-studio/download/) használatával csatlakozhat az SQL Database Edge-hez. Sql-parancsfájl futtatásával hozza létre az adatbázist és a táblát.
* Sql-adatbázis és tábla létrehozása [az SQLCMD](/sql/tools/sqlcmd-utility/) használatával az SQL Database Edge modulhoz való közvetlen csatlakozással. További információ: [Csatlakozás az adatbázis-kezelő motorhoz sqlcmd használatával című témakörben.](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)
* Az SQLPackage.exe segítségével telepítsen egy DAC-csomagfájlt az SQL Database Edge tárolóba. Ezt a folyamatot automatizálhatja az SqlPackage fájl URI-jának megadásával a modul kívánt tulajdonságkonfigurációjának részeként. Az SqlPackage.exe ügyféleszközzel közvetlenül is telepíthet egy DAC-csomagot az SQL Database Edge-re.

    Az SqlPackage.exe letöltéséről az [SQLpackage letöltése és telepítése](/sql/tools/sqlpackage-download/)című témakörben talál további információt. Az alábbiakban néhány mintaparancsok At SqlPackage.exe. További információt az SqlPackage.exe dokumentációjában talál.

    **DAC-csomag létrehozása**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **DAC-csomag alkalmazása**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>SQL-tábla és eljárás létrehozása a vízjelszintek tárolására és frissítésére

A vízjel tábla tárolására használják az utolsó időbélyeg, amelyre az adatok már szinkronizálva az Azure Storage-szal. A Transact-SQL (T-SQL) tárolt eljárás segítségével minden szinkronizálás után frissítheti a vízjeltáblát.

Futtassa ezeket a parancsokat az SQL Database Edge példányon:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Adatfeldolgozó folyamat létrehozása

Ebben a szakaszban egy Azure Data Factory-folyamatot hoz létre az Azure Blob storage-ba való szinkronizálásához az Azure SQL Database Edge egy táblájából.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Adatgyár létrehozása a Data Factory felhasználói felületének használatával

Hozzon létre egy adat-gyárat az [oktatóanyag](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)utasításainak követésével.

### <a name="create-a-data-factory-pipeline"></a>Adatfeldolgozó folyamat létrehozása

1. A Data Factory felhasználói felületének **Kezdési** lapon válassza a **Folyamat létrehozása lehetőséget.**

    ![Adatfeldolgozó folyamat létrehozása](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. A folyamat **Tulajdonságok** ablakának **Általános** lapján adja meg a névhez a **PeriodicSync** értéket.

3. Adja hozzá a külső tevékenységet a régi vízjel értékének leéséhez. A **Tevékenységek** ablaktáblán bontsa ki az **Általános** elemet, és húzza a **lookup** tevékenységet a folyamattervező felületére. Módosítsa a tevékenység nevét **OldWatermark**névre.

    ![A régi vízjel-keresmény hozzáadása](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Váltson a **Beállítások** lapra, és válassza az **Új** **forrásadatkészlethez lehetőséget.** Most hozzon létre egy adatkészletet, amely a vízjeltáblában lévő adatokat ábrázolja. Ez a tábla tartalmazza az előző másolási művelet során használt régi küszöbértéket.

5. Az **Új adatkészlet** ablakban válassza az **Azure SQL Server**elemet, majd a **Folytatás**lehetőséget.  

6. Az adatkészlet **Tulajdonságainak beállítása** ablakában a **Név**csoportban írja be a **VízjelAdathalmaz mezőbe.**

7. Csatolt **szolgáltatás**esetén válassza az **Új**lehetőséget, majd hajtsa végre az alábbi lépéseket:

    1. A **Név csoportban**írja be az **SQLDBEdgeLinkedService értéket.**

    2. A **Kiszolgáló neve mezőbe**írja be az SQL Database Edge kiszolgáló adatait.

    3. Válassza ki az **adatbázis nevét** a listából.

    4. Adja meg **a felhasználónevét** és **a jelszavát.**

    5. Az SQL Database Edge-példányhoz való csatlakozás teszteléséhez válassza a **Kapcsolat tesztelése**lehetőséget.

    6. Kattintson a **Létrehozás** gombra.

    ![Társított szolgáltatás létrehozása](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Válassza **az OK gombot.**

8. A **Beállítások** lapon válassza a **Szerkesztés**lehetőséget.

9. A **Kapcsolat** lapon válassza a **[dbo].[ vízjel]** a **táblázathoz.** Ha meg szeretné tekinteni az adatokat a táblázatban, válassza az **Adatok megtekintése**lehetőséget.

10. Váltson át a folyamatszerkesztőre a folyamatfül tetején, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával. A Lookup tevékenység tulajdonságablakában ellenőrizze, hogy a **Vízjeladatkészlet** ki van-e jelölve a **Forrás adatkészlet** listában.

11. A **Tevékenységek** ablaktáblán bontsa ki az **Általános** elemet, és húzzon egy másik **keresel tevékenységet** a folyamattervező felületére. Állítsa a nevet **NewWatermark** névre a tulajdonságok **ablakának Általános** lapján. Ez a lookup tevékenység leveszi az új vízjel értéket a forrásadatokat tartalmazó táblából, hogy átmásolhassa a célhelyre.

12. A második helykeresési tevékenység tulajdonságablakában váltson a **Beállítások** lapra, és válassza az **Új** lehetőséget, ha olyan adatkészletet szeretne létrehozni, amely az új vízjel értéket tartalmazó forrástáblára mutat.

13. Az **Új adatkészlet** ablakban válassza az **SQL Database Edge példányt**, majd a Continue **(Folytatás)** lehetőséget.

    1. A **Tulajdonságok beállítása** ablak **Név területén**írja be a **SourceDataset parancsot.** A **Csatolt szolgáltatás csoportban**válassza az **SQLDBEdgeLinkedService lehetőséget.**

    2. A **Tábla**csoportban jelölje ki a szinkronizálni kívánt táblát. Ehhez az adatkészlethez lekérdezést is megadhat, az oktatóanyag későbbi részében leírtak szerint. A lekérdezés elsőbbséget élvez az ebben a lépésben megadott táblával szemben.

    3. Válassza **az OK gombot.**

14. Váltson át a folyamatszerkesztőre a folyamatfül tetején, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával. A kereskövetési tevékenység tulajdonságablakában ellenőrizze, hogy a **SourceDataset** be van-e jelölve a **Forrás adatkészlet** listában.

15. Válassza a **Lekérdezés** lehetőséget a **Lekérdezés használata csoportban.** Frissítse a tábla nevét a következő lekérdezésben, majd írja be a lekérdezést. Csak a táblázat maximális értékét `timestamp` választja ki. Ügyeljen arra, hogy válassza az **Első sor csak**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![lekérdezés kiválasztása](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. A **Tevékenységek** ablaktáblán bontsa ki **& Átalakítás &,** és húzza a **Másolás** tevékenységet a **Tevékenységek** ablaktábláról a tervezőfelületre. Állítsa a tevékenység nevét **IncrementalCopy**.

17. A keresési tevékenységhez csatolt zöld gombot a másolási tevékenységhez húzva kapcsolja mindkét keresési tevékenységet a másolási tevékenységhez. Engedje fel az egérgombot, amikor látja, hogy a Másolás tevékenység szegélyszíne kékre változik.

18. Válassza ki a másolási tevékenységet, és győződjön meg arról, hogy a **tulajdonságok** ablakában megjelennek a tevékenység tulajdonságai.

19. Váltson a **Tulajdonságok** ablak **Forrás** lapjára, és hajtsa végre az alábbi lépéseket:

    1. A **Forrásadatkészlet** mezőben válassza a **SourceDataset (Forrásadatok beállítása) lehetőséget.**

    2. A **Lekérdezés használata**csoportban válassza a **Lekérdezés**lehetőséget.

    3. Írja be az SQL lekérdezést a **Lekérdezés** mezőbe. Íme egy mintalekérdezés:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. A **Fogadó** lapon válassza az **Új** lehetőséget az **Adatkészlet elesve**csoportban.

21. Ebben az oktatóanyagban a fogadó adattár egy Azure Blob-tároló adattár. Válassza az **Azure Blob storage**lehetőséget, majd válassza a **Folytatás** lehetőséget az **Új adatkészlet** ablakban.

22. A **Formátum kiválasztása** ablakban jelölje ki az adatok formátumát, majd kattintson a **Folytatás gombra.**

23. A **Tulajdonságok beállítása** ablak **Név területén**írja be a **SinkDataset parancsot.** A **Csatolt szolgáltatás csoportban**válassza az **Új**lehetőséget. Most hozzon létre egy kapcsolatot (egy összekapcsolt szolgáltatást) az Azure Blob storage-hoz.

24. Az **Új csatolt szolgáltatás (Azure Blob storage)** ablakban hajtsa végre az alábbi lépéseket:

    1. A **Név** mezőbe írja be az **AzureStorageLinkedService értéket.**

    2. A **Storage-fiók neve**csoportban válassza ki az Azure storage-fiókot az Azure-előfizetéshez.

    3. Tesztelje a kapcsolatot, majd válassza a **Befejezés gombot.**

25. A **Tulajdonságok beállítása** ablakban ellenőrizze, hogy az **AzureStorageLinkedService** be van-e jelölve a **Csatolt szolgáltatás csoportban.** Válassza a **Létrehozás** és **AZ OK gombot.**

26. A **Fogadó** lapon válassza a **Szerkesztés**lehetőséget.

27. Nyissa meg a SinkDataset **Kapcsolat** lapját, és hajtsa végre az alábbi lépéseket:

    1. A **Fájl elérési útja**mezőbe írja be az *asdedatasync/incrementalcopy parancsot,* ahol az *asdedatasync* a blobtároló neve, *a növekményes másolat* pedig a mappa neve. Hozza létre a tárolót, ha nem létezik, vagy használja egy meglévő nevét. Az Azure Data Factory automatikusan létrehozza a kimeneti mappa *inkrementális,* ha nem létezik. A **fájl elérési útjánál** a **Tallózás** gombot is használhatja a blobtárolóban található mappák megkereséséhez.

    2. A Fájl elérési **út** **Fájl** részében válassza a **Dinamikus tartalom hozzáadása [Alt+P]** lehetőséget, majd írja be ** @CONCATa ('Incremental-' pipeline() parancsot. RunId, '.txt')** a megnyíló ablakban. Válassza a **Finish** (Befejezés) elemet. A fájlnevet dinamikusan hozza létre a kifejezés. A folyamat minden futtatásához tartozik egy egyedi azonosító. A másolási tevékenység a futtatási azonosítót használja a fájlnév létrehozásához.

28. Váltson át a folyamatszerkesztőre a folyamatfül tetején, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával.

29. A **Tevékenységek** ablaktáblán bontsa ki az **Általános** elemet, és húzza a **Tárolt eljárás** tevékenységet a **Tevékenységek** ablaktáblából a folyamattervező felületére. Csatlakoztassa a Másolás tevékenység zöld (sikeres) kimenetét a Tárolt eljárás tevékenységhez.

30. Válassza a **tárolt eljárástevékenység lehetőséget** a folyamattervezőben, és módosítsa a nevét **SPtoUpdateWatermarkActivity névre.**

31. Váltson az **SQL-fiók** lapra, és válassza a ***QLDBEdgeLinkedService** lehetőséget a **Csatolt szolgáltatás csoportban.**

32. Váltson a **Tárolt eljárás** lapra, és hajtsa végre az alábbi lépéseket:

    1. A **Tárolt eljárás neve csoportban**válassza a **[dbo].[ usp_write_watermark]** című) című számban.

    2. A tárolt eljárásparamétereinek értékeinek megadásához válassza a **Paraméter importálása** lehetőséget, és adja meg a paraméterekhez a következő értékeket:

    |Név|Típus|Érték|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|Sztring|@{activity('OldWaterMark').output.firstRow.TableName}|

33. A folyamatbeállítások érvényesítéséhez válassza az **eszköztár Érvényesítés parancsát.** Ellenőrizze, hogy nincs-e érvényesítési hiba. A **Folyamatérvényesítési jelentés** **>>** ablak bezárásához válassza a lehetőséget.

34. Tegye közzé az entitásokat (csatolt szolgáltatások, adatkészletek és folyamatok) az Azure Data Factory szolgáltatásban az **Összes közzététele** gombra kattintva. Várjon, amíg megjelenik egy üzenet, amely megerősíti, hogy a közzétételi művelet sikeres volt.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Folyamat aktiválása ütemezés alapján

1. A folyamat eszköztárán válassza az **Eseményindító hozzáadása**lehetőséget, válassza az **Új/Szerkesztés**lehetőséget, majd az **Új**lehetőséget.

2. Nevezze el az **eseményindítót HourlySync**. A **Típus csoportban**válassza az **Ütemezés**lehetőséget. Állítsa az **Ismétlődés 1** óránként.

3. Válassza **az OK gombot.**

4. Kattintson **Az összes közzététele** gombra.

5. Válassza **az Eseményindító most**lehetőséget.

6. Váltson a bal oldali **Monitorozás** lapra. Láthatja a manuális eseményindítás által elindított folyamatfuttatás állapotát. A lista frissítéséhez kattintson a **Frissítés** gombra.

## <a name="next-steps"></a>További lépések

Az Azure Data Factory-folyamat ebben az oktatóanyagban adatokat másol egy táblából egy SQL Database Edge-példány egy helyet az Azure Blob storage óránként egyszer. Ha többet szeretne tudni arról, hogy miként használja a Data Factory-t más esetekben, olvassa el az alábbi [útmutatókat.](../data-factory/tutorial-copy-data-portal.md)
