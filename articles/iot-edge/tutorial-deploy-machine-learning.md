---
title: Oktatóanyag – Az Azure Machine Learning üzembe helyezése egy eszközre az Azure IoT Edge használatával
description: Ebben az oktatóanyagban hozzon létre egy Azure Machine Learning-modellt, majd telepítse azt modulként egy peremhálózati eszközre
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bfbf4a432f720b683ded4c85530135d86b24eba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76773000"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Oktatóanyag: Az Azure Machine Learning üzembe helyezése IoT Edge-modulként (előzetes verzió)

Azure Notebookok segítségével egy gépi tanulási modul, és üzembe helyezheti azt egy Azure IoT Edge-et futtató Linux-eszközön.
Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure Machine Learning-modul üzembe helyezésén, amely előrejelzi az eszközök meghibásodását szimulált géphőmérsékleti adatok alapján. Az Azure Machine Learning ioT Edge-en című [témakörben](../machine-learning/how-to-deploy-and-where.md)olvashat bővebben.

>[!NOTE]
>Az Azure Machine Learning moduljai nyilvános előzetes verzióban érhetők el az Azure IoT Edge szolgáltatásban.

Az ebben az oktatóanyagban létrehozott Azure Machine Learning-modul kiolvassa az eszköz által előállított környezeti adatokat, illetve rendellenesként vagy nem rendellenesként jelöli meg az üzeneteket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure Machine Learning-modul létrehozása
> * Modultároló leküldése egy Azure-beli tárolóregisztrációs adatbázisba
> * Azure Machine Learning-modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Az Azure virtuális gépek et IoT Edge-eszközként is használhatja a [Linux](quickstart-linux.md)gyorshasználatának lépéseit követve.
* Az Azure Machine Learning modul nem támogatja a Windows-tárolók.
* Az Azure Machine Learning modul nem támogatja az ARM processzorokat.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Egy Azure Machine Learning-munkaterület. Kövesse az [Azure Portal használata az Azure Machine Learning használatának](../machine-learning/tutorial-1st-experiment-sdk-setup.md) lépéseit, és ismerje meg, hogyan használhatja.
  * Jegyezze fel a munkaterület nevét, erőforráscsoportját és előfizetés-azonosítóját. Ezek az értékek mind elérhetők a munkaterület áttekintését az Azure Portalon. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni egy Azure-jegyzetfüzet csatlakoztatásához a munkaterületerőforrásaihoz.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning-modul létrehozása és üzembe helyezése

