---
title: "Azure IoT oldala az Azure Stream Analytics telepítése |} Microsoft Docs"
description: "Azure Stream Analytics peremhálózati eszköz egy modul telepítése"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Azure Stream Analytics egy IoT peremhálózati modulként telepítése – előzetes

Az IoT-eszközök nagy mennyiségű adat hozhat létre. Néha ezek az adatok elemzése vagy a felhőben a feltöltött adatok méretének csökkentésére vagy hajtható végre egyet az oda-vissza késését megszüntetéséhez elérése előtt feldolgozott rendelkezik.

IoT peremhálózati kihasználja a gyors telepítéshez előre elkészített Azure szolgáltatás IoT peremhálózati modulok és [Azure Stream Analytics] [ azure-stream] (ASA) az egyik ilyen modul. ASA feladat létrehozása a portálon, majd az IoT-központ portálra, ahol üzembe egy IoT-Edge modul származnak.  

Az Azure Stream Analytics metaadataik strukturált lekérdezési szintaxist tartalmaz a felhő- és IoT oldal adatelemzéshez eszközök. Az IoT-oldal ASA kapcsolatos további információkért lásd: [ASA dokumentáció](../stream-analytics/stream-analytics-edge.md).

Ez az oktatóanyag bemutatja, hogyan létrehozása az Azure Stream Analytics-feladat, és a központi telepítés egy IoT peremhálózati eszközön ahhoz, hogy közvetlenül az eszközön helyi telemetriai adatfolyam feldolgozásához, és hozhat létre a meghajtó azonnali művelet az eszközön.  Nincsenek ebben az oktatóanyagban érintett két modulok. A szimulált hőmérséklet-érzékelő modulok (tempSensor) hoz létre, és 120 fok, 5 másodpercenként 1 eggyel hőmérséklet adatok 20. A Stream Analytics modul alaphelyzetbe állítása a tempSensor, 30 másodperces átlagos 70 elérésekor. Éles környezetben használja előfordulhat, hogy ezt a funkciót le egy gép vagy megelőző intézkedéseket a hőmérséklet veszélyes szintek elérésekor. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az oldal adatfeldolgozásra történő ASA feladat létrehozása
> * Csatlakozás az új ASA feladatot más IoT peremhálózati modulok
> * Az ASA feladat IoT peremhálózati eszköz telepítése

## <a name="prerequisites"></a>Előfeltételek

* Az IoT-központ 
* Az eszközt, hogy létrehozása és konfigurálása a gyors üzembe helyezés vagy telepítése Azure IoT peremhálózati om a szimulált eszköz [Windows] [ lnk-tutorial1-win] és [Linux] [ lnk-tutorial1-lin]. Hasznos tudnivalók az eszköz kapcsolat kulcs és az eszközazonosító. 
* Az IoT-peremhálózati eszközön futó docker
    * [Docker telepítése Windows][lnk-docker-windows]
    * [Docker telepítése Linux rendszerre][lnk-docker-linux]
* Python 2.7.x az IoT-peremhálózati eszközön
    * [Python 2.7 telepítése Windows][lnk-python].
    * A legtöbb Linux terjesztésekről, beleértve az Ubuntu, már telepített Python 2.7.  Győződjön meg arról, hogy a pip telepítve van a következő paranccsal: `sudo apt-get install python-pip`.


## <a name="create-an-asa-job"></a>ASA feladat létrehozása

Ebben a szakaszban hozzon létre egy Azure Stream Analytics-feladat az IoT hub adatait, az elküldött telemetriai adatait kéri le az eszközt, és az eredmények továbbításához egy Azure Storage tárolója (BLOB). További információkért lásd: a **áttekintése** szakasza a [Stream Analytics dokumentációja][azure-stream]. 

### <a name="create-a-storage-account"></a>Create a storage account

Adja meg a ASA feladat kimenetként használható a végpont egy Azure Storage-fiók szükséges. Az alábbi példában a BLOB storage típust használ.  További információkért lásd: a **Blobok** szakasza a [Azure Storage-dokumentációt][azure-storage].

1. Az Azure-portálon lépjen a **hozzon létre egy erőforrást** , és írja be `Storage account` a keresési sávon. Válassza ki **tárfiók - blob, a fájl, a tábla, a várólista**.

2. Adja meg a tárfiók nevét, és válassza ki az ugyanazon a helyen, ahol az IoT hub. Kattintson a **Create** (Létrehozás) gombra. Ne felejtse el a nevet a későbbi használatra.

    ![új tárfiók][1]

