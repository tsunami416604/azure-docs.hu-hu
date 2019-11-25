---
title: 'Tutorial: Stream Analytics at the edge - Azure IoT Edge'
description: In this tutorial, you deploy Azure Stream Analytics as a module to an IoT Edge device
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7fbbe32efcedd4fa2635db1cc21f7ce98557515b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452530"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Deploy Azure Stream Analytics as an IoT Edge module

Many IoT solutions use analytics services to gain insight about data as it arrives in the cloud from IoT devices. Az Azure IoT Edge segítségével az [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)-logikát kihelyezheti magára az eszközre is. A telemetriastreamek peremeszközökön végzett feldolgozásával csökkenthető a feltöltött adatok mennyisége, valamint a gyakorlatban is használható elemzésekre való reagáláshoz szükséges idő.

Az Azure IoT Edge és az Azure Stream Analytics integrációja révén az Azure Portalon létrehozhat Azure Stream Analytics-feladatokat, majd további kód írása nélkül üzembe helyezheti azokat az IoT Edge-modulokon.  

Azure Stream Analytics provides a richly structured query syntax for data analysis, both in the cloud and on IoT Edge devices. For more information, see [Azure Stream Analytics documentation](../stream-analytics/stream-analytics-edge.md).

Ebben az oktatóanyagban a Stream Analytics modul az átlaghőmérsékletet számítja egy gördülő 30 másodperces ablakon belül. Ha az átlag eléri a 70-es értéket, a modul egy riasztást küld az eszköznek, amely egy műveletet hajt végre. Esetünkben a művelet a szimulált hőmérséklet-érzékelő visszaállítása. Éles környezetben ezzel a funkcióval leállíthat egy gépet vagy óvintézkedéseket tehet, amint a hőmérséklet veszélyes szintet ér el. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Stream Analytics-feladat létrehozása az adatok a peremeszközökön való feldolgozásához.
> * Az új Azure Stream Analytics-feladat csatlakoztatása más IoT Edge-modulokhoz.
> * Az Azure Stream Analytics üzembe helyezése egy IoT Edge-eszközön az Azure Portalról.

<center>

![Diagram - Tutorial architecture, stage and deploy ASA job](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md) or [Windows devices](quickstart.md).

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 


## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat létrehozása

In this section, you create an Azure Stream Analytics job that will do the following steps:
* Receive data from your IoT Edge device.
* Query the telemetry data for values outside a set range.
* Take action on the IoT Edge device based on the query results. 

### <a name="create-a-storage-account"></a>Create a storage account

Amikor Azure Stream Analytics-feladatot hoz létre egy IoT Edge-eszköz futtatásához, azt úgy kell tárolni, hogy meghívható legyen az eszközről. Használhat egy már létező Azure Storage-tárfiókot, vagy létrehozhat most egy újat. 

1. In the Azure portal, go to **Create a resource** > **Storage** > **Storage account**. 

1. Adja meg a következő értékeket a tárfiók létrehozásához:

   | Mező | Value (Díj) |
   | ----- | ----- |
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Név | Adja meg a tárfiók egyedi nevét. | 
   | Földrajzi egység | Válassza ki az Önhöz legközelebb eső helyet. |


1. Keep the default values for the other fields and select **Review + Create**.

1. Review your settings then select **Create**.

### <a name="create-a-new-job"></a>Új feladat létrehozása

1. In the Azure portal, go to **Create a resource** > **Internet of Things** > **Stream Analytics job**.

1. Adja meg a következő értékeket a feladat létrehozásához:

   | Mező | Value (Díj) |
   | ----- | ----- |
   | Feladat neve | Adja meg a feladat nevét. Például: **IoTEdgeJob** | 
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Földrajzi egység | Válassza ki az Önhöz legközelebb eső helyet. | 
   | Üzemeltetési környezet | Válassza az **Edge** lehetőséget. |
 
1. Kattintson a **Létrehozás** gombra.

### <a name="configure-your-job"></a>A feladat konfigurálása

Miután létrejött a Stream Analytics-feladat az Azure Portalon, konfigurálhatja azt egy bemenettel, egy kimenettel és egy lekérdezéssel az áthaladó adatokon való futtatáshoz. 

