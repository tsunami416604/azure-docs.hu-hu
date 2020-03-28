---
title: Egyéni Python-modul létrehozása - Azure IoT Edge | Microsoft dokumentumok
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Python-kóddal, és hogyan helyezheti üzembe peremhálózati eszközökön.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: a9ac9a3167d5335f6a6fd44049c892ba463dacad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372671"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Oktatóanyag: Python IoT Edge-modul fejlesztése és üzembe helyezése Linux-eszközökhöz

A Visual Studio-kód használatával fejlesztheti a Python-kódot, és üzembe helyezheti egy Azure IoT Edge-et futtató Linux-eszközre.

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti egy IoT Edge-modul létrehozásán és üzembe helyezésén, amely szűri az érzékelő adatokat a rövid útmutatóban beállított IoT Edge-eszközön. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * IoT Edge Python-modul létrehozása a Visual Studio Code-dal.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet egy modult a **Pythonban** a **Visual Studio Code használatával,** és hogyan telepítheti **linuxos eszközre.** Az IoT Edge nem támogatja a Python-modulokat Windows-eszközökhöz.

Az alábbi táblázat segítségével megismerheti a Python-modulok Linuxra történő fejlesztésének és üzembe helyezésének lehetőségeit:

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Vs code használata Python-modulokhoz Linux AMD64 rendszeren](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Vs Code python-modulok használata Linux ARM32 rendszeren](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt át kellett volna mennie az előző oktatóanyagon, hogy beállítsa a linuxos tárolók fejlesztéséhez a fejlesztői környezetet: [IoT Edge-modulok fejlesztése Linux-eszközökhöz](tutorial-develop-for-linux.md). Az oktatóanyag kitöltésével a következő előfeltételeknek kell megrendelkezniük:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató Linux-eszköz](quickstart-linux.md)
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)konfigurált [Visual Studio-kód.](https://code.visualstudio.com/)
* [A Docker CE](https://docs.docker.com/install/) linuxos tárolók futtatására van konfigurálva.

IoT Edge-modul fejlesztéséhez a Pythonban telepítse a következő további előfeltételeket a fejlesztői gépre:

* [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio-kódhoz.
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) a Python-csomagok telepítéséhez (általában a Python-telepítés részét képezi).

>[!Note]
>Ügyeljen arra, hogy a `bin` mappa van a platform elérési útján legyen. Ez általában a `~/.local/` út a UNIX és macOS rendszeren, és `%APPDATA%\Python` a Windows rendszeren.

## <a name="create-a-module-project"></a>Modulprojekt létrehozása

A következő lépések hozzon létre egy IoT Edge Python-modult a Visual Studio Code és az Azure IoT Tools használatával.

### <a name="create-a-new-project"></a>Új projekt létrehozása

A VS-kód segítségével hozzon létre egy Python-megoldássablont, amelyen felül építhet.

1. A Visual Studio Kód nézetében válassza a**Terminál** **megtekintése** > lehetőséget a VS Code integrált terminál megnyitásához.

1. A VS Code parancspaletta megnyitásához válassza a **Nézet** > **parancspaletta** lehetőséget.

1. A parancskatalógusban írja be és futtassa az **Azure: Sign in** parancsot, és az utasításokat követve jelentkezzen be az Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

1. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse az utasításokat, és adja meg a következő információkat a megoldás létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldásnak, vagy fogadja el az alapértelmezett **EdgeSolution nevet.** |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **PythonModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre ki van töltve az utolsó lépésben megadott névből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>A végső lemezképtár \<a\>rendszerleíró adatbázis "azurecr.io/pythonmodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóadattár hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code intézőben nyissa meg az **.env** fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse az .env fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a célarchitektúrát

Jelenleg a Visual Studio Code képes Python modulokat fejleszteni Linux AMD64 és Linux ARM32v7 eszközökhöz. Ki kell választania, hogy az egyes megoldásokkal melyik architektúrát célozza meg, mert a tároló az egyes architektúratípusokhoz másképp en fut. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancspalettát, és keressen az **Azure IoT Edge: Set Default Target Platform for Edge Solution (Alapértelmezett célplatform az edge-megoldáshoz) kifejezésre,** vagy válassza ki az ablak alján lévő oldalsávon található parancsikont.

2. A parancspalettán válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu virtuális gépet használunk IoT Edge eszközként, így megtartja az alapértelmezett **amd64-et.**

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Minden sablon tartalmaz mintakódot, amely szimulált érzékelőadatokat vesz fel a **SimulatedTemperatureSensor** modulból, és az IoT hubhoz irányítja. Ebben a szakaszban adja hozzá a kódot, amely a **pythonModule** kiterjesztésével elemzi az üzeneteket az elküldésük előtt.

1. A VS Code explorer, nyitott **modulok** > **PythonModule** > **main.py**.

2. A **main.py** fájl tetején importálja a **json**-kódtárat:

    ```python
    import json
    ```

3. A globális számlálóknál adja hozzá a **TEMPERATURE_THRESHOLD** és **TWIN_CALLBACKS** változókat. A hőmérsékleti határérték azt az értéket állítja be, amelyet a mért géphőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. Cserélje le a **input1_listener** függvényt a következő kódra:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. Frissítse a **figyelők** is hallgatni iker frissítéseket.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. Mentse a main.py fájlt.

7. A VS Code explorer, nyissa meg a **deployment.template.json** fájlt az IoT Edge-megoldás munkaterületén.

8. Adja hozzá a **PythonModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **moduleContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után:

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Ikermodul hozzáadása a telepítési sablonhoz](./media/tutorial-python-module/module-twin.png)

9. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>Építsd meg és nyomd meg a modult

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a PythonModule, amely kiszűri az üzeneteket, ahol a jelentett géphőmérséklet az elfogadható határokon belül. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. Nyissa meg a VS Code integrált terminált a **Terminál megtekintése** > **Terminal**lehetőséget választva.

1. Jelentkezzen be a Dockerbe a következő parancs megadásával a terminálon. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure-tároló beállításjegyzékéből. Ezeket az értékeket az Azure Portalon a beállításjegyzék **Access-kulcsok** szakaszából kérheti le.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   A használatát a használatát `--password-stdin`javasolja. Bár ez az ajánlott eljárás éles forgatókönyvekhez ajánlott, ez kívül esik az oktatóanyag hatókörén. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referencia.

1. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build- és leküldéses parancs három műveletet indít el. Először létrehoz egy új mappát a megoldás nevű **config,** amely tartalmazza a teljes központi telepítési jegyzékfájl, beépített információk a központi telepítési sablon és más megoldásfájlokat. Másodszor, fut `docker build` a tárolórendszerkép létrehozása a célarchitektúrának megfelelő dockerfile alapján. Ezután fut `docker push` a rendszerkép-tárház a tároló rendszerleíró adatbázisba.

## <a name="deploy-modules-to-device"></a>Modulok telepítése az eszközre

A Visual Studio Code Explorer és az Azure IoT Tools bővítmény segítségével telepítse a modulprojektet az IoT Edge-eszközre. Már rendelkezik egy központi telepítési jegyzékfájllal a forgatókönyvhöz, a **deployment.json** fájlhoz a konfigurációs mappában. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz működik.

1. A Visual Studio Kódkezelőben bontsa ki az **Azure IoT Hub-eszközök** szakaszt az IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt.

4. Kattintson a frissítés gombra. Meg kell jelennie az új **PythonModule** futó együtt **simulatedTemperaturesensor** modul és a **$edgeAgent** és **$edgeHub.**

## <a name="view-the-generated-data"></a>A létrehozott adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Kódkezelőjében kattintson a jobb gombbal az IoT Edge-eszköz nevére, és válassza **a Beépített eseményvégpont indítása parancsot.**

2. Tekintse meg az IoT Hubhoz érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek. Az IoT Edge-eszköz nek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután a PythonModule kódmódosításaink megvárják, amíg a gép hőmérséklete eléri a 25 fokot az üzenetek küldése előtt. Emellett hozzáadja a **Riasztás üzenettípust** minden olyan üzenethez, amely eléri az adott hőmérsékleti küszöbértéket.

## <a name="edit-the-module-twin"></a>A modul szerkesztése iker

A PythonModule modul ikerelem a központi telepítési jegyzékben a hőmérsékleti küszöbérték beállítása 25 fok. Az ikermodul segítségével a modulkód frissítése nélkül módosíthatja a funkciót.

1. A Visual Studio-kódban bontsa ki a részleteket az IoT Edge-eszköz alatt a futó modulok megtekintéséhez.

2. Kattintson a jobb gombbal a **PythonModule elemre,** és válassza **az Ikermodul szerkesztése parancsot.**

3. Keresse **meg a TemperatureThreshold** tulajdonságot a kívánt tulajdonságokban. Változtassa meg az értékét egy új hőmérséklet 5 fokkal 10 fokkal magasabb, mint a legutóbbi jelentett hőmérséklet.

4. Mentse a modul ikerfájlját.

5. Kattintson a jobb gombbal a modul ikerszerkesztő ablaktáblájának tetszőleges pontjára, és válassza **az Ikermodul frissítése parancsot**.

6. Figyelje a bejövő eszközről a felhőbe irányuló üzeneteket. Az új hőmérsékleti küszöbérték eléréséig meg kell jelennie az üzeneteknek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások, amelyek ebben a cikkben a költségek elkerülése érdekében.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll saját modulok készítésére, többet is megtudhat [saját IoT Edge-modulok fejlesztéséről,](module-development.md) illetve arról, hogyan [fejleszthet modulokat a Visual Studio-kóddal.](how-to-vs-code-develop-module.md) Példák az IoT Edge-modulok, beleértve a szimulált hőmérséklet modul, lásd: [IoT Edge modul minták](https://github.com/Azure/iotedge/tree/master/edge-modules) és [Az IoT Python SDK-minták.](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios)

Folytathatja a következő oktatóanyagokat, hogy megtudja, az Azure IoT Edge hogyan segíthet az Azure felhőszolgáltatásainak üzembe helyezésében a peremhálózati adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Egyéni vision szolgáltatás](tutorial-deploy-custom-vision.md)