3. Nyissa meg az újonnan létrehozott tárfiók. Kattintson a **keresse meg a blobok**. 
4. Hozzon létre egy új tároló ASA modul adatok tárolására. Állítsa a hozzáférési szintet **tároló**. Kattintson az **OK** gombra.

    ![tárolási beállítások][10]

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az Azure-portálon lépjen a **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** válassza **Stream Analytics-feladat**.

2. Adjon meg egy nevet, kattintson a **peremhálózati** üzemeltetési környezetben, és a többi alapértelmezett értéket használja.  Kattintson a **Create** (Létrehozás) gombra.

    >[!NOTE]
    >Jelenleg IoT peremhálózati ASA egy feladat nem támogatottak 2 USA nyugati régiója régióban. 

3. Használja fel a létrehozott feladat. Válassza ki **bemenetek** kattintson **Hozzáadás**.

4. Adja meg a bemeneti áljel `temperature`, a forrás típusa **adatfolyam**, és használja a többi paraméter alapértelmezett értéke. Kattintson a **Create** (Létrehozás) gombra.

   ![ASA bemeneti](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Válassza ki **kimenetek** kattintson **Hozzáadás**.

6. Adja meg a kimeneti alias `alert`, és használja a többi paraméter alapértelmezett értéke. Kattintson a **Create** (Létrehozás) gombra.

   ![ASA kimeneti](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. Válassza ki **lekérdezés**.
8. Az alapértelmezett szöveg cseréje a következő lekérdezést:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```
9. Kattintson a **Save** (Mentés) gombra.

## <a name="deploy-the-job"></a>A feladat telepítése

Most már készen áll a ASA feladat az IoT-peremhálózati eszközön telepítéséhez.

1. Az Azure portálon az IoT-központot, navigáljon a **IoT peremhálózati (előzetes verzió)** , és nyissa meg az IoT-peremhálózati eszköz részleteit megjelenítő oldalon.
1. Válassza ki **modulok beállítása**.
1. Ha korábban telepítette a tempSensor modul ezen az eszközön, akkor előfordulhat, hogy adatokkal való automatikus feltöltés. Ha nem, a modul hozzá lesz adva a következő lépésekkel:
   1. Kattintson a **IoT peremhálózati modul hozzá lesz adva**
   1. Adja meg `tempSensor` névként, és `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` a lemezkép URI-hoz. 
   1. Hagyja változatlanul az egyéb beállításokat, és kattintson a **mentése**.
1. Az ASA peremhálózati munkahelye hozzáadásához válassza **importálási Azure Stream Analytics IoT peremhálózati modul**.
1. Válassza ki az előfizetés és a ASA peremhálózati feladatot, amely létrehozta. 
1. Válassza ki az előfizetés és a létrehozott tárfiók. Kattintson a **Save** (Mentés) gombra.

    ![Állítsa a modul][6]

1. Másolja a nevét, a ASA modul automatikusan lett létrehozva. 

    ![hőmérséklet-modul][11]

1. Kattintson a **következő** útvonalak konfigurálása.
1. Másolja az alábbi **útvonalak**.  Cserélje le _{moduleName}_ másolt nevű modul:

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

1. Térjen vissza az eszköz részleteit megjelenítő oldalra, és kattintson a **frissítése**.  Az új Stream Analytics modul futtató mentén kell megjelennie a **IoT peremhálózati ügynök** modul és a **peremhálózati IoT hub**.

    ![a modul kimenete][7]

## <a name="view-data"></a>Adatok megtekintése

Most lépjen a IoT peremhálózati eszközön tekintse meg a a ASA és a tempSensor modul közötti interakció.

Ellenőrizze, hogy a modulok Docker fut:

   ```cmd/sh
   docker ps  
   ```

   ![docker kimeneti][8]

Összes rendszer naplók és a metrikák adatok megtekintéséhez. Használja a Stream Analytics Modulnév:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

A gép hőmérséklet fokozatosan növekedésnek 70 fok 30 másodpercig eléréséig nézheti kell lennie. Ezután a Stream Analytics modul elindítja a alaphelyzetbe állítását és a gép hőmérséklet vált vissza 21. 

   ![docker-napló][9]


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban konfigurálta az Azure Storage-tároló és a Streaming Analytics-feladat az IoT peremhálózati eszközön lévő adatok elemzésére.  Majd betölteni egy egyéni ASA modul áthelyezni az adatokat az eszközről, az adatfolyam keresztül történő letölthető BLOB.  Egyéb oktatóanyagok további megtekintéséhez, hogyan hozhat létre Azure IoT peremhálózati az üzleti megoldások is történik.

> [!div class="nextstepaction"] 
> [Az Azure Machine Learning modell rendszerbe állítása modulként][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
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