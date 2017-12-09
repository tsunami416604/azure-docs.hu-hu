---
title: "Azure IoT oldala Azure funkció telepítése |} Microsoft Docs"
description: "Azure-függvény peremhálózati eszköz egy modul telepítése"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1dfe46d307a076ae02362c4bba292602001ed915
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
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
* [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Azure IoT Edge-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). A közösségi Edition (CE) a platformhoz is elegendő ehhez az oktatóanyaghoz. 
* [.NET 2.0 SDK alapvető](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban használhatja a Visual STUDIO Code Azure IoT peremhálózati kiterjesztését olyan modul létrehozása, és hozzon létre egy **tároló kép** a fájlokból. Ezzel a lemezképpel leküldéses, majd egy **beállításjegyzék** , amely tárolja, és a képek kezeli. Végezetül telepít a lemezképet a beállításjegyzékből, futtassa az IoT-peremhálózati eszközön.  

Ebben az oktatóanyagban a Docker-kompatibilis beállításjegyzék is használhatja. Két népszerű Docker beállításjegyzék szolgáltatások érhető el a felhőben vannak [Azure tároló beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) és [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure-tároló beállításjegyzék használja. 

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **hozzon létre egy erőforrást** > **tárolók** > **Azure tároló beállításjegyzék** .
2. Nevezze el a beállításjegyzékben, válasszon egy előfizetést, válasszon egy erőforráscsoportot és beállítása a Termékváltozat **alapvető**. 
3. Kattintson a **Létrehozás** gombra.
4. A tároló beállításkulcs létrehozása után keresse meg a fájlt, és válassza ki **hívóbetűk**. 
5. Váltás **rendszergazdai jogú felhasználó** való **engedélyezése**.
6. Másolja a **bejelentkezési kiszolgáló**, **felhasználónév**, és **jelszó**. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni. 

## <a name="create-a-function-project"></a>Függvény-projekt létrehozása
A következő lépések bemutatják egy Visual Studio Code és az Azure IoT peremhálózati bővítmény IoT Edge-függvény létrehozása.
1. Nyissa meg a Visual Studio Code.
2. A Visual STUDIO Code integrált terminál megnyitásához, jelölje be **nézet** > **integrált terminál**.
3. Telepíteni (vagy frissítse) a **AzureIoTEdgeFunction** dotnet, a következő parancsot a integrált terminál-sablon:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Új modul projekt létrehozása. A következő parancs létrehozza a projektmappa **FilterFunction**, az aktuális munkakönyvtárban:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

3. Válassza ki **fájl** > **nyissa meg a mappa**, majd keresse meg a **FilterFunction** mappa, és nyissa meg a projektet a Visual STUDIO Code.
4. A Visual STUDIO Code Explorerben bontsa ki a **EdgeHubTrigger-c Sharp** mappát, majd nyissa meg a **run.csx** fájlt.
5. Cserélje le a fájl tartalmát az alábbira:

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
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
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

11. Mentse a fájlt.

## <a name="publish-a-docker-image"></a>A Docker-lemezkép közzététele

1. Összeállíthatja a Docker-lemezképét.
    1. A Visual STUDIO Code Explorerben bontsa ki a **Docker** mappa. Majd bontsa ki a mappát, a tároló platform vagy **linux-x64** vagy **windows-nano**. 
    2. Kattintson a jobb gombbal a **Dockerfile** fájlt, és kattintson a **Build IoT peremhálózati modul Docker kép**. 
    3. Keresse meg a **FilterFunction** projektmappa, és kattintson **EXE_DIR mappában válassza ki**. 
    4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filterfunction:latest`. A tároló beállításjegyzék cím megegyezik a bejelentkezési kiszolgáló másolt a beállításjegyzékből. Meg kell formájában `<your container registry name>.azurecr.io`.
 
4. Jelentkezzen be a Docker. Az integrált terminált adja meg a következő parancsot: 

   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   A felhasználónév, jelszó és a bejelentkezési kiszolgáló, a parancs használatával megkereséséhez nyissa meg az [Azure portálra] (https://portal.azure.com). A **összes erőforrás**, kattintson az Azure-tárolót beállításjegyzék nyissa meg a tulajdonságait, majd kattintson a csempére **hívóbetűk**. Az értékek másolása a **felhasználónév**, **jelszó**, és **bejelentkezési kiszolgáló** mezőket. 

3. A Docker-tárház küldje le a lemezképet. Válassza ki **nézet** > **paletta parancs...**  majd keresse meg a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép**.
4. Az előugró mezőbe írjon be lépésben használt kép néven 1.d.

## <a name="add-registry-credentials-to-your-edge-device"></a>Adja hozzá a peremhálózati eszköz beállításjegyzék hitelesítő adatokat
A peremhálózati futásidejű a peremhálózati eszköz futtató számítógépen adja hozzá a rendszerleíró adatbázis hitelesítő adatait. Ezáltal a tároló lekéréses futásidejű elérésére. 

- A Windows a következő parancsot:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- A Linux a következő parancsot:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Futtassa a megoldás

1. Az a **Azure-portálon**, keresse meg az IoT hub.
2. Ugrás a **IoT peremhálózati (előzetes verzió)** és az IoT-peremhálózati eszköz kiválasztásához.
1. Válassza ki **modulok beállítása**. 
2. Ha már telepítette a **tempSensor** modul erre az eszközre, akkor előfordulhat, hogy automatikusan kitöltődnek. Ha nem, kövesse az alábbi lépéseket veheti fel:
    1. Válassza ki **IoT peremhálózati modul hozzá lesz adva**.
    2. Az a **neve** mezőbe írja be `tempSensor`.
    3. Az a **lemezkép URI** mezőbe írja be `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul az egyéb beállításokat, és kattintson **mentése**.
1. Adja hozzá a **filterFunction** modul.
    1. Válassza ki **IoT peremhálózati modul hozzáadása** újra.
    2. Az a **neve** mezőbe írja be `filterFunction`.
    3. Az a **kép** mezőbe írja be a kép címét, például `<docker registry address>/filterfunction:latest`.
    74. Kattintson a **Save** (Mentés) gombra.
2. Kattintson a **Tovább** gombra.
3. Az a **útvonalak megadása** . lépés:, másolja az alábbi JSON a szövegmezőbe. Az első útvonal szállításokkal üzenetek a keresztül a "input1" végpont modulja a hőmérséklet-érzékelő. A második útvonal szállításokkal a modul az IoT hubhoz üzeneteit. Ez az útvonal `$upstream` egy különös cél, amely közli a peremhálózati Hub üzeneteket küldhet az IoT-központ. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Kattintson a **Tovább** gombra.
5. Az a **felülvizsgálati sablonja** lépésre, a **Submit**. 
6. Térjen vissza az IoT-peremhálózati eszköz részleteit megjelenítő oldalra, és kattintson a **frissítése**. Láthatja, hogy az új **filterfunction** modul fut, valamint a **tempSensor** modul és a **IoT peremhálózati futásidejű**. 

## <a name="view-generated-data"></a>Adatok generált megtekintése

A felhőbe küldött üzeneteket küld az IoT hub az IoT-peremhálózati eszköz eszköz figyelésére:
1. Az Azure IoT eszközkészlet mező konfigurálására a kapcsolati karakterlánc az IoT hub: 
    1. Az Azure-portálon az IoT hub keresse meg és jelölje ki **megosztott elérési házirendek**. 
    2. Válassza ki **iothubowner** értékének másolja **kapcsolati karakterlánc elsődleges kulcs**.
    1. Kattintson a Visual STUDIO Code explorer **IOT HUB-ESZKÖZÖKNEK** majd **...** . 
    1. Válassza ki **IoT Hub kapcsolati karakterlánc beállítása** és az előugró ablakban írja be az Iot-központ kapcsolati karakterláncot. 

1. Az IoT hub érkező adatok figyelésére, válassza ki a **nézet** > **parancs paletta...**  keresse meg a **IoT: D2C üzenet figyelni**. 
2. Az adatok figyelésének leállításához, használja a **IoT: D2C üzenet figyelés leállításának** a parancs paletta parancsot. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure-függvény szűrése a nyers adatokat az IoT-peremhálózati eszköz által generált kódot tartalmazó létrehozott. Tartsa felfedezése Azure IoT él, ismerje meg az átjáróként IoT peremhálózati eszköz használatával. 

> [!div class="nextstepaction"]
> [Az IoT-peremhálózati átjáró eszköz létrehozása](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
