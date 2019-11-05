---
title: Azure Machine Learning üzembe helyezése egy eszközön – Azure IoT Edge | Microsoft Docs
description: Ebben az oktatóanyagban létrehoz egy Azure Machine Learning modellt, majd üzembe helyezi modulként egy peremhálózati eszközön
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/16/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 28e5e09ea64f7ac1272e8ed126d5b4153b952c1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494044"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Oktatóanyag: Az Azure Machine Learning üzembe helyezése IoT Edge-modulként (előzetes verzió)

A Azure Notebooks használatával fejlesztheti a Machine learning modult, és üzembe helyezheti azt egy Azure IoT Edge rendszert futtató linuxos eszközön. 

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure Machine Learning-modul üzembe helyezésén, amely előrejelzi az eszközök meghibásodását szimulált géphőmérsékleti adatok alapján. A IoT Edge Azure Machine Learningával kapcsolatos további információkért tekintse meg a [Azure Machine learning dokumentációját](../machine-learning/service/how-to-deploy-to-iot.md).

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

* Az Azure-beli virtuális gépeket IoT Edge eszközként is használhatja a [Linux](quickstart-linux.md)gyors üzembe helyezésének lépésein követve.
* A Azure Machine Learning modul nem támogatja a Windows-tárolókat.
* Az Azure Machine Learning modul nem támogatja az ARM-processzorokat.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Egy Azure Machine Learning-munkaterület. Kövesse a Azure Portal használatának első lépései című témakör útmutatását, és Ismerje meg, hogyan használhatja [a Azure Machine learning](../machine-learning/service/quickstart-get-started.md) .
   * Jegyezze fel a munkaterület nevét, az erőforráscsoportot és az előfizetés AZONOSÍTÓját. Ezek az értékek a Azure Portal munkaterület áttekintésében érhetők el. Az oktatóanyag későbbi részében ezeket az értékeket fogja használni az Azure-jegyzetfüzetek munkaterület-erőforrásokhoz való összekapcsolásához. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning modul létrehozása és üzembe helyezése

