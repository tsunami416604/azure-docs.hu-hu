---
title: Adattárolás az Azure IoT Edge SQL-moduljával | Microsoft Docs
description: Megtudhatja, hogyan történik az adatok egy SQL Server-modul használatával történő helyi tárolása az IoT Edge-eszközön
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d0837787dcac44d2cc43701ac181ec7eac2dfa2c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687215"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Oktatóanyag: Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal

Az Azure IoT Edge és az SQL Server segítségével adatokat tárolhat és kérdezhet le a peremhálózaton. Az Azure IoT Edge beépített, alapszintű tárolási szolgáltatásokkal rendelkezik, amelyek elvégzik az üzenetek gyorsítótárazását, ha az eszköz offline állapotba kerül, majd a kapcsolat helyreállását követően továbbítják azokat. Szüksége lehet azonban ennél fejlettebb tárolási képességekre is, például az adatok helyi lekérdezéséhez. Helyi adatbázisok használatával az IoT Edge-eszközök összetettebb számítási tevékenységek végrehajtására képesek anélkül, hogy ehhez állandó IoT Hub-kapcsolatra lenne szükségük. A helyszíni technikusok például akkor is helyben megtekinthetik az érzékelők által az utóbbi napokban gyűjtött adatokat, ha azokat a rendszer csak havonta egyszer tölti fel a felhőbe a Machine Learning-modell javítása céljából.

