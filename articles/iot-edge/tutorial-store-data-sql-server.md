---
title: Oktatóanyag tároló adatainak SQL-modullal – Azure IoT Edge |} A Microsoft Docs
description: Megtudhatja, hogyan történik az adatok egy SQL Server-modul használatával történő helyi tárolása az IoT Edge-eszközön
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b77b44856e9623235051470bc087885765ee12c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080434"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Oktatóanyag: Az SQL Server-adatbázisok a peremhálózaton data Store

Telepítse az SQL Server modulhoz tárolja adatait az Azure IoT Edge-es Linux rendszerű eszközök.

Az Azure IoT Edge és az SQL Server segítségével adatokat tárolhat és kérdezhet le a peremhálózaton. Az Azure IoT Edge rendelkezik alapszintű tárolási képességeinek gyorsítótárazzák az üzeneteket, ha egy eszköz offline állapotba kerül, és majd továbbítják őket, amikor a kapcsolat helyreállt. Szüksége lehet azonban ennél fejlettebb tárolási képességekre is, például az adatok helyi lekérdezéséhez. Az IoT Edge-eszközök helyi adatbázisok hajthat végre a bonyolultabb számítási az IoT Hub-kapcsolat fenntartását anélkül. 

A jelen cikk az SQL Server-adatbázisok IoT Edge-eszközön történő üzembe helyezésének utasításait tartalmazza. Az IoT Edge-eszközön futó Azure Functions-függvények elvégzik a bejövő adatok rendszerezését, majd elküldik azokat az adatbázisnak. A cikkben szereplő lépések a tárolókban üzemeltetett egyéb adatbázisokra (például MySQL vagy PostgreSQL) is alkalmazhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Azure-függvény létrehozása a Visual Studio Code használatával
> * SQL-adatbázis üzembe helyezése az IoT Edge-eszközön
> * A Visual Studio Code használatával modulokat hozhat létre és helyezhet üzembe az IoT Edge-eszközön
> * A létrejött adatok megtekintése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez kell elvégezte az előző oktatóanyagban tároló Linux-fejlesztéshez a fejlesztési környezet beállítása: [A Linux rendszerű eszközök IoT Edge-modulok fejlesztése](tutorial-develop-for-linux.md). Az oktatóanyag elvégzésével helyben kell rendelkeznie a következő előfeltételek vonatkoznak: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Azure IoT Edge-es Linux rendszerű eszközök](quickstart-linux.md)
* Egy tároló-beállításjegyzéket, pl. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [A Visual Studio Code](https://code.visualstudio.com/) konfigurálva a [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [A docker CE](https://docs.docker.com/install/) konfigurált Linux-tárolók futtatásához.

Ebben az oktatóanyagban egy Azure Functions-modul adatokat küldeni az SQL Server. Az Azure Functions az IoT Edge-modul fejlesztése, telepítse a következő további előfeltételeket a fejlesztői gépen: 

* [C#(szolgáltató: omnisharp) Visual Studio Code-bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Ahhoz, hogy adatokat küldhessen egy adatbázisba, egy olyan modulra van szüksége, amely képes megfelelően rendszerezni, majd egy táblába elmenteni az adatokat. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

A következő lépések bemutatják, hogyan hozhat létre a Visual Studio Code és az Azure IoT-eszközök használatával IoT Edge-függvényt.

1. Nyissa meg a Visual Studio Code-ot.

2. A **View (Nézet)**  > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.

3. A parancskatalógus, írja be, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, például **SqlSolution**, vagy fogadja el az alapértelmezett. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **az Azure Functions - C#** . |
   | Provide a module name (Modulnév megadása) | A modulnak adja az **sqlFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>Néz ki a végső karakterláncban \<beállításjegyzék neve\>.azurecr.io/sqlfunction. |

   A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. 
   
### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a cél-architektúra

A Visual Studio Code jelenleg C modulok Linux AMD64- és Linux ARM32v7 eszközökhöz is fejleszthet. Kell választania, melyik architektúra célozza az egyes megoldások, mert a tároló összeállítása és futtatása eltérően az egyes architektúra. Az alapértelmezett érték a Linux-AMD64. 

1. A parancskatalógus megnyitásához, és keressen rá a **Azure IoT Edge: Állítsa be alapértelmezett célplatform peremhálózati megoldás**, vagy válassza ki a parancsikont a oldalsó sáv az ablak alján. 

2. A parancskatalógus válassza ki a cél-architektúra a lehetőségek listájából. Ebben az oktatóanyagban használjuk egy Ubuntu virtuális gép legyen az IoT Edge-eszköz, így megtartja az alapértelmezett **amd64**. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa meg a **modulok** > **sqlFunction** > **sqlFunction.cs**.

2. Cserélje le a fájl teljes tartalmát az alábbira:

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
                           logger.LogInformation($"{rows} rows were updated");
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

3. 35 sorban cserélje le a karakterlánc **\<sql-kapcsolati sztring\>** a következő karakterlánccal. A **adatforrás** tulajdonság hivatkozik, az SQL Server-tároló, amely még nem létezik, de a névvel létrehoz **SQL** a következő szakaszban. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Mentse a **sqlFunction.cs** fájlt. 

5. Nyissa meg a **sqlFunction.csproj** fájlt.

6. Keresse meg a csoportja csomaghivatkozásokhoz, és adjon hozzá egy új SqlClient tartalmazza. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Mentse a **sqlFunction.csproj** fájlt.

## <a name="add-the-sql-server-container"></a>Az SQL Server-tároló hozzáadása

Az IoT Edge-futtatókörnyezet által az IoT Edge-eszközön telepítendő modulokat az [üzembehelyezési jegyzékfájl](module-composition.md) határozza meg. A kód egy egyéni függvénymodul az előző szakaszban győződjön meg arról, hogy a megadott, de az SQL Server-modul már kész és elérhető az Azure piactéren. Utasítsa az IoT Edge-futtatókörnyezetet ennek belefoglalására, majd végezze el a konfigurálást az eszközön. 

1. A Visual Studio Code-ban nyissa meg a parancskatalógust kiválasztásával **nézet** > **parancskatalógus**.

2. A parancskatalógus, írja be, és futtassa a parancsot **Azure IoT Edge: IoT Edge-modul hozzáadása**. Adja meg a parancskatalógus új modul hozzáadása a következő információkat: 

   | Mező | Érték | 
   | ----- | ----- |
   | Select deployment template file (Üzembehelyezési sablonfájl kiválasztása) | A parancskatalógus kiemeli a deployment.template.json fájlt az aktuális megoldás mappában. Válassza ki a fájlt.  |
   | Select module template (Modulsablon kiválasztása) | Válassza ki **modul az Azure Marketplace-ről**. |

3. Az Azure IoT Edge-modul piactéren keresése és kiválasztása **SQL Server-modulja**. 

4. Módosítsa a modul nevét, a **sql**, összes betűjét. Ez a neve megegyezik a tároló neve a kapcsolati karakterláncban a sqlFunction.cs fájlban deklarált. 

5. Válassza ki **importálás** a modul hozzáadása a megoldáshoz. 

6. A megoldás mappáját, és nyissa meg a **deployment.template.json** fájlt. 

7. Keresse meg a **modulok** szakaszban. Három modult kell megjelennie. A modul *tempSensor* alapértelmezés szerint új megoldásokat a része, és az egyéb modulok használata a Tesztadatok biztosít. A modul *sqlFunction* a modul, amely eredetileg létrehozott és frissített új kóddal. Végül, a modul *sql* importált az Azure Marketplace-ről. 

   >[!Tip]
   >Az SQL Server-modul beállítása a környezeti változókat a központi telepítési jegyzékfájl az alapértelmezett jelszót tartalmaz. Ha éles környezetben hoz létre SQL Server-tárolót, minden esetben [módosítsa az alapértelmezett rendszergazdai jelszót](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Zárja be a **deployment.template.json** fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszokban egyetlen modullal hozott létre megoldást, majd hozzáadott egy másik modult az üzembehelyezési jegyzéksablonfájlhoz. Az SQL Server-modul a Microsoft nyilvánosan üzemelteti, de igény szerint tárolóalapúvá alakíthatja a Functions-modul a kódot kell. Ez a szakasz a megoldás felépítéséhez, sqlFunction modul tárolórendszerképek létrehozása, és küldje le a rendszerképet a tárolóregisztrációs adatbázis. 

1. A Visual Studio Code-ban válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet az integrált terminál megnyitásához.  

1. Jelentkezzen be a tárolóregisztrációs adatbázisba a Visual Studio Code felületén, hogy le tudja küldeni a rendszerképeket a regisztrációs adatbázisba. Az Azure Container Registry (ACR) hitelesítő adatokkal, a .env fájl hozzáadott használja. Az integrált terminálon írja be a következő parancsot:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    Láthatja, hogy egy biztonsági figyelmeztetés használatát javasolja a – jelszó-stdin paraméter. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referencia parancsot. 

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy deployment.json nevű fájlt egy új **config** nevű mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. Ez a két parancs a kód felépítéséhez, a modul tárolóba, és majd továbbítsa a kód a tárolóregisztrációs adatbázisba, a megoldás inicializálásakor megadott. 

Ellenőrizheti, hogy a sqlFunction modul sikeresen lett leküldve a tárolóregisztrációs adatbázisba. Az Azure Portalon lépjen a tárolóregisztrációs adatbázisba. Válassza ki **tárházak** és keressen rá a **sqlFunction**. A másik két modulok, tempSensor és az sql, nem lehet leküldeni a tárolóregisztrációs adatbázisba, mert azok a Microsoft beállításjegyzékek adattárait már van mutató.

## <a name="deploy-the-solution-to-a-device"></a>A megoldás üzembe helyezése egy eszközön

Az IoT Hub felületén keresztül modulokat állíthat be egy eszközön, de az IoT Hubhoz és az eszközökhöz a Visual Studio Code felületén keresztül is hozzáférhet. Ebben a szakaszban az IoT Hubhoz való hozzáférést fogja beállítani, majd a VS Code használatával üzembe fogja helyezni a megoldást az IoT Edge-eszközön. 

1. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

2. Kattintson a jobb gombbal az üzembe helyezés céleszközére, majd válassza a **Create deployment for a single device** (Üzemelő példány létrehozása egyetlen eszközhöz) lehetőséget. 

3. A Fájlkezelőben keresse meg a **config** belüli megoldását, és válassza a **deployment.amd64**. Kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. 

   Ne használja a deployment.template.json fájlt, egy manifest nasazení.

Ha az üzembe helyezés sikeres volt, a VS Code kimenetében egy megerősítő üzenet jelenik meg. 

Frissítse a VS Code Azure IoT Hub-eszközök szakaszát az eszköz állapotát. Az új modulokat találhatók, és megkezdi a jelentéshez, mintha a postafiókjába pár percen keresztül, a tárolók telepítése és elindítása. Azt is ellenőrizheti, hogy üzemel-e az összes modul az eszközön. Futtassa az alábbi parancsot az IoT Edge-eszközön a modulok állapotának megtekintéséhez. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Az SQL-adatbázis létrehozása

Ha alkalmazza az üzembehelyezési jegyzékfájlt az eszközön, akkor három futó modulja lesz. A tempSensor modul szimulált környezeti adatokat állít elő. Az sqlFunction modul az adatbázis számára megfelelő formátumba konvertálja az adatokat. Ez a szakasz az SQL-adatbázis beállítását mutatja be a hőmérsékletadatok mentéséhez. 

Futtassa az alábbi parancsokat az IoT Edge-eszközön. Ezek a parancsok csatlakozni a **sql** modul fut az eszközön, és hozzon létre egy adatbázist és a hozzá küldött hőmérsékleti adatokat tároló tábla. 

1. A parancssori eszköz az IoT Edge-eszközön csatlakozzon az adatbázishoz. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. Nyissa meg az SQL-parancssori eszközt.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Adatbázis létrehozása: 
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

   ![Helyi adatbázis tartalmának megtekintése](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó Azure Functions-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [az Azure Functions a Visual Studio Code és az Azure IoT Edge segítségével történő fejlesztését](how-to-develop-csharp-function.md) ismertető cikkben talál. 

Ha azt szeretné, próbálkozhat a peremhálózaton egy másik tárolási módszer, olvassa el az Azure Blob Storage használata az IoT Edge-ben. 

> [!div class="nextstepaction"]
> [Az Azure Blob Storage a peremhálózaton data Store az IoT Edge-ben](how-to-store-data-blob.md)
