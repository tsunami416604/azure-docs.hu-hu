---
title: Az Azure IoT Edge Python-modulja | Microsoft Docs
description: IoT Edge-modul létrehozása Python-kóddal, majd üzembe helyezése egy peremhálózati eszközön
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 88d772306cb9e67216b380aa885284ebedc77b5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632108"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Python-alapú IoT Edge-modul fejlesztése és üzembe helyezése szimulált eszközön – előzetes verzió

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows][lnk-tutorial1-win] vagy [Linux][lnk-tutorial1-lin] rendszeren című oktatóanyagban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Python-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a Visual Studio Code és a Docker használatával 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

> [!IMPORTANT]
> Jelenleg a Python-modul csak amd64 Linux-tárolókon futtatható, Windows- vagy ARM-alapú tárolókon nem. 

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutatóban vagy az első oktatóanyagban létrehozott Azure IoT Edge-eszköz.
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Python-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) ugyanazon a számítógépen, amelyre telepítve van a Visual Studio Code. A Community Edition (CE) kiadás elegendő ehhez az oktatóanyaghoz. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) a Python-csomagok telepítéséhez (általában a Python-telepítés részét képezi).

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban a VS Code-hoz készült Azure IoT Edge bővítménnyel épít fel egy modult és hoz létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Ehhez az oktatóanyaghoz bármilyen Docker-kompatibilis beállításjegyzéket használhat. A felhőben elérhető két népszerű Docker-beállításjegyzékszolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.
2. Nevezze el a beállításjegyzéket, válasszon egy előfizetést, válasszon egy erőforráscsoportot, és állítsa be az **Alapszintű** termékváltozatot. 
3. Kattintson a **Létrehozás** gombra.
4. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet. 
5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása
A következő lépések azt mutatják be, hogyan hozhat létre egy IoT Edge Python-modult a Visual Studio Code és az Azure IoT Edge bővítmény használatával.
1. A Visual Studio Code-ban válassza a **View** > **Integrated Terminal** (Nézet, Integrált terminál) elemet a VS Code integrált terminál megnyitásához.
2. Az integrált terminálban írja be a következő parancsot a **cookiecutter** telepítéséhez (vagy frissítéséhez) (javasoljuk, hogy ezt virtuális környezetben vagy felhasználói telepítésként hajtsa végre, az alábbiak szerint):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Hozzon létre egy projektet az új modulhoz. A következő parancs létrehozza a **FilterModule** nevű projektmappát a tárolóadattárral együtt. Ha Azure-beli tárolóregisztrációs adatbázist használ, az `image_repository` paraméternek `<your container registry name>.azurecr.io/filtermodule` formátumúnak kell lennie. Írja be a következő parancsot az aktuális munkakönyvtárba:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Válassza a **File** > **Open Folder** (Fájl, Mappa megnyitása) elemet.
5. Lépjen a **FilterModule** mappához, és kattintson a **Select Folder** (Mappa kiválasztása) elemre a projekt VS Code-ban való megnyitásához.
6. A VS Code Explorerben kattintson a **main.py** elemre, hogy megnyissa.
7. A **FilterModule** névtér tetején importálja a `json` könyvtárat:

    ```python
    import json
    ```

8. Globális számlálóként adja meg a `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` és `TWIN_CALLBACKS` elemet. A hőmérsékleti határérték azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Frissítse a `receive_message_callback` függvényt az alábbi tartalommal.

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

10. Adjon hozzá egy új `device_twin_callback` függvényt. A rendszer ezt a függvényt akkor hívja meg, amikor a kívánt tulajdonságok frissülnek.

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

11. Adjon hozzá egy új sort a `HubManager` osztályban az `__init__` metódushoz a hozzáadott `device_twin_callback` függvény inicializálásához.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Mentse el ezt a fájlt.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker-rendszerkép létrehozása és közzététele a beállításjegyzékben

1. A VS Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Használja azt a felhasználónevet, jelszót és bejelentkezési kiszolgálót, amelyet az Azure-beli tárolóregisztrációs adatbázisból másolt ki annak létrehozásakor.

2. A VS Code Explorerben kattintson a jobb gombbal a **module.json** fájlra, és kattintson az **IoT Edge-modul Docker-rendszerképének összeállítása és leküldése** elemre. A VS Code-ablak tetején lévő előugró legördülő listában válassza ki a tárolóplatformot, például Linux-alapú tároló esetén az **amd64** lehetőséget. A VS Code ezután tárolókba helyezi a `main.py` elemet és a szükséges függőségeket, majd leküldi őket az Ön által megadott tárolóregisztrációs adatbázisba. A rendszerkép felépítése első alkalommal több percig is eltarthat.

