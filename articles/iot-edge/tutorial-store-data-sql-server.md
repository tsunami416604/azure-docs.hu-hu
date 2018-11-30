---
title: Adattárolás az Azure IoT Edge SQL-moduljával | Microsoft Docs
description: Megtudhatja, hogyan történik az adatok egy SQL Server-modul használatával történő helyi tárolása az IoT Edge-eszközön
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 95041ca77930d87bff6ea31e2eab89a6634cfcf5
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442964"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Oktatóanyag: Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal

Az Azure IoT Edge és az SQL Server segítségével adatokat tárolhat és kérdezhet le a peremhálózaton. Az Azure IoT Edge rendelkezik alapszintű tárolási képességeinek gyorsítótárazzák az üzeneteket, ha egy eszköz offline állapotba kerül, és majd továbbítják őket, amikor a kapcsolat helyreállt. Szüksége lehet azonban ennél fejlettebb tárolási képességekre is, például az adatok helyi lekérdezéséhez. Helyi adatbázisok használatával az IoT Edge-eszközök összetettebb számítási tevékenységek végrehajtására képesek anélkül, hogy ehhez állandó IoT Hub-kapcsolatra lenne szükségük. Például egy gépen érzékelő tölt fel adatokat a felhőbe a jelentéskészítés és a egy machine learning modul javítása havonta egyszer. Ha a gépen egy technikusnak működik, hozzáférhet az elmúlt néhány napban helyileg az érzékelők adatstreamének felhasználásával.

