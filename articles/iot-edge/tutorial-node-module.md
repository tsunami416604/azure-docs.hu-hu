---
title: Az oktatóanyag egyéni Node.js modult - létrehozása az Azure IoT Edge |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Node.js-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 3b79c75b9846a4f8966a113c6e06fabc25bcf011
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792607"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Oktatóanyag: Fejlesztése és üzembe helyezése a Node.js IoT Edge-modul a szimulált eszköz

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A rövid útmutatókban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Node.js-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a Visual Studio Code és a Docker használatával 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [linuxos](quickstart-linux.md) rövid útmutató lépéseit követve.
* Az IoT Edge-hez a node.js modulok nem támogatja a Windows-tárolók. 

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
   * Ha Windows-eszközön végzi a fejlesztést, ellenőrizze, hogy a Docker [konfigurálva van-e Linux-tárolók használatára](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 
* [Node.js és npm](https://nodejs.org). Az npm csomag a Node.js részeként érhető el, ami azt jelenti, hogy a Node.js letöltésével az npm is automatikusan települ a számítógépre.

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
A következő lépések bemutatják, hogyan hozhat létre egy IoT Edge Node.js modult a Visual Studio Code és az Azure IoT-eszközök használatával.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Az **npm** használatával létrehozhat egy Node.js-megoldást, amelyre majd építkezhet. 

1. A Visual Studio Code-ban válassza a **View** > **Integrated Terminal** (Nézet, Integrált terminál) elemet a VS Code integrált terminál megnyitásához.

2. Az integrált terminálon írja be a következő parancsot a **yeoman** és a Node.js Azure IoT Edge-modul generátorának telepítéséhez: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 

3. A parancskatalógus, írja be, és futtassa a parancsot **Azure: Jelentkezzen be a** , és kövesse az utasításokat követve jelentkezzen be az Azure-fiókjával. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

4. A parancskatalógus, írja be, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **Node.js modult**. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **NodeModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének a neve, az előző lépésben megadott van töltve. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/nodemodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-node-module/repository.png)

A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. A megoldás munkaterület öt legfelső szintű összetevőt tartalmaz. A **modules** mappa a modul Node.js-kódját, valamint a modul tárolórendszerképként való összeállítására szolgáló Docker-fájlokat tartalmazza. Az **\.env** fájl a tárolóregisztrációs adatbázis hitelesítő adatait tárolja. A **deployment.template.json** fájl tartalmazza, amely egy eszközön modulok üzembe helyezéséhez használja az IoT Edge-futtatókörnyezet és **deployment.debug.template.json** fájlt tartalmaz, a hibakeresési verzió modulok. Ebben az oktatóanyagban nem fogja szerkeszteni a **\.vscode** mappát vagy a **\.gitignore** fájlt. 

Ha nem adott meg tárolóregisztrációs adatbázist a megoldás létrehozásakor, de elfogadta az alapértelmezett localhost:5000 értéket, akkor nem lesz \.env fájlja. 

<!--
   ![Node.js solution workspace](./media/tutorial-node-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóadattár hitelesítő adatait, és megosztja azokat az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. 

1. A VS Code Explorerben nyissa meg a **.env** fájlt. 
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben. 
3. Mentse el ezt a fájlt. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Mindegyik sablon tartalmaz egy mintakódot is, amely fogadja a **tempSensor** modul szimulált érzékelőadatait, és továbbítja azokat az IoT Hub felé. Ebben a szakaszban kódot adunk hozzá, amely elemzi az üzeneteket azok elküldése előtt. 

1. A VS Code Explorerben nyissa meg a **modules** > **NodeModule** > **app.js** modult.

5. Adjon hozzá egy hőmérsékleti határértéket tároló változót a szükséges csomópontmodulok alá. A hőmérsékleti határérték azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```javascript
    var temperatureThreshold = 25;
    ```

6. Cserélje le a teljes `PipeMessage` függvényt a `FilterMessage` függvényre.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. Cserélje le a `pipeMessage` függvénynevet a `filterMessage` névre a `client.on()` függvényben.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. Másolja a következő kódrészletet a `client.open()` függvény visszahívási részébe a `client.on()` után az `else` utasításon belülre. A rendszer ezt a függvényt akkor hívja meg, amikor a kívánt tulajdonságok frissülnek.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. Mentse az app.js fájlban.

10. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. A fájl arra utasítja az IoT Edge-ügynök, mely modulok üzembe helyezéséhez ebben az esetben **tempSensor** és **NodeModule**, és az IoT Edge hubot jelzi, hogyan irányítsa az üzenetek közöttük. A Visual Studio Code-bővítmény automatikusan kitölti a legtöbb, hogy a központi telepítési sablont a szükséges, de győződjön meg arról, hogy minden működik-e a megoldás pontos információ: 

   1. Az IoT Edge az alapértelmezett platform értékre van állítva **amd64** a VS Code állapotsorban, ami azt jelenti, a **NodeModule** a lemezkép verziószámát Linux AMD64-es értékre van állítva. Módosítsa az alapértelmezett platform az állapotsorban **amd64** való **arm32v7** , amely az IoT Edge-eszköz architektúra esetén. 

      ![A modul lemezképplatformmal frissítése](./media/tutorial-node-module/image-platform.png)

   2. Ellenőrizze, hogy a sablonban a megfelelő modulnév van-e megadva az alapértelmezett **SampleModule** név helyett, amelyet az IoT Edge-megoldás létrehozásakor módosított.

   3. A **registryCredentials** szakasz tárolja a Docker registry hitelesítő adataival, úgy, hogy az IoT Edge-ügynök a modul rendszerképének kérheti le. A tényleges felhasználónév–jelszó párokat az .env fájl tárolja, amelyet a Git figyelmen kívül hagy. Ha még nem tette, vegye fel a hitelesítő adatait a .env fájlba.  

   4. Ha szeretne további tudnivalók a központi telepítési jegyzékek, [megtudhatja, hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat az IoT Edge](module-composition.md).

11. Adja hozzá a NodeModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Ikermodul központi telepítési sablon hozzáadása](./media/tutorial-node-module/module-twin.png)

12. Mentse a deployment.template.json fájlt.


## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a NodeModule modulhoz, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe, ahonnan a modul rendszerképét leküldheti az Azure Container Registrybe: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Használja az első szakaszban az Azure Container Registryből kimásolt felhasználónevet, jelszót és bejelentkezési kiszolgálót. Vagy kérje le őket ismét az Azure Portalon a tárolóregisztrációs adatbázis **Hozzáférési kulcsok** szakaszában.

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy `deployment.json` fájlt egy új **config** mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. Ez a két parancs a kód felépítéséhez, a Node.js-kódnak tárolóalapúvá és leküldése a tároló-beállításjegyzékbe, amely a megoldás inicializálásakor a megadott. 

A tárolórendszerkép teljes címét a címkével a VS Code integrált termináljában futó `docker build` parancsban tekintheti meg. A rendszerkép címe a `module.json` fájlban lévő információk alapján áll össze az **\<adattár\>:\<verzió\>-\<platform\>** formátumban. Ebben az oktatóanyagban a következőképpen kell kinéznie: **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

>[!TIP]
>Ha hibaüzenet jelenik meg, és a modul leküldéses próbál, győződjön meg arról, a következő ellenőrzések:
>* Jelentkezett be a Docker, a Visual Studio Code hitelesítő adatok használatával a tárolóregisztrációs adatbázisból? Ezeket a hitelesítő adatokat eltérnek az Azure Portalra való bejelentkezéshez használja azokat.
>* A tároló-beállításjegyzékbe a helyes? Nyissa meg **modulok** > **nodemodule** > **module.json** , és keresse meg a **tárház** mező. A lemezképtár hasonlóan kell kinéznie  **\<registryname\>.azurecr.io/nodemodule**. 
>* Tárolók rendszert futtató fejlesztői gépen azonos típusú készít? Visual Studio Code-ot alapértelmezett Linux-tárolók amd64. A fejlesztői gépén arm32v7 Linux-tárolók fut, a platform a tárolóplatform megfelelően a VS Code-ablak alján kék állapotsávját frissíteni.
>* Az IoT Edge-hez a node.js modulok nem támogatja a Windows-tárolók.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. Az Azure IoT Hub-eszközkészlet bővítmény (korábbi nevén Azure IoT-eszközkészlet bővítmény) használata a Visual Studio Code-modulok is telepítheti. Már elő van készítve egy üzembehelyezési jegyzék a forgatókönyvhöz, a **deployment.json** fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

1. Futtassa a VS Code parancskatalógus **Azure IoT hubbal: Válassza ki az IoT Hub**. 

2. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot. 

3. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

4. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-node-module/create-deployment.png)

5. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt. 

6. Kattintson a frissítés gombra. Látható, hogy az új **NodeModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett. 


## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak. 

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája. 

Magán az IoT Edge-eszközön az üzembehelyezési modulok állapotának megtekintéséhez használja az `iotedge list` parancsot. Négy modult kell látnia: a két IoT Edge-futtatókörnyezeti modult, a tempSensort és az ebben az oktatóanyagban létrehozott egyéni modult. Az összes modul elindítása eltarthat néhány percig, ezért ha nem látja mindet azonnal, futtassa újra a parancsot. 

Az egyes modulok által létrehozott üzenetek megtekintéséhez használja az `iotedge logs <module name>` parancsot. 

Az IoT Hubra beérkező üzenetek a Visual Studio Code segítségével tekinthetők meg. 

1. Az IoT Hubra érkező adatok monitorozásához kattintson a **...** elemre, majd válassza a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
2. Egy adott eszköz D2C üzeneteinek monitorozásához kattintson a listában a jobb gombbal az eszközre, és válassza ki a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
3. Adatok monitorozásának leállításához futtassa a parancsot **Azure IoT hubbal: D2C üzenet monitorozásának leállítása** a parancskatalógus. 
4. A modul ikerdokumentumának megtekintéséhez vagy frissítéséhez kattintson a listában a jobb gombbal a modulra, és válassza ki az **Edit module twin** (Modul ikerdokumentumának szerkesztése) lehetőséget. A modul ikerdokumentumának frissítéséhez mentse az ikerdokumentum JSON-fájlját, és kattintson a jobb gombbal a szerkesztési területre, majd válassza az **Update Module Twin** (Modul ikerdokumentumának frissítése) lehetőséget.
5. A Docker-naplók megtekintéséhez telepítheti a [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code (Docker a VS Code-hoz) eszközt, és megkeresheti helyileg a futó modulokat a Docker Explorerben. A helyi menüben kattintson a **Show Logs** (Naplók megjelenítése) lehetőségre az integrált terminálban való megtekintéshez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Továbbléphet bármelyik alábbi oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Az Azure Function üzembe helyezése modulként](tutorial-deploy-function.md)
> [Az Azure Stream Analytics üzembe helyezése modulként](tutorial-deploy-stream-analytics.md)