Ebben a szakaszban a betanított gépi tanulási modell fájljait és egy Azure Machine Learning tárolóba alakítja át. A Docker-rendszerképhez szükséges összetevők megtalálhatók az [Azure IoT Edge GitHub-adattár AI-eszközkészletében](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Az alábbi lépéseket követve feltöltheti a tárházat Microsoft Azure Notebooksba a tároló létrehozásához, és leküldheti a Azure Container Registryba.


1. Navigáljon a Azure Notebooks projektjeihez. A [Azure Portal](https://portal.azure.com) Azure Machine learning munkaterületéről, illetve az Azure-fiókkal való bejelentkezéshez is bejelentkezhet a [Microsoft Azure Notebooksba](https://notebooks.azure.com/home/projects) .

2. Válassza a GitHub-tárház **feltöltése**lehetőséget.

3. Adja meg a következő GitHub-adattár nevét: `Azure/ai-toolkit-iot-edge`. Ha meg szeretné őrizni a saját projektet, törölje a **nyilvános** mezőt. Válassza az **Importálás**lehetőséget. 

4. Az importálás befejezése után navigáljon az új **AI-Toolkit-IOT-Edge** projekthez, és nyissa meg a **IoT Edge anomália észlelése oktatóanyag** mappát. 

5. Ellenőrizze, hogy a projekt fut-e. Ha nem, válassza **a Futtatás ingyenes számításkor**lehetőséget.

   ![Futtatás ingyenes számítási feladatokban](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Nyissa meg a **aml_config/config. JSON** fájlt.

7. Szerkessze a konfigurációs fájlt, hogy tartalmazza az Azure-előfizetés AZONOSÍTÓjának, az előfizetéshez tartozó erőforráscsoport, valamint a Azure Machine Learning munkaterület nevét. Ezek az értékek az Azure-beli munkaterület **Áttekintés** szakaszában olvashatók be. 

8. Mentse a konfigurációs fájlt.

9. Nyissa meg a **00-anomália-Detection-tutorial. ipynb** fájlt.

10. Ha a rendszer kéri, válassza ki a **Python 3,6** kernelt, majd válassza a **kernel beállítása**lehetőséget.

11. Szerkessze a jegyzetfüzet első celláját a megjegyzések részben leírtak szerint. Használja ugyanazt az erőforráscsoportot, az előfizetés AZONOSÍTÓját és a munkaterület nevét, amelyet a konfigurációs fájlhoz adott hozzá.

12. Futtassa a cellákat a jegyzetfüzetben, és válassza ki őket, és válassza a **Futtatás** vagy a `Shift + Enter`megnyomása lehetőséget.

    >[!TIP]
    >Az anomália-észlelési oktatóanyagban található jegyzetfüzetek némelyike nem kötelező, mert olyan erőforrásokat hoz létre, amelyeket egyes felhasználók még nem használhatnak, mint például egy IoT Hub. Ha az első cellába helyezi a meglévő erőforrásadatokat, hibaüzeneteket fog kapni, ha olyan cellákat futtat, amelyek új erőforrásokat hoznak létre, mert az Azure nem hoz létre duplikált erőforrásokat. Ez rendben van, így figyelmen kívül hagyhatja a hibákat, vagy kihagyhatja a választható szakaszt. 

A jegyzetfüzet összes lépésének elvégzésével elvégezte az anomáliák észlelésének modelljét, amely Docker-tárolói rendszerképként lett létrehozva, és leküldte a rendszerképet Azure Container Registry. Ezután tesztelte a modellt, és végül üzembe helyezte a IoT Edge eszközön. 

## <a name="view-container-repository"></a>Tároló adattárának megtekintése

Győződjön meg arról, hogy a tároló rendszerképének létrehozása és tárolása a gépi tanulási környezethez társított Azure Container registryben történt. Az előző szakaszban használt jegyzetfüzet automatikusan átadja a tároló rendszerképét és a beállításjegyzék hitelesítő adatait a IoT Edge eszköz számára, de tudnia kell, hogy hol tárolja őket, így később is megtalálhatja az adatokat. 

1. A [Azure Portal](https://portal.azure.com)navigáljon a Machine learning szolgáltatás munkaterületére. 

2. Az **Áttekintés** szakasz a munkaterület részleteit, valamint a hozzájuk tartozó erőforrásokat sorolja fel. Válassza ki a **beállításazonosító** értékét, amelynek a munkaterület nevét, majd a véletlenszerűen megadott számokat kell megadnia. 

3. A tároló beállításjegyzékében válassza a **tárolók**lehetőséget. Meg kell jelennie egy **tempanomalydetection** nevű tárháznak, amelyet a korábbi szakaszban futtatott jegyzetfüzetből hoztak létre. 

4. Válassza a **tempanomalydetection**lehetőséget. Látnia kell, hogy az adattárnak van egy címkéje: **1**. 

   Most, hogy már ismeri a beállításjegyzék nevét, a tárház nevét és a címkét, ismeri a tároló teljes rendszerképének elérési útját. A képek elérési útjai úgy néznek ki, mint a **\<registry_name\>. azurecr.IO/tempanomalydetection:1**. A rendszerkép elérési útja segítségével telepítheti a tárolót IoT Edge eszközökre. 

5. A tároló beállításjegyzékében válassza a **hozzáférési kulcsok**elemet. Meg kell jelennie a hozzáférési hitelesítő adatok számának, beleértve a **bejelentkezési kiszolgálót** , valamint a **felhasználónevet**és a **jelszót** a rendszergazda felhasználó számára.

   Ezek a hitelesítő adatok az üzembe helyezési jegyzékben is szerepelhetnek, így biztosítható, hogy a IoT Edge eszköz hozzáférjen a tárolók rendszerképeihez a beállításjegyzékből. 

Most már tudja, hol tárolja a Machine Learning tároló képét. A következő szakasz végigvezeti a IoT Edge eszközön modulként futó tároló megtekintésének lépésein. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Megtekintheti az egyes IoT Edge-modulok által létrehozott üzeneteket, és megtekintheti az IoT-központjába küldött üzeneteket.

### <a name="view-data-on-your-iot-edge-device"></a>Adatok megtekintése az IoT Edge-eszközön

IoT Edge-eszközén megtekintheti az egyes modulok által küldött üzeneteket.

Előfordulhat, hogy a `iotedge` parancsok futtatásához a `sudo` for emelt szintű engedélyekkel kell rendelkeznie. A kijelentkezés és a visszajelentkezés az eszközre automatikusan frissíti az engedélyeket.

1. Tekintse meg az összes modult az IoT Edge-eszközön.

   ```cmd/sh
   iotedge list
   ```

2. Tekintse meg az egy adott eszközről küldött üzeneteket. Használja az előző parancs kimenetéből származó modulnevet.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Az IoT-központba érkező adatok megtekintése

Megtekintheti az eszközről a felhőbe irányuló üzeneteket, amelyeket az IoT hub a Visual Studio Code (korábbi nevén Azure IoT Toolkit Extension) [azure IoT hub Toolkit-bővítményének](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) használatával kap.

A következő lépések azt mutatják be, hogyan állítható be a Visual Studio Code az IoT-központba érkező, eszközről a felhőbe küldött üzenetek monitorozására.

1. A Visual Studio Code-ban válassza az **IoT Hub-eszközök** elemet.

2. Ezt követően válassza a **...** , majd az **IoT Hub kapcsolati sztring beállítása** lehetőséget a menüben.

   ![IoT Hub-kapcsolatok karakterláncának beállítása](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Az oldal tetején megnyíló szövegmezőben adja meg a saját IoT Hubjához tartozó iothubowner kapcsolati sztringet. IoT Edge-eszközének ekkor meg kell jelennie az IoT Hub-eszközök listájában.

4. Válassza a **...** újra lehetőséget, majd válassza a **figyelés a beépített esemény végpontjának megkezdése**lehetőséget.

5. Figyelje meg a tempSensor felől öt másodpercenként érkező üzeneteket. Az üzenettörzs egy **anomália**nevű tulajdonságot tartalmaz, amelyet a machinelearningmodule igaz vagy hamis értékkel rendelkezik. Az **AzureMLResponse** tulajdonság „OK” értéket tartalmaz, ha a modell sikeresen lefutott.

   ![Azure Machine Learning válasz az üzenet törzsében](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy, az Azure Machine Learning által működtetett IoT Edge-modult. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja jelentőséggel bíró üzleti információkká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Képek osztályozása a Custom Vision szolgáltatás használatával](tutorial-deploy-custom-vision.md)

