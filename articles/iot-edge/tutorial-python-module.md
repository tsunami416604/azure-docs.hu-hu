---
title: Az Azure IoT peremhálózati Python modul |} Microsoft Docs
description: Hozzon létre egy IoT peremhálózati modul Python kódját, és központilag telepítenie kell a peremhálózati eszköz
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3c46df85f95377f5740526542ac1baf5a8fd77c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Fejlesztés és Python IoT peremhálózati modul telepítése a szimulált eszköz – előzetes

Az IoT-Edge modulok, amely megvalósítja az üzleti logikát, közvetlenül az IoT peremhálózati eszköz a kód telepítésére használhatja. Ez az oktatóanyag végigvezeti létrehozása és telepítése az IoT peremhálózati modul érzékelőadatait szűrő. A szimulált IoT peremhálózati eszköz, a központi telepítése Azure IoT peremhálózati a szimulált eszköz létrehozott fogjuk [Windows] [ lnk-tutorial1-win] vagy [Linux] [ lnk-tutorial1-lin]oktatóanyagok. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * Visual Studio Code segítségével hozzon létre egy IoT peremhálózati Python modul
> * Visual Studio Code- és Docker segítségével hozzon létre egy docker-lemezképet, és tegye közzé a beállításjegyzék 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az IoT-Edge modul, amely ebben az oktatóanyagban létrehozhat szűrők az eszköz által létrehozott hőmérséklet adatokat. Ez csak akkor küldi el üzenetek előtt Ha hőmérséklete meghaladja a küszöbértéket. Ilyen típusú elemzés peremére akkor hasznos, ha továbbítani, és a felhőben tárolt adatok mennyisége csökkenteni. 

