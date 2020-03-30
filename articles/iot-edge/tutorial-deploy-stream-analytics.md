---
title: Oktatóanyag – Stream Analytics az Azure IoT Edge használatával
description: Ebben az oktatóanyagban az Azure Stream Analytics-et egy IoT Edge-eszközre modulként telepíti.
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d8c3bde0f32c1df6c98f6a71f6ab830c21256903
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76906286"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Oktatóanyag: Az Azure Stream Analytics üzembe helyezése IoT Edge-modulként

Számos IoT-megoldás elemzési szolgáltatásokat használ, hogy betekintést nyerjen az adatokba, amint azok az IoT-eszközökről érkeznek a felhőbe. Az Azure IoT Edge segítségével az [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)-logikát kihelyezheti magára az eszközre is. A telemetriastreamek peremeszközökön végzett feldolgozásával csökkenthető a feltöltött adatok mennyisége, valamint a gyakorlatban is használható elemzésekre való reagáláshoz szükséges idő.

Az Azure IoT Edge és az Azure Stream Analytics integrálva van a számítási feladatok fejlesztésének egyszerűsítése érdekében. Létrehozhat egy Azure Stream Analytics-feladatot az Azure Portalon, majd üzembe helyezheti azt IoT Edge-modulként további kód nélkül.  

Az Azure Stream Analytics egy gazdagon strukturált lekérdezésszintaxist biztosít az adatelemzéshez, mind a felhőben, mind az IoT Edge-eszközökön. További információt az [Azure Stream Analytics dokumentációjában](../stream-analytics/stream-analytics-edge.md)talál.

Ebben az oktatóanyagban a Stream Analytics modul az átlaghőmérsékletet számítja egy gördülő 30 másodperces ablakon belül. Ha az átlag eléri a 70-es értéket, a modul egy riasztást küld az eszköznek, amely egy műveletet hajt végre. Esetünkben a művelet a szimulált hőmérséklet-érzékelő visszaállítása. Éles környezetben ezzel a funkcióval leállíthat egy gépet vagy óvintézkedéseket tehet, amint a hőmérséklet veszélyes szintet ér el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Azure Stream Analytics-feladat létrehozása az adatok a peremeszközökön való feldolgozásához.
> * Az új Azure Stream Analytics-feladat csatlakoztatása más IoT Edge-modulokhoz.
> * Az Azure Stream Analytics üzembe helyezése egy IoT Edge-eszközön az Azure Portalról.

<center>

