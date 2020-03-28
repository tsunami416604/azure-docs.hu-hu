---
title: Oktatóanyag a Node.js modul fejlesztése Linuxhoz - Azure IoT Edge | Microsoft dokumentumok
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Node.js-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 71b22bf9bf040abcdf513a4f8baa916930c8972e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772226"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Oktatóanyag: Node.js IoT Edge-modul fejlesztése és üzembe helyezése Linux-eszközökhöz

A Visual Studio-kód használatával node.js-kódot fejleszthet, és üzembe helyezheti egy Azure IoT Edge-et futtató Linux-eszközre.

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A rövid útmutatókban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * IoT Edge Node.js-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a Visual Studio Code és a Docker használatával
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet ki egy modult a **Node.js-ben** a **Visual Studio Code**használatával, és hogyan telepítheti **linuxos eszközre.** Az IoT Edge nem támogatja a Node.js modulokat Windows-eszközökhöz.

Az alábbi táblázat segítségével megismerheti a Node.js modulok fejlesztésének és üzembe helyezésének lehetőségeit:

| Node.js | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Vs Code használata node.js modulokhoz Linux AMD64 rendszeren](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Vs Code használata node.js modulokhoz Linux ARM32 rendszeren](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt át kellett volna mennie az előző oktatóanyagon, hogy beállítsa a linuxos tárolók fejlesztéséhez a fejlesztői környezetet: [IoT Edge-modulok fejlesztése Linux-eszközökhöz](tutorial-develop-for-linux.md). Az oktatóanyagok kitöltésével a következő előfeltételeket kell megtöltenie:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató Linux-eszköz](quickstart-linux.md)
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)konfigurált [Visual Studio-kód.](https://code.visualstudio.com/)
* [A Docker CE](https://docs.docker.com/install/) linuxos tárolók futtatására van konfigurálva.

IoT Edge-modul fejlesztéséhez a Node.js-ben telepítse a következő további előfeltételeket a fejlesztői gépre:

* [Node.js és npm](https://nodejs.org). Az npm csomag a Node.js részeként érhető el, ami azt jelenti, hogy a Node.js letöltésével az npm is automatikusan települ a számítógépre.

## <a name="create-a-module-project"></a>Modulprojekt létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre egy IoT Edge Node.js modult a Visual Studio Code és az Azure IoT Tools használatával.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Az **npm** használatával létrehozhat egy Node.js-megoldást, amelyre majd építkezhet.

1. A Visual Studio Kód nézete mezőben válassza az**Integrált terminál** **megtekintése** > lehetőséget a VS Code integrált terminál megnyitásához.

2. Az integrált terminálon írja be a következő parancsot a **yeoman** és a Node.js Azure IoT Edge-modul generátorának telepítéséhez:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. A VS Code parancspaletta megnyitásához válassza a **Nézet** > **parancspaletta** lehetőséget.

4. A parancskatalógusban írja be és futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot, és az utasításokat követve jelentkezzen be Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

5. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldásnak, vagy fogadja el az alapértelmezett **EdgeSolution nevet.** |
   | Select module template (Modulsablon kiválasztása) | Válassza **a Node.js modul lehetőséget.** |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **NodeModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre ki van töltve az utolsó lépésben megadott névből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>A végső képtár \<úgy\>néz ki, mint a rendszerleíró adatbázis neve .azurecr.io/nodemodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóadattár hitelesítő adatait, és megosztja azokat az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code intézőben nyissa meg az **.env** fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a célarchitektúrát

Jelenleg a Visual Studio Code node.js modulokat fejleszthet Linux AMD64 és Linux ARM32v7 eszközökhöz. Ki kell választania, hogy az egyes megoldásokkal melyik architektúrát célozza meg, mert a tároló az egyes architektúratípusokhoz másképp en fut. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancspalettát, és keressen az **Azure IoT Edge: Set Default Target Platform for Edge Solution (Alapértelmezett célplatform az edge-megoldáshoz) kifejezésre,** vagy válassza ki az ablak alján lévő oldalsávon található parancsikont.

2. A parancspalettán válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu virtuális gépet használunk IoT Edge eszközként, így megtartja az alapértelmezett **amd64-et.**

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Minden sablon hoz egy mintakódot tartalmaz, amely szimulált érzékelő adatokat a **SimulatedTemperatureSensor** modulból, és továbbítja azt az IoT Hub. Ebben a szakaszban kódot adunk hozzá, amely elemzi az üzeneteket azok elküldése előtt.

1. A VS Code explorer programban nyissa meg a**NodeModule** > **app.js modulokat.** **modules** > 

2. Adjon hozzá egy hőmérsékleti határértéket tároló változót a szükséges csomópontmodulok alá. A hőmérsékleti határérték azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Cserélje le a teljes `PipeMessage` függvényt a `FilterMessage` függvényre.

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

4. Cserélje le a `pipeMessage` függvénynevet a `filterMessage` névre a `client.on()` függvényben.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Másolja a következő kódrészletet a `client.open()` függvény visszahívási részébe a `client.on()` után az `else` utasításon belülre. A rendszer ezt a függvényt akkor hívja meg, amikor a kívánt tulajdonságok frissülnek.

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

6. Mentse az app.js fájlt.

7. A VS Code explorer, nyissa meg a **deployment.template.json** fájlt az IoT Edge-megoldás munkaterületén.

8. Adja hozzá a NodeModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Ikermodul hozzáadása a telepítési sablonhoz](./media/tutorial-node-module/module-twin.png)

9. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>Építsd meg és nyomd meg a modult

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és kódot adott hozzá a NodeModule-hoz, amely kiszűri az üzeneteket, ahol a jelentett géphőmérséklet az elfogadható határokon belül van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

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

4. Kattintson a frissítés gombra. Meg kell jelennie az új **NodeModule** futó együtt **simulatedTemperaturesensor** modul és a **$edgeAgent** és **$edgeHub.**

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Kódkezelőjében kattintson a jobb gombbal az IoT Edge-eszköz nevére, és válassza **a Beépített eseményvégpont indítása parancsot.**

2. Tekintse meg az IoT Hubhoz érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek. Az IoT Edge-eszköz nek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután a NodeModule kódmódosításaink megvárják, amíg a gép hőmérséklete eléri a 25 fokot az üzenetek küldése előtt. Emellett hozzáadja a **Riasztás üzenettípust** minden olyan üzenethez, amely eléri az adott hőmérsékleti küszöbértéket.

## <a name="edit-the-module-twin"></a>A modul szerkesztése iker

A NodeModule modul ikerelem a központi telepítési jegyzékben a hőmérsékleti küszöbérték beállítása 25 fok. Az ikermodul segítségével a modulkód frissítése nélkül módosíthatja a funkciót.

1. A Visual Studio-kódban bontsa ki a részleteket az IoT Edge-eszköz alatt a futó modulok megtekintéséhez.

2. Kattintson a jobb gombbal **a NodeModule** elemre, és válassza **az Ikermodul szerkesztése parancsot.**

3. Keresse **meg a TemperatureThreshold** tulajdonságot a kívánt tulajdonságokban. Változtassa meg az értékét egy új hőmérséklet 5 fokkal 10 fokkal magasabb, mint a legutóbbi jelentett hőmérséklet.

4. Mentse a modul ikerfájlját.

5. Kattintson a jobb gombbal a modul ikerszerkesztő ablaktáblájának tetszőleges pontjára, és válassza **az Ikermodul frissítése parancsot**.

6. Figyelje a bejövő eszközről a felhőbe irányuló üzeneteket. Az új hőmérsékleti küszöbérték eléréséig meg kell jelennie az üzeneteknek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll saját modulok készítésére, többet is megtudhat [saját IoT Edge-modulok fejlesztéséről,](module-development.md) illetve arról, hogyan [fejleszthet modulokat a Visual Studio-kóddal.](how-to-vs-code-develop-module.md) Példák az IoT Edge-modulok, beleértve a szimulált hőmérséklet modul, lásd: [IoT Edge modul minták.](https://github.com/Azure/iotedge/tree/master/edge-modules)

Folytathatja a következő oktatóanyagokat, hogy megtudja, az Azure IoT Edge hogyan segíthet az Azure felhőszolgáltatásainak üzembe helyezésében a peremhálózati adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Egyéni vision szolgáltatás](tutorial-deploy-custom-vision.md)