> [!IMPORTANT]
> Jelenleg a Python modul csak futtatható amd64 Linux tárolókban; azt a Windows-tárolók és ARM-alapú tárolók nem futtatható. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure IoT peremhálózati eszköz, a gyors üzembe helyezés vagy első oktatóanyaga, amely létrehozta.
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati karakterlánca.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [A Visual Studio Code Python bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) ugyanazon a számítógépen, amelyen a Visual Studio Code. A közösségi Edition (CE) is használhatók ehhez az oktatóanyaghoz. 
* [Python](https://www.python.org/downloads/).
* [A pip](https://pip.pypa.io/en/stable/installing/#installation) Python-csomagokat (általában a Python-telepítés mellékelt) telepítéséhez.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban a VS Code-hoz készült Azure IoT Edge bővítménnyel épít fel egy modult és hoz létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Ehhez az oktatóanyaghoz bármilyen Docker-kompatibilis beállításjegyzéket használhat. A felhőben elérhető két népszerű Docker-beállításjegyzékszolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.
2. Nevezze el a beállításjegyzéket, válasszon egy előfizetést, válasszon egy erőforráscsoportot, és állítsa be az **Alapszintű** termékváltozatot. 
3. Kattintson a **Létrehozás** gombra.
4. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet. 
5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni. 

## <a name="create-an-iot-edge-module-project"></a>Az IoT-Edge modul projekt létrehozása
A következő lépések bemutatják a hozzon létre egy Visual Studio Code és az Azure IoT peremhálózati bővítmény IoT peremhálózati Python modult.
1. A Visual Studio Code, válassza ki **nézet** > **integrált terminál** a Visual STUDIO Code integrált terminál megnyitásához.
2. Az integrált terminált, adja meg a következő parancs futtatásával telepítse (vagy frissítse) **cookiecutter** (javasoljuk, hogy ezzel a virtuális környezetbe, vagy a felhasználó telepítésként alább látható módon):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Hozzon létre egy projektet az új modulhoz. A következő parancs létrehozza a projektmappa **FilterModule**, és a tároló-tárház. A paraméter `image_repository` formájában kell `<your container registry name>.azurecr.io/filtermodule` Azure tároló beállításjegyzék használata. Írja be a következő parancsot az aktuális munkakönyvtárba:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Válassza ki **fájl** > **nyissa meg a mappa**.
5. Keresse meg a **FilterModule** mappa, és kattintson **Mappaválasztás** megnyitni a projektet a Visual STUDIO Code.
6. A Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson **main.py** való megnyitásához.
7. Felső részén a **FilterModule** névtérhez, importálása a `json` könyvtár:

    ```python
    import json
    ```

8. Adja hozzá a `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS`, és `TWIN_CALLBACKS` a globális számlálók. A hőmérséklet küszöbértéket ahhoz, hogy az adatokat az IoT-központ küldendő beállítja az haladhatja meg a mért hőmérséklet értéket.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. A függvény frissítése `receive_message_callback` a tartalom alatt.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Adja hozzá egy új funkció `device_twin_callback`. Ez a funkció fog hívható meg a kívánt tulajdonságának frissítésekor.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. Osztály `HubManager`, az új sort a `__init__` metódust is, a `device_twin_callback` függvény az előzőekben adott hozzá.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Mentse a fájlt.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker-rendszerkép létrehozása és közzététele a beállításjegyzékben

1. A VS Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Használja a felhasználónév, jelszó és bejelentkezési kiszolgáló másolt az Azure-tárolót beállításkulcs létrehozása után.

2. A VS Code Explorerben kattintson a jobb gombbal a **module.json** fájlra, és kattintson az **IoT Edge-modul Docker-rendszerképének összeállítása és leküldése** elemre. A Visual STUDIO Code ablak tetején előugró legördülő mezőben válassza ki a tároló platformot, például **amd64** Linux tároló. Visual STUDIO Code containerize a `main.py` és szükséges függőségek, majd a megadott tároló beállításjegyzék. A lemezkép létrehozásához először több percig is eltarthat.

3. A VS Code integrált termináljában hozzáférhet a teljes tárolórendszerképhez címkével együtt. Az összeállítás és a leküldés meghatározásáról a `module.json` fájlban talál további információt.

## <a name="add-registry-credentials-to-edge-runtime"></a>Peremhálózati futásidejű beállításjegyzék hitelesítő adatok hozzáadása
Adja hozzá az Edge-futtatókörnyezethez a beállításjegyzék hitelesítő adatait azon a számítógépen, amelyen az Edge-eszközt futtatja. Ezek a hitelesítő adatok hozzáférést a futásidejű való lekérésére a tárolót. 

- Windowson futtassa az alábbi parancsot:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Linuxon futtassa az alábbi parancsot:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>A megoldás futtatása

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az IoT hubot.
2. Lépjen az **IoT Edge (előzetes verzió)** részhez, és válassza ki az IoT Edge-eszközt.
3. Válassza a **Modulok beállítása** lehetőséget. 
2. Ellenőrizze, hogy a **tempSensor** modul automatikusan feltöltődik értékkel. Ha nem, tegye a következőket veheti fel:
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `tempSensor`.
    3. A **Rendszerkép URI** mezőbe írja be a következőt: `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul a többi beállítást, és kattintson a **Mentés** gombra.
9. Adja hozzá a **filterModule** modul, amely a korábbi szakaszokban létrehozott. 
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `filterModule`.
    3. A **Rendszerkép URI** mezőbe írja be a rendszerkép címét, például a következőt: `<your container registry address>/filtermodule:0.0.1-amd64`. A rendszerkép teljes címe az előző szakaszban található.
    4. Ellenőrizze a **engedélyezése** jelölőnégyzetet, hogy a modul iker szerkesztheti. 
    5. Cserélje le a JSON-t a szövegmező számára a modul iker a következő JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Kattintson a **Save** (Mentés) gombra.
10. Kattintson a **Tovább** gombra.
11. Az **Útvonalak megadása** lépésben másolja az alábbi JSON-t a szövegmezőbe. Modulok összes üzenetet a peremhálózati futásidejű tegye közzé. A futásidejű deklaratív szabályok határozzák meg, ahol az üzenetek áramlását. Ebben az oktatóanyagban kell két útvonalak. Az első útvonal szállítja a keresztül a "input1" végpontot, amely a konfigurált végpont modulja a hőmérséklet-érzékelő üzenetek a **FilterMessages** kezelő. A második útvonal a szűrőmodulból az IoT Hubra szállítja az üzeneteket. Ebben az útvonalban az `upstream` egy speciális cél, amely alapján az Edge Hub az IoT Hubnak küldi az üzeneteket. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Kattintson a **Tovább** gombra.
5. A **Sablon áttekintése** lépésben kattintson a **Küldés** elemre. 
6. Térjen vissza az IoT Edge-eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Láthatja, hogy az új **filtermodule** fut, valamint a **tempSensor** modul és a **IoT peremhálózati futásidejű**. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az IoT Edge-eszközről az IoT hubra küldött, az eszközről a felhőbe irányuló üzenetek monitorozása:
1. Az Azure IoT-eszközkészlet bővítmény konfigurálása kapcsolati karakterlánccal az IoT hubhoz: 
    1. Nyissa meg a Visual STUDIO Code explorer kiválasztásával **nézet** > **Explorer**. 
    3. Kattintson a explorer **IOT HUB-ESZKÖZÖKNEK** majd **...** . Kattintson a **IoT Hub kapcsolati karakterlánc beállítása** és az IoT hub, amely az IoT-peremhálózati eszköz csatlakozik az előugró ablakban adja meg a kapcsolati karakterláncot. 

        Keresse meg a kapcsolati karakterláncot, kattintson a csempére az IoT hub, az Azure portálon, és kattintson a **megosztott elérési házirendek**. A **megosztott elérési házirendek**, kattintson a **iothubowner** házirend, és másolja az IoT-központ kapcsolati karakterlánc a **iothubowner** ablak.   

1. Az IoT hub érkező adatok figyelésére, válassza ki a **nézet** > **parancs paletta** , és keresse meg a **IoT: D2C üzenet figyelni** parancs. 
2. Az adatok figyelésének leállításához, használja a **IoT: D2C üzenet figyelés leállításának** menüparancshoz. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létre egy IoT biztonsági szűréséhez a nyers adatokat az IoT-peremhálózati eszköz által generált kódot tartalmazó modult. Be vagy más módszereket, amelyek segítségével Azure IoT peremhálózati tájékozódhat az adatok az üzleti elemzések készítése a peremhálózaton kapcsolja be az alábbi oktatóanyagok tovább.

> [!div class="nextstepaction"]
> [Azure-függvény modulként telepítése](tutorial-deploy-function.md)
> [telepítése Azure Stream Analytics modulként](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
