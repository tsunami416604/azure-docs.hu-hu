---
title: SQL Database DAC-csomagok és Stream Analytics feladatok használata az Azure SQL Database Edge használatával | Microsoft Docs
description: Tudnivalók a Stream Analytics feladatok használatáról SQL Database Edge-ben
keywords: SQL Database Edge, Stream Analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74384158"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>SQL Database DAC-csomagok és Stream Analytics feladatok használata a SQL Database Edge használatával

Az Azure SQL Database Edge Preview egy optimalizált, a IoT és az Edge-alapú környezetekhez készült adatkezelő motor. Ez az Microsoft SQL Server adatbázismotor legújabb verziójára épül, amely piacvezető teljesítményt, biztonságot és lekérdezés-feldolgozási képességeket biztosít. A SQL Server piacvezető kapcsolati adatbázis-felügyeleti képességeivel együtt a Azure SQL Database Edge a valós idejű elemzések és az összetett események feldolgozására szolgáló, beépített folyamatos átviteli képességgel rendelkezik.

Az Azure SQL Database Edge a SqlPackage. exe natív implementációját is biztosítja, amely lehetővé teszi, hogy [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -csomagot helyezzen üzembe a SQL Database Edge telepítése során.

Azure SQL Database Edge két választható paramétert tesz elérhetővé `module twin's desired properties` a IoT Edge modul beállításával:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Mező | Leírás |
|------|-------------|
| SqlPackage | Az SQL Database DAC-csomagot tartalmazó *. zip fájl Azure Blob Storage URI azonosítója.
| ASAJobInfo | Az ASA Edge-feladatokhoz tartozó Azure Blob Storage URI-ja. További információ: [ASA Edge-feladatok közzététele SQL Database Edge-hez](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database DAC-csomagok használata a SQL Database Edge használatával

SQL Database DAC-csomag (*. dacpac) SQL Database Edge-vel való használatához hajtsa végre a következő lépéseket:

1. SQL Database DAC-csomag létrehozása vagy kibontása. Lásd: [DAC kibontása egy adatbázisból](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) egy meglévő SQL Server adatbázis DAC-csomagjának létrehozásával kapcsolatban.

2. Zip a *. dacpac, és töltse fel egy Azure Blob Storage-fiókba. A fájlok Azure Blob Storage-ba való feltöltésével kapcsolatos további információkért lásd: [Blobok feltöltése, letöltése és listázása a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Közös hozzáférési aláírás létrehozása a zip-fájlhoz a Azure Portal használatával. További információ: [hozzáférés delegálása közös hozzáférésű aláírásokkal (SAS)](../storage/common/storage-sas-overview.md).

4. Frissítse az SQL Database Edge-modul konfigurációját, hogy tartalmazza a DAC-csomag közös hozzáférésű URI-JÁT. Az SQL Database Edge-modul frissítéséhez hajtsa végre a következő lépéseket:

    1. A Azure Portal lépjen a IoT Hub üzemelő példányra.

    2. A bal oldali ablaktáblán válassza a **IoT Edge**lehetőséget.

    3. A **IoT Edge** lapon keresse meg és válassza ki azt a IoT-szegélyt, ahol az SQL Database Edge-modul telepítve van.

    4. A **IoT Edge eszköz** eszköz lapon válassza a **modul beállítása**lehetőséget.

    5. A **modulok beállítása** lapon válassza a **Konfigurálás** az SQL Database Edge-modulban lehetőséget.

    6. A **IoT Edge egyéni modulok** ablaktáblán válassza a **modul Twin-k kívánt tulajdonságainak beállítása**lehetőséget. Frissítse a kívánt tulajdonságokat, hogy tartalmazza a `SQLPackage` beállítás URI-ját, ahogy az az alábbi példában is látható.

        > [!NOTE]
        > A következő JSON SAS URI-ja csupán egy példa. Cserélje le az URI-t az üzemelő példány tényleges URI azonosítójának helyére.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Kattintson a **Mentés** gombra.

    8. A **modulok beállítása** lapon válassza a **tovább**lehetőséget.

    9. A **modulok beállítása** lapon válassza a **tovább** , majd a **Küldés**lehetőséget.

5. A modul frissítését követően a DAC-csomagfájl le van töltve, kibontva, és az SQL Database Edge-példányon lesz telepítve.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Folyamatos átviteli feladatok használata SQL Database Edge használatával

Azure SQL Database Edge a stream Analytics futtatókörnyezet natív implementációját tartalmazza. Ez a megvalósítás lehetővé teszi, hogy egy Azure Stream Analytics Edge-feladatot hozzon létre, és ezt a feladatot SQL Database Edge streaming-feladatokként telepítse. Stream Analytics Edge-feladatok létrehozásához hajtsa végre a következő lépéseket:

1. Az előnézeti [URL-cím](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)használatával lépjen a Azure Portal. Ez az előzetes verziójú URL-cím lehetővé teszi Stream Analytics Edge-feladatok SQL Database kimenetének konfigurálását.

2. Hozzon létre egy új **IoT Edge-eszközökön futó Azure stream Analytics** feladatot. Válassza ki az **Edge**-t tároló üzemeltetési környezetet.

3. Adja meg a Azure Stream Analytics-feladathoz tartozó bemenetet és kimenetet. Az itt beállított összes SQL-kimenet az adatbázis egyetlen táblájához van kötve. Ha több táblázatba kell továbbítania az adatokat, több SQL Database kimenetet kell létrehoznia. Beállíthatja, hogy az SQL-kimenetek különböző adatbázisokra mutassanak.

    **Bemenet**. Válassza a EdgeHub lehetőséget az Edge-feladathoz tartozó bemenetként, és adja meg az erőforrás-információkat.

    **Kimenet**. Válassza ki SQL Database a kimenetként. Válassza a **SQL Database beállításainak manuális**megadása lehetőséget. Adja meg az adatbázis és a tábla konfigurációjának részleteit.

    |Mező      | Leírás |
    |---------------|-------------|
    |Kimeneti alias | A kimeneti alias neve.|
    |Adatbázis | Az SQL-adatbázis neve. Az SQL Database Edge-példányon található adatbázis érvényes nevének kell lennie.|
    |Kiszolgálónév | Az SQL-példány neve (vagy IP-címe) és portszáma. SQL Database Edge-telepítés esetén a 1433 a (z **) TCP:.,** a kiszolgáló nevét használhatja.|
    |Felhasználónév | Olyan SQL-bejelentkezési fiók, amely adatolvasóval és adatírókkal fér hozzá a korábban megadott adatbázishoz.|
    |Jelszó | A korábban megadott SQL-bejelentkezési fiók jelszava.|
    |Tábla | A folyamatos átviteli feladatokhoz használandó tábla neve.|
    |Particionálás öröklése| Engedélyezi az előző lekérdezési lépés vagy bemenet particionálási sémájának öröklését. Ha ez a beállítás engedélyezve van, a lemezes táblára való írásnál nagyobb átviteli sebesség várható, és a feladatokhoz teljes mértékben párhuzamos topológia szükséges.|
    |Köteg mérete| Az összes tömeges beszúrási tranzakcióval ellátott rekordok maximális száma.|

    Íme egy példa bemeneti/kimeneti konfiguráció:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > A Azure Stream Analytics SQL kimeneti adapterével kapcsolatos további információkért lásd: [Azure stream Analytics kimenet Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Adja meg az Edge-feladatokhoz tartozó ASA-feladatok lekérdezését. A lekérdezésnek a megadott bemeneti/kimeneti aliasokat kell használnia a lekérdezés bemeneti és kimeneti neveként. További információ: [stream Analytics lekérdezés nyelvi leírása](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Adja meg az Edge-feladatokhoz tartozó Storage-fiók beállításait. A Storage-fiók a peremhálózati feladatokhoz tartozó közzétételi célként van használatban.

6. A **Konfigurálás**területen válassza a **Közzététel**lehetőséget, majd kattintson a **Közzététel** gombra. Mentse az SAS URI-t az SQL Database Edge-modullal való használatra.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Az Stream Analytics Edge-feladatok üzembe helyezése SQL Database Edge-ben

Ha a folyamatos átviteli feladatot az SQL Database Edge-modulban szeretné telepíteni, frissítse az SQL Database Edge-modul konfigurációját, hogy az a korábbi lépésből tartalmazza a folyamatos átviteli feladatokhoz tartozó SAS URI-t. Az SQL Database Edge-modul frissítése:

1. A Azure Portal lépjen a IoT Hub üzemelő példányra.

2. A bal oldali ablaktáblán válassza a **IoT Edge**lehetőséget.

3. A **IoT Edge** lapon keresse meg és válassza ki azt a IoT-szegélyt, ahol az SQL Database Edge-modul telepítve van.

4. A **IoT Edge eszköz** eszköz lapon válassza a **modul beállítása**lehetőséget.

5. A **modulok beállítása** lapon válassza a **Konfigurálás** az SQL Database Edge-modulban lehetőséget.

6. A **IoT Edge egyéni modulok** ablaktáblán válassza a **modul Twin-k kívánt tulajdonságainak beállítása**lehetőséget. Frissítse a kívánt tulajdonságokat, hogy tartalmazza a `ASAJobInfo` beállítás URI-ját, ahogy az az alábbi példában is látható.

    > [!NOTE]
    > A következő JSON SAS URI-ja csupán egy példa. Cserélje le az URI-t az üzemelő példány tényleges URI azonosítójának helyére.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Kattintson a **Mentés** gombra.

8. A **modulok beállítása** lapon válassza a **tovább**lehetőséget.

9. A **modulok beállítása** lapon válassza a **tovább** , majd a **Küldés**lehetőséget.

10. A modul frissítése után a stream Analytics-feladatsor le van töltve, kibontva, és az SQL Database Edge-példányon lesz telepítve.

## <a name="next-steps"></a>További lépések

- A díjszabással és a rendelkezésre állással kapcsolatos részletekért lásd: [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Kérelem engedélyezése Azure SQL Database Edge számára az előfizetéshez.
- Első lépésként lásd: [SQL Database Edge üzembe helyezése Azure Portal használatával](deploy-portal.md).
