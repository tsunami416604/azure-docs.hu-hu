---
title: "Azure IoT oldala az Azure Stream Analytics telepítése |} Microsoft Docs"
description: "Azure Stream Analytics peremhálózati eszköz egy modul telepítése"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 88dc0860b4455d19b05b4f4f1766210e3f3c704e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Azure Stream Analytics egy IoT peremhálózati modulként telepítése – előzetes

Az IoT-eszközök nagy mennyiségű adat hozhat létre. Néha ezek az adatok elemzése vagy a felhőben a feltöltött adatok méretének csökkentésére vagy hajtható végre egyet az oda-vissza késését megszüntetéséhez elérése előtt feldolgozott rendelkezik.

[Az Azure Stream Analytics] [ azure-stream] (ASA) társítását strukturált lekérdezési szintaxist tartalmaz a felhő- és IoT oldal adatelemzéshez eszközök. Az IoT-oldal ASA kapcsolatos további információkért lásd: [ASA dokumentáció](../stream-analytics/stream-analytics-edge.md).

Ez az oktatóanyag bemutatja, hogyan létrehozása az Azure Stream Analytics-feladat, és a központi telepítés egy IoT peremhálózati eszközön ahhoz, hogy közvetlenül az eszközön helyi telemetriai adatfolyam feldolgozásához, és hozhat létre a meghajtó azonnali művelet az eszközön.  Nincsenek ebben az oktatóanyagban érintett két modulok. Egy szimulált hőmérséklet-érzékelő modul (tempSensor), amely 20 hőmérséklet adatokat és 120 fok, 5 másodpercenként 1 eggyel állít elő, és egy nagyobb, mint 100 fok hőmérsékletek kiszűrő ASA modul. Az ASA modul is visszaállítja a tempSensor, 30 másodperces átlagos 100 elérésekor.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az oldal adatfeldolgozásra történő ASA feladat létrehozása
> * Csatlakozás az új ASA feladatot más IoT peremhálózati modulok
> * Az ASA feladat IoT peremhálózati eszköz telepítése

## <a name="prerequisites"></a>Előfeltételek

* Az IoT-központ 
* Az eszközt, hogy létrehozása és konfigurálása a gyors üzembe helyezés vagy telepítése Azure IoT peremhálózati om a szimulált eszköz [Windows] [ lnk-tutorial1-win] és [Linux] [ lnk-tutorial1-lin].
* Az IoT-peremhálózati eszközön docker
    * [Docker telepítése Windows] [ lnk-docker-windows] , és győződjön meg arról, hogy fut-e.
    * [Telepítse a Docker Linux] [ lnk-docker-linux] , és győződjön meg arról, hogy fut-e.
* Python 2.7.x az IoT-peremhálózati eszközön
    * [Python 2.7 telepítése Windows][lnk-python].
    * A legtöbb Linux terjesztésekről, beleértve az Ubuntu, már telepített Python 2.7.  Győződjön meg arról, hogy a pip telepítve van a következő paranccsal: `sudo apt-get install python-pip`.

> [!NOTE]
> Megjegyzés: az eszköz kapcsolati karakterlánc és az IoT peremhálózati eszköz azonosítója ehhez az oktatóanyaghoz szükség lesz.

IoT peremhálózati kihasználja a gyors telepítéshez előre elkészített Azure szolgáltatás IoT peremhálózati modulok és Azure Stream Analytics (ASA) egy ilyen modul. ASA feladat létrehozása a portálon, majd az IoT-központ portálra, ahol üzembe egy IoT-Edge modul származnak.  

Azure Stream Analytics további információkért tekintse meg a **áttekintése** szakasza a [Stream Analytics dokumentációja][azure-stream].

## <a name="create-an-asa-job"></a>ASA feladat létrehozása

Ebben a szakaszban hozzon létre egy Azure Stream Analytics-feladat az IoT hub adatait, az elküldött telemetriai adatait kéri le az eszközt, és az eredmények továbbításához egy Azure Storage tárolója (BLOB). További információkért lásd: a **áttekintése** szakasza a [Stream Analytics dokumentációja][azure-stream]. 

> [!NOTE]
> Adja meg a ASA feladat kimenetként használható a végpont egy Azure Storage-fiók szükséges. Az alábbi példában a BLOB storage típust használ.  További információkért lásd: a **Blobok** szakasza a [Azure Storage-dokumentációt][azure-storage].

1. Az Azure-portálon lépjen a **hozzon létre egy erőforrás -> tárolási**, kattintson a **láthatja az összes**, és kattintson a **tárfiók - blob, a fájl, a tábla, a várólista**.

2. Adja meg a tárfiók nevét, és válassza ki az IoT Hub tároló ugyanazon a helyen. Kattintson a **Create** (Létrehozás) gombra. Jegyezze fel a későbbi használatra.

    ![új tárfiók][1]

3. Az Azure-portálon lépjen az újonnan létrehozott tárfiók. Kattintson a **keresse meg a blobok** alatt **Blob szolgáltatás**. 
4. Hozzon létre egy új tároló ASA modul adatok tárolására. Állítsa a hozzáférési szintet _tároló_. Kattintson az **OK** gombra.

    ![tárolási beállítások][10]

