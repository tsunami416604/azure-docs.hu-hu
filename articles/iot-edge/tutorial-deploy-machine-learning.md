---
title: Oktatóanyag az Azure Machine Learning az Azure IoT Edge - eszköz üzembe helyezése |} A Microsoft Docs
description: Az oktatóanyagban egy Azure Machine Learning-példányt helyezünk üzembe modulként egy peremhálózati eszközre.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/21/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0f7201ffd71a6bc3e68f83f005c693cae4fef84a
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649000"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Oktatóanyag: Azure Machine Learning, az IoT Edge-modul (előzetes verzió) üzembe helyezése

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure Machine Learning-modul üzembe helyezésén, amely előrejelzi az eszközök meghibásodását szimulált géphőmérsékleti adatok alapján. Az IoT Edge-ben az Azure Machine Learning szolgáltatással kapcsolatos további információkért lásd: [Azure Machine Learning dokumentációs](../machine-learning/service/how-to-deploy-to-iot.md).

Az ebben az oktatóanyagban létrehozott Azure Machine Learning-modul kiolvassa az eszköz által előállított környezeti adatokat, illetve rendellenesként vagy nem rendellenesként jelöli meg az üzeneteket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Machine Learning-modul létrehozása
> * Modultároló leküldése egy Azure-beli tárolóregisztrációs adatbázisba
> * Azure Machine Learning-modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése

