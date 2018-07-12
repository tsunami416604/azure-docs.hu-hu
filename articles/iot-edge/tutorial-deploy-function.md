---
title: Azure-függvény üzembe helyezése az Azure IoT Edge használatával | Microsoft Docs
description: Azure-függvény üzembe helyezése modulként Edge-eszközre
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0445817f9ff403156025e38a1e14a3892a9a292b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38667022"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules---preview"></a>Oktatóanyag: Azure-függvény üzembe helyezése IoT Edge-modulként – előzetes verzió

Az Azure Functions használatával olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure-függvény létrehozásán és üzembe helyezésén, amely érzékelőadatokat szűr az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows][lnk-tutorial1-win]vagy [Linux][lnk-tutorial1-lin] rendszeren című oktatóanyagban létrehozott szimulált IoT Edge-eszközön. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:     

> [!div class="checklist"]
> * Azure-függvény létrehozása a Visual Studio Code használatával
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a VS Code és a Docker használatával 
> * A modul üzembe helyezése egy tárolóregisztrációs adatbázisból az IoT Edge-eszközön
> * Szűrt adatok megtekintése

>[!NOTE]
>Az Azure Functions modulok nyilvános [előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhetők el az Azure IoT Edge szolgáltatásban. 

Az ebben az oktatóanyagban létrehozott Azure-függvény szűri az eszköze által létrehozott hőmérsékletadatokat, és csak akkor küld üzeneteket az Azure IoT Hubra, amikor a hőmérséklet egy megadott küszöbérték felett van. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyaghoz létrehozott Functions-modul teszteléséhez egy IoT Edge-eszközre lesz szüksége. Használhatja a [Linux](quickstart-linux.md) vagy a [Windows](quickstart.md) rendszerhez készült rövid útmutatóban konfigurált eszközt.

A fejlesztői gépen a következő előfeltételeknek kell rendelkezésre állnia: 
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code-hoz. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) a fejlesztői gépen. 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban a VS Code-hoz készült Azure IoT Edge bővítménnyel épít fel egy modult és hoz létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Ehhez az oktatóanyaghoz bármilyen Docker-kompatibilis beállításjegyzéket használhat. A felhőben elérhető két népszerű Docker-beállításjegyzékszolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

    ![tárolóregisztrációs adatbázis létrehozása](./media/tutorial-deploy-function/create-container-registry.png)

2. Nevezze el a regisztrációs adatbázist, és válasszon ki egy előfizetést.
3. Azt javasoljuk, hogy az erőforráscsoport esetében használja az IoT Hubot tartalmazó erőforráscsoport nevét. Ha az összes erőforrást egy csoportban tartja, együtt kezelheti azokat. A teszteléshez használt erőforráscsoport törlése például az adott csoportban található összes teszterőforrást is törli. 
4. A termékváltozat beállítása legyen **Alapszintű**, a **Rendszergazdai felhasználó** beállítást pedig állítsa **Engedélyezés** értékre. 
5. Kattintson a **Create** (Létrehozás) gombra.
6. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet. 
7. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni. 

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása
A következő lépések azt mutatják be, hogyan hozhat létre IoT Edge-függvényt a Visual Studio Code és az Azure IoT Edge bővítmény használatával.

1. Nyissa meg a Visual Studio Code-ot.
2. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Integrated Terminal** (Integrált terminál) elemet. 
2. A **View (Nézet)** > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.
3. A parancskatalógusban írja be és futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot, és az utasításokat követve jelentkezzen be Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.
3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 

   1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni. 
   2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
   3. Modulsablonként válassza az **Azure Functions - C#** lehetőséget. 
   4. A modulnak adja a **CSharpFunction** nevet. 
   5. Adja meg az előző szakaszban létrehozott Azure Container Registryt az első modul rendszerképadattáraként. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére. A végső sztring a következőképpen néz ki: **\<registry name\>.azurecr.io/csharpfunction**.