A jelen cikk az SQL Server-adatbázisok IoT Edge-eszközön történő üzembe helyezésének utasításait tartalmazza. Az IoT Edge-eszközön futó Azure Functions-függvények elvégzik a bejövő adatok rendszerezését, majd elküldik azokat az adatbázisnak. A cikkben szereplő lépések a tárolókban üzemeltetett egyéb adatbázisokra (például MySQL vagy PostgreSQL) is alkalmazhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Azure-függvény létrehozása a Visual Studio Code használatával
> * SQL-adatbázis üzembe helyezése az IoT Edge-eszközön
> * A Visual Studio Code használatával modulokat hozhat létre és helyezhet üzembe az IoT Edge-eszközön
> * A létrejött adatok megtekintése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [Linux-](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójának lépéseit követve.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C#(szolgáltató: omnisharp) Visual Studio Code-bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben az oktatóanyagban használhatja az Azure IoT Edge-bővítmény a Visual Studio Code fel egy modult, és hozzon létre egy **tárolórendszerkép** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

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

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Ahhoz, hogy adatokat küldhessen egy adatbázisba, egy olyan modulra van szüksége, amely képes megfelelően rendszerezni, majd egy táblába elmenteni az adatokat. 

A következő lépések azt mutatják be, hogyan hozhat létre IoT Edge-függvényt a Visual Studio Code és az Azure IoT Edge bővítmény használatával.

1. Nyissa meg a Visual Studio Code-ot.

2. A **View (Nézet)** > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, például **SqlSolution**, vagy fogadja el az alapértelmezett. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **az Azure Functions - C#** . |
   | Provide a module name (Modulnév megadása) | A modulnak adja az **sqlFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. Néz ki a végső karakterláncban \<beállításjegyzék neve\>.azurecr.io/sqlFunction. |

   A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét: egy \.vscode mappát, egy modules mappát, egy üzembe helyezési jegyzéksablonfájlt. és egy \.env fájlt. 
   
4. Minden alkalommal, amikor létrehoz egy új IoT Edge-megoldást, a VS Code megadását kéri a beállításjegyzék hitelesítő adatait a \.env fájlt. Ezt a fájlt a git-figyelmen kívül hagyja, és az IoT Edge-bővítmény segítségével később az IoT Edge-eszköz beállításjegyzék hozzáférést biztosítanak. Nyissa meg a \.env fájlt. 

5. Az .env fájlban adja meg az IoT Edge-futtatókörnyezet számára a regisztrációs adatbázis hitelesítő adatait, hogy az hozzáférhessen a modulrendszerképekhez. Keresse meg a **CONTAINER_REGISTRY_USERNAME** és a **CONTAINER_REGISTRY_PASSWORD** szakaszt, és szúrja be a hitelesítő adatait az egyenlőségjel után: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. Mentse az .env fájlt.

7. A VS Code Explorerben nyissa meg a **modulok** > **sqlFunction** > **sqlFunction.cs**.

8. Cserélje le a fájl tartalmát a következő kódra:

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
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
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

6. 35 sorban cserélje le a karakterlánc **\<sql-kapcsolati sztring\>** a következő karakterlánccal. A **adatforrás** tulajdonság hivatkozik, az SQL Server tároló név, amely hoz létre a nevű **SQL** a következő szakaszban. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Mentse a **sqlFunction.cs** fájlt. 

## <a name="add-a-sql-server-container"></a>SQL Server-tároló hozzáadása

Az IoT Edge-futtatókörnyezet által az IoT Edge-eszközön telepítendő modulokat az [üzembehelyezési jegyzékfájl](module-composition.md) határozza meg. A kód egy egyéni függvénymodul az előző szakaszban győződjön meg arról, hogy a megadott, de az SQL Server-modult már épül. Utasítsa az IoT Edge-futtatókörnyezetet ennek belefoglalására, majd végezze el a konfigurálást az eszközön. 

1. A Visual Studio Code Explorerben nyissa meg a **deployment.template.json** fájlt. 

2. Keresse meg a **modulok** szakaszban. A listában a következő két modulnak kell szerepelnie: a szimulált adatokat előállító **tempSensor** modulnak és a saját **sqlFunction** moduljának.

3. Ha Windows-tárolókat használ, módosítsa az **sqlFunction.settings.image** szakaszt.

   ```json
   "image": "${MODULES.sqlFunction.windows-amd64}"
   ```

4. Adja hozzá a következő kódot egy harmadik modul deklarálásához. Adjon hozzá egy vesszőt az sqlFunction szakasz után, és szúrja be a következőt:

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "env":{},
       "settings": {
           "image": "",
           "createOptions": ""
       }
   }
   ```

   ![SQL Server-tároló hozzáadása](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. Az IoT Edge-eszközt a Docker-tárolók típusától függően frissíteni a **sql** modul paraméterei, az alábbi kódra:
   * Windows-tárolók:

      ```json
      "env": {
         "ACCEPT_EULA": {"value": "Y"},
         "SA_PASSWORD": {"value": "Strong!Passw0rd"}
       },
       "settings": {
          "image": "microsoft/mssql-server-windows-developer",
          "createOptions": {
              "HostConfig": {
                  "Mounts": [{"Target": "C:\\\\mssql","Source": "sqlVolume","Type": "volume"}],
                  "PortBindings": {
                      "1433/tcp": [{"HostPort": "1401"}]
                  }
              }
          }
      }
      ```

   * Linux-tárolók:

      ```json
      "env": {
         "ACCEPT_EULA": {"value": "Y"},
         "SA_PASSWORD": {"value": "Strong!Passw0rd"}
       },
       "settings": {
          "image": "mcr.microsoft.com/mssql/server:latest",
          "createOptions": {
              "HostConfig": {
                  "Mounts": [{"Target": "/var/opt/mssql","Source": "sqlVolume","Type": "volume"}],
                  "PortBindings": {
                      "1433/tcp": [{"HostPort": "1401"}]
                  }
              }
          }
      }
      ```

   >[!Tip]
   >Ha éles környezetben hoz létre SQL Server-tárolót, minden esetben [módosítsa az alapértelmezett rendszergazdai jelszót](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Mentse a **deployment.template.json** fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszokban egyetlen modullal hozott létre megoldást, majd hozzáadott egy másik modult az üzembehelyezési jegyzéksablonfájlhoz. Most létre kell hoznia a megoldást és a modulok tárolórendszerképeit, majd le kell küldenie a rendszerképeket a tárolóregisztrációs adatbázisba. 

1. Jelentkezzen be a tárolóregisztrációs adatbázisba a Visual Studio Code felületén, hogy le tudja küldeni a rendszerképeket a regisztrációs adatbázisba. Használja ugyanazokat a hitelesítő adatokat, mint amelyeket az .env fájlhoz hozzáadott. Az integrált terminálon írja be a következő parancsot:

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    
    A jelszó megadását kéri. Illessze be a parancssort (a biztonság, jelszó rejtett), majd nyomja le a jelszó **Enter**. 

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy deployment.json nevű fájlt egy új **config** nevű mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. Ez a két parancs a kód felépítéséhez, a modul tárolóba, és majd továbbítsa a kód a tárolóregisztrációs adatbázisba, a megoldás inicializálásakor megadott. 

## <a name="deploy-the-solution-to-a-device"></a>A megoldás üzembe helyezése egy eszközön

Az IoT Hub felületén keresztül modulokat állíthat be egy eszközön, de az IoT Hubhoz és az eszközökhöz a Visual Studio Code felületén keresztül is hozzáférhet. Ebben a szakaszban az IoT Hubhoz való hozzáférést fogja beállítani, majd a VS Code használatával üzembe fogja helyezni a megoldást az IoT Edge-eszközön. 

1. A VS Code parancskatalógusában válassza ki az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot.

2. Az utasításokat követve jelentkezzen be Azure-fiókjába. 

3. A parancskatalógusban válassza ki saját Azure-előfizetését, majd IoT Hubját. 

4. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

5. Kattintson a jobb gombbal az üzembe helyezés céleszközére, majd válassza a **Create deployment for a single device** (Üzemelő példány létrehozása egyetlen eszközhöz) lehetőséget. 

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-store-data-sql-server/create-deployment.png)

6. A fájlkezelőben keresse meg a megoldás **config** mappáját, és jelölje ki a **deployment.json** fájlt. Kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. 

Ha a telepítés sikeres, a VS Code egy üzenetben erősíti meg azt a kimenetben. 

Azt is ellenőrizheti, hogy üzemel-e az összes modul az eszközön. Futtassa az alábbi parancsot az IoT Edge-eszközön a modulok állapotának megtekintéséhez. Ennek futtatása eltarthat néhány percig.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Az SQL-adatbázis létrehozása

Ha alkalmazza az üzembehelyezési jegyzékfájlt az eszközön, akkor három futó modulja lesz. A tempSensor modul szimulált környezeti adatokat állít elő. Az sqlFunction modul az adatbázis számára megfelelő formátumba konvertálja az adatokat. 

Ez a szakasz az SQL-adatbázis beállítását mutatja be a hőmérsékletadatok mentéséhez. 

1. A parancssori eszköz csatlakozhat az adatbázishoz. 
   * Windows-tárolók:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Linux-tárolók: 

      ```bash
      sudo docker exec -it sql bash
      ```

2. Nyissa meg az SQL-parancssori eszközt.
   * Windows-tárolók:

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Linux-tárolók: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
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

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó Azure Functions-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [az Azure Functions a Visual Studio Code és az Azure IoT Edge segítségével történő fejlesztését](how-to-develop-csharp-function.md) ismertető cikkben talál. 

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Az érzékelők adatainak szűrése C# kód használatával](tutorial-csharp-module.md)
