---
title: SQL Database DAC-csomag és Stream Analytics feladatok használata az Azure SQL Database Edge használatával | Microsoft Docs
description: Tudnivalók a Stream Analytics feladatok használatáról SQL Database Edge-ben
keywords: SQL Database Edge, Stream Analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 1b6b3f4e4be9d056bc53fac2eb2f1f3fcd768085
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514991"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>SQL Database DAC-csomag és Stream Analytics-feladatok használata a SQL Database Edge használatával

Az Azure SQL Database Edge Preview egy optimalizált, a IoT és az Edge-alapú környezetekhez készült adatkezelő motor. A Microsoft SQL Server adatbázismotor legújabb verziójára épül, amely piacvezető teljesítményt, biztonsági és lekérdezés-feldolgozási képességeket biztosít. A SQL Server piacvezető kapcsolati adatbázis-felügyeleti képességeivel együtt a Azure SQL Database Edge a valós idejű elemzések és az összetett események feldolgozására szolgáló, beépített folyamatos átviteli képességgel rendelkezik.

Az Azure SQL Database Edge a SQLPackage. exe natív implementációját is biztosítja, amely lehetővé teszi, hogy a felhasználók [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -csomagot telepítsenek a SQL Database Edge telepítése során.

Az Azure SQL Database Edge két választható paramétert tesz elérhetővé az IoT Edge modul *kívánt tulajdonságok* lehetőségével.

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
| SQLPackage | Az SQL Database DAC-csomagot tartalmazó *. zip fájl Azure Blob Storage URI-ja.
| ASAJobInfo | Azure Blob Storage URI az ASA Edge-feladatokhoz. Az ASA Edge-feladatok közzétételével kapcsolatos további információkért tekintse meg az [ASA Edge-feladatok közzététele SQL Database Edge]()-ben című témakört.

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database DAC-csomagok használata a SQL Database Edge használatával

Ha SQL Database DAC-csomagot (*. dacpac) SQL Database Edge-sel szeretné használni, kövesse az alábbi lépéseket.

1. SQL Database DAC-csomag létrehozása vagy kibontása. A [DAC kibontása meglévő adatbázisból](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) című témakörben említett fogalmakat használhatja egy meglévő SQL Database DacPac létrehozásához.

2. Zip a * *. dacpac* , és töltse fel az Azure Blob Storage-fiókba. A fájlok Azure Blob Storageba való feltöltésével kapcsolatos további információkért lásd: [Blobok feltöltése, letöltése és listázása a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. SAS-aláírás létrehozása a zip-fájlhoz Azure Portal használatával. További információ: [hozzáférés delegálása közös hozzáférésű aláírásokkal (SAS)](../storage/common/storage-sas-overview.md).

4. Frissítse az SQL Database Edge-modul konfigurációját, hogy tartalmazza a DAC-csomag SAS URI-JÁT. Az SQL Database Edge-modul frissítése

    1. A Azure Portal keresse meg a IoT Hub üzembe helyezését.

    2. A bal oldali ablaktáblán kattintson a **IoT Edge**elemre.

    3. A **IoT Edge** lapon keresse meg és kattintson arra a IoT Edgera, ahol az SQL Database Edge-modul telepítve van.

    4. A *IoT Edge eszköz* eszköz lapon kattintson a **csoport beállítása elemre**. 

    5. A **modulok beállítása** lapon kattintson a *Konfigurálás* az SQL Database Edge-modulra elemre. 

    6. A **IoT Edge egyéni modulok** ablaktáblán válassza a *modulok Twin 's kívánt tulajdonságainak beállítása* elemet, majd frissítse a kívánt tulajdonságokat, hogy tartalmazza a SQLPackage beállítás URI-ját az alábbi példában látható módon. 

        > [!NOTE]
        > Az alábbi SAS URI csak illusztrációként használható. Cserélje le az URI-t az üzemelő példány tényleges URI azonosítójának helyére.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Kattintson a **Save** (Mentés) gombra.

    8. A **modulok beállítása** lapon kattintson a *tovább*gombra.

    9. A **modulok beállítása** lapon kattintson a *tovább* gombra, majd a **Küldés**gombra.

5. A modul frissítésének közzététele után a rendszer letölti a dacpac-fájlt, kicsomagolja és telepíti az SQL Database Edge-példányon.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Folyamatos átviteli feladatok használata SQL Database Edge használatával

Azure SQL Database Edge a stream Analytics futtatókörnyezet natív implementációját tartalmazza. Ez lehetővé teszi, hogy a felhasználók Azure Stream Analytics Edge-feladatot hozzanak létre, és ezt a feladatot SQL Database Edge streaming-feladatokként telepítsék. A stream Analytics Edge-feladat létrehozásához kövesse az alábbi lépéseket.

1. Az előnézeti [URL-cím](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)használatával keresse meg a Azure Portal. Ez az előzetes verziójú URL-cím lehetővé teszi, hogy a felhasználók konfigurálják SQL Database kimenetét egy stream Analytics Edge-feladatokhoz

2. Hozzon létre egy új **IoT Edge-eszközökön futó Azure stream Analytics** feladatot, és válassza ki az üzemeltetési környezet célzási **szegélyét**.

3. Adja meg a Azure Stream Analytics-feladathoz tartozó *bemenetet* és *kimenetet* . Minden SQL-kimenet (alább konfigurálva) egy, az adatbázison belüli egyetlen táblához van kötve. Ha több táblára van szükség az adatátvitelhez, több SQL Database kimenetet kell létrehoznia. Az SQL-kimenetek úgy konfigurálhatók, hogy különböző adatbázisokra mutassanak.

    *Bevitel – válassza a EdgeHub lehetőséget az Edge-feladathoz tartozó bemenetként, majd adja meg az erőforrás-információkat.*

    *Kimenet – válassza ki SQL Database kimenetként, "adja meg a SQL Database beállításokat manuálisan", és adja meg az adatbázis és a tábla konfigurációjának részleteit.*

    |Mező      | Leírás |
    |---------------|-------------|
    |Kimeneti alias | A kimeneti alias neve.|
    |Adatbázis | A SQL Database neve. Ennek érvényes adatbázisnév kell lennie, amely megtalálható az SQL Database Edge-példányon.|
    |Kiszolgálónév | Az SQL-példány neve (vagy IP-címe) és portszáma. SQL Database Edge-telepítéshez a **TCP:., a 1433** pedig a kiszolgáló neveként használható.|
    |Felhasználónév | Olyan SQL-bejelentkezési fiók, amely adatolvasóval és adatíró-hozzáféréssel rendelkezik a fent említett adatbázishoz.|
    |Jelszó | A fent említett SQL-bejelentkezési fiók jelszava.|
    |Tábla | A folyamatos átviteli feladatokhoz használandó tábla neve.|
    |Particionálás öröklése| Ez az SQL-kimeneti konfigurációs beállítás lehetővé teszi az előző lekérdezési lépés vagy bemenet particionálási sémájának öröklését. Ezzel a beállítással egy lemezes táblára írhat, és teljes mértékben párhuzamos topológiával rendelkezik a feladatokhoz, ezért várhatóan jobb átviteli sebesség jelenik meg.|
    |Köteg mérete| A köteg mérete az összes tömegesen beszúrt tranzakcióval eljuttatott rekordok maximális száma.|

    Példa bemeneti/kimeneti konfigurációra:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
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

6. A konfigurálás területen válassza a közzététel lehetőséget, majd kattintson a Közzététel gombra. Mentse az SAS URL-címét az SQL Database Edge-modullal való használatra.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>A stream Analytics Edge-feladatok üzembe helyezése az SQL Database Edge-ben

Ha a folyamatos átviteli feladatot az SQL Database Edge-modulban szeretné telepíteni, frissítse az SQL Database Edge-modul konfigurációját, hogy az a fenti lépésből tartalmazza a folyamatos átviteli feladatokhoz tartozó SAS URI-t. Az SQL Database Edge-modul frissítése

1. A Azure Portal keresse meg a IoT Hub üzembe helyezését.

2. A bal oldali ablaktáblán kattintson a **IoT Edge**elemre.

3. A **IoT Edge** lapon keresse meg és kattintson arra a IoT Edgera, ahol az SQL Database Edge-modul telepítve van.

4. A *IoT Edge eszköz* eszköz lapon kattintson a **csoport beállítása elemre**. 

5. A **modulok beállítása** lapon kattintson a *Konfigurálás* az SQL Database Edge-modulra elemre. 

6. A **IoT Edge egyéni modulok** ablaktáblán válassza a *modulok Twin 's kívánt tulajdonságainak beállítása* lehetőséget, majd frissítse a kívánt tulajdonságokat, hogy tartalmazza a ASAJobInfo beállítás URI-ját az alábbi példában látható módon. 

    > [!NOTE]
    > Az alábbi SAS URI csak illusztrációként használható. Cserélje le az URI-t az üzemelő példány tényleges URI azonosítójának helyére.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Kattintson a **Save** (Mentés) gombra.

8. A **modulok beállítása** lapon kattintson a *tovább*gombra.

9. A **modulok beállítása** lapon kattintson a *tovább* gombra, majd a **Küldés**gombra.

10. A modul frissítését követően a stream Analytics-feladatsor le lesz töltve, kibontva, és az SQL Database Edge-példányon van telepítve.

## <a name="next-steps"></a>További lépések

- A díjszabással és a rendelkezésre állással kapcsolatos részletekért lásd: [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Az előfizetéshez tartozó Azure SQL Database Edge engedélyezése iránti kérelem.
- Első lépésként tekintse meg a következőket:
  - [SQL Database Edge üzembe helyezése Azure Portal](deploy-portal.md)