4. A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. Van egy **.vscode** mappa, egy **modules** mappa, egy üzembehelyezési jegyzéksablonfájl és egy **.env** fájl. Nyissa meg a következőt: **modules** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Cserélje le a fájl tartalmát a következő kódra:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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
   ```

6. Mentse a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a CSharpFunction modulhoz, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe, ahonnan a modul rendszerképét leküldheti az Azure Container Registrybe: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Használja azt a felhasználónevet és bejelentkezési kiszolgálót, amelyet az Azure Container Registryből másolt ki korábban. A rendszer a jelszó megadását fogja kérni. Illessze be a jelszót a parancssorba, majd nyomja le az **Enter** billentyűt.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. Ebből a fájlból tudja meg az IoT Edge-futtatókörnyezet, hogy mely modulokat kell üzembe helyezni egy eszközön. További információt az üzembehelyezési jegyzékekről az [IoT Edge-modulok használatát, konfigurálását és újrahasznosítását](module-composition.md) ismertető cikkben olvashat.

3. Keresse meg az üzembehelyezési jegyzék **registryCredentials** szakaszát. Frissítse a **felhasználónév**, **jelszó** és **cím** értékeket a tárolóregisztrációs adatbázisban szereplő hitelesítő adatokra. Ez a szakasz engedélyezi az eszközön lévő IoT Edge-futtatókörnyezetnek, hogy lekérje a privát regisztrációs adatbázisban tárolt tárolórendszerképeket. A tényleges felhasználónév és jelszó párokat az .env fájl tárolja, amelyet a git figyelmen kívül hagy.

5. Mentse el ezt a fájlt.

6. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build IoT Edge solution** (IoT Edge-megoldás összeállítása) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy `deployment.json` fájlt egy új **config** mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a függvényeket, majd leküldi őket a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

## <a name="view-your-container-image"></a>A tárolórendszerkép megtekintése

A Visual Studio Code sikert jelző üzenetet küld, miután leküldte a tárolórendszerképet a tárolóregisztrációs adatbázisba. Ha személyesen is ellenőrizni szeretné a műveletet, keresse meg a rendszerképet az adatbázisban. 

1. Az Azure Portalon navigáljon a saját Azure Container Registry területére. 
2. Válassza az **Adattárak** elemet.
3. A **csharpfunction** elemnek szerepelnie kell az adattárak között. Válassza ki az adattárat a további részletek megtekintéséhez.
4. A **Címkék** szakaszban a következőt kellene látnia: **0.0.1-amd64**. Ez a címke jelzi a létrehozott rendszerkép verziószámát és platformját. Ezek az értékek a CSharpFunction mappa **module.json** fájljában találhatók meg. 

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A Functions-modult az Azure Portal segítségével helyezheti üzembe az IoT Edge-eszközökön a gyors útmutatókban bemutatott módon, de a modulokat a Visual Studio Code használatával is üzembe helyezheti és monitorozhatja. A következő szakaszokban az előfeltételek között felsorolt, VS Code-hoz készült Azure IoT Edge-bővítményt használjuk. Telepítse most, ha korábban nem tette meg. 

1. A **View (Nézet)** > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.

2. Keresse meg, majd futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot. Az utasításokat követve jelentkezzen be Azure-fiókjába. 

3. A parancskatalógusban keresse meg és futtassa az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot. 

4. Keresse meg az IoT Hubot tartalmazó előfizetést, majd válassza ki az IoT Hubot, amelyhez hozzá kíván férni.

5. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

6. Kattintson a jobb gombbal az IoT Edge-eszköze nevére, majd válassza a **Create Deployment for IoT Edge device** (Üzembe helyezés létrehozása IoT Edge-eszközhöz) parancsot. 

7. Navigáljon a CSharpFunction modult tartalmazó megoldásmappához. Nyissa meg a **config** mappát, és válassza ki a **deployment.json** fájlt. Kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre.

8. Frissítse az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. Látható, hogy az új **CSharpFunction** fut a **TempSensor** modul, valamint a **$edgeAgent** és a **$edgeHub** mellett. 

   ![Üzembe helyezett modulok megtekintése a VS Code-ban](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az IoT Hubba érkező összes üzenetet láthatja a parancskatalógus **Azure IoT Hub: Start Monitoring D2C Message** (Azure IoT Hub: D2C üzenetek monitorozásának elindítása) parancsának futtatásával.

Beállíthat egy szűrőt is, hogy csak a megjelölt eszközről az IoT Hubba érkező üzenetek jelenjenek meg. Kattintson a jobb gombbal a kívánt eszközre az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) területen, és válassza a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának elindítása) parancsot.

Az üzenetek monitorozásának leállításához futtassa a parancskatalógus **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: D2C üzenetek monitorozásának leállítása) parancsát. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Távolítsa el az IoT Edge-szolgáltatás futtatókörnyezetét az IoT-eszköz platformjáról (Linux vagy Windows).

#### <a name="windows"></a>Windows

Távolítsa el az IoT Edge-futtatókörnyezetet.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Törölje az eszközön létrehozott tárolókat. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Távolítsa el az IoT Edge-futtatókörnyezetet.

```bash
sudo apt-get remove --purge iotedge
```

Törölje az eszközön létrehozott tárolókat. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Távolítsa el a tároló-futtatókörnyezetet.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó Azure Functions-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [az Azure Functions a Visual Studio Code és az Azure IoT Edge segítségével történő fejlesztését](how-to-develop-csharp-function.md) ismertető cikkben talál. 

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Átlagértékek keresése lebegő Azure Stream Analytics-ablakban](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
