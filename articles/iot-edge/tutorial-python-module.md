---
title: Azure IoT Edge Python – oktatóanyag | Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Python-kóddal, és hogyan helyezheti üzembe peremhálózati eszközökön.
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0a32f925aa1ff4066a893fb107f4d785bd1fd8f8
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423561"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Oktatóanyag: Python-alapú IoT Edge-modul fejlesztése és üzembe helyezése szimulált eszközön

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows][lnk-quickstart-win] vagy [Linux][lnk-quickstart-lin] rendszeren című rövid útmutatóban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Python-modul létrehozása a Visual Studio Code-dal.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [linuxos](quickstart-linux.md) rövid útmutató lépéseit követve.
* Az IoT Edge-hez elérhető Python-modulok nem támogatják a Windows-eszközöket.

Felhőerőforrások:

* Egy ingyenes szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code-hoz.
* [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) a Python-csomagok telepítéséhez (általában a Python-telepítés részét képezi).

>[!Note]
>Ügyeljen arra, hogy a `bin` mappa van a platform elérési útján legyen. Ez általában a `~/.local/` út a UNIX és macOS rendszeren, és `%APPDATA%\Python` a Windows rendszeren.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban a VS Code-hoz készült Azure IoT Edge bővítménnyel épít fel egy modult és hoz létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Ehhez az oktatóanyaghoz bármilyen Docker-kompatibilis beállításjegyzéket használhat. A Dockerhez a felhőben elérhető két népszerű regisztrációsadatbázis-szolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.
2. Nevezze el a beállításjegyzéket, válasszon egy előfizetést, válasszon egy erőforráscsoportot, és állítsa be az **Alapszintű** termékváltozatot. 
3. Kattintson a **Létrehozás** gombra.
4. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet. 
5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása
A következő lépésekben létrehozunk egy IoT Edge Python-modult a Visual Studio Code és az Azure IoT Edge bővítmény használatával.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

A **cookiecutter** Python-csomag használatával létrehozhat egy Python-megoldássablont, amelyre majd építkezhet. 

1. A Visual Studio Code-ban válassza a **View** > **Integrated Terminal** (Nézet, Integrált terminál) elemet a VS Code integrált terminál megnyitásához.

2. Az integrált terminálban írja be a következő parancsot a **cookiecutter** telepítéséhez (vagy frissítéséhez), amellyel létrehozhatja az IoT Edge-megoldássablont a VS Code-ban:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Győződjön meg, hogy a cookiecutter telepítési könyvtára a környezet `Path` útvonalán legyen, hogy egy parancssorból meg lehessen hívni.

3. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 

4. A parancskatalógusban írja be és futtassa az **Azure: Sign in** parancsot, és az utasításokat követve jelentkezzen be az Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

5. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 

   1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni. 
   2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
   3. A modul sablonjaként válassza a **Python-modult**. 
   4. A modulnak adja a **PythonModule** nevet. 
   5. Adja meg az előző szakaszban létrehozott Azure Container Registry-adatbázist az első modul rendszerképadattáraként. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére. A sztring végső változata a következőképp néz ki: \<regisztrációs adatbázis neve\>.azurecr.io/pythonmodule.
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-python-module/repository.png)

A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. A megoldás munkaterület öt legfelső szintű összetevőt tartalmaz. Ebben az oktatóanyagban nem szerkeszti a **\.gitignore** fájlt. A **modules** mappa a modul Python-kódját, valamint a modul tárolórendszerképként való összeállítására szolgáló Docker-fájlokat tartalmazza. Az **\.env** fájl a tárolóregisztrációs adatbázis hitelesítő adatait tárolja. A **deployment.template.json** fájl az IoT Edge-futtatókörnyezet által a modulok eszközön való üzembe helyezéséhez használt információkat tartalmazza. 