3. A VS Code integrált termináljában hozzáférhet a teljes tárolórendszerképhez címkével együtt. Az összeállítás és a leküldés meghatározásáról a `module.json` fájlban talál további információt.

## <a name="add-registry-credentials-to-edge-runtime"></a>Beállításjegyzékhez tartozó hitelesítő adatok hozzáadása az Edge-futtatókörnyezethez
Adja hozzá az Edge-futtatókörnyezethez a beállításjegyzék hitelesítő adatait azon a számítógépen, amelyen az Edge-eszközt futtatja. Ezek a hitelesítő adatok hozzáférést nyújtanak a futtatókörnyezetnek a tároló lekéréséhez. 

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
2. Ellenőrizze, hogy a rendszer automatikusan feltölti-e adatokkal a **tempSensor** modult. Ha nem, akkor az alábbi lépéseket követve adhatja meg az adatokat:
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `tempSensor`.
    3. A **Rendszerkép URI** mezőbe írja be a következőt: `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul a többi beállítást, és kattintson a **Mentés** gombra.
9. Adja hozzá az előző szakaszokban létrehozott **filterModule** modult. 
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `filterModule`.
    3. A **Rendszerkép URI** mezőbe írja be a rendszerkép címét, például a következőt: `<your container registry address>/filtermodule:0.0.1-amd64`. A rendszerkép teljes címe az előző szakaszban található.
    4. Jelölje be az **Engedélyezés** jelölőnégyzetet, hogy szerkeszthesse a modul ikerdokumentumát. 
    5. Cserélje le a modul ikerdokumentumához tartozó szövegmezőben lévő JSON-t a következő JSON-ra: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Kattintson a **Mentés** gombra.
10. Kattintson a **Tovább** gombra.
11. Az **Útvonalak megadása** lépésben másolja az alábbi JSON-t a szövegmezőbe. A modulok minden üzenetet az Edge-futtatókörnyezetben tesznek közzé. A futtatókörnyezetben lévő deklaratív szabályok határozzák meg az üzenetek célját. Ebben az oktatóanyagban két útvonalra lesz szüksége. Az első útvonal a hőmérséklet-érzékelőből a szűrőmodulba szállítja az üzeneteket az input1 végponton keresztül, amelyet a **FilterMessages** kezelővel konfigurált. A második útvonal a szűrőmodulból az IoT Hubra szállítja az üzeneteket. Ebben az útvonalban az `upstream` egy speciális cél, amely alapján az Edge Hub az IoT Hubnak küldi az üzeneteket. 

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
6. Térjen vissza az IoT Edge-eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Látható, hogy az új **filtermodule** fut a **tempSensor** modullal és az **IoT Edge-futtatókörnyezettel** együtt. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az IoT Edge-eszközről az IoT hubra küldött, az eszközről a felhőbe irányuló üzenetek monitorozása:
1. Az Azure IoT-eszközkészlet bővítmény konfigurálása kapcsolati sztringgel az IoT hubhoz: 
    1. Nyissa meg a VS Code Explorert a **View** > **Explorer** (Nézet, Explorer) elem kiválasztásával. 
    3. Az Explorerben kattintson az **IOT HUB DEVICES** (IoT Hub-eszközök), majd a **...** elemre. Kattintson a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre, és a felugró ablakban adja meg annak az IoT-központnak a kapcsolati sztringjét, amelyhez az IoT Edge-eszköz csatlakozik. 

        A kapcsolati sztring kereséséhez kattintson az IoT-központ csempéjére az Azure Portalon, majd kattintson a **Megosztott elérési szabályzatok** elemre. A **Megosztott elérési szabályzatok** területen kattintson az **iothubowner** szabályzatra, és másolja ki az **iothubowner** ablakban látható IoT Hub kapcsolati sztringet.   

1. Az IoT-központra érkező adatok monitorozásához válassza a **Nézet** > **Parancskatalógus** elemet, és keressen rá az **IoT: D2C üzenet monitorozásának megkezdése** menüparancsra. 
2. Az adatok monitorozásának leállításához használja az **IoT: D2C üzenet monitorozásának leállítása** menüparancsot. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Továbbléphet bármelyik alábbi oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Az Azure Function üzembe helyezése modulként](tutorial-deploy-function.md)
> [Az Azure Stream Analytics üzembe helyezése modulként](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
