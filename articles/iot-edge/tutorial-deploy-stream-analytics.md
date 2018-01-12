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
ms.openlocfilehash: f40fb81fc03e796b906db12bf3bf6904b27b46eb
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Azure Stream Analytics egy IoT peremhálózati modulként telepítése – előzetes

Az IoT-eszközök nagy mennyiségű adat hozhat létre. Feltöltött adatok mennyiségének csökkentésére, vagy végrehajtható egyet az oda-vissza késését megszüntetéséhez, az adatok néha kell elemezni vagy dolgozni, mielőtt a felhő eléri.

Az Azure IoT peremhálózati kihasználja a előre elkészített Azure szolgáltatás IoT peremhálózati modulok gyors telepítéshez. [Az Azure Stream Analytics] [ azure-stream] egy ilyen modul. Egy Azure Stream Analytics-feladat létrehozása a portálon, és keresse meg az Azure IoT Hub portálon üzembe egy IoT-Edge-modul. 

Az Azure Stream Analytics metaadataik strukturált lekérdezési szintaxist tartalmaz a felhő- és IoT oldal adatelemzéshez eszközök. Az IoT-oldal Azure Stream Analytics kapcsolatos további információkért lásd: [Azure Stream Analytics-dokumentáció](../stream-analytics/stream-analytics-edge.md).

Ez az oktatóanyag végigvezeti egy Azure Stream Analytics-feladat létrehozása és telepítése az IoT peremhálózati eszközön. Így lehetővé teszi a feldolgozni egy helyi telemetriai adatfolyam közvetlenül az eszközön, és hozzon létre riasztást, amelyek azonnali intézkedést meghajtó az eszközön. 

Az oktatóanyag két modulok mutat: 
* Egy szimulált hőmérséklet érzékelő modul (tempSensor), amely 20 hőmérséklet adatokat és 120 fok, 5 másodpercenként 1 eggyel állít elő. 
* A Stream Analytics a modul, amely alaphelyzetbe állítja a tempSensor, 30 másodperces átlagos 70 elérésekor. Éles környezetben használhat ez a funkció le egy gép vagy megelőző intézkedéseket veszélyes szintek elérésekor a hőmérséklet. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Azure Stream Analytics-feladat adatfeldolgozásra történő a helyen.
> * Csatlakoztassa az új Azure Stream Analytics-feladat más IoT peremhálózati modulok.
> * Telepítse az Azure Stream Analytics-feladat IoT peremhálózati eszköz.

## <a name="prerequisites"></a>Előfeltételek

* Az IoT-központ. 
* Az eszközt, hogy létrehozása és konfigurálása a gyors üzembe helyezés vagy a cikkek a szimulált eszköz Azure IoT peremhálózati telepítéséről [Windows] [ lnk-tutorial1-win] vagy [Linux] [ lnk-tutorial1-lin]. Hasznos tudnivalók az eszköz kapcsolat kulcs és az eszközazonosító. 
* Az IoT-peremhálózati eszközön futó docker.
    * [Docker telepítése Windows][lnk-docker-windows].
    * [Telepítse a Docker Linux][lnk-docker-linux].
