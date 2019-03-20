---
title: Az Azure IoT Edge - eszköz üzembe helyezése az Azure Machine Learning |} A Microsoft Docs
description: Ebben az oktatóanyagban létrehoz egy Azure Machine Learning-modellhez, majd üzembe helyezése modulként edge-eszköz
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 985f1f73fbfc8c75df8393615fca32f5d1c08b9d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078312"
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

* Használhatja az Azure virtuális gép IoT Edge-eszköz esetében ez a rövid útmutató lépéseit követve [Linux](quickstart-linux.md).
* Az Azure Machine Learning modul nem támogatja a Windows-tárolók.
* Az Azure Machine Learning modul nem támogatja az ARM-processzort.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Egy Azure Machine Learning-munkaterület. Kövesse a [Azure Machine Learning használatának első lépései az Azure portal használatával](../machine-learning/service/quickstart-get-started.md) hozzon létre egyet, és ismerje meg, hogyan kell használni.
   * Jegyezze meg a munkaterület neve, az erőforráscsoport és az előfizetés-azonosítójára. Ezek az értékek mind elérhetők a a munkaterület áttekintése az Azure Portalon. A munkaterület erőforrások eléréséhez az Azure Notebooks az oktatóanyag későbbi részében fogja használni ezeket az értékeket. 


### <a name="disable-process-identification"></a>Folyamatazonosítás letiltása

>[!NOTE]
>
> Az Azure Machine Learning előzetes verziója nem támogatja folyamatazonosítás biztonsági funkcióját, amely az IoT Edge szolgáltatásban alapértelmezés szerint engedélyezve van.
> Az alábbi lépések végrehajtásával tilthatja le. Ez azonban nem használható éles környezetben. Ezeket a lépéseket csak akkor szükség, a Linux rendszerű eszközökön. 

Adja meg az IP-cím és port: kell letiltani a folyamat azonosítása az IoT Edge-eszközön, **workload_uri** és **management_uri** a a **csatlakozás** a szakasz az IoT Edge-démon konfigurációját.

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

Írja be ugyanezeket a címeket a konfiguráció **listen** (figyelés) szakaszába. Példa:

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Mentse és zárja be a konfigurációs fájlt.

