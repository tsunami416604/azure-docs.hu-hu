---
title: Oktatóanyag – ASA-feladatok üzembe helyezése Azure IoT Edge-eszközökön | Microsoft Docs
description: Az oktatóanyagban egy Azure Stream Analytics-példányt helyezünk üzembe modulként egy IoT Edge-eszközre.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2188e21cfd29ac8ac2d44878819ee62a3e2d555e
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566941"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module-preview"></a>Oktatóanyag: Az Azure Stream Analytics üzembe helyezése IoT Edge-modulként (előzetes verzió)

Számos IoT-megoldás alkalmaz elemzési szolgáltatásokat az IoT-eszközökről a felhőbe érkező adatok beható elemzéséhez. Az Azure IoT Edge segítségével az [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)-logikát kihelyezheti magára az eszközre is. A telemetriastreamek peremeszközökön végzett feldolgozásával csökkenthető a feltöltött adatok mennyisége, valamint a gyakorlatban is használható elemzésekre való reagáláshoz szükséges idő.

Az Azure IoT Edge és az Azure Stream Analytics integrációja révén az Azure Portalon létrehozhat Azure Stream Analytics-feladatokat, majd további kód írása nélkül üzembe helyezheti azokat az IoT Edge-modulokon.  

Az Azure Stream Analytics gazdagon strukturált lekérdezési szintaxist biztosít az adatelemzéshez a felhőben és az IoT Edge-eszközökön egyaránt. Az IoT Edge-en futó Azure Stream Analyticsszel kapcsolatos további információkat [az Azure Stream Analytics dokumentációjában](../stream-analytics/stream-analytics-edge.md) talál.

Ebben az oktatóanyagban a Stream Analytics modul az átlaghőmérsékletet számítja egy gördülő 30 másodperces ablakon belül. Ha az átlag eléri a 70-es értéket, a modul egy riasztást küld az eszköznek, amely egy műveletet hajt végre. Esetünkben a művelet a szimulált hőmérséklet-érzékelő visszaállítása. Éles környezetben ezzel a funkcióval leállíthat egy gépet vagy óvintézkedéseket tehet, amint a hőmérséklet veszélyes szintet ér el. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Stream Analytics-feladat létrehozása az adatok a peremeszközökön való feldolgozásához.
> * Az új Azure Stream Analytics-feladat csatlakoztatása más IoT Edge-modulokhoz.
> * Az Azure Stream Analytics üzembe helyezése egy IoT Edge-eszközön az Azure Portalról.

<center>
![Az oktatóanyag architektúradiagramja](./media/tutorial-deploy-stream-analytics/ASATutorialDiagram.png)
</center>

