---
title: Oktatóanyag – ASA-feladatok üzembe helyezése Azure IoT Edge-eszközökön | Microsoft Docs
description: Az oktatóanyagban egy Azure Stream Analytics-példányt helyezünk üzembe modulként egy IoT Edge-eszközre.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: afbdf2171c1fc1eef95514526a509d171e262d4a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435682"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module-preview"></a>Oktatóanyag: Az Azure Stream Analytics üzembe helyezése IoT Edge-modulként (előzetes verzió)

Számos IoT-megoldás alkalmaz elemzési szolgáltatásokat az IoT-eszközökről a felhőbe érkező adatok beható elemzéséhez. Az Azure IoT Edge segítségével az [Azure Stream Analytics][azure-stream]-logikát kihelyezheti magára az eszközre is. A telemetriastreamek peremeszközökön végzett feldolgozásával csökkenthető a feltöltött adatok mennyisége, valamint a gyakorlatban is használható elemzésekre való reagáláshoz szükséges idő.

Az Azure IoT Edge és az Azure Stream Analytics integrációja révén az Azure Portalon létrehozhat Azure Stream Analytics-feladatokat, majd további kód írása nélkül üzembe helyezheti azokat az IoT Edge-modulokon.  

Az Azure Stream Analytics gazdagon strukturált lekérdezési szintaxist biztosít az adatelemzéshez a felhőben és az IoT Edge-eszközökön egyaránt. Az IoT Edge-en futó Azure Stream Analyticsszel kapcsolatos további információkat [az Azure Stream Analytics dokumentációjában](../stream-analytics/stream-analytics-edge.md) talál.

Ebben az oktatóanyagban a Stream Analytics modul az átlaghőmérsékletet számítja egy gördülő 30 másodperces ablakon belül. Ha az átlag eléri a 70-es értéket, a modul egy riasztást küld az eszköznek, amely egy műveletet hajt végre. Esetünkben a művelet a szimulált hőmérséklet-érzékelő visszaállítása. Éles környezetben ezzel a funkcióval leállíthat egy gépet vagy óvintézkedéseket tehet, amint a hőmérséklet veszélyes szintet ér el. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Stream Analytics-feladat létrehozása az adatok a peremeszközökön való feldolgozásához.
> * Az új Azure Stream Analytics-feladat csatlakoztatása más IoT Edge-modulokhoz.
> * Az Azure Stream Analytics üzembe helyezése egy IoT Edge-eszközön az Azure Portalról.