Ebben a szakaszban konvertálja a betanított gépi tanulási modell fájljait, és egy Azure Machine Learning-tárolóba. A Docker-rendszerképhez szükséges összetevők megtalálhatók az [Azure IoT Edge GitHub-adattár AI-eszközkészletében](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Kövesse az alábbi lépéseket a tárház microsoft Azure-jegyzetfüzetekbe való feltöltéséhez a tároló létrehozásához és az Azure Container Registry-hez való leküldése.

1. Keresse meg az Azure Notebooks-projekteket. Az Azure Machine Learning-munkaterületről az [Azure Portalon](https://portal.azure.com) vagy a Microsoft Azure Notebooks-ba az [Azure-fiókjával](https://notebooks.azure.com/home/projects) bejelentkezhet.

2. Válassza **a GitHub-táregyesítés feltöltése lehetőséget.**

3. Adja meg a következő GitHub-tárház nevét: `Azure/ai-toolkit-iot-edge`. Törölje a jelet a **Nyilvános** jelölőnégyzetből, ha a projektet titokban szeretné tartani. Kattintson az **Importálás** gombra.

4. Miután az importálás befejeződött, lépjen be az új **ai-toolkit-iot-edge** projektbe, és nyissa meg az **IoT Edge anomáliadetektálási oktatóanyag** mappáját.

5. Ellenőrizze, hogy fut-e a projekt. Ha nem, válassza **a Futtatás ingyenes számításon**lehetőséget.

   ![Futtatás ingyenes számításon](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Nyissa meg a **aml_config/config.json** fájlt.

7. Szerkessze a konfigurációs fájlt, hogy tartalmazza az Azure-előfizetés-azonosító, egy erőforráscsoport értékeit az előfizetésben, és az Azure Machine Learning munkaterület nevét. Ezeket az értékeket az Azure-beli munkaterület **áttekintése** szakaszából szerezheti be.

8. Mentse a konfigurációs fájlt.

9. Nyissa meg a **00-anomália-detektálás-tutorial.ipynb** fájlt.

10. Amikor a rendszer kéri, jelölje ki a **Python 3.6** kernelt, majd válassza **a Kernel beállítása**lehetőséget.

11. A megjegyzésekben található utasításoknak megfelelően szerkeszti a jegyzetfüzet első celláját. Használja ugyanazt az erőforráscsoportot, előfizetés-azonosítót és munkaterületi nevet, amelyet a konfigurációs fájlhoz adott.

12. Futtassa a jegyzetfüzet celláit a futtatásuk, illetve a **Futtatás** vagy lenyomása `Shift + Enter`lehetőségre kattintva.

    >[!TIP]
    >Az anomáliadetektálási oktatóanyag-jegyzetfüzet egyes cellái nem kötelezőek, mert olyan erőforrásokat hoznak létre, amelyegyes felhasználók számára előfordulhat, hogy még nem, például egy IoT Hub. Ha a meglévő erőforrás-adatokat az első cellába helyezi, hibaüzenetet kap, ha futtatja az új erőforrásokat létrehozó cellákat, mert az Azure nem hoz létre ismétlődő erőforrásokat. Ez rendben van, és figyelmen kívül hagyhatja a hibákat, vagy teljesen kihagyhatja azokat a választható szakaszokat.

A jegyzetfüzet ben leírt összes lépés végrehajtásával betanított egy anomáliaészlelési modellt, egy Docker-tárolórendszerképként építette fel, és lelökte azt az Azure Container Registry-be. Ezután tesztelte a modellt, és végül üzembe helyezte az IoT Edge-eszközre.

## <a name="view-container-repository"></a>Tárolótár megtekintése

Ellenőrizze, hogy a tárolórendszerkép sikeresen létrejött-e, és a gépi tanulási környezethez társított Azure-tároló beállításjegyzékben tárolva. Az előző szakaszban használt jegyzetfüzet automatikusan biztosította a tárolórendszerképet és a beállításjegyzék hitelesítő adatait az IoT Edge-eszközhöz, de tudnia kell, hogy hol vannak tárolva, hogy később saját maga is megtalálhassa az információkat.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a Machine Learning-szolgáltatás munkaterületét.

2. Az **Áttekintés** szakasz felsorolja a munkaterület részleteit, valamint a kapcsolódó erőforrásokat. Válassza ki a **Rendszerleíróadatbázis** értékét, amely a munkaterület nevének, majd a véletlen számoknak kell lennie.

3. A tárolóbeállításban válassza az **Adattárak**lehetőséget. Meg kell jelennie egy tároló nevű **tempanomalydetection,** amely a korábban futtatott jegyzetfüzet által létrehozott.

4. Válassza **a tempanomalydetection lehetőséget.** Látnia kell, hogy a tárház egy címkével rendelkezik: **1**.

   Most, hogy már ismeri a rendszerleíró adatbázis nevét, a tárház nevét és a címke, ismeri a tároló teljes rendszerkép elérési útját. A képelérési utak ** \<a\>registry_name .azurecr.io/tempanomalydetection:1**. A rendszerkép elérési útja segítségével telepítheti ezt a tárolót IoT Edge-eszközökre.

5. A tároló beállításjegyzékében válassza az **Access-kulcsok**lehetőséget. Számos hozzáférési hitelesítő adatot kell látnia, beleértve a **bejelentkezési kiszolgálót** és a **felhasználónevet**, valamint a rendszergazdai felhasználók **jelszavát.**

   Ezek a hitelesítő adatok szerepelhetnek a központi telepítési jegyzékben, hogy az IoT Edge-eszköz hozzáférést a tárolórendszerképek lekérése a beállításjegyzékből.

Most már tudja, hol tárolja a Machine Learning-tároló rendszerképet. A következő szakasz ban végigvezeti a lépéseket, hogy a tároló futó modulként az IoT Edge-eszközön.

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Megtekintheti az egyes IoT Edge-modulok által létrehozott üzeneteket, és megtekintheti az IoT-központjába küldött üzeneteket.

### <a name="view-data-on-your-iot-edge-device"></a>Adatok megtekintése az IoT Edge-eszközön

IoT Edge-eszközén megtekintheti az egyes modulok által küldött üzeneteket.

Előfordulhat, hogy `sudo` a parancsok futtatásához `iotedge` emelt szintű engedélyeket kell használnia. A kijelentkezés és az eszközre való bejelentkezés automatikusan frissíti az engedélyeket.

1. Tekintse meg az összes modult az IoT Edge-eszközön.

   ```cmd/sh
   iotedge list
   ```

2. Tekintse meg az egy adott eszközről küldött üzeneteket. Használja az előző parancs kimenetéből származó modulnevet.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Az IoT-központba érkező adatok megtekintése

Megtekintheti az IOt-központ által fogadott eszközök ről felhőbe irányuló üzeneteit a [Visual Studio-kód Azure IoT Hub bővítményével.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

A következő lépések azt mutatják be, hogyan állítható be a Visual Studio Code az IoT-központba érkező, eszközről a felhőbe küldött üzenetek monitorozására.

1. A Visual Studio Code-ban válassza az **IoT Hub-eszközök** elemet.

2. Ezt követően válassza a **...**, majd az **IoT Hub kapcsolati sztring beállítása** lehetőséget a menüben.

   ![IoT hub kapcsolati karakterláncának beállítása](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Az oldal tetején megnyíló szövegmezőben adja meg a saját IoT Hubjához tartozó iothubowner kapcsolati sztringet. IoT Edge-eszközének ekkor meg kell jelennie az IoT Hub-eszközök listájában.

4. Válassza a **...** lehetőséget, majd válassza **a Beépített eseményvégpont indítása**lehetőséget.

5. Figyelje meg a tempSensor felől öt másodpercenként érkező üzeneteket. Az üzenet **törzse egy anomália**nevű tulajdonságot tartalmaz, amelyet a machinelearningmodule igaz vagy hamis értékkel biztosít. Az **AzureMLResponse** tulajdonság „OK” értéket tartalmaz, ha a modell sikeresen lefutott.

   ![Az Azure Machine Learning válasza az üzenettörzsben](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy, az Azure Machine Learning által működtetett IoT Edge-modult. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja jelentőséggel bíró üzleti információkká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Képek osztályozása a Custom Vision szolgáltatás használatával](tutorial-deploy-custom-vision.md)
