---
title: Az oktatóanyag a Linux - az Azure IoT Edge-Node.js modul fejlesztése |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Node.js-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 2725f0a34ace3a353df5f746df299aeaf0ea92b3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574246"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Oktatóanyag: Fejleszthet és helyezhet üzembe egy Node.js IoT Edge-modul a Linux rendszerű eszközök

A Visual Studio Code használatával Node.js-kód fejlesztése és üzembe helyezése az Azure IoT Edge-es Linux rendszerű eszközök. 

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A rövid útmutatókban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Node.js-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a Visual Studio Code és a Docker használatával 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatókör

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy moduljában lévő **Node.js** használatával **Visual Studio Code**, és hogyan helyezhet üzembe, hogy egy **Linux eszköz**. IoT Edge nem támogatja a Node.js modulok Windows-eszközökhöz.

A következő táblázat segítségével fejleszteni és üzembe helyezni a Node.js modulok a lehetőségek megismerése: 

| Node.js | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux-AMD64** | ![A VS Code használata a Linux-AMD64 Node.js modulok](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![A VS Code használata a Linux ARM32 Node.js modulok](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez kell elvégezte az előző oktatóanyagban tároló Linux-fejlesztéshez a fejlesztési környezet beállítása: [A Linux rendszerű eszközök IoT Edge-modulok fejlesztése](tutorial-develop-for-linux.md). Ezek az oktatóanyagok egyikét kitöltésével helyen kell rendelkeznie a következő előfeltételek vonatkoznak: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Azure IoT Edge-es Linux rendszerű eszközök](quickstart-linux.md)
* Egy tároló-beállításjegyzéket, pl. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [A Visual Studio Code](https://code.visualstudio.com/) konfigurálva a [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [A docker CE](https://docs.docker.com/install/) konfigurált Linux-tárolók futtatásához.

Fejlesztés Node.js-ben az IoT Edge-modul, a fejlesztői gépen telepítse a következő további Előfeltételek: 

* [Node.js és npm](https://nodejs.org). Az npm csomag a Node.js részeként érhető el, ami azt jelenti, hogy a Node.js letöltésével az npm is automatikusan települ a számítógépre.

## <a name="create-a-module-project"></a>A modul projekt létrehozása
A következő lépések bemutatják, hogyan hozhat létre egy IoT Edge Node.js modult a Visual Studio Code és az Azure IoT-eszközök használatával.

### <a name="create-a-new-project"></a>Új projekt létrehozása

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


### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóadattár hitelesítő adatait, és megosztja azokat az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. 

1. A VS Code Explorerben nyissa meg a **.env** fájlt. 
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben. 
3. Mentse el ezt a fájlt. 

### <a name="select-your-target-architecture"></a>Válassza ki a cél-architektúra

Jelenleg a Visual Studio Code fejleszthet a Node.js modulok Linux AMD64- és Linux ARM32v7 eszközökhöz. Kell választania, melyik architektúra célozza az egyes megoldások, mert a tároló összeállítása és futtatása eltérően az egyes architektúra. Az alapértelmezett érték a Linux-AMD64. 

1. A parancskatalógus megnyitásához, és keressen rá a **Azure IoT Edge: Állítsa be alapértelmezett célplatform peremhálózati megoldás**, vagy válassza ki a parancsikont a oldalsó sáv az ablak alján. 

2. A parancskatalógus válassza ki a cél-architektúra a lehetőségek listájából. Ebben az oktatóanyagban használjuk egy Ubuntu virtuális gép legyen az IoT Edge-eszköz, így megtartja az alapértelmezett **amd64**.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Mindegyik sablon tartalmaz egy mintakódot is, amely fogadja a **tempSensor** modul szimulált érzékelőadatait, és továbbítja azokat az IoT Hub felé. Ebben a szakaszban kódot adunk hozzá, amely elemzi az üzeneteket azok elküldése előtt. 

1. A VS Code Explorerben nyissa meg a **modules** > **NodeModule** > **app.js** modult.

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

6. Mentse az app.js fájlban.

7. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt.

8. Adja hozzá a NodeModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Ikermodul központi telepítési sablon hozzáadása](./media/tutorial-node-module/module-twin.png)

9. Mentse a deployment.template.json fájlt.


## <a name="build-and-push-your-module"></a>És a modul leküldéses

Az előző szakaszban létrehozott egy IoT Edge-megoldás, és hozzá kódot a NodeModule, hogy a rendszer kiszűri azokat az üzeneteket a gép jelentett hőmérséklet esetén az elfogadható határokon belül. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

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

4. Kattintson a frissítés gombra. Látható, hogy az új **NodeModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal az IoT Edge-eszköz neve, és válassza ki **figyelési D2C-Messages Start**.

2. Az IoT Hub érkező üzenetek megjelenítése. Az üzenetek kimarad, míg igénybe vehet, mert az IoT Edge-eszköz kapni az új központi telepítést, és indítsa el a modult. Azt a NodeModule kóddal végzett módosításokat, majd várjon, amíg a gép hőmérséklet 25 fok eléri üzenetek elküldése előtt. Hozzáadja az üzenettípus **riasztási** , az üzenetek, eléri a hőmérséklet-küszöbérték. 


## <a name="edit-the-module-twin"></a>A modul ikereszköz szerkesztése

NodeModule ikermodulja manifest nasazení használjuk a hőmérséklet küszöbérték 25 fok beállítása. Az ikermodul segítségével anélkül, hogy a modul kódot kellene működésének módosításához.

1. A Visual Studio Code bontsa ki a Részletek területen az IoT Edge-eszköz a futó modulok megtekintéséhez. 

2. Kattintson a jobb gombbal **NodeModule** válassza **szerkesztési ikermodul**. 

3. Keresés **TemperatureThreshold** a kívánt tulajdonságait. Módosítsa az értékét egy új hőmérséklet a legújabb jelentett hőmérséklet meghaladja a 10 értéket 5 fok. 

4. Mentse a modul ikereszköz fájlt.

5. Kattintson a jobb gombbal a Szerkesztés panelre és válassza az ikermodul bárhol **frissítés ikermodul**. 

5. A bejövő eszköz a felhőbe irányuló üzenetek figyeléséhez. Az üzenetek, amíg az új hőmérséklet küszöbérték elérése kell megjelennie. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok hozhat létre, hogy többet is megtudhat [saját IoT Edge-modulok fejlesztése](module-development.md) , illetve [fejlesztése a Visual Studio Code-dal modulok](how-to-vs-code-develop-module.md). Továbbra is be a következő oktatóanyagok az Azure IoT Edge segítségével üzembe helyezheti az Azure cloud services, a peremhálózaton adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
