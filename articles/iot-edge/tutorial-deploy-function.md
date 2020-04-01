---
title: 'Oktatóanyag: Az Azure-függvények üzembe helyezése modulokként – Azure IoT Edge'
description: Ebben az oktatóanyagban egy Azure-függvényt fejleszt ioT Edge-modulként, majd üzembe helyezi egy peremhálózati eszközre.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: f909ca12ce080fc5d1241bcc649c041361e405a7
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421180"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Oktatóanyag: Az Azure-függvények üzembe helyezése IoT Edge-modulokként

Az Azure Functions használatával olyan kódot helyezhet üzembe, amely közvetlenül az Azure IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő Azure-függvény létrehozásának és a szimulált IoT Edge-eszközön való üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows](quickstart.md) vagy [Linux](quickstart-linux.md) rendszeren című rövid útmutatóban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure-függvény létrehozása a Visual Studio Code használatával.
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a VS Code és a Docker használatával.
> * A modul üzembe helyezése egy tárolóregisztrációs adatbázisból az IoT Edge-eszközön.
> * Szűrt adatok megtekintése.

<center>

![Diagram – Bemutatóarchitektúra: a funkciómodul színpadra helyezése és telepítése](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Az ebben az oktatóanyagban létrehozott Azure-függvény szűri az eszköze által létrehozott hőmérsékletadatokat. A függvény csak akkor küld felfelé irányuló üzeneteket az Azure IoT Hubra, amikor a hőmérséklet egy megadott küszöbérték felett van.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt át kellett volna mennie az előző oktatóanyagon, hogy beállítsa a linuxos tárolók fejlesztéséhez a fejlesztői környezetet: [IoT Edge-modulok fejlesztése Linux-eszközökhöz](tutorial-develop-for-linux.md). Az oktatóanyag kitöltésével a következő előfeltételeknek kell megrendelkezniük:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató Linux-eszköz](quickstart-linux.md)
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)konfigurált [Visual Studio-kód.](https://code.visualstudio.com/)
* [A Docker CE](https://docs.docker.com/install/) linuxos tárolók futtatására van konfigurálva.

IoT Edge-modul azure Functions használatával való fejlesztéséhez telepítse a következő további előfeltételeket a fejlesztői gépre:

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Az Azure IoT Tools for Visual Studio-kód, amely az előfeltételekben telepített felügyeleti képességeket, valamint néhány kódsablonokat biztosít. Ebben a szakaszban a Visual Studio Code használatával készíthet Azure-függvényt tartalmazó IoT Edge-megoldást.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Hozzon létre egy C# függvénymegoldás-sablont, amelyet saját kódjával szabhat testre.

1. Nyissa meg a Visual Studio Code-ot a fejlesztői gépen.

2. A VS-kód parancspaletta megnyitásához válassza a **Nézet** > **parancspaletta**lehetőséget.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldásnak, például **FunctionSolution**, vagy fogadja el az alapértelmezett et. |
   | Select module template (Modulsablon kiválasztása) | Válassza **az Azure Functions - C# lehetőséget.** |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. Az utolsó karakterlánc \<a\>rendszerleíró adatbázis "azurecr.io/CSharpFunction. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a célarchitektúrát

Jelenleg a Visual Studio Code c modulokat fejleszthet Linux AMD64 és Linux ARM32v7 eszközökhöz. Ki kell választania, hogy az egyes megoldásokkal melyik architektúrát célozza meg, mert a tároló az egyes architektúratípusokhoz másképp en fut. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancspalettát, és keressen az **Azure IoT Edge: Set Default Target Platform for Edge Solution (Alapértelmezett célplatform az edge-megoldáshoz) kifejezésre,** vagy válassza ki az ablak alján lévő oldalsávon található parancsikont.

2. A parancspalettán válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu virtuális gépet használunk IoT Edge eszközként, így megtartja az alapértelmezett **amd64-et.**

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Adjunk hozzá néhány további kódot, hogy a modul feldolgozza az üzeneteket a szélén, mielőtt továbbítana őket az IoT Hubra.

1. A Visual Studio Code programban nyissa meg a**CSharpFunction** > **CSharpFunction.cs** **modulokat.** > 

1. Cserélje le a **CSharpFunction.cs** fájl tartalmát a következő kódra. Ez a kód telemetriai adatokat kap a környezeti és a gép hőmérsékletéről, és csak akkor továbbítja az üzenetet az IoT Hubba, ha a gép hőmérséklete egy meghatározott küszöbérték felett van.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

1. Mentse a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és módosította a **CSharpFunction-t,** hogy kiszűrje az üzeneteket az elfogadható küszöbérték alatti jelentett gépi hőmérséklettel. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

Ebben a szakaszban adja meg a hitelesítő adatokat a tároló beállításjegyzék második alkalommal (az első volt az IoT Edge-megoldás **.env** fájljában) a fejlesztői gépről helyileg bejelentkezve, hogy a Visual Studio-kód leküldéses rendszerképeket a rendszerleíró adatbázisba.

1. Nyissa meg a VS Code integrált terminált a **Terminál megtekintése** > **Terminal**lehetőséget választva.

2. Az integrált terminálban az alábbi paranccsal jelentkezzen be a tárolóregisztrációs adatbázisba. Használja azt a felhasználónevet és bejelentkezési kiszolgálót, amelyet az Azure Container Registry-adatbázisból másolt ki korábban.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Amikor a rendszer kéri a jelszó megadását, illessze be a jelszót (az nem lesz látható a terminálablakban) a tárolóbeállításjegyzékbe, és nyomja le az **Enter billentyűt.**

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. A VS Code Explorerben kattintson a jobb gombbal a deployment.template.json fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

Amikor azt mondja a Visual Studio Code-nak a megoldás létrehozásához, először a központi telepítési sablonban lévő információkat hozza létre, és létrehoz egy deployment.json fájlt egy **config**nevű új mappában. Ezután két parancsot futtat `docker build` az `docker push`integrált terminálon: és . A build parancs létrehozza a kódot, és konténerezi a függvényeket. Ezután a leküldéses parancs leküldéses a kódot a tároló rendszerleíró adatbázisába, amely a megoldás inicializálásakor megadott.

## <a name="view-your-container-image"></a>A tárolórendszerkép megtekintése

A Visual Studio Code sikert jelző üzenetet küld, miután leküldte a tárolórendszerképet a tárolóregisztrációs adatbázisba. Ha személyesen is ellenőrizni szeretné a műveletet, keresse meg a rendszerképet az adatbázisban.

1. Az Azure Portalon tallózzon az Azure Container Registry-adatbázisra.
2. Válassza az **Adattárak** elemet.
3. Ekkor a **csharpfunction** adattár jelenik meg a listában. Válassza ki az adattárat a további részletek megtekintéséhez.
4. A **Címkék** szakaszban megjelenik a **0.0.1-amd64** címke. Ez a címke jelzi a létrehozott rendszerkép verziószámát és platformját. Ezek az értékek a CSharpFunction mappa module.json fájljában találhatók meg.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A függvények modulját a gyors útmutatókban bemutatott módon az Azure Portal segítségével helyezheti üzembe az IoT Edge-eszközökön. A modulokat emellett a Visual Studio Code használatával is üzembe helyezheti és monitorozhatja. A következő szakaszok az Azure IoT-eszközök vs-kódhoz, amely az előfeltételekben felsorolt. Ha még nem tette meg, telepítse most a bővítményt.

1. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for single device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Navigáljon a **CSharpFunction** függvényt tartalmazó megoldásmappához. Nyissa meg a konfigurációs mappát, jelölje ki a **deployment.json** fájlt, és válassza **az Edge Deployment Manifest kiválasztása**lehetőséget.

4. Frissítse az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. Az új **CSharpFunction** függvénynek a **SimulatedTemperatureSensor** modullal, valamint a **$edgeAgent** és **a $edgeHub**együtt kell futnia. Eltarthat néhány percig, amíg az új modulok megjelennek. Az IoT Edge-eszköz nek le kell kérnie az új üzembe helyezési információkat az IoT Hubból, el kell indítania az új tárolókat, majd jelentenie kell az állapotot az IoT Hubnak.

   ![Üzembe helyezett modulok megtekintése a VS Code-ban](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Az IoT Hub hoz: A **beépített eseményvégpont indítása** a parancspalettán az IoT hubra érkező összes üzenet megtekintéséhez.

A nézetet szűrheti is, hogy csak a megjelölt eszközről az IoT Hub-központra érkező üzenetek jelenjenek meg. Kattintson a jobb gombbal az eszközre az **Azure IoT Hub-eszközök szakaszban,** és válassza **a Beépített eseményvégpont figyelésének megkezdése parancsot.**

Az üzenetek figyelésének leállításához futtassa az **Azure IoT Hub: Stop Monitoring beépített eseményvégponta** a parancspalettán.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-függvénymodult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll saját modulok készítésére, többet is megtudhat arról, hogyan [fejleszthet az Azure IoT Edge for Visual Studio-kóddal.](how-to-vs-code-develop-module.md)

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Átlagértékek keresése lebegő Azure Stream Analytics-ablakban](tutorial-deploy-stream-analytics.md)