>[!NOTE]
>Az IoT Edge-hez készült Azure Stream Analytics-modulok jelenleg [nyilvános előzetes verzióként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhetők el.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [Linux-](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójának lépéseit követve.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 


## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat létrehozása

Ebben a szakaszban egy Azure Stream Analytics-feladatot hozunk létre az IoT-központról érkező adatok fogadására, az eszközről küldött telemetriaadatok lekérdezésére, majd az eredmények továbbítására egy Azure Blob Storage-tárolóba. 

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Amikor Azure Stream Analytics-feladatot hoz létre egy IoT Edge-eszköz futtatásához, azt úgy kell tárolni, hogy meghívható legyen az eszközről. Használhat egy már létező Azure Storage-tárfiókot, vagy létrehozhat most egy újat. 

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Tárolás** > **Tárfiók – blob, fájl, tábla, üzenetsor** elemet. 

1. Adja meg a következő értékeket a tárfiók létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Name (Név) | Adja meg a tárfiók egyedi nevét. | 
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |

1. Fogadja el az alapértelmezett értékeket a többi mezőben, és válassza a **Létrehozás** lehetőséget. 

### <a name="create-a-new-job"></a>Új feladat létrehozása

1. Az Azure Portalon lépjen az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Stream Analytics-feladat** pontra.

1. Adja meg a következő értékeket a feladat létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Feladat neve | Adja meg a feladat nevét. Például: **IoTEdgeJob** | 
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. | 
   | Üzemeltetési környezet | Válassza az **Edge** lehetőséget. |
 
1. Kattintson a **Létrehozás** gombra.

### <a name="configure-your-job"></a>A feladat konfigurálása

Miután létrejött a Stream Analytics-feladat az Azure Portalon, konfigurálhatja azt egy bemenettel, egy kimenettel és egy lekérdezéssel az áthaladó adatokon való futtatáshoz. 

Ebben a szakaszban három elem (bemenet, kimenet és lekérdezés) használatával hozhat létre egy feladatot, amely hőmérsékleti adatokat fogad az IoT Edge-eszköztől. Egy gördülő 30 másodperces ablakon belül elemzi az adatokat. Ha az ablakban az átlaghőmérséklet 70 fok fölé kerül, akkor a rendszer riasztást küld az IoT Edge-eszköznek. A következő szakaszban a feladat üzembe helyezésekor fogja pontosan meghatározni, honnan érkeznek az adatok, és hová tartanak.  

1. Keresse meg a Stream Analytics-feladatot az Azure Portalon. 

1. A **Feladattopológia** területen válassza a **Bemenetek** lehetőséget, majd a **Streambemenet hozzáadása** elemet.

   ![Azure Stream Analytics-bemenet](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Válassza az **Edge Hub** elemet a legördülő listából.

1. Az **Új bemenet** panelen adja meg a **temperature** (hőmérséklet) nevet a bemenet aliasaként. 

1. Fogadja el az alapértelmezett értékeket a többi mezőben, és válassza a **Mentés** lehetőséget.

1. A **Feladattopológia** területen nyissa meg a **Kimenetek** pontot, és válassza a **Hozzáadás** elemet.

   ![Azure Stream Analytics-kimenet](./media/tutorial-deploy-stream-analytics/asa_output.png)

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

1. A **Konfigurálás** területen válassza a **Tárfiók beállításai** lehetőséget.

1. Válassza ki a **Tárfiók hozzáadása** lehetőséget. 

1. Válassza ki a **tárfiókját** a legördülő listából.

1. A **Tároló** mezőben válassza az **Új létrehozása** lehetőséget, és adja meg a tárfiók nevét. 

1. Kattintson a **Mentés** gombra. 

## <a name="deploy-the-job"></a>A feladat üzembe helyezése

Most készen áll az Azure Stream Analytics-feladat IoT Edge-eszközön való üzembe helyezésére. 

Ebben a szakaszban az Azure Portal **Modulok beállítása** varázslójával hozhat létre *üzembehelyezési jegyzéket*. Az üzembehelyezési jegyzék egy olyan JSON-fájl, amely leírja az eszközre üzembe helyezendő összes modult, a modul rendszerképeit tároló tárolóregisztrációs adatbázisokat, a modulok kezelésének módját és azt, hogyan kommunikálhatnak egymással a modulok. Az IoT Edge-eszköz lekéri az üzembehelyezési jegyzéket az IoT Hubról, majd az abban lévő információkkal üzembe helyezi és konfigurálja az összes hozzárendelt modult. 

Ebben az oktatóanyagban két modult helyezhet üzembe. Az első a **tempSensor**, amely egy hőmérséklet- és páratartalom-érzékelőt szimuláló modul. A második a Stream Analytics-feladat. Az érzékelőmodul biztosítja az adatstreamet, amelyet a feladatlekérdezés elemezni fog. 

1. Az Azure Portalon, az IoT-központban lépjen az **IoT Edge** részhez, és nyissa meg az IoT Edge-eszköz adatait tartalmazó lapot.

1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.  

1. Ha korábban már üzembe helyezte a tempSensor modult ezen az eszközön, előfordulhat, hogy az automatikusan megjelenik a mezőben. Amennyiben nem, vegye fel a modult a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **IoT Edge-modul** lehetőséget.
   1. A névnél adja meg a **tempSensor** nevet.
   1. A kép URI-címénél adja meg az **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** címet. 
   1. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.

1. Adja hozzá az Azure Stream Analytics Edge-feladatot a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **Azure Stream Analytics-modul** lehetőséget.
   1. Válassza ki az előfizetést, és a létrehozott Azure Stream Analytics Edge-feladatot. 
   1. Kattintson a **Mentés** gombra.

1. Miután a Stream Analytics-feladatot közzétette a létrehozott tárolóban, kattintson a modul nevére a Stream Analytics-modul felépítésének megtekintéséhez. 

   A rendszerkép URI egy standard Azure Stream Analytics rendszerképre mutat. Ez ugyanaz a rendszerkép, amelyet az IoT Edge-eszközökre üzembe helyezett összes feladathoz használ. 

   Az ikermodul az **ASAJobInfo** nevű kívánt tulajdonsággal van konfigurálva. A tulajdonság értéke a feladat tárolóban lévő definíciójára mutat. Ez a tulajdonság meghatározza, hogyan van konfigurálva a Stream Analytics-rendszerkép az adott feladat információival. 

1. Zárja be a modul oldalát.

1. Jegyezze fel a Stream Analytics-modul nevét, mert a következő lépésben szüksége lesz rá, majd a folytatáshoz válassza a **Következő** lehetőséget.

1. Cserélje le az **Útvonalak** alapértelmezett értékét az alábbi kódra. Mind a három _{moduleName}_ helyére írja az Azure Stream Analytics-modul nevét. 

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

   Az itt megadott útvonalak határozzák meg az adatok áramlását az IoT Edge-eszközben. A tempSensor érzékelőből küldött telemetriai adatok az IoT Hubra kerülnek, valamint a Stream Analytics-feladatban konfigurált **hőmérséklet** bemenethez. A **riasztás** kimenet üzenetei az IoT Hubra kerülnek, valamint a visszaállítás parancs aktiválása érdekében a tempSensor modulhoz. 

1. Kattintson a **Tovább** gombra.

1. Az **Üzembe helyezés áttekintése** lépésben kattintson a **Küldés** elemre.

1. Térjen vissza az eszköz részleteit tartalmazó oldalra, majd kattintson a **Frissítés** elemre.  

    Látható, hogy az új Stream Analytics-modul az IoT Edge-ügynökmodullal és az IoT Edge-központtal együtt fut.

    ![A modul kimenete](./media/tutorial-deploy-stream-analytics/module_output2.png)

## <a name="view-data"></a>Adatok megtekintése

Most az IoT Edge-eszközre lépve ellenőrizze az Azure Stream Analytics-modul és a tempSensor modul közti interakciót.

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

Látnia kell, ahogy a géphőmérséklete folyamatosan emelkedik, amíg valamelyik 30 másodperces ablakban eléri a 70 fokot. A Stream Analytics-modul ekkor kiváltja a rendszer alaphelyzetbe állítását, és a géphőmérséklet visszaáll 21 fokra. 

   ![Docker-napló](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Stream Analytics-feladatot hozott létre az IoT Edge-eszközből származó adatok elemzéséhez. Ezután letöltötte az Azure Stream Analytics-modult az IoT Edge-eszközre, hogy helyileg lehessen feldolgozni és megválaszolni a hőmérséklet-növekedési adatokat, valamint az összesített adatstreamet a felhőbe feltölteni. A többi oktatóanyagra tovább lépve megtudhatja, hogyan lehet az Azure IoT Edge használatával egyéb üzleti megoldásokat létrehozni.

> [!div class="nextstepaction"] 
> [Azure Machine Learning-modell üzembe helyezése modulként](tutorial-deploy-machine-learning.md)
