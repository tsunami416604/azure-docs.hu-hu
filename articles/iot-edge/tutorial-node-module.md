---
title: Azure IoT Edge Node.js – oktatóanyag | Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Node.js-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 9ab441bdd30e7598dacfec8dd74702aef0299e1b
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413490"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Oktatóanyag: Node.js-alapú IoT Edge-modul fejlesztése és üzembe helyezése egy szimulált eszközön

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows][lnk-tutorial1-win] vagy [Linux][lnk-tutorial1-lin] rendszeren című oktatóanyagban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Node.js-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a Visual Studio Code és a Docker használatával 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [Linux-](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójának lépéseit követve.
* Az Azure Machine Learning-modul nem támogatja az ARM processzorokat.

Felhőerőforrások:

* Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code-hoz. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js és npm](https://nodejs.org). Az npm csomag a Node.js részeként érhető el, ami azt jelenti, hogy a Node.js letöltésével az npm is automatikusan települ a számítógépre.

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
A következő lépések azt mutatják be, hogyan hozhat létre egy IoT Edge Node.js-modult a Visual Studio Code és az Azure IoT Edge bővítmény használatával.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Az **npm** használatával létrehozhat egy Node.js-megoldást, amelyre majd építkezhet. 

1. A Visual Studio Code-ban válassza a **View** > **Integrated Terminal** (Nézet, Integrált terminál) elemet a VS Code integrált terminál megnyitásához.

2. Az integrált terminálon írja be a következő parancsot a **yeoman** és a Node.js Azure IoT Edge-modul generátorának telepítéséhez: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 

3. A parancskatalógusban írja be és futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot, és az utasításokat követve jelentkezzen be Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

4. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 

   1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni. 
   2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
   3. A modul sablonjaként válassza a **Node.js-modult**. 
   4. A modulnak adja a **NodeModule** nevet. 
   5. Adja meg az előző szakaszban létrehozott Azure Container Registryt az első modul rendszerképadattáraként. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére. A sztring végül a következőképp néz ki: **\<regisztrációs adatbázis neve\>.azurecr.io/nodemodule**.
 
A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. Ez egy **.vscode** mappát, egy **modules** mappát, egy **.env** fájlt és egy üzembe helyezési jegyzéksablonfájlt tartalmaz

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
    var temperatureThreshold = 30;
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

9. Mentse el ezt a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a NodeModule modulhoz, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe, ahonnan a modul rendszerképét leküldheti az Azure Container Registrybe: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Használja az első szakaszban az Azure Container Registryből kimásolt felhasználónevet, jelszót és bejelentkezési kiszolgálót. Vagy kérje le őket ismét az Azure Portalon a tárolóregisztrációs adatbázis **Hozzáférési kulcsok** szakaszában.

2. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. 

   Az `$edgeAgent` a fájlból tudja meg, hogy két modult kell üzembe helyeznie: az eszközadatokat szimuláló **tempSensor**, valamint a **NodeModule** modult. A `NodeModule.image` értéke a rendszerkép Linux amd64-es verziójára lett beállítva. További információt az üzembehelyezési jegyzékekről az [IoT Edge-modulok használatát, konfigurálását és újrahasznosítását](module-composition.md) ismertető cikkben olvashat.

   A fájl a regisztrációs adatbázis hitelesítő adatait is tartalmazza. A sablonfájlban a felhasználóneve és a jelszava helyén helyőrzők állnak. Az üzembehelyezési jegyzék létrehozásakor a rendszer a mezőket automatikusan kitölti a **.env** fájlban megadott értékekkel. 

4. Adja hozzá a NodeModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után: 
    ```json
        "NodeModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```
5. Mentse el ezt a fájlt.
6. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build IoT Edge solution** (IoT Edge-megoldás összeállítása) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy `deployment.json` fájlt egy új **config** mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs elvégzi a kód buildelését, tárolóba helyezi a Node.js-kódot, majd leküldi azt a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

A tárolórendszerkép teljes címét a címkével a VS Code integrált termináljában futó `docker build` parancsban tekintheti meg. A rendszerkép címe a `module.json` fájlban lévő információk alapján áll össze az **\<adattár\>:\<verzió\>-\<platform\>** formátumban. Ebben az oktatóanyagban a következőképpen kell kinéznie: **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A Node.js-modult az Azure Portal segítségével helyezheti üzembe az IoT Edge-eszközökön a gyors útmutatókban bemutatott módon, de a modulokat a Visual Studio Code használatával is üzembe helyezheti és monitorozhatja. A következő szakaszokban az előfeltételek között felsorolt, VS Code-hoz készült Azure IoT Edge-bővítményt használjuk. Telepítse most, ha korábban nem tette meg. 

1. A **View (Nézet)** > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.

2. Keresse meg, majd futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot. Az utasításokat követve jelentkezzen be Azure-fiókjába. 

3. A parancskatalógusban keresse meg és futtassa az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot. 

4. Keresse meg az IoT Hubot tartalmazó előfizetést, majd válassza ki az IoT Hubot, amelyhez hozzá kíván férni.

5. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

6. Kattintson a jobb gombbal az IoT Edge-eszköze nevére, majd válassza a **Create Deployment for IoT Edge device** (Üzembe helyezés létrehozása IoT Edge-eszközhöz) parancsot. 

7. Navigáljon a NodeModule modult tartalmazó megoldásmappához. Nyissa meg a **config** mappát, és válassza ki a **deployment.json** fájlt. Kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre.

8. Frissítse az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. Látható, hogy az új **NodeModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett. 


## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hubra érkező adatok monitorozásához kattintson a **...** elemre, majd válassza a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
2. Egy adott eszköz D2C üzeneteinek monitorozásához kattintson a listában a jobb gombbal az eszközre, és válassza ki a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
3. Az adatok monitorozásának leállításához futtassa a parancskatalógusban az **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: D2C üzenetek monitorozásának leállítása) parancsot. 
4. A modul ikerdokumentumának megtekintéséhez vagy frissítéséhez kattintson a listában a jobb gombbal a modulra, és válassza ki az **Edit module twin** (Modul ikerdokumentumának szerkesztése) lehetőséget. A modul ikerdokumentumának frissítéséhez mentse az ikerdokumentum JSON-fájlját, és kattintson a jobb gombbal a szerkesztési területre, majd válassza az **Update Module Twin** (Modul ikerdokumentumának frissítése) lehetőséget.
5. A Docker-naplók megtekintéséhez telepítheti a [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code (Docker a VS Code-hoz) eszközt, és megkeresheti helyileg a futó modulokat a Docker Explorerben. A helyi menüben kattintson a **Show Logs** (Naplók megjelenítése) lehetőségre az integrált terminálban való megtekintéshez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a már létrehozott erőforrásokat és konfigurációkat.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

> [!IMPORTANT]
> Az Azure-erőforrások és -erőforráscsoportok törlése nem vonható vissza. A törlést követően az erőforráscsoport és a benne foglalt erőforrások véglegesen törölve lesznek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Ha csak az IoT Hubot szeretné törölni, hajtsa végre az alábbi parancsot a saját hubja és a saját erőforráscsoportja nevével:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


A teljes erőforráscsoport név alapján való törléséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. A **Szűrés név alapján...** mezőbe írja be az IoT Hubot tartalmazó erőforráscsoport nevét. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Továbbléphet bármelyik alábbi oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Az Azure Function üzembe helyezése modulként](tutorial-deploy-function.md)
> [Az Azure Stream Analytics üzembe helyezése modulként](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
