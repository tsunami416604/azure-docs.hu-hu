---
title: "Azure IoT oldala Azure funkció telepítése |} Microsoft Docs"
description: "Azure-függvény peremhálózati eszköz egy modul telepítése"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: bfb37ae51400210ef80a0f267b294d1e2e465b76
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Azure-függvény, egy IoT-Edge-modul telepítése – előzetes
Az Azure Functions, amely megvalósítja az üzleti logikát, közvetlenül az IoT peremhálózati eszköz a kód telepítésére használhatja. Ez az oktatóanyag végigvezeti létrehozása és telepítése az Azure-függvény érzékelőadatait a szimulált IoT peremhálózati eszközön létrehozott tartozó telepítése Azure IoT Edge a szimulált eszköz a szűrő [Windows] [ lnk-tutorial1-win]vagy [Linux] [ lnk-tutorial1-lin] oktatóanyagok. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:     

> [!div class="checklist"]
> * Egy Azure-függvény létrehozása a Visual Studio Code segítségével
> * VS-kód és a Docker segítségével hozzon létre egy Docker-lemezképet, és tegye közzé a beállításjegyzék 
> * A modul az IoT-peremhálózati eszköz telepítése
> * Adatok generált megtekintése


Ez az oktatóanyag során létrehozott Azure-függvény szűrők az eszköz által létrehozott hőmérséklet adatokat és csak üzeneteket küld előtt Azure IoT Hub hőmérséklete esetén egy meghatározott küszöbérték feletti. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure IoT peremhálózati eszköz, amelyet a gyors üzembe helyezés vagy az oktatóanyag előző hozott létre.
* [A Visual Studio Code](https://code.visualstudio.com/). 
* [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Telepítheti a bővítményt a Visual Studio Code bővítmények panelén.)
* [Azure IoT Edge-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Telepítheti a bővítményt a Visual Studio Code bővítmények panelén.)
* [Docker](https://docs.docker.com/engine/installation/). A közösségi Edition (CE) a platformhoz is elegendő ehhez az oktatóanyaghoz. 
* [.NET 2.0 SDK alapvető](https://www.microsoft.com/net/core#windowscmd). 

## <a name="set-up-a-docker-registry"></a>A Docker beállításjegyzék beállítása
Ebben az oktatóanyagban egy Azure-függvény létrehozása és használhatja a Visual STUDIO Code Azure IoT peremhálózati kiterjesztését egy [Docker kép](https://docs.docker.com/glossary/?term=image) vele. Ezután Docker rendszerkép leküldéses egy [Docker-tárház](https://docs.docker.com/glossary/?term=repository) által üzemeltetett egy [Docker beállításjegyzék](https://docs.docker.com/glossary/?term=registry). Végezetül a csomagolt Docker-lemezkép központi telepítése egy [Docker-tároló](https://docs.docker.com/glossary/?term=container) az IoT-peremhálózati eszközön a beállításjegyzékből.  

Ebben az oktatóanyagban a Docker-kompatibilis beállításjegyzék is használhatja. Két népszerű Docker beállításjegyzék szolgáltatások érhető el a felhőben vannak **Azure tároló beállításjegyzék** és **Docker Hub**:

- [Az Azure tároló beállításjegyzék](https://docs.microsoft.com/en-us/azure/container-registry/) érhető el egy [előfizetéssel](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Ebben az oktatóanyagban a **alapvető** előfizetés is elegendő. 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) egy szabad titkos tárházat biztosít, ha létrehoz egy (ingyenes) Docker-azonosító. 
    1. Létrehoz egy Docker-Azonosítót, kövesse az utasításokat a [egy Docker-Azonosítót regisztrálni](https://docs.docker.com/docker-id/#register-for-a-docker-id) a Docker helyen. 

    2. Hozzon létre egy saját Docker-tárházat, kövesse a [Docker hub új-tárház létrehozása](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) a Docker helyen.

Ez az oktatóanyag teljes adott esetben parancsok vannak megadva a Azure tároló beállításjegyzék és a Docker központ.

## <a name="create-a-function-project"></a>Függvény-projekt létrehozása
A következő lépések bemutatják egy Visual Studio Code és az Azure IoT peremhálózati bővítmény IoT Edge-függvény létrehozása.
1. Megnyitás Visual STUDIO Code.
2. Használja a **nézet |} Terminálszolgáltatások integrált** nyissa meg a Visual STUDIO Code integrált terminál parancsra.
3. Az integrált terminált, adja meg a következő parancs futtatásával telepítse (vagy frissítse) a **AzureIoTEdgeFunction** dotnet-sablon:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Az integrált terminált adja meg az új modul projekt létrehozása a következő parancsot:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > Ezzel a paranccsal létrejön a projektmappa **FilterFunction**, az aktuális munkakönyvtárban. Ha azt szeretné, egy másik helyen létrehozásához, módosítsa a könyvtárat a parancs futtatása előtt.

3. Használja a **fájl |} Nyissa meg a mappa** parancs, keresse meg a **FilterFunction** mappára, majd kattintson **Mappaválasztás** megnyitni a projektet a Visual STUDIO Code.
4. A Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson a **EdgeHubTrigger-c Sharp** mappát, majd kattintson a **run.csx** fájlra való megnyitásához.
5. Adja hozzá a következő utasítás után a `#r "Microsoft.Azure.Devices.Client"` utasítást:

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. Adja hozzá a következő using utasításokat a meglévő után `using` utasításokat:

    ```csharp
    using Newtonsoft.Json;
    ```

1. A következő osztályok hozzáadása. Ezeket az osztályokat határozza meg a várt séma a bejövő üzenet törzsét.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
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

1. Cserélje le a választörzs a **futtatása** metódus a következő kóddal. Üzenetek a hőmérséklet-küszöbérték és az üzenet törzsét hőmérséklet értéke alapján szűri.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. Mentse a fájlt.

## <a name="publish-a-docker-image"></a>A Docker-lemezkép közzététele

1. Összeállíthatja a Docker-lemezképét.
    1. A Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson a **Docker** mappára a megnyitásához. Válassza ki a mappát, a tároló platform vagy **linux-x64** vagy **windows-nano**. 
    2. Kattintson a jobb gombbal a **Dockerfile** fájlt, és kattintson a **Build IoT peremhálózati modul Docker kép**. 
    3. Az a **Mappaválasztás** navigáljon a projektmappa **FilterFunction**, és kattintson a **EXE_DIR mappában válassza ki**. 
    4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például `<docker registry address>/filterfunction:latest`; ahol *docker beállításjegyzék cím* Docker Hub használata a rendszer a Docker-Azonosítóját, vagy hasonló `<your registry name>.azurecr.io`, Azure tároló beállításjegyzék használata.
 
4. Jelentkezzen be a Docker. Integrált terminált adja meg a következő parancsot: 

    - Docker Hub (adja meg a hitelesítő adatokat):
        
        ```csh/sh
        docker login
        ```

    - Azure-tárolót beállításjegyzék:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        A felhasználónév, jelszó és a bejelentkezési kiszolgáló, a parancs használatával megkereséséhez nyissa meg az [Azure portálra] (https://portal.azure.com). A **összes erőforrás**, kattintson az Azure-tárolót beállításjegyzék nyissa meg a tulajdonságait, majd kattintson a csempére **hívóbetűk**. Az értékek másolása a **felhasználónév**, **jelszó**, és **bejelentkezési kiszolgáló** mezőket. A bejelentkezési kiszolgáló sould a kötelező forma: `<your registry name>.azurecr.io`.

3. A Docker-tárház küldje le a lemezképet. Használja a **nézet |} Paletta... parancs |} Peremhálózati: Leküldéses IoT peremhálózati modul Docker kép** menü parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Használja a kép néven lépésben használt 1.c.

## <a name="add-registry-credentials-to-your-edge-device"></a>Adja hozzá a peremhálózati eszköz beállításjegyzék hitelesítő adatokat
A peremhálózati futásidejű a peremhálózati eszköz futtató számítógépen adja hozzá a rendszerleíró adatbázis hitelesítő adatait. Ezáltal a tároló lekéréses futásidejű elérésére. 

- A Windows a következő parancsot:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- A Linux a következő parancsot:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Futtassa a megoldás

1. Az a **Azure-portálon**, keresse meg az IoT hub.
2. Ugrás a **IoT peremhálózati (előzetes verzió)** és az IoT-peremhálózati eszköz kiválasztásához.
1. Válassza ki **modulok beállítása**. 
2. Adja hozzá a **tempSensor** modul. Ez a lépés csak akkor szükséges, ha korábban még nem telepítette a **tempSensor** modul az IoT-peremhálózati eszközön.
    1. Válassza ki **IoT peremhálózati modul hozzá lesz adva**.
    2. Az a **neve** mezőbe írja be `tempSensor`.
    3. Az a **lemezkép URI** mezőbe írja be `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul az egyéb beállításokat, és kattintson **mentése**.
1. Adja hozzá a **filterfunction** modul.
    1. Válassza ki **IoT peremhálózati modul hozzáadása** újra.
    2. Az a **neve** mezőbe írja be `filterfunction`.
    3. Az a **kép** mezőbe írja be a kép címét, például `<docker registry address>/filterfunction:latest`.
    74. Kattintson a **Save** (Mentés) gombra.
2. Kattintson a **Tovább** gombra.
3. Az a **útvonalak megadása** . lépés:, másolja az alábbi JSON a szövegmezőbe. Modulok összes üzenetet a peremhálózati futásidejű tegye közzé. A futásidejű deklaratív szabályok határozzák meg, ahol az üzenetek áramlását. Ebben az oktatóanyagban kell két útvonalak. Az első útvonal szállítja a keresztül a "input1" végpontot, amely a konfigurált végpont modulja a hőmérséklet-érzékelő üzenetek a **FilterMessages** kezelő. A második útvonal szállításokkal a modul az IoT hubhoz üzeneteit. Ez az útvonal `upstream` egy különös cél, amely közli a peremhálózati Hub üzeneteket küldhet az IoT-központ. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Kattintson a **Tovább** gombra.
5. Az a **felülvizsgálati sablonja** lépésre, a **Submit**. 
6. Térjen vissza az IoT-peremhálózati eszköz részleteit megjelenítő oldalra, és kattintson a **frissítése**. Láthatja, hogy az új **filterfunction** modul fut, valamint a **tempSensor** modul és a **IoT peremhálózati futásidejű**. 

## <a name="view-generated-data"></a>Adatok generált megtekintése

A felhőbe küldött üzeneteket küld az IoT hub az IoT-peremhálózati eszköz eszköz figyelésére:
1. Az Azure IoT eszközkészlet mező konfigurálására a kapcsolati karakterlánc az IoT hub: 
    1. Használja a **nézet |} Explorer** menüparancs a Visual STUDIO Code explorer megnyitásához. 
    3. Kattintson a explorer **IOT HUB-ESZKÖZÖKNEK** majd **...** . Kattintson a **IoT Hub kapcsolati karakterlánc beállítása** és az IoT hub, amely az IoT-peremhálózati eszköz csatlakozik az előugró ablakban adja meg a kapcsolati karakterláncot. 

        Keresse meg a kapcsolati karakterláncot, kattintson a csempére az IoT hub, az Azure portálon, és kattintson a **megosztott elérési házirendek**. A **megosztott elérési házirendek**, kattintson a **iothubowner** házirend, és másolja az IoT-központ kapcsolati karakterlánc a **iothubowner** ablak.   

1. Az IoT hub érkező adatok figyeléséről az **nézet |} Paletta... parancs |} IoT: Figyelni D2C üzenet** menüparancshoz. 
2. Az adatok figyelésének leállításához, használja a **nézet |} Paletta... parancs |} IoT: Állítsa le a figyelő D2C üzenete** menüparancshoz. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure-függvény szűrése a nyers adatokat az IoT-peremhálózati eszköz által generált kódot tartalmazó létrehozott. Tartsa felfedezése Azure IoT él, ismerje meg az átjáróként IoT peremhálózati eszköz használatával. 

> [!div class="nextstepaction"]
> [Az IoT-peremhálózati átjáró eszköz létrehozása](how-to-create-gateway-device.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
