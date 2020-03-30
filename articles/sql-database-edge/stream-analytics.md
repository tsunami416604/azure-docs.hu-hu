---
title: SQL Database DAC-csomagok és Stream Analytics-feladatok használata az Azure SQL Database Edge használatával | Microsoft dokumentumok
description: Tudnivalók a Stream Analytics-feladatok használatáról az SQL Database Edge-ben
keywords: sql adatbázis széle, stream analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384158"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>SQL Database DAC-csomagok és Stream Analytics-feladatok használata AZ SQL Database Edge-el

Az Azure SQL Database Edge Preview egy optimalizált relációs adatbázis-motor, amely IOt- és peremhálózati telepítésekhez készült. A Microsoft SQL Server Database Engine legújabb verzióira épül, amely iparágvezető teljesítményt, biztonságot és lekérdezésfeldolgozási lehetőségeket biztosít. Az SQL Server iparágvezető relációs adatbázis-kezelési képességei mellett az Azure SQL Database Edge beépített streamelési képességet biztosít a valós idejű elemzéshez és az összetett eseményfeldolgozáshoz.

Az Azure SQL Database Edge az SqlPackage.exe natív implementációját is biztosítja, amely lehetővé teszi az [SQL Database DAC-csomag](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) üzembe helyezését az SQL Database Edge telepítése során.

Az Azure SQL Database Edge két `module twin's desired properties` választható paramétert tetszésszerint az IoT Edge modul beállításával elérhetővé teszi:

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
| SqlPackage | Az SQL Database DAC csomagot tartalmazó *.zip-fájl Azure Blob storage URI-ja.
| ASAJobInfo | Az Azure Blob storage URI az ASA Edge feladathoz. További információ: [Az ASA Edge feladat közzététele az SQL Database Edge számára](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)című témakörben található.

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database DAC-csomagok használata SQL Database Edge-el

Ha SQL Database DAC csomagot (*.dacpac) szeretne használni az SQL Database Edge-el, tegye az alábbi lépéseket:

1. SQL Database DAC-csomag létrehozása vagy kibontása. A [DAC-csomagok adatbázisból történő kibontása](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) című témakörben további információt talál arról, hogyan hozhat létre DAC-csomagot egy meglévő SQL Server-adatbázishoz.

2. Zip a *.dacpac és töltse fel egy Azure Blob tárfiókba. A fájlok Azure Blob storage-ba való feltöltésével kapcsolatos további információkért [lásd: Blobok feltöltése, letöltése és listázása az Azure Portalon.](../storage/blobs/storage-quickstart-blobs-portal.md)

3. Hozzon létre egy közös hozzáférési aláírást a zip-fájlhoz az Azure Portalhasználatával. További információ: [Hozzáférés delegálása megosztott hozzáférésű aláírásokkal (SAS).](../storage/common/storage-sas-overview.md)

4. Frissítse az SQL Database Edge modul konfigurációját, hogy tartalmazza a DAC-csomag megosztott hozzáférésű URI-ját. Az SQL Database Edge modul frissítéséhez tegye a következőket:

    1. Az Azure Portalon nyissa meg az IoT Hub üzembe helyezését.

    2. A bal oldali ablaktáblában válassza az **IoT Edge**lehetőséget.

    3. Az **IoT Edge** lapon keresse meg és jelölje ki az IoT peremhálózati, ahol az SQL Database Edge modul telepítve van.

    4. Az **IoT Edge device** oldalon válassza a **Modul beállítása**lehetőséget.

    5. A **Modulok beállítása** lapon válassza a **Konfigurálás** az SQL Database Edge modulon lehetőséget.

    6. Az **IoT Edge egyéni modulok** ablaktábláján válassza **a Modul ikerkívánt tulajdonságainak beállítása**lehetőséget. Frissítse a kívánt tulajdonságokat, `SQLPackage` hogy tartalmazza a beállítás URI-ját, ahogy az a következő példában látható.

        > [!NOTE]
        > A SAS URI a következő JSON csak egy példa. Cserélje le az URI-t a központi telepítésből származó tényleges URI-val.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Kattintson a **Mentés** gombra.

    8. A **Modulok beállítása** lapon válassza a **Tovább gombot.**

    9. A **Modulok beállítása** lapon válassza a **Tovább,** majd a **Küldés lehetőséget.**

5. A modul frissítésután a DAC csomagfájl letöltődik, kicsomagolva és az SQL Database Edge példányon való üzembe helyezéskor.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Streamelési feladatok használata az SQL Database Edge-el

Az Azure SQL Database Edge a streamelemzési futásidő natív implementációjával rendelkezik. Ez a megvalósítás lehetővé teszi, hogy hozzon létre egy Azure Stream Analytics peremhálózati feladat, és üzembe helyezi azt a feladatot, mint egy SQL Database Edge streamelési feladat. A Stream Analytics peremhálózati feladatának létrehozásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az Azure Portalt az előzetes [URL-cím](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)használatával. Ez az előzetes URL-cím lehetővé teszi az SQL Database kimenetének konfigurálását a Stream Analytics peremhálózati feladathoz.

2. Hozzon létre egy új **Azure Stream Analytics IoT** Edge-feladat. Válassza ki az Edge-et megcélozó üzemeltetési **környezetet.**