5. Az Azure-portálon lépjen a **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** válassza **Stream Analytics-feladat**.

6. Adjon meg egy nevet, kattintson a **peremhálózati** üzemeltetési környezetben, és a többi alapértelmezett értéket használja.  Kattintson a **Create** (Létrehozás) gombra.

    >[!NOTE]
    >Jelenleg nincsenek IoT oldal ASA feladatok támogató régiók száma korlátozott. Jelölje ki a következők egyikét az helyeként: USA középső RÉGIÓJA, Nyugat középső Régiójában, északi középső Régiójában, USA keleti régiója, Kanada keleti, kelet-Japánban, Kelet-Ázsiában, Észak-Európa, Egyesült Királyság déli, Kelet-Ausztrália, Dél-Brazília. 

    ![ASA létrehozása][5]

2. Használja fel a létrehozott feldolgozás alatt **feladat topológia**, jelölje be **bemenetek**, kattintson a **Hozzáadás**.

3. Adjon meg nevet `temperature`, válassza a **adatfolyam** a forrástípus és a többi paraméter az Alapértelmezések használata. Kattintson a **Create** (Létrehozás) gombra.

    ![ASA bemeneti][2]

    > [!NOTE]
    > További bemenetek IoT peremhálózati meghatározott végpontokhoz tartalmazhatnak.

4. A **feladat topológia**, jelölje be **kimenetek**, kattintson a **Hozzáadás**.

5. Adjon meg nevet `alert` és használhatja az alapértelmezett értékeket. Kattintson a **Create** (Létrehozás) gombra.

    ![ASA kimeneti][3]

6. A **feladat topológia**, jelölje be **lekérdezés**, és írja be a következő:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>A feladat telepítése

Most már készen áll a ASA feladat az IoT-peremhálózati eszközön telepítéséhez.

1. Az Azure portálon az IoT-központot, navigáljon a **IoT peremhálózati (előzetes verzió)** , és nyissa meg a *{deviceId}*a panelen.

1. Válassza ki **modulok beállítása**, majd jelölje be **importálási Azure szolgáltatás IoT peremhálózati modul**.

1. Válassza ki az előfizetés és a ASA peremhálózati feladatot, amely létrehozta. Ezután válassza ki a tárfiók. Kattintson a **Save** (Mentés) gombra.

    ![Állítsa a modul][6]

1. Kattintson a **IoT peremhálózati modul hozzáadása** a hőmérséklet-érzékelő modul hozzá lesz. Adja meg _tempSensor_ olyan név esetén `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` a kép URL-címe. Hagyja változatlanul az egyéb beállításokat, és kattintson a **mentése**.

    ![hőmérséklet-modul][11]

1. Másolja a ASA modul nevét. Kattintson a **következő** útvonalak konfigurálása.

1. Másolja az alábbi **útvonalak**.  Cserélje le _{moduleName}_ másolt modul nevű:

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

1. Kattintson a **Tovább** gombra.

1. Az a **felülvizsgálati sablonja** lépésre, a **Submit**.

1. Térjen vissza az eszköz részleteit megjelenítő oldalra, és kattintson a **frissítése**.  Láthatja, hogy az új _{moduleName}_ modul fut, valamint a **IoT peremhálózati ügynök** modul és a **peremhálózati IoT hub**.

    ![a modul kimenete][7]

## <a name="view-data"></a>Adatok megtekintése

Most lépjen a IoT peremhálózati eszközön tekintse meg a a ASA és a tempSensor modul közötti interakció.

1. A parancssorba konfigurálása a futásidejű a IoT peremhálózati eszköz kapcsolati karakterlánc:

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. Futtassa a parancsot a futtatókörnyezet elindításához:

    ```cmd/sh
    iotedgectl start  
    ```

1. Futtassa a parancsot a futó modulok:

    ```cmd/sh
    docker ps  
    ```

    ![docker kimeneti][8]

1. Futtassa a parancsot a rendszer naplóit, és a metrikai adatok megjelenítéséhez. A modul neve a fenti használja:

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![docker-napló][9]

1. Az Azure portálon, a tárfiókban lévő alatt **Blob szolgáltatás**, kattintson a **keresse meg a blobok**, jelölje ki a tárolót, és válassza ki az újonnan létrehozott JSON-fájlt.

1. Kattintson a **letöltése** és az eredmények megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban konfigurálta az Azure Storage-tároló és a Streaming Analytics-feladat az IoT peremhálózati eszközön lévő adatok elemzésére.  Majd betölteni egy egyéni ASA modul áthelyezni az adatokat az eszközről, az adatfolyam keresztül történő letölthető BLOB.  Egyéb oktatóanyagok további megtekintéséhez, hogyan hozhat létre Azure IoT peremhálózati az üzleti megoldások is történik.

> [!div class="nextstepaction"] 
> [Az Azure Machine Learning modell rendszerbe állítása modulként][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/