Ha nem adott meg tárolóregisztrációs adatbázist a megoldás létrehozásakor, de elfogadta az alapértelmezett localhost:5000 értéket, akkor nem lesz \.env fájlja. 

   ![Python-megoldás munkaterülete](./media/tutorial-python-module/workspace.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóadattár hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. 

1. A VS Code Explorerben nyissa meg a .env fájlt. 
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben. 
3. Mentse el ezt a fájlt. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Mindegyik sablon tartalmaz egy mintakódot is, amely fogadja a **tempSensor** modul szimulált érzékelőadatait, és továbbítja őket az IoT Hubnak. Ebben a szakaszban adja hozzá a kódot, amely a **pythonModule** kiterjesztésével elemzi az üzeneteket az elküldésük előtt. 

1. A VS Code Explorerben nyissa meg a **modules** > **PythonModule** > **main.py** modult.

2. A **main.py** fájl tetején importálja a **json**-kódtárat:

    ```python
    import json
    ```

3. A globális számlálóknál adja hozzá a **TEMPERATURE_THRESHOLD** és **TWIN_CALLBACKS** változókat. A hőmérsékleti határérték azt az értéket állítja be, amelyet a mért géphőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Cserélje le a **receive_message_callback** függvényt az alábbi kódra:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Adjon hozzá egy új függvényt **module_twin_callback** néven. A rendszer ezt a függvényt akkor hívja meg, amikor a kívánt tulajdonságok frissülnek.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. Adjon hozzá egy új sort a **HubManager** osztályban az **__init__** metódushoz az imént hozzáadott **module_twin_callback** függvény inicializálásához:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Mentse el ezt a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a **PythonModule** modulhoz, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet az elfogadható küszöbérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe. Ezután küldje le a modul rendszerképét az Azure Container Registry-adatbázisba: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Használja az első szakaszban az Azure Container Registry-adatbázisból kimásolt felhasználónevet, jelszót és bejelentkezési kiszolgálót. Vagy le is kérheti ezeket az értékeket az Azure Portalon a tárolóregisztrációs adatbázis **Hozzáférési kulcsok** szakaszában.

2. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a deployment.template.json fájlt. 

   Az **$edgeAgent** ebből a fájlból tudja meg, hogy két modult kell üzembe helyeznie: az eszközadatokat szimuláló **tempSensor**, valamint a **PythonModule** modult. A **PythonModule.image** értéke a rendszerkép Linux amd64-es verziójára lett beállítva. További információt az üzembehelyezési jegyzékekről az [IoT Edge-modulok használatát, konfigurálását és újrahasznosítását](module-composition.md) ismertető cikkben olvashat.

   A fájl a regisztrációs adatbázis hitelesítő adatait is tartalmazza. A sablonfájlban a felhasználónév és a jelszó helyén helyőrzők állnak. Az üzembehelyezési jegyzék létrehozásakor a rendszer automatikusan kitölti a mezőket az .env fájlban megadott értékekkel. 

3. Adja hozzá a **PythonModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **moduleContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után: 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Mentse el ezt a fájlt.

5. A VS Code Explorerben kattintson a jobb gombbal a deployment.template.json fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy deployment.json nevű fájlt egy új **config** nevű mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a Python-kódot, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

A tárolórendszerkép teljes címét a címkével a VS Code integrált termináljában futó `docker build` parancsban tekintheti meg. A rendszerkép címe a module.json fájlban lévő információk alapján áll össze az \<adattár\>:\<verzió\>-\<platform\> formátumban. Ebben az oktatóanyagban a következőképpen kell kinéznie: registryname.azurecr.io/pythonmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. A Visual Studio Code Azure IoT-eszközkészlet bővítményével is üzembe helyezhet modulokat. Már elő van készítve egy üzembehelyezési jegyzék a forgatókönyvhöz, a **deployment.json** fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

1. A VS Code parancskatalógusában futtassa az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot. 

2. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot. 

3. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

4. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-python-module/create-deployment.png)

5. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt. 

6. Kattintson a frissítés gombra. Látható, hogy az új **PythonModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hub-központra érkező adatok monitorozásához válassza a három pont (**...**) elemet, majd a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
2. Egy adott eszköz D2C üzeneteinek monitorozásához kattintson a listában a jobb gombbal az eszközre, és válassza ki a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
3. Az adatok monitorozásának leállításához futtassa a parancskatalógusban az **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: D2C üzenetek monitorozásának leállítása) parancsot. 
4. A modul ikerdokumentumának megtekintéséhez vagy frissítéséhez kattintson a listában a jobb gombbal a modulra, és válassza az **Edit module twin** (Modul ikerdokumentumának szerkesztése) lehetőséget. A modul ikerdokumentumának frissítéséhez mentse az ikerdokumentum JSON-fájlját, kattintson a jobb gombbal a szerkesztési területre, majd válassza az **Update Module Twin** (Modul ikerdokumentumának frissítése) lehetőséget.
5. A Docker-naplók megtekintéséhez telepítse a [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code (Docker a VS Code-hoz) eszközt. A helyi rendszerben a futó modulokat a Docker Explorerben találja meg. A helyi menüben kattintson a **Show Logs** (Naplók megjelenítése) elemre az integrált terminálban való megtekintéshez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

### <a name="delete-local-resources"></a>Helyi erőforrások törlése

Ha el szeretné távolítani IoT Edge-futtatókörnyezetet és az ahhoz tartozó erőforrásokat az eszközről, használja a következő parancsokat. 

Távolítsa el az IoT Edge-futtatókörnyezetet.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Ha eltávolította az IoT Edge-futtatókörnyezetet, az általa létrehozott tárolók leállnak, de továbbra is ott lesznek az eszközön. Tekintse meg az összes tárolót.

   ```bash
   sudo docker ps -a
   ```

Törölje az eszközön létrehozott futtatókörnyezeti tárolókat.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Töröljön minden további tárolót, amely a `docker ps` kimenetében listázva volt – ezt a tárolók nevére való hivatkozással teheti meg. 

Távolítsa el a tároló-futtatókörnyezetet.

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Az Azure Functions üzembe helyezése modulként](tutorial-deploy-function.md)
> [Az Azure Stream Analytics üzembe helyezése modulként](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