>[!NOTE]
>Az Azure Machine Learning moduljai nyilvános előzetes verzióban érhetők el az Azure IoT Edge szolgáltatásban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [Linux-](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójának lépéseit követve.
* Az Azure Machine Learning-modul nem támogatja az ARM processzorokat.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Egy Azure Machine Learning-munkaterület. A létrehozáshoz kövesse [a modellek IoT Edge-ben történő üzembe helyezésének előkészítését](../machine-learning/service/how-to-deploy-to-iot.md) ismertető témakörben található utasításokat.


### <a name="disable-process-identification"></a>Folyamatazonosítás letiltása

>[!NOTE]
>
> Az Azure Machine Learning előzetes verziója nem támogatja folyamatazonosítás biztonsági funkcióját, amely az IoT Edge szolgáltatásban alapértelmezés szerint engedélyezve van.
> Az alábbi lépések végrehajtásával tilthatja le. Ez azonban nem használható éles környezetben. Ezeket a lépéseket csak Linuxon kell végrehajtani, mivel a Windows Edge-futtatókörnyezet telepítésének is ugyanezek a lépései.

IoT Edge-eszközén a folyamatazonosítás letiltásához meg kell adnia a **workload_uri** és a **management_uri** értékhez az IP-címet és portot az IoT Edge-démon konfigurációjának **connect** (csatlakozás) szakaszában.

Elsőként szerezze be az IP-címet. Írja be az `ifconfig` parancsot a parancssorba, majd másolja ki a **docker0** interfész IP-címét.

Szerkessze az IoT Edge-démon konfigurációs fájlját:

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Frissítse a konfiguráció **connect** (csatlakozás) szakaszát az IP-címmel. Például:
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Írja be ugyanezeket a címeket a konfiguráció **listen** (figyelés) szakaszába. Például:

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Hozzon létre egy IOTEDGE_HOST nevű környezeti változót a management_uri címével (a végleges beállításhoz adja hozzá ehhez: `/etc/environment`). Például:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-machine-learning-service-container"></a>Az Azure Machine Learning tároló létrehozása
Ebben a szakaszban a betanított modell fájlok letöltéséhez, és átalakíthatja az Azure Machine Learning szolgáltatás tárolóba.

Ha szeretne létrehozni egy Docker-tárolót, amely a gépi tanulási modellt tartalmazza, kövesse [a modellek IoT Edge-ben történő üzembe helyezésének előkészítését](../machine-learning/service/how-to-deploy-to-iot.md) ismertető témakörben található utasításokat.  A Docker-rendszerképhez szükséges összetevők megtalálhatók az [Azure IoT Edge GitHub-adattár AI-eszközkészletében](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a name="view-the-container-repository"></a>A tárolóadattár megtekintése

Ellenőrizze, hogy a tárolórendszerkép sikeresen létrejött-e és a Machine Learning-környezethez tartozó Azure tárolóregisztrációs adatbázisba lett-e mentve.

1. Az [Azure Portalon](https://portal.azure.com) lépjen **Az összes szolgáltatás** elemre, és válassza a **Tárolóregisztrációs adatbázisok** lehetőséget.
2. Válassza ki a saját beállításjegyzékét. A névnek az **mlcr** karakterekkel kell kezdődnie, továbbá a modulkezelési alkalmazás beállításakor megadott erőforráscsoporthoz, helyhez és előfizetéshez kell tartoznia.
3. Válassza a **Hozzáférési kulcsok** elemet
4. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** mezők értékeit.  Ezek az adatok ahhoz kellenek, hogy az Edge-eszközeiről is hozzá tudjon férni a beállításjegyzékhez.
5. Válassza az **Adattárak** elemet
6. Válassza a **machinelearningmodule** elemet
7. Most már rendelkezésére áll a tároló rendszerképének teljes elérési útja. Jegyezze fel a rendszerkép elérési útját, mert szüksége lesz rá a következő szakaszban. Az elérési útnak a következőképpen kell kinéznie: **<tárolóregisztrációs_adatbázis_neve>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az IoT-központot.

1. Lépjen az **IoT Edge** részhez, és válassza ki az IoT Edge-eszközt.

1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.

1. A **Registry Settings** (Regisztrációs adatbázis beállításai) szakaszban adja meg az Azure tárolóregisztrációs adatbázisból másolt hitelesítő adatokat.

   ![A jegyzékfájl a tárolójegyzék hitelesítő adatainak hozzáadása](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. Ha már üzembe helyezte a tempSensor modult az IoT Edge-eszközön, akkor lehet, hogy automatikusan ki van töltve. Ha a modul még nem szerepel a listában, akkor vegye fel.

    1. Kattintson a **Hozzáadás** gombra, és válassza az **IoT Edge-modul** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `tempSensor`.
    3. A **Rendszerkép URI** mezőbe írja be a következőt: `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
    4. Kattintson a **Mentés** gombra.

1. Adja hozzá az Ön által létrehozott Machine Learning-modult.

    1. Kattintson a **Hozzáadás** gombra, és válassza az **IoT Edge-modul** lehetőséget.
    1. A **Név** mezőbe írja a következőt: `machinelearningmodule`
    1. A **Rendszerkép** mezőbe írja be a rendszerkép címét, például a következőt: `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Kattintson a **Mentés** gombra.

1. Az **Add Modules** (Modulok hozzáadása) lépésben kattintson a **Next** (Tovább) gombra.

1. Az **Útvonalak megadása** lépésben másolja az alábbi JSON-t a szövegmezőbe. Az első útvonal a hőmérséklet-érzékelőből a Machine Learning-modulba szállítja az üzeneteket az összes Azure Machine Learning-modul által használt „amlInput” végponton keresztül. A második útvonal a Machine Learning-modulból az IoT Hubra szállítja az üzeneteket. Ebben az útvonalban az „amlOutput” az a végpont, amelyet az összes Azure Machine Learning-modul használ az adatok kimenetének küldéséhez, az „$upstream” pedig az IoT Hubot jelöli.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. Kattintson a **Tovább** gombra.

1. Az **Üzembe helyezés áttekintése** lépésben kattintson a **Küldés** elemre.

1. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre.  Látható, hogy az új **machinelearningmodule** modul fut a **tempSensor** modullal és az IoT Edge-futtatókörnyezeti modulokkal együtt.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Megtekintheti az egyes IoT Edge-modulok által létrehozott üzeneteket, és megtekintheti az IoT-központjába küldött üzeneteket.

### <a name="view-data-on-your-iot-edge-device"></a>Adatok megtekintése az IoT Edge-eszközön

IoT Edge-eszközén megtekintheti az egyes modulok által küldött üzeneteket.

Ha ezeket a parancsokat Linux-eszközön hajtja végre, lehetséges, hogy használnia kell a `sudo` parancsot az emelt szintű engedélyekhez.

1. Tekintse meg az összes modult az IoT Edge-eszközön.

   ```cmd/sh
   iotedge list
   ```

2. Tekintse meg az egy adott eszközről küldött üzeneteket. Használja az előző parancs kimenetéből származó modulnevet.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Az IoT-központba érkező adatok megtekintése

Az eszköz a felhőbe irányuló üzeneteket az IoT hub által fogadott használatával megtekintheti a [Azure IoT Hub-eszközkészlet bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (korábbi nevén Azure IoT-eszközkészlet bővítmény).

A következő lépések azt mutatják be, hogyan állítható be a Visual Studio Code az IoT-központba érkező, eszközről a felhőbe küldött üzenetek monitorozására.

1. A Visual Studio Code-ban válassza az **IoT Hub-eszközök** elemet.

2. Ezt követően válassza a **...**, majd az **IoT Hub kapcsolati sztring beállítása** lehetőséget a menüben.

   ![Az IoT Hub kapcsolati karakterlánc beállítása](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Az oldal tetején megnyíló szövegmezőben adja meg a saját IoT Hubjához tartozó iothubowner kapcsolati sztringet. IoT Edge-eszközének ekkor meg kell jelennie az IoT Hub-eszközök listájában.

4. Ezt követően ismét válassza a **...**, majd a **D2C üzenet monitorozásának megkezdése** lehetőséget.

5. Figyelje meg a tempSensor felől öt másodpercenként érkező üzeneteket. Az üzenettörzs tartalmaz egy **anomaly** nevű tulajdonságot, amelyhez a machinelearningmodule igaz vagy hamis értéket rendel. Az **AzureMLResponse** tulajdonság „OK” értéket tartalmaz, ha a modell sikeresen lefutott.

   ![Az Azure Machine Learning szolgáltatás válasza az üzenet törzse](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy, az Azure Machine Learning által működtetett IoT Edge-modult. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja jelentőséggel bíró üzleti információkká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Képek osztályozása a Custom Vision szolgáltatás használatával](tutorial-deploy-custom-vision.md)