>[!NOTE]
>Az IoT Edge-hez készült Azure Stream Analytics-modulok jelenleg [nyilvános előzetes verzióként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhetők el.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [Linux-](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójának lépéseit követve.
* Az Azure Machine Learning-modul nem támogatja az ARM processzorokat.

Felhőerőforrások:

* Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 


## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat létrehozása

Ebben a szakaszban egy Azure Stream Analytics-feladatot hozunk létre az IoT-központról érkező adatok fogadására, az eszközről küldött telemetriaadatok lekérdezésére, majd az eredmények továbbítására egy Azure Blob Storage-tárolóba. További információkat a [Stream Analytics dokumentációjának][azure-stream] „Áttekintés” szakaszában talál. 

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az Azure Stream Analytics-feladatokhoz szükség van egy Azure Storage-fiókra, amely a feladat kimenetének végpontjaként szolgál. Az ebben a szakaszban szereplő példa egy blob típusú tárolót használ. További információt [az Azure Storage dokumentációjának][azure-storage] „Blobok” szakaszában talál.

1. Az Azure Portalon lépjen az **Erőforrás létrehozása** menüpontra, írja be a **Tárfiók** kifejezést a keresőmezőbe, majd válassza a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget.

1. A **Tárfiók létrehozása** panelen adja meg a tárfiók nevét, válassza ki ugyanazt a helyet, ahol az IoT-központot tárolja, válassza ugyanazt az erőforráscsoportot, amelyet az IoT-központhoz is, majd kattintson a **Létrehozás** gombra. Jegyezze fel a nevet későbbi felhasználásra.

    ![Tárfiók létrehozása][1]


### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az Azure Portalon lépjen az **Erőforrás létrehozása** > **Eszközök internetes hálózata** pontra, majd válassza a **Stream Analytics-feladat** lehetőséget.

1. Az **Új Stream Analytics-feladat** panelen hajtsa végre a következő lépéseket:

   1. A **Feladat neve** mezőbe írja be a feladat nevét.
   
   1. Használja ugyanazt az **erőforráscsoportot** és **helyet**, mint az IoT Hub esetében. 

      > [!NOTE]
      > Az Azure Stream Analytics-feladatok IoT Edge-en való futtatása az USA 2. nyugati régiójában jelenleg nem támogatott. 

   1. Az **Üzemeltetési környezet** mezőben válassza a **Perem** lehetőséget.
    
1. Kattintson a **Létrehozás** gombra.

1. A létrehozott feladatban a **Feladattopológia** területen nyissa meg a **Bemenetek** pontot.

   ![Azure Stream Analytics-bemenet](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Válassza a **Streambemenet hozzáadása**, majd az **Edge Hub** lehetőséget.

1. Az **Új bemenet** panelen adja meg a **temperature** (hőmérséklet) nevet a bemenet aliasaként. 

1. Kattintson a **Mentés** gombra.

1. A **Feladattopológia** területen nyissa meg a **Kimenetek** pontot.

   ![Azure Stream Analytics-kimenet](./media/tutorial-deploy-stream-analytics/asa_output.png)

1. Válassza a **Hozzáadás**, majd az **Edge Hub** lehetőséget.

1. Az **Új kimenet** panelen adja meg az **alert** (riasztás) nevet a kimenet aliasaként. 

1. Kattintson a **Mentés** gombra.

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget, majd cserélje le az alapértelmezett szöveget a következő lekérdezésre, amely riasztást indít, ha az átlagos géphőmérséklet bármely 30 másodperces időablakban eléri a 70 fokot:

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

1. A **Konfigurálás** területen válassza az **IoT Edge-beállítások** lehetőséget.

1. Válassza ki a **tárfiókját** a legördülő listából.

1. A **Tároló** mezőben válassza az **Új létrehozása** lehetőséget, és adja meg a tárfiók nevét. 

1. Kattintson a **Mentés** gombra. 


## <a name="deploy-the-job"></a>A feladat üzembe helyezése

Most készen áll az Azure Stream Analytics-feladat IoT Edge-eszközön való üzembe helyezésére.

1. Az Azure Portalon, az IoT-központban lépjen az **IoT Edge** részhez, és nyissa meg az IoT Edge-eszköz adatait tartalmazó lapot.

1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.  

   Ha korábban már üzembe helyezte a tempSensor modult ezen az eszközön, előfordulhat, hogy az automatikusan megjelenik a mezőben. Amennyiben nem, vegye fel a modult a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **IoT Edge-modul** lehetőséget.
   1. A névnél adja meg a **tempSensor** nevet.
   1. A kép URI-címénél adja meg az **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** címet. 
   1. A többi beállítást ne módosítsa.
   1. Kattintson a **Mentés** gombra.

1. Adja hozzá az Azure Stream Analytics Edge-feladatot a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **Azure Stream Analytics-modul** lehetőséget.
   1. Válassza ki az előfizetést, és a létrehozott Azure Stream Analytics Edge-feladatot. 
   1. Kattintson a **Mentés** gombra.

1. Kattintson a **Tovább** gombra.

1. Cserélje le az **Útvonalak** alapértelmezett értékét az alábbi kódra. A _{moduleName}_ helyére írja az Azure Stream Analytics-modul nevét. A modul nevének egyeznie kell a feladat nevével, amelyből létre lett hozva. 

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

1. Az **Üzembe helyezés áttekintése** lépésben kattintson a **Küldés** elemre.

1. Térjen vissza az eszköz részleteit tartalmazó oldalra, majd kattintson a **Frissítés** elemre.  

    Látható, hogy az új Stream Analytics-modul az IoT Edge-ügynökmodullal és az IoT Edge-központtal együtt fut.

    ![A modul kimenete][7]

## <a name="view-data"></a>Adatok megtekintése

Most az IoT Edge-eszközre lépve ellenőrizze az Azure Stream Analytics-modul és a tempSensor modul közti interakciót.

1. Ellenőrizze, hogy fut-e mindegyik modul a Dockerben:

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
1. Tekintse át az összes rendszernaplót és metrikaadatot. Használja a Stream Analytics-modul nevét:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Látnia kell, ahogy a géphőmérséklete folyamatosan emelkedik, amíg valamelyik 30 másodperces ablakban eléri a 70 fokot. A Stream Analytics-modul ekkor kiváltja a rendszer alaphelyzetbe állítását, és a géphőmérséklet visszaáll 21 fokra. 

   ![Docker-napló][9]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a már létrehozott erőforrásokat és konfigurációkat.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

> [!IMPORTANT]
> Az Azure-erőforrások és -erőforráscsoportok törlése nem vonható vissza. A törlést követően az erőforráscsoport és a benne foglalt erőforrások véglegesen törölve lesznek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Ha csak az IoT Hubot szeretné törölni, hajtsa végre az alábbi parancsot a saját hubja és a saját erőforráscsoportja nevével:

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


A teljes erőforráscsoport név alapján való törléséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

1. A **Szűrés név alapján...** mezőbe írja be az IoT Hubot tartalmazó erőforráscsoport nevét. 

1. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
1. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Stream Analytics-feladatot hozott létre az IoT Edge-eszközből származó adatok elemzéséhez. Ezután letöltötte az Azure Stream Analytics-modult az IoT Edge-eszközre, hogy helyileg lehessen feldolgozni és megválaszolni a hőmérséklet-növekedési adatokat, valamint az összesített adatstreamet a felhőbe feltölteni. A többi oktatóanyagra tovább lépve megtudhatja, hogyan lehet az Azure IoT Edge használatával egyéb üzleti megoldásokat létrehozni.

> [!div class="nextstepaction"] 
> [Azure Machine Learning-modell üzembe helyezése modulként][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
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
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

