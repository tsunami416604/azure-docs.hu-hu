---
title: "Az Azure Machine Learning Azure IoT oldala telepítése |} Microsoft Docs"
description: "Azure Machine Learning peremhálózati eszköz egy modul telepítése"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9b8475dcc51fb24fadd1faa4a2008b25a4464080
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Azure Machine Learning egy IoT peremhálózati modulként telepítése – előzetes

Az IoT-Edge modulok, amely megvalósítja az üzleti logikát, közvetlenül az IoT peremhálózati eszköz a kód telepítésére használhatja. Ez az oktatóanyag bemutatja, hogyan üzembe helyezése az Azure Machine Learning modulban, amikor egy eszköz nem működik a szimulált IoT peremhálózati eszközön létrehozott tartozó telepítése Azure IoT Edge a szimulált eszköz a érzékelőadatait alapján előrejelzi [Windows] [ lnk-tutorial1-win] vagy [Linux] [ lnk-tutorial1-lin] oktatóanyagok. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Az IoT-peremhálózati eszköz egy Azure Machine Learning-modul telepítése
> * Adatok generált megtekintése

Ha szeretné használni a saját [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) fogja végrehajtani a megoldás a modell [a modell rendszerbe állítása](https://aka.ms/aml-iot-edge-doc) IoT Edge és a gazdagép, a tároló beállításjegyzékbeli például [Azure tároló beállításjegyzék](../container-registry/index.yml) vagy Docker.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure IoT peremhálózati eszköz, a gyors üzembe helyezés vagy első oktatóanyaga, amely létrehozta.
* Az IoT hub, amely az IoT-peremhálózati eszköz csatlakozik az IoT-központ kapcsolati karakterláncát.
* Az Azure ML-tároló

## <a name="create-the-azure-ml-container"></a>Az Azure ML-tároló létrehozása
Az Azure ML tároló létrehozásához kövesse a megjelenő utasításokat a [Azure IoT peremhálózati AI eszköztára](https://aka.ms/aml-iot-edge-anomaly-detection).

## <a name="run-the-solution"></a>Futtassa a megoldás

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg az IoT hub.
1. Ugrás a **IoT peremhálózati (előzetes verzió)** és az IoT-peremhálózati eszköz kiválasztásához.
1. Válassza ki **modulok beállítása**.
1. Válassza ki **hozzáadása IoT peremhálózati modul**.
1. Az a **neve** mezőbe írja be `tempSensor`.
1. Az a **lemezkép URI** mezőbe írja be `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Hagyja változatlanul az egyéb beállításokat, és válassza ki **mentése**.
1. Továbbra is a **modulok hozzáadása** jelölje be a lépésben **hozzáadása IoT peremhálózati modul** újra.
1. Az a **neve** mezőbe írja be az előző szakaszban végzett a tároló nevét. Tekintse meg a [Azure IoT peremhálózati AI eszköztára](https://aka.ms/aml-iot-edge-anomaly-detection) segítség keresése a neve.
1. Az a **kép** mezőbe írja be a lemezkép URI-azonosítója a tárolóhoz, amelybe az előző szakaszban végzett. Tekintse meg a [Azure IoT peremhálózati AI eszköztára](https://aka.ms/aml-iot-edge-anomaly-detection) segítség keresése a lemezképet.
1. Kattintson a **Save** (Mentés) gombra.
1. Vissza a **modulok hozzáadása** lépésre, a **következő**.
1. A modul az útvonalak frissítése:
1. Az a **útvonalak megadása** . lépés:, másolja az alábbi JSON a szövegmezőbe. Modulok összes üzenetet a peremhálózati futásidejű tegye közzé. A futásidejű deklaratív szabályok határozzák meg, ahol az üzenetek áramlását. Ebben az oktatóanyagban kell két útvonalak. Az első útvonal szállításokkal a hőmérséklet-érzékelő üzeneteit a machine learning modulra "mlInput" végpont keresztül ez az a végpont, amelyek az összes Azure Machine Learning modulok használják. A második útvonal szállításokkal üzenetek IoT-központ a machine learning modulban. Ez az útvonal "mlOutput" az összes Azure Machine Learning modulok kimeneti adatokat használó endput, és "fölérendelt" célja egy speciális, amely közli a peremhálózati Hub üzeneteket küldhet az IoT-központ. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Kattintson a **Tovább** gombra. 
1. Az "Felülvizsgálati sablonja" lépésben "Küldés" gombra. 
1. Térjen vissza az eszköz részleteit megjelenítő oldalra, és kattintson a "Frissítés".  Az új "machinelearningmodule" a "tempSensor modul" és "IoT peremhálózati futásidejű" együtt fut kell megjelennie.

## <a name="view-generated-data"></a>Adatok generált megtekintése

 A Visual STUDIO Code, használja a **nézet |} Paletta... parancs |} IoT: Start figyelési D2C üzenetek** parancs az IoT Hub érkező adatok figyelésére. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure Machine Learning technológiával IoT peremhálózati modul telepítette. Bármely más módszereket, amelyek segítségével Azure IoT peremhálózati tájékozódhat az adatok az üzleti elemzések készítése a peremhálózaton kapcsolja be az egyéb oktatóanyagok be tovább.

> [!div class="nextstepaction"]
> [Egy Azure-függvény modul telepítése](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md