![Diagram – Oktatóanyag-architektúra: ASA-feladat színpadra helyezése és üzembe helyezése](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Az Azure virtuális gépeket IoT Edge-eszközként is használhatja a [Linux](quickstart-linux.md) vagy [Windows eszközökhöz](quickstart.md)futó rövid útmutató lépéseit követve.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat létrehozása

Ebben a szakaszban egy Azure Stream Analytics-feladatot hoz létre, amely a következő lépéseket fogja megtenni:

* Adatok fogadása az IoT Edge-eszközről.
* A telemetriai adatok lekérdezése egy meghatározott tartományon kívüli értékekhez.
* A lekérdezés eredményei alapján műveleteket az IoT Edge-eszközön.

### <a name="create-a-storage-account"></a>Create a storage account

Amikor Azure Stream Analytics-feladatot hoz létre egy IoT Edge-eszköz futtatásához, azt úgy kell tárolni, hogy meghívható legyen az eszközről. Használhatja a meglévő Azure Storage-fiók, vagy hozzon létre egy újat most.

1. Az Azure Portalon nyissa meg az > **Erőforrás-tárfiók** > **Storage account** **létrehozása**című területet.

1. Adja meg a következő értékeket a tárfiók létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében. |
   | Erőforráscsoport | Azt javasoljuk, hogy ugyanazt az erőforráscsoportot használja az IoT Edge-rövidútmutatók és oktatóanyagok összes teszterőforrásához. Például: **IoTEdgeResources**. |
   | Név | Adja meg a tárfiók egyedi nevét. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |

1. Tartsa meg a többi mező alapértelmezett értékeit, és válassza a **Véleményezés + Létrehozás**lehetőséget.

1. Tekintse át a beállításokat, majd válassza a **Létrehozás lehetőséget.**

### <a name="create-a-new-job"></a>Új feladat létrehozása

1. Az Azure Portalon nyissa meg az**Erőforrás-internet** >  > a dolgok**Stream Analytics-feladat** **című feladatának létrehozása**című részét.

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

   ![Azure Stream Analytics – bemenet hozzáadása](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Válassza az **Edge Hub** elemet a legördülő listából.

1. Az **Új bemenet** panelen adja meg a **temperature** (hőmérséklet) nevet a bemenet aliasaként.

1. Fogadja el az alapértelmezett értékeket a többi mezőben, és válassza a **Mentés** lehetőséget.

1. A **Feladattopológia** területen nyissa meg a **Kimenetek** pontot, és válassza a **Hozzáadás** elemet.

   ![Azure Stream Analytics – kimenet hozzáadása](./media/tutorial-deploy-stream-analytics/asa-output.png)

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

1. A **Konfigurálás**csoportban válassza **a Tárfiók beállításai lehetőséget,** majd válassza **a Tárfiók hozzáadása**lehetőséget.

   ![Azure Stream Analytics – tárfiók hozzáadása](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Válassza ki az oktatóanyag elején létrehozott **tárfiókot** a legördülő menüből.

1. A **Tároló** mezőben válassza az **Új létrehozása** lehetőséget, és adja meg a tárfiók nevét.

1. Kattintson a **Mentés** gombra.

## <a name="deploy-the-job"></a>A feladat üzembe helyezése

Most készen áll az Azure Stream Analytics-feladat IoT Edge-eszközön való üzembe helyezésére.

Ebben a szakaszban az Azure Portal **Modulok beállítása** varázslójával hozhat létre *üzembehelyezési jegyzéket*. Az üzembehelyezési jegyzék egy olyan JSON-fájl, amely leírja az eszközre üzembe helyezendő összes modult, a modul rendszerképeit tároló tárolóregisztrációs adatbázisokat, a modulok kezelésének módját és azt, hogyan kommunikálhatnak egymással a modulok. Az IoT Edge-eszköz lekéri az üzembehelyezési jegyzéket az IoT Hubról, majd az abban lévő információkkal üzembe helyezi és konfigurálja az összes hozzárendelt modult.

Ebben az oktatóanyagban két modult helyezhet üzembe. Az első a **SimulatedTemperatureSensor**, amely egy olyan modul, amely egy hőmérséklet- és páratartalom-érzékelőt szimulál. A második a Stream Analytics-feladat. Az érzékelőmodul biztosítja az adatstreamet, amelyet a feladatlekérdezés elemezni fog.

1. Az Azure Portalon keresse meg az IoT-központot.

1. Nyissa meg az **IoT**Edge-et, majd nyissa meg az IoT Edge-eszköz részleteit tartalmazó lapot.

1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.  

1. Ha korábban telepítette a SimulatedTemperatureSensor modult ezen az eszközön, előfordulhat, hogy automatikusan feltölti. Amennyiben nem, vegye fel a modult a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **IoT Edge-modul** lehetőséget.
   1. A névhez írja be a **SimulatedTemperatureSensor (SimulatedTemperatureSensor ) nevet.**
   1. A kép URI-címénél adja meg az **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** címet.
   1. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.

1. Adja hozzá az Azure Stream Analytics Edge-feladatot a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **Azure Stream Analytics-modul** lehetőséget.
   1. Válassza ki az előfizetést, és a létrehozott Azure Stream Analytics Edge-feladatot.
   1. Kattintson a **Mentés** gombra.

   Miután mentette a módosításokat, a Stream Analytics-feladat részleteit közzéteszi a létrehozott tárolóban.

1. Amikor a Stream Analytics modul hozzáadódik a modulok listájához, válassza ki a nevét, hogy hogyan épül fel, és frissítse a beállításait az **IoT Edge Module frissítése** lapon.

   A **Modulbeállítások** lapon található a **lemezkép** URI-ja, amely egy szabványos Azure Stream Analytics-lemezképre mutat. Ez az egy lemezkép minden Stream Analytics-modulhoz használatos, amely egy IoT Edge-eszközre lesz telepítve.

   A **Moduliker beállításai** lapon látható az ABAJobInfo nevű Azure **ASAJobInfo**Stream Analytics (ASA) tulajdonságot meghatározó JSON. A tulajdonság értéke a feladat tárolóban lévő definíciójára mutat. Ez a tulajdonság a Stream Analytics-lemezkép konfigurálása az adott feladat részleteivel.

   Alapértelmezés szerint a Stream Analytics modul ugyanazt a nevet veszi fel, mint a feladat, amelyen alapul. Ezen az oldalon módosíthatja a modul nevét, de ez nem szükséges.

1. Válassza **a Mégse** vagy **a Mentés lehetőséget.**

1. Jegyezze fel a Stream Analytics-modul nevét, mert a következő lépésben szüksége lesz rá, majd válassza a **Tovább: Útvonalak** lehetőséget a folytatáshoz.

1. Az **Útvonalak** lapon megadhatja, hogy az üzenetek hogyan továbbítódnak a modulok és az IoT Hub között. Az üzenetek név-érték párok használatával készülnek. Cserélje le `route` `upstream` az alapértelmezett és a nevet és az értékeket a következő táblázatban látható párokra, a következő név-/értékpárokra, _a{moduleName}_ példányait az Azure Stream Analytics modul nevére.

    | Név | Érték |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    Az itt megadott útvonalak határozzák meg az adatok áramlását az IoT Edge-eszközben. A telemetriai adatokat simulatedTemperaturesensor küldi az **temperature** IoT Hub és a hőmérséklet-bemenet, amely konfigurálva volt a Stream Analytics-feladat. A **riasztási** kimeneti üzeneteket az IoT Hub és a SimulatedTemperatureSensor modul az alaphelyzetbe állítási parancs aktiválásához küldi el.

1. Válassza a **Tovább lehetőséget: Véleményezés + Létrehozás**.

1. A **Véleményezés + Létrehozás** lapon láthatja, hogy a varázslóban megadott adatok hogyan alakulnak át JSON-telepítési jegyzékfájllá. Ha végzett a jegyzékfájl áttekintésével, válassza a **Létrehozás lehetőséget.**

1. Visszatér az eszköz részleteit tartalmazó lapra. Válassza a **Frissítés**lehetőséget.  

    Látnia kell az új Stream Analytics-modul fut, valamint az IoT Edge-ügynök és az IoT Edge hub-modulok. Eltarthat néhány percig, amíg az információ eléri az IoT Edge-eszközt, majd az új modulok indításához. Ha nem látja azonnal futni a modulokat, folytassa az oldal frissítését.

    ![Az eszköz által jelentett SimulatedTemperatureSensor és ASA modul](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Adatok megtekintése

Most az IoT Edge-eszköz, hogy ellenőrizze az Azure Stream Analytics modul és a SimulatedTemperatureSensor modul közötti interakció.

1. Ellenőrizze, hogy fut-e mindegyik modul a Dockerben:

   ```cmd/sh
   iotedge list  
   ```

1. Tekintse át az összes rendszernaplót és metrikaadatot. Használja a Stream Analytics-modul nevét:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Tekintse meg a reset parancsot befolyásolja a SimulatedTemperatureSensor megtekintésével az érzékelő naplók:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Meg lehet nézni a gép hőmérséklete fokozatosan emelkedik, amíg el nem éri a 70 fok 30 másodpercig. A Stream Analytics-modul ekkor kiváltja a rendszer alaphelyzetbe állítását, és a géphőmérséklet visszaáll 21 fokra.

   ![A parancskimenet visszaállítása modulnaplókba](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások, amelyek ebben a cikkben a költségek elkerülése érdekében.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Stream Analytics-feladatot hozott létre az IoT Edge-eszközből származó adatok elemzéséhez. Ezután letöltötte az Azure Stream Analytics-modult az IoT Edge-eszközre, hogy helyileg lehessen feldolgozni és megválaszolni a hőmérséklet-növekedési adatokat, valamint az összesített adatstreamet a felhőbe feltölteni. A többi oktatóanyagra tovább lépve megtudhatja, hogyan lehet az Azure IoT Edge használatával egyéb üzleti megoldásokat létrehozni.

> [!div class="nextstepaction"]
> [Azure Machine Learning-modell üzembe helyezése modulként](tutorial-deploy-machine-learning.md)
