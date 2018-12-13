---
title: Az oktatóanyag egy Azure-függvényt az Azure IoT Edge - eszköz üzembe helyezése |} A Microsoft Docs
description: Ebben az oktatóanyagban fejlesztése az Azure IoT Edge-modul adatbázisként működik, akkor telepítheti az edge-eszköz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1488f6aff202f8b307b883d8a795d7df20066661
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081880"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Oktatóanyag: Üzembe helyezni az Azure IoT Edge-modulok működik

Az Azure Functions használatával olyan kódot helyezhet üzembe, amely közvetlenül az Azure IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő Azure-függvény létrehozásának és a szimulált IoT Edge-eszközön való üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows](quickstart.md) vagy [Linux](quickstart-linux.md) rendszeren című rövid útmutatóban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:     

> [!div class="checklist"]
> * Azure-függvény létrehozása a Visual Studio Code használatával.
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a VS Code és a Docker használatával.
> * A modul üzembe helyezése egy tárolóregisztrációs adatbázisból az IoT Edge-eszközön.
> * Szűrt adatok megtekintése.

<center>
![Diagram – oktatóanyag architektúra, előkészítése és üzembe helyezése függvénymodul](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Az Azure Function moduljai [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhetők el az Azure IoT Edge szolgáltatásban. 

Az ebben az oktatóanyagban létrehozott Azure-függvény szűri az eszköze által létrehozott hőmérsékletadatokat. A függvény csak akkor küld felfelé irányuló üzeneteket az Azure IoT Hubra, amikor a hőmérséklet egy megadott küszöbérték felett van. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Állítható a fejlesztői gépén vagy egy virtuális gép mentése az Edge-eszköz esetében ez a rövid útmutató lépéseit követve [Linux](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md).

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben az oktatóanyagban használhatja az Azure IoT Edge-bővítmény a Visual Studio Code fel egy modult, és hozzon létre egy **tárolórendszerkép** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Minden olyan Docker-kompatibilis beállításjegyzéket a tárolólemezképek tárolására használható. Két népszerű Docker beállításjegyzék szolgáltatások [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

    ![Tároló-beállításjegyzék létrehozása az Azure Portalon](./media/tutorial-deploy-function/create-container-registry.png)

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

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Az előfeltételként feltelepített Visual Studio Code-hoz készült Azure IoT Edge-bővítmény felügyeleti funkciókat és sablonkódokat is tartalmaz. Ebben a szakaszban a Visual Studio Code használatával készíthet Azure-függvényt tartalmazó IoT Edge-megoldást. 

1. Nyissa meg a Visual Studio Code-ot a fejlesztői gépen.

2. A **View (Nézet)** > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, például **FunctionSolution**, vagy fogadja el az alapértelmezett. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **az Azure Functions - C#** . |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. Néz ki a végső karakterláncban \<beállításjegyzék neve\>.azurecr.io/CSharpFunction. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-function/repository.png)

4. A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét: egy \.vscode mappát, egy modules mappát, egy üzembe helyezési jegyzéksablonfájlt. és egy \.env fájlt. A VS Code Explorerben nyissa meg a **modules** > **CSharpFunction** > **CSharpFunction.cs** fájlt.

5. Cserélje le a tartalmát a **CSharpFunction.cs** fájlt a következő kóddal. Ez a kód megkapja a telemetriai adatokat gyűjthessen környezeti és a gép hőmérséklet, és csak akkor továbbítja az üzenet továbbítása az IoT Hub, ha a gép hőmérséklet egy meghatározott küszöbérték fölött van.

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
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
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

6. Mentse a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a **CSharpFunction** függvényhez, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet alacsonyabb az elfogadható határértéknél. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

Ebben a szakaszban kétszer meg kell adnia a tárolóregisztrációs adatbázis hitelesítő adatait. Egyszer azért, hogy helyileg bejelentkezzen a fejlesztői gépről, hogy a Visual Studio Code le tudja küldeni a rendszerképeket a regisztrációs adatbázisba. Másodszor pedig az IoT Edge-megoldás **.env** kiterjesztésű fájlja miatt, ami engedélyezi a rendszerképek lekérését az IoT Edge-eszköz számára a beállításjegyzékből. 

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet. 

2. Az integrált terminálban az alábbi paranccsal jelentkezzen be a tárolóregisztrációs adatbázisba. Használja azt a felhasználónevet és bejelentkezési kiszolgálót, amelyet az Azure Container Registry-adatbázisból másolt ki korábban.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Amikor a rendszer kéri a jelszót, másolja be a tárolóregisztrációs adatbázis jelszavát, majd nyomja le az **Enter** billentyűt.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. Ebből a fájlból tudja meg az IoT Edge-futtatókörnyezet, hogy mely modulokat kell üzembe helyezni egy eszközön. Figyelje meg, hogy a **CSharpFunction** függvénymodul együtt szerepel a **tempSensor** modullal, amely a teszt adatait biztosítja. További információt az üzembehelyezési jegyzékekről az [IoT Edge-modulok használatát, konfigurálását és újrahasznosítását](module-composition.md) ismertető cikkben olvashat.

   ![Az üzembehelyezési jegyzék megkeresése a modulban](./media/tutorial-deploy-function/deployment-template.png)

3. Nyissa meg az **.env** kiterjesztésű fájlt az IoT Edge-megoldás munkaterületén. A tárolóregisztrációs adatbázis hitelesítő adatait nem kell az üzembe helyezési jegyzéksablonhoz adnia, hiszen ez a git által figyelmen kívül hagyott fájl tárolja őket. Adja meg a tárolóregisztrációs adatbázis **felhasználónevét** és **jelszavát**. 

5. Mentse el ezt a fájlt.

6. A VS Code Explorerben kattintson a jobb gombbal a deployment.template.json fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy deployment.json nevű fájlt egy új **config** nevű mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a függvényeket, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

## <a name="view-your-container-image"></a>A tárolórendszerkép megtekintése

A Visual Studio Code sikert jelző üzenetet küld, miután leküldte a tárolórendszerképet a tárolóregisztrációs adatbázisba. Ha személyesen is ellenőrizni szeretné a műveletet, keresse meg a rendszerképet az adatbázisban. 

1. Az Azure Portalon tallózzon az Azure Container Registry-adatbázisra. 
2. Válassza az **Adattárak** elemet.
3. Ekkor a **csharpfunction** adattár jelenik meg a listában. Válassza ki az adattárat a további részletek megtekintéséhez.
4. A **Címkék** szakaszban megjelenik a **0.0.1-amd64** címke. Ez a címke jelzi a létrehozott rendszerkép verziószámát és platformját. Ezek az értékek a CSharpFunction mappa module.json fájljában találhatók meg. 

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A függvények modulját a gyors útmutatókban bemutatott módon az Azure Portal segítségével helyezheti üzembe az IoT Edge-eszközökön. A modulokat emellett a Visual Studio Code használatával is üzembe helyezheti és monitorozhatja. A következő szakaszokban az előfeltételek között felsorolt, VS Code-hoz készült Azure IoT Edge-bővítményt használjuk. Ha még nem tette meg, telepítse most a bővítményt. 

1. A **View (Nézet)** > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.

2. Keresse meg, majd futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot. Az utasításokat követve jelentkezzen be Azure-fiókjába. 

3. A parancskatalógusban keresse meg és futtassa az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot. 

4. Keresse meg az IoT Hub-központot tartalmazó előfizetést, majd válassza ki az IoT Hub-központot, amelyhez hozzá kíván férni.

5. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

6. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for single device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

7. Navigáljon a **CSharpFunction** függvényt tartalmazó megoldásmappához. Nyissa meg a konfigurációs mappát, válassza ki a **deployment.json** fájlt, és válassza a **kiválasztása peremhálózati üzembe helyezési Manifest**.

8. Frissítse az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. Látható, hogy az új **CSharpFunction** fut a **TempSensor** modul, valamint a **$edgeAgent** és a **$edgeHub** mellett. Eltarthat néhány másodpercig jelenik meg az új modulokat. Az IoT Edge-eszköz rendelkezik az új központi telepítési adatok kinyerése az IoT Hub, indítsa el az új tárolókat és jelentést készít az állapot vissza az IoT hubnak. 

   ![Üzembe helyezett modulok megtekintése a VS Code-ban](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az IoT Hub-központra érkező összes üzenetet a parancskatalógus **Azure IoT Hub: Start Monitoring D2C Message** (Azure IoT Hub: D2C üzenetek monitorozásának elindítása) parancsának futtatásával tekintheti meg.

A nézetet szűrheti is, hogy csak a megjelölt eszközről az IoT Hub-központra érkező üzenetek jelenjenek meg. Kattintson a jobb gombbal a kívánt eszközre az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) területen, és válassza a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának elindítása) parancsot.

Az üzenetek monitorozásának leállításához futtassa a parancskatalógus **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: D2C üzenetek monitorozásának leállítása) parancsát. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-függvénymodult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [az Azure-függvények Visual Studio Code és Azure IoT Edge segítségével történő fejlesztését](how-to-develop-csharp-function.md) ismertető cikkben talál. 

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Átlagértékek keresése lebegő Azure Stream Analytics-ablakban](tutorial-deploy-stream-analytics.md)