* Python 2.7.x az IoT-peremhálózati eszközön.
    * [Python 2.7 telepítése Windows][lnk-python].
    * A legtöbb Linux terjesztésekről, beleértve az Ubuntu, már telepített Python 2.7. Győződjön meg arról, hogy telepítve van-e a pip, használja a következő parancsot: `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Egy Azure Stream Analytics-feladat létrehozása

Ebben a szakaszban egy Azure Stream Analytics-feladat az IoT hub adatait, a küldött telemetriai adatait kéri le az eszközt, és továbbíthatja a eredményeket egy Azure Blob storage tárolót hoz létre. További információkért lásd: a "Overview" szakaszában a [Stream Analytics-dokumentáció][azure-stream]. 

### <a name="create-a-storage-account"></a>Create a storage account

Adja meg az Azure Stream Analytics-feladat kimenetként használható a végpont egy Azure Storage-fiók szükséges. A jelen szakaszban ismertetett példa a Blob storage típust használ. További információkért lásd: a "Blobok" szakaszában a [Azure Storage-dokumentációt][azure-storage].

1. Az Azure-portálon lépjen **hozzon létre egy erőforrást**, adja meg **tárfiók** a keresési mezőbe, és válassza a **tárfiók - blob, a fájl, a tábla, a várólista**.

2. Az a **storage-fiók létrehozása** panelen adja meg a tárfiók nevét, válassza ki az IoT hub tároló ugyanazon a helyen, és válassza **létrehozása**. Jegyezze fel a későbbi használatra.

    ![Create a storage account][1]

3. Ugrás az újonnan létrehozott tárfiók, és válassza **keresse meg a blobok**. 

4. Hozzon létre egy új tároló az Azure Stream Analytics modul tárolja az adatokat, állítsa be a hozzáférési szint beállítása azokhoz a **tároló**, majd válassza ki **OK**.

    ![tárolási beállítások][10]

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az Azure-portálon lépjen **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát**, majd válassza ki **Stream Analytics-feladat**.

2. Az a **új Stream Analytics-feladat** ablaktáblán tegye a következőket:

    a. Az a **feladatnév** mezőbe írja be a feladat nevét.
    
    b. A **üzemeltetési környezet**, jelölje be **peremhálózati**.
    
    c. A többi mezőben az alapértelmezett értékeket használja.

    > [!NOTE]
    > USA 2. nyugati régióban jelenleg, IoT oldal Azure Stream Analytics-feladatok nem támogatottak. 

3. Kattintson a **Létrehozás** gombra.

4. A létrehozott feldolgozás alatt **feladat topológia**, jelölje be **bemenetek**, majd válassza ki **hozzáadása**.

5. Az a **új bemeneti** ablaktáblán tegye a következőket:

    a. Az a **bemeneti alias** adja meg a **hőmérséklet**.
    
    b. Az a **forrástípus** mezőben válassza **adatfolyam**.
    
    c. A többi mezőben az alapértelmezett értékeket használja.

   ![Az Azure Stream Analytics bemeneti](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. Kattintson a **Létrehozás** gombra.

7. A **feladat topológia**, jelölje be **kimenetek**, majd válassza ki **Hozzáadás**.

8. Az a **új kimeneti** ablaktáblán tegye a következőket:

    a. Az a **kimeneti alias** mezőbe írja be **riasztás**.
    
    b. A többi mezőben az alapértelmezett értékeket használja. 
    
    c. Kattintson a **Létrehozás** gombra.

   ![Az Azure Stream Analytics-kimenet](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. A **feladat topológia**, jelölje be **lekérdezés**, majd cserélje le az alapértelmezett szövege a következő lekérdezést:

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

10. Kattintson a **Mentés** gombra.

## <a name="deploy-the-job"></a>A feladat telepítése

Most már készen áll az Azure Stream Analytics-feladat az IoT-peremhálózati eszközön telepítéséhez.

1. Az Azure portálon az IoT hub az Ugrás **IoT peremhálózati (előzetes verzió)**, majd nyissa meg az IoT-peremhálózati eszköz részleteit megjelenítő oldalon.

2. Válassza ki **modulok beállítása**.  
    Ha korábban telepítette a tempSensor modul ezen az eszközön, akkor előfordulhat, hogy adatokkal való automatikus feltöltés. Ha nem, a modul hozzá a következő módon:

   a. Válassza ki **IoT peremhálózati modul hozzá lesz adva**.

   b. A név mezőbe írja be **tempSensor**.
    
   c. Adja meg a lemezkép URI **microsoft/azureiotedge-szimulált-hőmérséklet-érzékelő: 1.0-előzetes**. 

   d. Hagyja változatlanul az egyéb beállításokat.
   
   e. Kattintson a **Mentés** gombra.

3. Az Azure Stream Analytics peremhálózati munkahelye hozzáadásához válassza **importálási Azure Stream Analytics IoT peremhálózati modul**.

4. Válassza ki az előfizetés és az Azure Stream Analytics peremhálózati feladatot, amely létrehozta. 

5. Válassza ki az előfizetés és a létrehozott, és válassza ki a tárfiók **mentése**.

    ![Állítsa a modul][6]

6. Másolja az Azure Stream Analytics-modul nevét. 

    ![hőmérséklet-modul][11]

7. Útvonalak konfigurálásához jelölje ki **következő**.

8. Másolja a következő kódot a **útvonalak**. Cserélje le _{moduleName}_ másolt nevű modul:

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

9. Válassza ki **következő**.

10. Az a **felülvizsgálati sablonja** lépésben jelölje be **Submit**.

11. Térjen vissza az eszköz részleteit megjelenítő oldalra, és válassza ki **frissítése**.  
    Az új Stream Analytics modul fut, valamint az IoT-Edge ügynök modul és a peremhálózati IoT hub kell megjelennie.

    ![a modul kimenete][7]

## <a name="view-data"></a>Adatok megtekintése

Most lépjen a IoT peremhálózati eszközön tekintse meg a az Azure Stream Analytics és a tempSensor modul közötti interakció.

1. Ellenőrizze, hogy a modulok Docker fut:

   ```cmd/sh
   docker ps  
   ```

   ![docker kimeneti][8]

2. Összes rendszer naplók és a metrikák adatok megtekintéséhez. Használja a Stream Analytics Modulnév:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

A gép hőmérséklet fokozatosan növekedésnek 70 fok 30 másodpercig eléréséig nézheti kell lennie. A Stream Analytics modul elindítja a alaphelyzetbe állítani, és a gép hőmérséklet vált vissza 21. 

   ![docker-napló][9]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurálta az Azure storage-tároló és a Streaming Analytics-feladat az IoT peremhálózati eszközön lévő adatok elemzésére. Majd betölteni egy egyéni Azure Stream Analytics modul áthelyezni az adatokat az eszközről, az adatfolyam keresztül történő letölthető blob. Hogyan hozhat létre Azure IoT peremhálózati további megoldások az üzleti megtekintéséhez az egyéb oktatóanyagok történik.

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
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
