---
title: Azure Stream Analytics Edge-feladatok használata az Azure SQL Edge használatával (előzetes verzió)
description: Tudnivalók a Stream Analytics feladatok használatáról az Azure SQL Edge-ben (előzetes verzió)
keywords: SQL Edge, Stream Analytics,
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b57cb8cae80381a6c2cd88358dd9284ba56c919
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597316"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Azure Stream Analytics feladatok használata az SQL Edge használatával

Az Azure SQL Edge (előzetes verzió) egy optimalizált, a IoT és a peremhálózati környezetekben üzemelő üzemelő adatbázis motorja. Ez az Microsoft SQL Server adatbázismotor legújabb verziójára épül, amely piacvezető teljesítményt, biztonságot és lekérdezés-feldolgozási képességeket biztosít. Az Azure SQL Edge a SQL Server piacvezető, az adatbázis-kezelési képességeivel együtt biztosítja a valós idejű elemzési és összetett események feldolgozására szolgáló, beépített folyamatos átviteli képességet.

Az Azure SQL Edge a stream Analytics futtatókörnyezet natív implementációját tartalmazza. Ez a megvalósítás lehetővé teszi, hogy létrehoz egy Azure Stream Analytics Edge-feladatot, és üzembe helyezi ezt a feladatot SQL Edge streaming-feladatokként. Azure Stream Analytics feladatok telepíthetők az SQL Edge-be az `module twin's desired properties` SQL Edge-modul opcióján keresztül elérhető ASAJobInfo paraméterrel:

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
| ASAJobInfo | Az ASA Edge-feladatokhoz tartozó Azure Blob Storage URI-ja.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Azure Stream Analytics Edge-feladatok létrehozása

1. Nyissa meg az Azure Portalt.

2. Hozzon létre egy új **IoT Edge-eszközökön futó Azure stream Analytics** feladatot. Válassza ki az **Edge**-t tároló üzemeltetési környezetet.

3. Adja meg a Azure Stream Analytics-feladathoz tartozó bemenetet és kimenetet. Az itt beállított összes SQL-kimenet az adatbázis egyetlen táblájához van kötve. Ha több táblázatba kell továbbítania az adatokat, több SQL Database kimenetet kell létrehoznia. Beállíthatja, hogy az SQL-kimenetek különböző adatbázisokra mutassanak.

    **Bemenet**. Válassza a EdgeHub lehetőséget az Edge-feladathoz tartozó bemenetként, és adja meg az erőforrás-információkat.

    **Kimenet**. Válassza ki SQL Database a kimenetként. Válassza a **SQL Database beállításainak manuális**megadása lehetőséget. Adja meg az adatbázis és a tábla konfigurációjának részleteit.

    |Mező      | Leírás |
    |---------------|-------------|
    |Kimeneti alias | A kimeneti alias neve.|
    |Adatbázis | Az SQL-adatbázis neve. Érvényes nevet kell adni az SQL Edge-példányon található adatbázisnak.|
    |Kiszolgálónév | Az SQL-példány neve (vagy IP-címe) és portszáma. SQL Edge-telepítés esetén a kiszolgálónévhez használhatja a **TCP:., 1433** nevet.|
    |Felhasználónév | Olyan SQL-bejelentkezési fiók, amely adatolvasóval és adatírókkal fér hozzá a korábban megadott adatbázishoz.|
    |Jelszó | A korábban megadott SQL-bejelentkezési fiók jelszava.|
    |Táblázat | A folyamatos átviteli feladatokhoz használandó tábla neve.|
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

6. A **Konfigurálás**területen válassza a **Közzététel**lehetőséget, majd kattintson a **Közzététel** gombra. Mentse az SAS URI-t az SQL Edge-modullal való használatra.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Azure Stream Analytics Edge-feladatok üzembe helyezése az SQL Edge-ben

Ha az SQL Edge-modulban szeretné üzembe helyezni a folyamatos átviteli feladatot, frissítse az SQL Edge-modul konfigurációját, hogy az a korábbi lépésből tartalmazza a folyamatos átviteli feladatokhoz tartozó SAS URI-t. Az SQL Edge-modul frissítése:

1. A Azure Portal lépjen a IoT Hub üzemelő példányra.

2. A bal oldali ablaktáblán válassza a **IoT Edge**lehetőséget.

3. A **IoT Edge** lapon keresse meg és válassza ki azt a IoT Edge, amelyben az SQL Edge-modul telepítve van.

4. A **IoT Edge eszköz** eszköz lapon válassza a **modul beállítása**lehetőséget.

5. A **modulok beállítása** lapon válassza a **Konfigurálás** az SQL Edge-modulban lehetőséget.

6. A **IoT Edge egyéni modulok** ablaktáblán válassza a **modul Twin-k kívánt tulajdonságainak beállítása**lehetőséget. Frissítse a kívánt tulajdonságokat, hogy tartalmazza a beállítás URI-JÁT `ASAJobInfo` , ahogy az az alábbi példában is látható.

    > [!NOTE]
    > A következő JSON SAS URI-ja csupán egy példa. Cserélje le az URI-t az üzemelő példány tényleges URI azonosítójának helyére.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Kattintson a **Mentés** gombra.

8. A **modulok beállítása** lapon válassza a **tovább**lehetőséget.

9. A **modulok beállítása** lapon válassza a **tovább** , majd a **Küldés**lehetőséget.

10. A modul frissítése után a rendszer letölti a stream Analytics-feladatot tartalmazó fájlt, kicsomagolja és telepíti az SQL Edge-példányon.

## <a name="next-steps"></a>További lépések

- [Az SQL Edge üzembe helyezése Azure Portalon keresztül](deploy-portal.md).

- [Adatfolyam-adatforrások](stream-data.md)

- [Gépi tanulás és AI a ONNX az SQL Edge-ben (előzetes verzió)](onnx-overview.md)