Hozzon létre egy környezeti változó IOTEDGE_HOST a management_uri címmel (szeretné véglegesen beállítani, adja hozzá a `/etc/environment`). Példa:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```

Az IoT Edge szolgáltatás a módosítások érvénybe léptetéséhez indítsa újra.

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="create-and-deploy-azure-machine-learning-module"></a>Létrehozása és üzembe helyezése az Azure Machine Learning modul

Ebben a szakaszban betanított machine learning-modell fájlok átalakíthatók, és azokat egy Azure Machine Learning szolgáltatás a tárolót. A Docker-rendszerképhez szükséges összetevők megtalálhatók az [Azure IoT Edge GitHub-adattár AI-eszközkészletében](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Kövesse az alábbi lépéseket az adott tárházba feltöltése a Microsoft Azure notebookok a tároló létrehozása és leküldése az Azure Container Registrybe.


1. Keresse meg az Azure-jegyzetfüzetek projektek. Beszerezheti a az Azure Machine Learning szolgáltatás munkaterületről a a [az Azure portal](https://portal.azure.com) vagy bejelentkezik a [a Microsoft Azure notebookok](https://notebooks.azure.com/home/projects) az Azure-fiókkal.

2. Válassza ki **töltse fel a GitHub-adattárat**.

3. Adja meg az alábbi GitHub-adattár neve: `Azure/ai-toolkit-iot-edge`. Törölje a jelet a **nyilvános** jelölőnégyzetet, ha meg szeretné tartani a projekt személyes. Válassza ki **importálás**. 

4. Az importálás befejeződése után keresse meg az új **ai-eszközkészlet-iot-edge** projektre, és nyissa meg a **IoT Edge rendellenességek észlelése oktatóanyag** mappát. 

5. Ellenőrizze, hogy fut-e a projekthez. Ha nem, válassza ki a **ingyenes számítási futtathatók**.

   ![Az ingyenes számítási futtatása](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Nyissa meg a **aml_config/config.json** fájlt.

7. Például az értékeket az Azure-előfizetése Azonosítóját, az erőforráscsoport az előfizetéshez és az Azure Machine Learning szolgáltatás munkaterület neve az a konfigurációs fájl szerkesztésével. Az összes értéket kap a **áttekintése** szakasz a munkaterület az Azure-ban. 

8. Mentse a konfigurációs fájlt.

9. Nyissa meg a **00 – anomáliadetektálási-észlelés – tutorial.ipynb** fájlt.

10. Amikor a rendszer kéri, válassza ki a **Python 3.6-os** kernel válassza **beállítása Kernel**.

11. Szerkessze az első olyan cellára a jegyzetfüzet a megjegyzések utasítások szerint. Használja az ugyanabban az erőforráscsoportban, előfizetés-azonosító és a konfigurációs fájlban hozzáadott munkaterület nevét.

12. A cellák futtassa a jegyzetfüzet kiválasztásával őket **futtatása** vagy megnyomásával `Shift + Enter`.

    >[!TIP]
    >A cellákat a rendellenességek észlelése oktatóanyag jegyzetfüzet némelyike nem kötelező, mivel általuk létrehozott erőforrásokat, amelyek előfordulhat, hogy néhány felhasználó, vagy előfordulhat, hogy nem rendelkezik még, például egy IoT hubot. Ha a meglévő erőforrás adatai helyezi az első olyan cellára, hibákat kap, az új erőforrásokat hozhat létre, mert az Azure nem hozza létre az ismétlődő erőforrások cellákat futtatásakor. Ez nem okoz gondot, és figyelmen kívül hagyja a hibákat, vagy hagyja ki teljes mértékben választható szakaszt. 

A notebook található összes lépést elvégezte, betanított egy anomáliadetektálási modell, egy Docker-tároló rendszerképét, beépített és leküldte a rendszerképet az Azure Container Registrybe. Ezután tesztelni a modellt, és végül üzembe helyezte azt az IoT Edge-eszköz. 

## <a name="view-container-repository"></a>Tároló-beállításjegyzékbe megtekintése

Ellenőrizze, hogy a tároló rendszerképének lett sikeresen létrehozott és az Azure container registrybe, amely a machine learning-környezet társítva van tárolva. A notebook, amely automatikusan használja az előző szakaszban a tárolórendszerképet, és az IoT Edge-eszközön a tárolójegyzék hitelesítő adatainak megadott, de tudnia kell, hol vannak tárolva, hogy információkat találhatja meg a saját maga később. 

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a Machine Learning szolgáltatás munkaterületet. 

2. A **áttekintése** szakasz sorolja fel a munkaterület részletei, valamint az összes kapcsolódó erőforrás. Válassza ki a **beállításjegyzék** érték, amely a munkaterület neve véletlenszerű számot kell lennie. 

3. Válassza ki a tárolóregisztrációs **Tárházak**. Megjelenik egy tárház nevű **tempanomalydetection** , amely hozta létre a notebookot a korábbi szakaszban futtatta. 

4. Válassza ki **tempanomalydetection**. Kell látnia, hogy rendelkezik-e a tárház egy címke: **1**. 

   Most, hogy megismerte az adatbázis nevét, az adattár nevét és a címke, a teljes lemezkép elérési útja a tároló ismernie. Képek elérési útjait kinéznie  **\<registry_name\>.azurecr.io/tempanomalydetection:1**. A lemezkép elérési útja segítségével a tároló üzembe IoT Edge-eszközökön. 

5. Válassza ki a tárolóregisztrációs **hozzáférési kulcsok**. Megjelenik a hozzáférési hitelesítő adatokat, beleértve számos **bejelentkezési kiszolgáló** és a **felhasználónév**, és **jelszó** egy rendszergazdai felhasználó számára.

   Ezek a hitelesítő adatok is részét képezhetik a központi telepítési jegyzékfájlba való hozzáférésre az IoT Edge eszköz lekéréses tárolórendszerképeket a beállításjegyzékből. 

Most már ismeri a Machine Learning tárolólemezkép tárolására. Ez a szakasz végigvezeti a lépéseken, hogy hogyan működik az IoT Edge-eszközön üzembe helyezett modulként. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Megtekintheti az egyes IoT Edge-modulok által létrehozott üzeneteket, és megtekintheti az IoT-központjába küldött üzeneteket.

### <a name="view-data-on-your-iot-edge-device"></a>Adatok megtekintése az IoT Edge-eszközön

IoT Edge-eszközén megtekintheti az egyes modulok által küldött üzeneteket.

Előfordulhat, hogy kell használnia `sudo` futtatásához emelt szintű engedélyek `iotedge` parancsokat. Jelentkezzen ki, majd jelentkezzen be újból az eszköz automatikusan frissíti az engedélyeket.

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

5. Figyelje meg a tempSensor felől öt másodpercenként érkező üzeneteket. Az üzenettörzs nevű tulajdonságot tartalmaz **anomáliadetektálási**, amely a machinelearningmodule biztosít egy igaz vagy hamis értéket. Az **AzureMLResponse** tulajdonság „OK” értéket tartalmaz, ha a modell sikeresen lefutott.

   ![Az Azure Machine Learning szolgáltatás válasza az üzenet törzse](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy, az Azure Machine Learning által működtetett IoT Edge-modult. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja jelentőséggel bíró üzleti információkká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Képek osztályozása a Custom Vision szolgáltatás használatával](tutorial-deploy-custom-vision.md)

