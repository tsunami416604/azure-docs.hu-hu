---
title: Egyéni Python-modul létrehozása – Azure IoT Edge | Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Python-kóddal, és hogyan helyezheti üzembe peremhálózati eszközökön.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 55cdb70a02aae62a69f4fc930f4c6ee385a24702
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839549"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Oktatóanyag: Python IoT Edge modul fejlesztése és üzembe helyezése Linux-eszközökhöz

A Visual Studio Code használatával C kódot fejleszthet, és a Azure IoT Edge rendszert futtató linuxos eszközre telepítheti. 

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti egy IoT Edge modul létrehozásán és üzembe helyezésén, amely a gyors útmutatóban beállított IoT Edge eszközön szűri az érzékelőket. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Python-modul létrehozása a Visual Studio Code-dal.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet a **Python** modult a **Visual Studio Code**használatával, és hogyan telepítheti azt egy **Linux**-eszközre. A IoT Edge nem támogatja a Windows-eszközökhöz készült Python-modulokat. 

A következő táblázat segítségével megismerheti a Python-modulok Linux rendszeren történő fejlesztésének és üzembe helyezésének lehetőségeit: 

| Python | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![A VS Code használata Python-modulokhoz Linux AMD64 rendszeren](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![A VS Code használata Python-modulokhoz Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt el kellett volna végeznie az előző oktatóanyagot a fejlesztői környezet létrehozásához a Linux-tárolók fejlesztéséhez: [IoT Edge modulok fejlesztése Linux](tutorial-develop-for-linux.md)-eszközökhöz. Ezeknek az oktatóanyagoknak a végrehajtásával a következő előfeltételek szükségesek: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge rendszert futtató Linux-eszköz](quickstart-linux.md)
* Egy tároló-beállításjegyzék, például [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* A [Visual Studio Code](https://code.visualstudio.com/) az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)van konfigurálva.
* A [Docker CE](https://docs.docker.com/install/) Linux-tárolók futtatására van konfigurálva.

Ha IoT Edge modult szeretne fejleszteni a Pythonban, telepítse a következő további előfeltételeket a fejlesztői gépre: 

* [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) a Python-csomagok telepítéséhez (általában a Python-telepítés részét képezi).

>[!Note]
>Ügyeljen arra, hogy a `bin` mappa van a platform elérési útján legyen. Ez általában a `~/.local/` út a UNIX és macOS rendszeren, és `%APPDATA%\Python` a Windows rendszeren.

## <a name="create-a-module-project"></a>Modul-projekt létrehozása
Az alábbi lépések egy IoT Edge Python-modult hoznak létre a Visual Studio Code és az Azure IoT Tools használatával.

### <a name="create-a-new-project"></a>Új projekt létrehozása

A VS Code használatával hozzon létre egy Python-megoldási sablont, amelyet felépíthet a tetejére. 

1. A Visual Studio Code-ban válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet a VS Code integrált terminál megnyitásához.

1. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)**  > **Command Palette (Parancskatalógus)** elemet. 

1. A parancs palettáján írja be és futtassa az Azure **parancsot: Jelentkezzen** be, és kövesse az Azure-fiókba való bejelentkezéshez szükséges utasításokat. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

1. A Command paletta írja be és futtassa a parancsot **Azure IoT Edge: Új IoT Edge megoldás**. Kövesse az utasításokat, és adja meg a következő információkat a megoldás létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **PythonModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve az utolsó lépésben megadott névvel. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>A rendszerkép utolsó tárháza a \<következőhöz\>hasonló: beállításjegyzék neve. azurecr.IO/pythonmodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóadattár hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. 

1. A VS Code Explorerben nyissa meg a **.env** fájlt. 
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben. 
3. Mentse az .env fájlt. 

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

A Visual Studio Code jelenleg Linux AMD64 és Linux rendszerű ARM32v7-eszközökhöz is fejleszt C modulokat. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel a tárolót az egyes architektúrák típusainál eltérően építették és futtatják. Az alapértelmezett érték a Linux AMD64. 

1. Nyissa meg a parancssort, **és keressen rá Azure IoT Edge: Állítsa be az Edge-megoldás**alapértelmezett célként megadott platformját, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban. 

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Minden sablon tartalmaz egy mintakód-t, amely szimulált érzékelői adatokhoz vezet a **SimulatedTemperatureSensor** modulból, és átirányítja azt az IoT hubhoz. Ebben a szakaszban adja hozzá a kódot, amely a **pythonModule** kiterjesztésével elemzi az üzeneteket az elküldésük előtt. 

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

8. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. 

9. Adja hozzá a **PythonModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **moduleContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Ikermodul központi telepítési sablon hozzáadása](./media/tutorial-python-module/module-twin.png)

10. Mentse a Deployment. template. JSON fájlt.

## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a PythonModule, amely kiszűri azokat az üzeneteket, amelyekben a jelentett gépi hőmérséklet az elfogadható határértékeken belül van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet.

1. Jelentkezzen be a Docker-be a következő parancs beírásával a terminálon. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure Container registryből. Ezeket az értékeket a beállításjegyzék **hozzáférési kulcsok** részéből kérheti le a Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, `--password-stdin`amely a használatát javasolja. Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a Docker [bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a konfigurációban, amely tartalmazza a teljes telepítési jegyzékfájlt, kiépítve a telepítési sablonban és más megoldási fájlokban található információkat. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja `docker push` , hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.


## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

A Visual Studio Code Explorer és az Azure IoT Tools bővítmény használatával telepítse a modul-projektet a IoT Edge eszközre. Már van előkészítve egy üzembe helyezési jegyzékfájl a forgatókönyvhöz, a **telepítési. JSON** fájl a konfigurációs mappában. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Code Explorerben bontsa ki az **Azure IoT hub-eszközök** szakaszt a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt.

4. Kattintson a frissítés gombra. Meg kell jelennie az új **PythonModule** , amelyen a **SimulatedTemperatureSensor** modul, valamint a **$edgeAgent** és a **$edgeHub**fut. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a IoT Edge eszköz nevére, és válassza a **figyelés beépített esemény végpontjának indítása**lehetőséget.

2. Megtekintheti a IoT Hub érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mivel a IoT Edge eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután megvárja a PythonModule-kód módosításait, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. Az üzenet típusú riasztást is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez. 

## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A PythonModule modult az üzembe helyezési jegyzékben használta, hogy 25 fokos hőmérsékleti küszöbértéket állítson be. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studio Code-ban bontsa ki a IoT Edge eszköz alatti részleteket a futó modulok megtekintéséhez. 

2. Kattintson a jobb gombbal a **PythonModule** elemre, és válassza a **modul Twin szerkesztése**lehetőséget. 

3. A kívánt tulajdonságok között keresse meg a **TemperatureThreshold** . Módosítsa az értékét egy új 5 fokos hőmérsékletre, a legutóbbi jelentett hőmérsékletnél pedig 10 fokkal magasabbra. 

4. Mentse a modul különálló fájlját.

5. Kattintson a jobb gombbal a modul dupla szerkesztési paneljén bárhová, és válassza a **modul Twin frissítése**elemet. 

6. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Ekkor az új hőmérsékleti küszöb eléréséig az üzenetek leállnak. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben törölheti a cikkben használt helyi konfigurációkat és az Azure-erőforrásokat a díjak elkerüléséhez. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok létrehozására, többet is megtudhat a [saját IoT Edge moduljainak fejlesztéséről](module-development.md) , illetve a [Visual Studio Code](how-to-vs-code-develop-module.md)-hoz készült modulok fejlesztéséről. Folytassa a következő oktatóanyagokkal, amelyből megtudhatja, hogyan hozhatja Azure IoT Edge az Azure Cloud Services üzembe helyezését az adathordozón lévő adatfeldolgozás és-elemzés során.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)