Ebben a szakaszban három elem (bemenet, kimenet és lekérdezés) használatával hozhat létre egy feladatot, amely hőmérsékleti adatokat fogad az IoT Edge-eszköztől. Egy gördülő 30 másodperces ablakon belül elemzi az adatokat. Ha az ablakban az átlaghőmérséklet 70 fok fölé kerül, akkor a rendszer riasztást küld az IoT Edge-eszköznek. A következő szakaszban a feladat üzembe helyezésekor fogja pontosan meghatározni, honnan érkeznek az adatok, és hová tartanak.  

1. Keresse meg a Stream Analytics-feladatot az Azure Portalon. 

1. A **Feladattopológia** területen válassza a **Bemenetek** lehetőséget, majd a **Streambemenet hozzáadása** elemet.

   ![Azure Stream Analytics - add input](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Válassza az **Edge Hub** elemet a legördülő listából.

1. Az **Új bemenet** panelen adja meg a **temperature** (hőmérséklet) nevet a bemenet aliasaként. 

1. Fogadja el az alapértelmezett értékeket a többi mezőben, és válassza a **Mentés** lehetőséget.

1. A **Feladattopológia** területen nyissa meg a **Kimenetek** pontot, és válassza a **Hozzáadás** elemet.

   ![Azure Stream Analytics - add output](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Válassza az **Edge Hub** elemet a legördülő listából.

1. Az **Új kimenet** panelen adja meg az **alert** (riasztás) nevet a kimenet aliasaként. 

1. Fogadja el az alapértelmezett értékeket a többi mezőben, és válassza a **Mentés** lehetőséget.

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget.

1. Cserélje le az alapértelmezett szöveget a következő lekérdezésre. Az SQL-kód visszaállítás parancsot küld a riasztás kimenetének, ha az átlagos géphőmérséklet bármely 30 másodperces időablakban eléri a 70 fokot. A visszaállítás parancs előre be van programozva az érzékelőbe elvégezhető műveletként. 

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

1. Kattintson a **Mentés** gombra.

### <a name="configure-iot-edge-settings"></a>Az IoT Edge-beállítások konfigurálása

Ha elő szeretné készíteni a Stream Analytics-feladatot egy IoT Edge-eszközre való üzembe helyezésre, a feladatot egy tárfiókon lévő tárolóval kell társítania. Amikor a feladat üzembe helyezésébe kezd, a feladat definícióját exportálja a tárolóba. 

1. Under **Configure**, select **Storage account settings** then select **Add storage account**. 

   ![Azure Stream Analytics - add storage account](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Select the **Storage account** that you created at the beginning of this tutorial from the drop-down menu.

1. A **Tároló** mezőben válassza az **Új létrehozása** lehetőséget, és adja meg a tárfiók nevét. 

1. Kattintson a **Mentés** gombra. 

## <a name="deploy-the-job"></a>A feladat üzembe helyezése

Most készen áll az Azure Stream Analytics-feladat IoT Edge-eszközön való üzembe helyezésére. 

Ebben a szakaszban az Azure Portal **Modulok beállítása** varázslójával hozhat létre *üzembehelyezési jegyzéket*. Az üzembehelyezési jegyzék egy olyan JSON-fájl, amely leírja az eszközre üzembe helyezendő összes modult, a modul rendszerképeit tároló tárolóregisztrációs adatbázisokat, a modulok kezelésének módját és azt, hogyan kommunikálhatnak egymással a modulok. Az IoT Edge-eszköz lekéri az üzembehelyezési jegyzéket az IoT Hubról, majd az abban lévő információkkal üzembe helyezi és konfigurálja az összes hozzárendelt modult. 

Ebben az oktatóanyagban két modult helyezhet üzembe. The first is **SimulatedTemperatureSensor**, which is a module that simulates a temperature and humidity sensor. A második a Stream Analytics-feladat. Az érzékelőmodul biztosítja az adatstreamet, amelyet a feladatlekérdezés elemezni fog. 

1. Az Azure Portalon keresse meg az IoT-központot.

1. Go to **IoT Edge**, and then open the details page for your IoT Edge device.

1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.  

1. If you previously deployed the SimulatedTemperatureSensor module on this device, it might autopopulate. Amennyiben nem, vegye fel a modult a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **IoT Edge-modul** lehetőséget.
   1. For the name, type **SimulatedTemperatureSensor**.
   1. A kép URI-címénél adja meg az **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** címet. 
   1. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.

1. Adja hozzá az Azure Stream Analytics Edge-feladatot a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **Azure Stream Analytics-modul** lehetőséget.
   1. Válassza ki az előfizetést, és a létrehozott Azure Stream Analytics Edge-feladatot. 
   1. Kattintson a **Mentés** gombra.

   Once you save your changes, the details of your Stream Analytics job are published to the storage container that you created. 

1. When the Stream Analytics module is added to the list of modules, select **Configure** to see how it's structured. 

   A rendszerkép URI egy standard Azure Stream Analytics rendszerképre mutat. This one image is used for every Stream Analytics module that gets deployed to an IoT Edge device. 

   Az ikermodul az **ASAJobInfo** nevű kívánt tulajdonsággal van konfigurálva. A tulajdonság értéke a feladat tárolóban lévő definíciójára mutat. This property is how the Stream Analytics image is configured with your specific job details. 

   By default, the Stream Analytics module takes the same name as the job it's based on. You can change the module name on this page if you like, but it's not necessary. 

1. Close the module configuration page.

1. Jegyezze fel a Stream Analytics-modul nevét, mert a következő lépésben szüksége lesz rá, majd a folytatáshoz válassza a **Következő** lehetőséget.

1. Cserélje le az **Útvonalak** alapértelmezett értékét az alábbi kódra. Mind a három _{moduleName}_ helyére írja az Azure Stream Analytics-modul nevét. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   Az itt megadott útvonalak határozzák meg az adatok áramlását az IoT Edge-eszközben. The telemetry data from SimulatedTemperatureSensor are sent to IoT Hub and to the **temperature** input that was configured in the Stream Analytics job. The **alert** output messages are sent to IoT Hub and to the SimulatedTemperatureSensor module to trigger the reset command. 

1. Kattintson a **Tovább** gombra.

1. In the **Review Deployment** step, you can see how the information you provided in the wizard is converted into a JSON deployment manifest. When you're done reviewing the manifest, select **Submit**.

1. Térjen vissza az eszköz részleteit tartalmazó oldalra, majd kattintson a **Frissítés** elemre.  

    You should see the new Stream Analytics module running, along with the IoT Edge agent and IoT Edge hub modules. It may take a few minutes for the information to reach your IoT Edge device, and then for the new modules to start. If you don't see the modules running right away, continue refreshing the page.

    ![SimulatedTemperatureSensor and ASA module reported by device](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Adatok megtekintése

Now you can go to your IoT Edge device to check out the interaction between the Azure Stream Analytics module and the SimulatedTemperatureSensor module.

1. Ellenőrizze, hogy fut-e mindegyik modul a Dockerben:

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. Tekintse át az összes rendszernaplót és metrikaadatot. Használja a Stream Analytics-modul nevét:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. View the reset command affect the SimulatedTemperatureSensor by viewing the sensor logs:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   You can watch the machine's temperature gradually rise until it reaches 70 degrees for 30 seconds. A Stream Analytics-modul ekkor kiváltja a rendszer alaphelyzetbe állítását, és a géphőmérséklet visszaáll 21 fokra. 

   ![Reset command output into module logs](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Stream Analytics-feladatot hozott létre az IoT Edge-eszközből származó adatok elemzéséhez. Ezután letöltötte az Azure Stream Analytics-modult az IoT Edge-eszközre, hogy helyileg lehessen feldolgozni és megválaszolni a hőmérséklet-növekedési adatokat, valamint az összesített adatstreamet a felhőbe feltölteni. A többi oktatóanyagra tovább lépve megtudhatja, hogyan lehet az Azure IoT Edge használatával egyéb üzleti megoldásokat létrehozni.

> [!div class="nextstepaction"] 
> [Azure Machine Learning-modell üzembe helyezése modulként](tutorial-deploy-machine-learning.md)
