---
title: Hozzon létre egyéni Python modult – Azure IoT Edge |} A Microsoft Docs
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
ms.openlocfilehash: 09a3ff744e5e3d4a8c62bf8337179b9d8699c3c8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244556"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Oktatóanyag: Fejleszthet és helyezhet üzembe egy Linux rendszerű eszközök Python IoT Edge-modul

A Visual Studio Code használatával C kódjának fejlesztése és üzembe helyezése az Azure IoT Edge-es Linux rendszerű eszközök. 

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti létrehozása és telepítése az IoT Edge-modul, amely a rövid útmutatóban beállítása IoT Edge-eszközön érzékelőadatokat szűr. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Python-modul létrehozása a Visual Studio Code-dal.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatókör

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy moduljában lévő **Python** használatával **Visual Studio Code**, és hogyan helyezhet üzembe, hogy egy **Linux eszköz**. IoT Edge nem támogatja a Python-modulok Windows-eszközökhöz. 

A következő táblázat segítségével fejleszteni és üzembe helyezni a Python-modulok linuxra a lehetőségek megismerése: 

| Python | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux-AMD64** | ![A VS Code használata a Linux-AMD64 Python-modulok](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![A VS Code használata a Linux ARM32 Python-modulok](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez kell elvégezte az előző oktatóanyagban tároló Linux-fejlesztéshez a fejlesztési környezet beállítása: [A Linux rendszerű eszközök IoT Edge-modulok fejlesztése](tutorial-develop-for-linux.md). Ezek az oktatóanyagok egyikét kitöltésével helyen kell rendelkeznie a következő előfeltételek vonatkoznak: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Azure IoT Edge-es Linux rendszerű eszközök](quickstart-linux.md)
* Egy tároló-beállításjegyzéket, pl. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [A Visual Studio Code](https://code.visualstudio.com/) konfigurálva a [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [A docker CE](https://docs.docker.com/install/) konfigurált Linux-tárolók futtatásához.

Az IoT Edge-modul Python nyelven fejleszt, telepítse a következő további előfeltételeket a fejlesztői gépen: 

* [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) a Python-csomagok telepítéséhez (általában a Python-telepítés részét képezi).

>[!Note]
>Ügyeljen arra, hogy a `bin` mappa van a platform elérési útján legyen. Ez általában a `~/.local/` út a UNIX és macOS rendszeren, és `%APPDATA%\Python` a Windows rendszeren.

## <a name="create-a-module-project"></a>A modul projekt létrehozása
Az alábbi lépéseket egy IoT Edge Python-modul létrehozása a Visual Studio Code és az Azure IoT-eszközök használatával.

### <a name="create-a-new-project"></a>Új projekt létrehozása

A **cookiecutter** Python-csomag használatával létrehozhat egy Python-megoldássablont, amelyre majd építkezhet. 

1. A Visual Studio Code-ban válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet a VS Code integrált terminál megnyitásához.

2. A terminalba írja be a telepítéséhez (vagy frissítéséhez) a következő parancsot **cookiecutteru**, amelyet az IoT Edge-megoldássablon létrehozásához használhat:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Győződjön meg, hogy a könyvtár cookiecutteru helyéül annak érdekében, hogy indítsa el a parancssorból lehetővé teszik, hogy a környezet elérési út szerepel. A könyvtár része a telepítési parancsfájl kimenete például `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Indítsa újra a Visual Studio Code-útvonalra a módosítások átvételéhez. 

3. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)**  > **Command Palette (Parancskatalógus)** elemet. 

4. A parancskatalógus, adja meg, és futtassa a parancsot **Azure: Jelentkezzen be a** , és kövesse az utasításokat követve jelentkezzen be az Azure-fiókjával. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

5. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. Kövesse az utasításokat, és adja meg a megoldás létrehozásához a következő információkat:

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Python Module** (Python-modul) lehetőséget. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **PythonModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének a neve, az előző lépésben megadott van töltve. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/pythonmodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóadattár hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. 

1. A VS Code Explorerben nyissa meg a **.env** fájlt. 
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben. 
3. Mentse az .env fájlt. 

### <a name="select-your-target-architecture"></a>Válassza ki a cél-architektúra

A Visual Studio Code jelenleg C modulok Linux AMD64- és Linux ARM32v7 eszközökhöz is fejleszthet. Kell választania, melyik architektúra célozza az egyes megoldások, mert a tároló összeállítása és futtatása eltérően az egyes architektúra. Az alapértelmezett érték a Linux-AMD64. 

1. A parancskatalógus megnyitásához, és keressen rá a **Azure IoT Edge: Állítsa be alapértelmezett célplatform peremhálózati megoldás**, vagy válassza ki a parancsikont a oldalsó sáv az ablak alján. 

2. A parancskatalógus válassza ki a cél-architektúra a lehetőségek listájából. Ebben az oktatóanyagban használjuk egy Ubuntu virtuális gép legyen az IoT Edge-eszköz, így megtartja az alapértelmezett **amd64**. 

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

10. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>És a modul leküldéses

Az előző szakaszban létrehozott egy IoT Edge-megoldás, és hozzá kódot a PythonModule, hogy a rendszer kiszűri azokat az üzeneteket a gép jelentett hőmérséklet esetén az elfogadható határokon belül. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet.

1. Jelentkezzen be a írja be a következő parancsot a terminálon. Jelentkezzen be a felhasználónév, jelszó és az Azure container Registry bejelentkezési kiszolgáló. Kérheti, hogy ezeket az értéteket a **hozzáférési kulcsok** szakasz a tárolójegyzék az Azure Portalon.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy kap egy biztonsági figyelmeztetés használatát javasolja `--password-stdin`. Adott ajánlott üzemi forgatókönyvek esetén ajánlott, bár ebben az oktatóanyagban hatókörén kívül esik. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) hivatkozást.

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build és a leküldéses parancs három műveletet indítja el. Először létrehoz egy új mappát a megoldásban nevű **config** , amely társítja a teljes üzembe helyezési jegyzékfájl, beépített ki a központi telepítési sablont információk és egyéb megoldást. Másodszor, futtatásuk `docker build` hozhat létre a tárolórendszerképet a cél-architektúra a megfelelő docker-fájl alapján. Ezt követően futtatja `docker push` paranccsal küldje le a tároló-beállításjegyzék a lemezképtárból.


## <a name="deploy-modules-to-device"></a>Eszköz modulok telepítése

A Visual Studio Code Explorerben és az Azure IoT Tools bővítmény használatával a modul projekt telepítése az IoT Edge-eszköz. Már rendelkezik egy manifest nasazení előkészítve a forgatókönyvnek a **deployment.json** config mappában található fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz üzembe helyezéséig.

1. A Visual Studio Code Explorerben bontsa ki a **Azure IoT Hub-eszközök** szakasz az IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt.

4. Kattintson a frissítés gombra. Látható, hogy az új **PythonModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal az IoT Edge-eszköz neve, és válassza ki **Start beépített esemény végpont**.

2. Az IoT Hub érkező üzenetek megjelenítése. Az üzenetek kimarad, míg igénybe vehet, mert az IoT Edge-eszköz kapni az új központi telepítést, és indítsa el a modult. Azt a PythonModule kóddal végzett módosításokat, majd várjon, amíg a gép hőmérséklet 25 fok eléri üzenetek elküldése előtt. Hozzáadja az üzenettípus **riasztási** , az üzenetek, eléri a hőmérséklet-küszöbérték. 

## <a name="edit-the-module-twin"></a>A modul ikereszköz szerkesztése

PythonModule ikermodulja manifest nasazení használjuk a hőmérséklet küszöbérték 25 fok beállítása. Az ikermodul segítségével anélkül, hogy a modul kódot kellene működésének módosításához.

1. A Visual Studio Code bontsa ki a Részletek területen az IoT Edge-eszköz a futó modulok megtekintéséhez. 

2. Kattintson a jobb gombbal **PythonModule** válassza **szerkesztési ikermodul**. 

3. Keresés **TemperatureThreshold** a kívánt tulajdonságait. Módosítsa az értékét egy új hőmérséklet a legújabb jelentett hőmérséklet meghaladja a 10 értéket 5 fok. 

4. Mentse a modul ikereszköz fájlt.

5. Kattintson a jobb gombbal a Szerkesztés panelre és válassza az ikermodul bárhol **frissítés ikermodul**. 

6. A bejövő eszköz a felhőbe irányuló üzenetek figyeléséhez. Az üzenetek, amíg az új hőmérséklet küszöbérték elérése kell megjelennie. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások használt ebben a cikkben díjak elkerülése érdekében. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok hozhat létre, hogy többet is megtudhat [saját IoT Edge-modulok fejlesztése](module-development.md) , illetve [fejlesztése a Visual Studio Code-dal modulok](how-to-vs-code-develop-module.md). Továbbra is be a következő oktatóanyagok az Azure IoT Edge segítségével üzembe helyezheti az Azure cloud services, a peremhálózaton adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)