3. Definiáljon egy bemeneti és kimeneti azure Stream Analytics-feladathoz. Minden itt beállított SQL-kimenet egyetlen táblához van kötve az adatbázisban. Ha több táblába kell adatokat streamelnie, több SQL-adatbázis kimenetet kell létrehoznia. Az SQL kimenetek konfigurálhatók úgy, hogy különböző adatbázisokra mutassanak.

    **Beírás**. Válassza az EdgeHub-ot a peremhálózati feladat bemeneteként, és adja meg az erőforrás-adatokat.

    **Kimenet .** Válassza ki az SQL Database a kimeneti. Válassza **az SQL Database beállításainak manuális megadása lehetőséget.** Adja meg az adatbázis és a tábla konfigurációs adatait.

    |Mező      | Leírás |
    |---------------|-------------|
    |Kimeneti alias | A kimeneti alias neve.|
    |Adatbázis | Az SQL-adatbázis neve. Az SQL Database Edge-példányon található adatbázis érvényes nevének kell lennie.|
    |Kiszolgálónév | Az SQL-példány neve (vagy IP-címe) és portszámának adatai. Az SQL Database Edge központi telepítéséhez használhatja a **tcp:.,1433** a kiszolgáló nevét.|
    |Felhasználónév | SQL bejelentkezési fiók, amely adatolvasó és adatíró hozzáférést biztosít a korábban megadott adatbázishoz.|
    |Jelszó | A korábban megadott SQL bejelentkezési fiók jelszava.|
    |Tábla | A streamelési feladat kimenetére kimenetre kerülő tábla neve.|
    |Particionálás öröklése| Lehetővé teszi az előző lekérdezési lépés vagy bemenet particionálási séma öröklését. Ha ez a beállítás engedélyezve van, akkor számíthat arra, hogy jobb átviteli sebességet, amikor ír egy lemezalapú tábla, és egy teljesen párhuzamos topológiát a feladathoz.|
    |Köteg mérete| Az egyes tömeges beszúrási tranzakciókhoz küldött rekordok maximális száma.|

    Íme egy minta bemeneti/kimeneti konfiguráció:

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
    > Az Azure Stream Analytics SQL kimeneti adapterével kapcsolatos további információkért tekintse meg az [Azure Stream Analytics azure SQL-adatbázisba való kimenetét.](../stream-analytics/stream-analytics-sql-output-perf.md)

4. Adja meg az ASA-feladatlekérdezést a peremhálózati feladathoz. Ez a lekérdezés a megadott bemeneti/kimeneti aliasokat kell használnia a lekérdezés bemeneti és kimeneti neveként. További információt a [Stream Analytics lekérdezési nyelvének hivatkozása című témakörben talál.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

5. Állítsa be a tárfiók beállításait a peremhálózati feladathoz. A tárfiók a peremhálózati feladat közzétételi célként szolgál.

6. A **Konfigurálás**csoportban válassza a **Közzététel**lehetőséget, majd kattintson a **Közzététel gombra.** Mentse a SAS URI-t az SQL Database Edge modullal való használatra.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>A Stream Analytics peremhálózati feladat telepítése az SQL Database Edge-re

A streamelési feladat az SQL Database Edge modulüzembe való üzembe helyezéséhez frissítse az SQL Database Edge modul konfigurációját, hogy tartalmazza a SAS URI-t a streamelési feladathoz a korábbi lépésből. Az SQL Database Edge modul frissítése:

1. Az Azure Portalon nyissa meg az IoT Hub üzembe helyezését.

2. A bal oldali ablaktáblában válassza az **IoT Edge**lehetőséget.

3. Az **IoT Edge** lapon keresse meg és jelölje ki az IoT peremhálózati, ahol az SQL Database Edge modul telepítve van.

4. Az **IoT Edge device** oldalon válassza a **Modul beállítása**lehetőséget.

5. A **Modulok beállítása** lapon válassza a **Konfigurálás** az SQL Database Edge modulon lehetőséget.

6. Az **IoT Edge egyéni modulok** ablaktábláján válassza **a Modul ikerkívánt tulajdonságainak beállítása**lehetőséget. Frissítse a kívánt tulajdonságokat, `ASAJobInfo` hogy tartalmazza a beállítás URI-ját, ahogy az a következő példában látható.

    > [!NOTE]
    > A SAS URI a következő JSON csak egy példa. Cserélje le az URI-t a központi telepítésből származó tényleges URI-val.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Kattintson a **Mentés** gombra.

8. A **Modulok beállítása** lapon válassza a **Tovább gombot.**

9. A **Modulok beállítása** lapon válassza a **Tovább,** majd a **Küldés lehetőséget.**

10. A modul frissítésután a stream elemzési feladat fájl letöltődik, kicsomagolva, és az SQL Database Edge-példányon telepítve lesz.

## <a name="next-steps"></a>További lépések

- A díjszabással és a rendelkezésre állással kapcsolatos részletekről az [Azure SQL Database Edge című témakörben talál.](https://azure.microsoft.com/services/sql-database-edge/)
- Az Azure SQL Database Edge előfizetéshez való engedélyezése.
- Első lépések: [Az SQL Database Edge telepítése az Azure Portalon keresztül](deploy-portal.md)című témakörben található.