A jelen cikk az SQL Server-adatbázisok IoT Edge-eszközön történő üzembe helyezésének utasításait tartalmazza. Az IoT Edge-eszközön futó Azure Functions-függvények elvégzik a bejövő adatok rendszerezését, majd elküldik azokat az adatbázisnak. A cikkben szereplő lépések a tárolókban üzemeltetett egyéb adatbázisokra (például MySQL vagy PostgreSQL) is alkalmazhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Azure-függvény létrehozása a Visual Studio Code használatával
> * SQL-adatbázis üzembe helyezése az IoT Edge-eszközön
> * A Visual Studio Code használatával modulokat hozhat létre és helyezhet üzembe az IoT Edge-eszközön
> * A létrejött adatok megtekintése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [Linux](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójában létrehozott Azure IoT Edge-eszköz.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
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

Ahhoz, hogy adatokat küldhessen egy adatbázisba, egy olyan modulra van szüksége, amely képes megfelelően rendszerezni, majd egy táblába elmenteni az adatokat. 

A következő lépések azt mutatják be, hogyan hozhat létre IoT Edge-függvényt a Visual Studio Code és az Azure IoT Edge bővítmény használatával.

1. Nyissa meg a Visual Studio Code-ot.
2. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Integrated Terminal** (Integrált terminál) elemet.
3. A **View (Nézet)** > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.
4. A parancskatalógusban írja be és futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot, és az utasításokat követve jelentkezzen be Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.
3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 
   1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni. 
   2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
   3. Modulsablonként válassza az **Azure Functions - C#** lehetőséget. 
   4. A modulnak adja az **sqlFunction** nevet. 
   5. Adja meg az előző szakaszban létrehozott Azure Container Registryt az első modul rendszerképadattáraként. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére. A sztring végső változata a következőképpen néz ki: **\<regisztrációs adatbázis neve\>.azurecr.io/sqlFunction**.

4. A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. A munkaterületen egy **modules** és egy **.vscode** mappa, valamint egy üzembehelyezési jegyzéksablonfájl található. Nyissa meg a **modules** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx** fájlt.

5. Cserélje le a fájl tartalmát a következő kódra:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

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

           //Store the data in SQL db
           const string str = "<sql connection string>";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
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

6. A 24. sorban cserélje le az **\<sql connection string\>** (SQL-kapcsolati sztring) értékét az alábbi sztringre. A **Data Source** (Adatforrás) tulajdonság az SQL Server-tároló nevére hivatkozik, amelyet **SQL** néven fog létrehozni a következő szakaszban. 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Mentse a **run.csx** fájlt. 

## <a name="add-a-sql-server-container"></a>SQL Server-tároló hozzáadása

Az IoT Edge-futtatókörnyezet által az IoT Edge-eszközön telepítendő modulokat az [üzembehelyezési jegyzékfájl](module-composition.md) határozza meg. Az előző szakaszban hozzáadta az egyéni Functions-modul létrehozásához tartozó kódot, a rendszer azonban már létrehozta az SQL Server-modult. Utasítsa az IoT Edge-futtatókörnyezetet ennek belefoglalására, majd végezze el a konfigurálást az eszközön. 

1. A Visual Studio Code Explorerben nyissa meg a **deployment.template.json** fájlt. 
2. Keresse meg a **moduleContent.$edgeAgent.properties.desired.modules** szakaszt. A listában a következő két modulnak kell szerepelnie: a szimulált adatokat előállító **tempSensor** modulnak és a saját **sqlFunction** moduljának.
3. Adja hozzá a következő kódot a harmadik modul deklarálásához:

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "settings": {
           "image": "",
           "createOptions": ""
       }
   }
   ```

4. Az IoT Edge-eszköz operációs rendszerétől függően az alábbi kóddal frissítse az **sql.settings** paramétereit:

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >Ha éles környezetben hoz létre SQL Server-tárolót, minden esetben [módosítsa az alapértelmezett rendszergazdai jelszót](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

5. Mentse a **deployment.template.json** fájlt. 

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszokban egyetlen modullal hozott létre megoldást, majd hozzáadott egy másik modult az üzembehelyezési jegyzéksablonfájlhoz. Most létre kell hoznia a megoldást és a modulok tárolórendszerképeit, majd le kell küldenie a rendszerképeket a tárolóregisztrációs adatbázisba. 

1. A deployment.template.json fájlban adja meg az IoT Edge-futtatókörnyezet számára a regisztrációs adatbázis hitelesítő adatait, hogy az hozzáférhessen a modulrendszerképekhez. Keresse meg a **moduleContent.$edgeAgent.properties.desired.runtime.settings** szakaszt. 
2. Szúrja be a következő JSON-kódot a **loggingOptions** szakasz után:

   ```JSON
   "registryCredentials": {
       "myRegistry": {
           "username": "",
           "password": "",
           "address": ""
       }
   }
   ```

3. Írja be a regisztrációs adatbázis hitelesítő adatait a **username**, **password** és **address** mezőbe. Használja azokat az értékeket, amelyeket az Azure-beli tárolóregisztrációs adatbázis létrehozásakor másolt ki, az oktatóanyag első lépésében.
4. Mentse a **deployment.template.json** fájlt.
5. Jelentkezzen be a tárolóregisztrációs adatbázisba a Visual Studio Code felületén, így le tudja küldeni a rendszerképeket a regisztrációs adatbázisba. Használja az üzembehelyezési jegyzékfájlhoz az imént hozzáadott hitelesítő adatokat. Az integrált terminálon írja be a következő parancsot: 

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    A rendszer a jelszó megadását fogja kérni. Illessze be a jelszót a parancssorba, majd nyomja le az **Enter** billentyűt.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

6. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build IoT Edge solution** (IoT Edge-megoldás összeállítása) lehetőséget. 

## <a name="deploy-the-solution-to-a-device"></a>A megoldás üzembe helyezése egy eszközön

Az IoT Hub felületén keresztül modulokat állíthat be egy eszközön, de az IoT Hubhoz és az eszközökhöz a Visual Studio Code felületén keresztül is hozzáférhet. Ebben a szakaszban az IoT Hubhoz való hozzáférést fogja beállítani, majd a VS Code használatával üzembe fogja helyezni a megoldást az IoT Edge-eszközön. 

1. A VS Code parancskatalógusában válassza ki az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot.
2. Az utasításokat követve jelentkezzen be Azure-fiókjába. 
3. A parancskatalógusban válassza ki saját Azure-előfizetését, majd IoT Hubját. 
4. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 
5. Kattintson a jobb gombbal az üzembe helyezés céleszközére, majd válassza a **Create deployment for IoT Edge device** (Üzemelő példány létrehozása IoT Edge-eszközhöz) lehetőséget. 
6. A fájlkezelőben keresse meg a megoldás **config** mappáját, és jelölje ki a **deployment.json** fájlt. Kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. 

Ha a telepítés sikeres, a VS Code egy üzenetben erősíti meg azt a kimenetben. Azt is ellenőrizheti, hogy üzemel-e az összes modul az eszközön. 

Futtassa az alábbi parancsot az IoT Edge-eszközön a modulok állapotának megtekintéséhez. Ennek futtatása eltarthat néhány percig.

   ```bash
   sudo iotedge list
   ```

## <a name="create-the-sql-database"></a>Az SQL-adatbázis létrehozása

Ha alkalmazza az üzembehelyezési jegyzékfájlt az eszközön, akkor három futó modulja lesz. A tempSensor modul szimulált környezeti adatokat állít elő. Az sqlFunction modul az adatbázis számára megfelelő formátumba konvertálja az adatokat. 

Ez a szakasz az SQL-adatbázis beállítását mutatja be a hőmérsékletadatok mentéséhez. 

1. Szintén egy parancssorból létesítsen kapcsolatot az adatbázissal. 
   * Windows-tárolók:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Linux-tárolók: 

      ```bash
      docker exec -it sql bash
      ```

2. Nyissa meg az SQL-parancssori eszközt.
   * Windows-tárolók:

      ```cmd
      sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

   * Linux-tárolók: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Password'
      ```

3. Adatbázis létrehozása: 

   * Windows-tárolók
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Linux-tárolók
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Végezze el a tábla definiálását.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Az SQL Server Docker-fájljának testreszabásával megadhatja, hogy a rendszer automatikusan elvégezze az SQL Server üzembe helyezését több IoT Edge-eszközön. További információkért lásd a [Microsoft SQL Server-tároló demóprojektjét](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>A helyi adatok megtekintése

Ha a tábla létrejött, az sqlFunction modul elkezdi menteni az adatokat az IoT Edge-eszköz helyi SQL Server 2017-adatbázisába. 

Futtassa a következő parancsot az SQL-parancssori eszközből a formázott tábla adatainak megtekintéséhez: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![A helyi adatok megtekintése](./media/tutorial-store-data-sql-server/view-data.png)



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
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor")
```

#### <a name="linux"></a>Linux

Távolítsa el az IoT Edge-futtatókörnyezetet.

```bash
sudo apt-get remove --purge iotedge
```

Törölje az eszközön létrehozott tárolókat. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor")
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
