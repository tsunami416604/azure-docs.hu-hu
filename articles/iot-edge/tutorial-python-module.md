---
title: Az oktatóanyag egyéni Python modult - létrehozása az Azure IoT Edge |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Python-kóddal, és hogyan helyezheti üzembe peremhálózati eszközökön.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 24ca97c21ac3728880db4c924179be1b78ec2f18
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565768"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Oktatóanyag: Fejlesztése és üzembe helyezése egy Python IoT Edge-modul a szimulált eszköz

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A rövid útmutatókban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

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

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code.
* [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) a Python-csomagok telepítéséhez (általában a Python-telepítés részét képezi).

>[!Note]
>Ügyeljen arra, hogy a `bin` mappa van a platform elérési útján legyen. Ez általában a `~/.local/` út a UNIX és macOS rendszeren, és `%APPDATA%\Python` a Windows rendszeren.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben az oktatóanyagban használhatja az Azure IoT-eszközök a Visual Studio Code fel egy modult, és hozzon létre egy **tárolórendszerkép** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Minden olyan Docker-kompatibilis beállításjegyzéket a tárolólemezképek tárolására használható. Két népszerű Docker beállításjegyzék szolgáltatások [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

Ha még nem rendelkezik egy tároló-beállításjegyzéket, az alábbi lépésekkel hozzon létre egy új Azure-ban:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

2. Adja meg a következő értékeket a tárolóregisztrációs adatbázis létrehozásához:

   | Mező | Érték | 
   | ----- | ----- |
   | Beállításjegyzék neve | Egyedi nevet adjon meg. |
   | Előfizetés | A legördülő listából válasszon egy előfizetést. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |
   | Rendszergazdai felhasználó | Állítsa **Engedélyezés** értékre. |
   | SKU | Válassza az **Alapszintű** lehetőséget. | 

5. Kattintson a **Létrehozás** gombra.

6. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, majd válassza a **Hozzáférési kulcsok** elemet. 

7. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. A tároló-beállításjegyzékbe való hozzáférés biztosításához az oktatóanyag későbbi részében használja ezeket az értékeket. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása
Az alábbi lépéseket egy IoT Edge Python-modul létrehozása a Visual Studio Code és az Azure IoT-eszközök használatával.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

A **cookiecutter** Python-csomag használatával létrehozhat egy Python-megoldássablont, amelyre majd építkezhet. 

1. A Visual Studio Code-ban válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet a VS Code integrált terminál megnyitásához.

2. Az integrált terminálban írja be a következő parancsot a **cookiecutter** telepítéséhez (vagy frissítéséhez), amellyel létrehozhatja az IoT Edge-megoldássablont a VS Code-ban:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Győződjön meg, hogy a könyvtár cookiecutteru helyéül annak érdekében, hogy indítsa el a parancssorból lehetővé teszik, hogy a környezet elérési út szerepel. A könyvtár része a telepítési parancsfájl kimenete például `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Indítsa újra a Visual Studio Code-útvonalra a módosítások átvételéhez. 

3. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 

4. A parancskatalógus, adja meg, és futtassa a parancsot **Azure: Jelentkezzen be a** , és kövesse az utasításokat követve jelentkezzen be az Azure-fiókjával. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

5. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **PythonModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének a neve, az előző lépésben megadott van töltve. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/pythonmodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-python-module/repository.png)

A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. A megoldás munkaterület öt legfelső szintű összetevőt tartalmaz. A **modules** mappa a modul Python-kódját, valamint a modul tárolórendszerképként való összeállítására szolgáló Docker-fájlokat tartalmazza. Az **\.env** fájl a tárolóregisztrációs adatbázis hitelesítő adatait tárolja. A **deployment.template.json** fájl az IoT Edge-futtatókörnyezet által a modulok eszközön való üzembe helyezéséhez használt információkat tartalmazza. És **deployment.debug.template.json** tárolók a hibakeresési verzió-modulok fájlt. Ebben az oktatóanyagban nem fogja szerkeszteni a **\.vscode** mappát vagy a **\.gitignore** fájlt.  

Ha nem adott meg tárolóregisztrációs adatbázist a megoldás létrehozásakor, de elfogadta az alapértelmezett localhost:5000 értéket, akkor nem lesz \.env fájlja. 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

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

7. Mentse a main.py fájlt.

8. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. A fájl arra utasítja az IoT Edge-ügynök, mely modulok üzembe helyezéséhez ebben az esetben **tempSensor** és **PythonModule**, és az IoT Edge hubot jelzi, hogyan irányítsa az üzenetek közöttük. A Visual Studio Code-bővítmény automatikusan kitölti a legtöbb, hogy a központi telepítési sablont a szükséges, de győződjön meg arról, hogy minden működik-e a megoldás pontos információ: 

   1. Az IoT Edge az alapértelmezett platform értékre van állítva **amd64** a VS Code állapotsorban, ami azt jelenti, a **PythonModule** a lemezkép verziószámát Linux AMD64-es értékre van állítva. Módosítsa az alapértelmezett platform az állapotsorban **amd64** való **arm32v7** vagy **windows-amd64** , amely az IoT Edge-eszköz architektúra esetén. 

      ![A modul lemezképplatformmal frissítése](./media/tutorial-python-module/image-platform.png)

   2. Ellenőrizze, hogy a sablonban a megfelelő modulnév van-e megadva az alapértelmezett **SampleModule** név helyett, amelyet az IoT Edge-megoldás létrehozásakor módosított.

   3. A **registryCredentials** szakasz tárolja a Docker registry hitelesítő adataival, úgy, hogy az IoT Edge-ügynök a modul rendszerképének kérheti le. A tényleges felhasználónév–jelszó párokat az .env fájl tárolja, amelyet a Git figyelmen kívül hagy. Ha még nem tette, vegye fel a hitelesítő adatait a .env fájlba.  

   4. Ha szeretne további tudnivalók a központi telepítési jegyzékek, [megtudhatja, hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat az IoT Edge](module-composition.md).

9. Adja hozzá a **PythonModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **moduleContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Ikermodul központi telepítési sablon hozzáadása](./media/tutorial-python-module/module-twin.png)

10. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-solution"></a>És a megoldás leküldéses

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a **PythonModule** modulhoz, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet az elfogadható küszöbérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe. Ezután küldje le a modul rendszerképét az Azure Container Registry-adatbázisba: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Használja az első szakaszban az Azure Container Registry-adatbázisból kimásolt felhasználónevet, jelszót és bejelentkezési kiszolgálót. Vagy le is kérheti ezeket az értékeket az Azure Portalon a tárolóregisztrációs adatbázis **Hozzáférési kulcsok** szakaszában.

2. A VS Code Explorerben kattintson a jobb gombbal a deployment.template.json fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy deployment.json nevű fájlt egy új **config** nevű mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a Python-kódot, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

A tárolórendszerkép teljes címét a címkével a VS Code integrált termináljában futó `docker build` parancsban tekintheti meg. A rendszerkép címe a module.json fájlban lévő információk alapján áll össze az \<adattár\>:\<verzió\>-\<platform\> formátumban. Ebben az oktatóanyagban a következőképpen kell kinéznie: registryname.azurecr.io/pythonmodule:0.0.1-amd64.

>[!TIP]
>Ha hibaüzenet jelenik meg, és a modul leküldéses próbál, győződjön meg arról, a következő ellenőrzések:
>* Jelentkezett be a Docker, a Visual Studio Code hitelesítő adatok használatával a tárolóregisztrációs adatbázisból? Ezeket a hitelesítő adatokat eltérnek az Azure Portalra való bejelentkezéshez használja azokat.
>* A tároló-beállításjegyzékbe a helyes? Nyissa meg **modulok** > **cmodule** > **module.json** , és keresse meg a **tárház** mező. A lemezképtár hasonlóan kell kinéznie  **\<registryname\>.azurecr.io/pythonmodule**. 
>* Tárolók rendszert futtató fejlesztői gépen azonos típusú készít? Visual Studio Code-ot alapértelmezett Linux-tárolók amd64. Ha a fejlesztői gépén arm32v7 Linux-tárolók, frissítse a platform kék állapotsávját megfelelően a VS Code-ablak alján. Python-modulok Windows-tárolók nem támogatottak. 

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. Az Azure IoT Hub-eszközkészlet bővítmény (korábbi nevén Azure IoT-eszközkészlet bővítmény) használata a Visual Studio Code-modulok is telepítheti. Már elő van készítve egy üzembehelyezési jegyzék a forgatókönyvhöz, a **deployment.json** fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

1. Futtassa a VS Code parancskatalógus **Azure IoT hubbal: Válassza ki az IoT Hub**. 

2. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot. 

3. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

4. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-python-module/create-deployment.png)

5. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt. 

6. Kattintson a frissítés gombra. Látható, hogy az új **PythonModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak. 

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája. 

Magán az IoT Edge-eszközön az üzembehelyezési modulok állapotának megtekintéséhez használja az `iotedge list` parancsot. Négy modult kell látnia: a két IoT Edge-futtatókörnyezeti modult, a tempSensort és az ebben az oktatóanyagban létrehozott egyéni modult. Az összes modul elindítása eltarthat néhány percig, ezért ha nem látja mindet azonnal, futtassa újra a parancsot. 

Az egyes modulok által létrehozott üzenetek megtekintéséhez használja az `iotedge logs <module name>` parancsot. 

Az IoT Hubra beérkező üzenetek a Visual Studio Code segítségével tekinthetők meg. 

1. Az IoT Hub-központra érkező adatok monitorozásához válassza a három pont (**...**) elemet, majd a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
2. Egy adott eszköz D2C üzeneteinek monitorozásához kattintson a listában a jobb gombbal az eszközre, és válassza ki a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
3. Adatok monitorozásának leállításához futtassa a parancsot **Azure IoT hubbal: D2C üzenet monitorozásának leállítása** a parancskatalógus. 
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
