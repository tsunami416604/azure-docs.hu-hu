---
title: Oktatóanyag – Adatok tárolása SQL-modullal az Azure IoT Edge használatával
description: Ez az oktatóanyag bemutatja, hogyan tárolhatja az adatokat helyileg az IoT Edge-eszközön egy SQL Server modullal
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d1b5ea9a9f78bc8a83159a34026d58d7a8cc89b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944271"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Oktatóanyag: Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal

Sql Server-modul üzembe helyezése az Azure IoT Edge-et futtató Linux-eszközön tárolt adatok tárolására.

Az Azure IoT Edge és az SQL Server segítségével adatokat tárolhat és kérdezhet le a peremhálózaton. Az Azure IoT Edge alapvető tárolási képességekkel rendelkezik az üzenetek gyorsítótárazásához, ha egy eszköz offline állapotba kerül, majd továbbítsa őket, amikor a kapcsolat újra létrejön. Szüksége lehet azonban ennél fejlettebb tárolási képességekre is, például az adatok helyi lekérdezéséhez. Az IoT Edge-eszközök használhatják a helyi adatbázisokat összetettebb számítástechnikai műveletek elvégzéséhez anélkül, hogy az IoT Hubhoz való kapcsolat fenntartása.

A jelen cikk az SQL Server-adatbázisok IoT Edge-eszközön történő üzembe helyezésének utasításait tartalmazza. Az IoT Edge-eszközön futó Azure Functions-függvények elvégzik a bejövő adatok rendszerezését, majd elküldik azokat az adatbázisnak. A cikkben szereplő lépések a tárolókban üzemeltetett egyéb adatbázisokra (például MySQL vagy PostgreSQL) is alkalmazhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure-függvény létrehozása a Visual Studio Code használatával
> * SQL-adatbázis üzembe helyezése az IoT Edge-eszközön
> * A Visual Studio Code használatával modulokat hozhat létre és helyezhet üzembe az IoT Edge-eszközön
> * A létrejött adatok megtekintése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt át kellett volna mennie az előző oktatóanyagon, hogy beállítsa a linuxos tárolók fejlesztéséhez a fejlesztői környezetet: [IoT Edge-modulok fejlesztése Linux-eszközökhöz](tutorial-develop-for-linux.md). Az oktatóanyag kitöltésével a következő előfeltételeknek kell megrendelkezniük:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató](quickstart-linux.md)AMD64 Linux-eszköz.
  * Az ARM-eszközök, például a Raspberry Pis, nem futtathatnak SQL Server t. Ha az SQL-t arm-eszközön szeretné használni, regisztrálhat az [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) előzetes verzióban történő kipróbálásához.
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)konfigurált [Visual Studio-kód.](https://code.visualstudio.com/)
* [A Docker CE](https://docs.docker.com/install/) linuxos tárolók futtatására van konfigurálva.

Ez az oktatóanyag egy Azure Functions modult használ az SQL Server adatok küldéséhez. IoT Edge-modul azure Functions használatával való fejlesztéséhez telepítse a következő további előfeltételeket a fejlesztői gépre:

* [C# a Visual Studio Code (powered by OmniSharp) bővítmény visual studio kód](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Ahhoz, hogy adatokat küldhessen egy adatbázisba, egy olyan modulra van szüksége, amely képes megfelelően rendszerezni, majd egy táblába elmenteni az adatokat.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre egy IoT Edge-függvényt a Visual Studio Code és az Azure IoT Tools használatával.

1. Nyissa meg a Visual Studio Code-ot.

2. Nyissa meg a VS-kód parancspalettát a **Nézet** > **parancspaletta**kiválasztásával.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldásnak, például **az SqlSolution-** t, vagy fogadja el az alapértelmezett et. |
   | Select module template (Modulsablon kiválasztása) | Válassza **az Azure Functions - C# lehetőséget.** |
   | Provide a module name (Modulnév megadása) | A modulnak adja az **sqlFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>Az utolsó karakterlánc \<a\>rendszerleíró adatbázis "azurecr.io/sqlfunction. |

   A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét.

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

1. A VS Code explorer programban nyissa meg az**sqlFunction** > **sqlFunction.cs** **modulokat.** > 

2. Cserélje le a fájl teljes tartalmát a következő kódra:

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

3. A 35-ös sorban cserélje le az ** \<SQL kapcsolatkarakterláncot\> ** a következő karakterláncra. Az **Adatforrás** tulajdonság az SQL Server tárolóra hivatkozik, amely még nem létezik. A következő szakaszban **az SQL** névvel hozza létre.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Mentse a **sqlFunction.cs** fájlt.

5. Nyissa meg az **sqlFunction.csproj** fájlt.

6. Keresse meg a csomaghivatkozások csoportját, és adjon hozzá egy újat az SqlClient-hez.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Mentse az **sqlFunction.csproj** fájlt.

## <a name="add-the-sql-server-container"></a>Az SQL Server tároló hozzáadása

Az IoT Edge-futtatókörnyezet által az IoT Edge-eszközön telepítendő modulokat az [üzembehelyezési jegyzékfájl](module-composition.md) határozza meg. Megadta a kódot, hogy egy testreszabott függvény modul az előző szakaszban, de az SQL Server modul már ki van építve, és elérhető az Azure Marketplace-en. Utasítsa az IoT Edge-futtatókörnyezetet ennek belefoglalására, majd végezze el a konfigurálást az eszközön.

1. A Visual Studio-kódban nyissa meg a parancspalettát a **Nézet** > **parancspaletta lehetőségkiválasztásával.**

2. A parancspalettán írja be és futtassa az **Azure IoT Edge: IoT Edge modul hozzáadása parancsot.** A parancspalettán adja meg a következő információkat egy új modul hozzáadásához:

   | Mező | Érték |
   | ----- | ----- |
   | Select deployment template file (Üzembehelyezési sablonfájl kiválasztása) | A parancspaletta kiemeli a deployment.template.json fájlt az aktuális megoldásmappában. Jelölje ki a fájlt.  |
   | Select module template (Modulsablon kiválasztása) | Válassza a **modul lehetőséget az Azure Marketplace-ről.** |

3. Az Azure IoT Edge modul piactéren keresse meg és válassza az **SQL Server Module**lehetőséget.

4. Módosítsa a modul nevét **sql**, minden kisbetűs. Ez a név megegyezik a sqlFunction.cs fájlban lévő kapcsolati karakterláncban deklarált tárolónévvel.

5. Válassza az **Importálás** lehetőséget, ha hozzá szeretné adni a modult a megoldáshoz.

6. Nyissa meg a megoldás mappájában a **deployment.template.json** fájlt.

7. Keresse meg a **modulok szakaszt.** Három modulnak kell látnia. A *SimulatedTemperatureSensor* modul alapértelmezés szerint szerepel az új megoldásokban, és tesztadatokat biztosít a többi modulhoz való használatra. Az *sqlFunction* modul az a modul, amelyet eredetileg létrehozott és frissített az új kóddal. Végül a modul *sql* lett importálva az Azure Piactérről.

   >[!Tip]
   >Az SQL Server modul hoz egy alapértelmezett jelszó készlet a környezeti változók a központi telepítési jegyzékfájl. Ha éles környezetben hoz létre SQL Server-tárolót, minden esetben [módosítsa az alapértelmezett rendszergazdai jelszót](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Zárja be a **deployment.template.json** fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszokban egyetlen modullal hozott létre megoldást, majd hozzáadott egy másik modult az üzembehelyezési jegyzéksablonfájlhoz. Az SQL Server modult a Microsoft nyilvánosan üzemelteti, de a függvények modulban kell tárolóba helyeznie a kódot. Ebben a szakaszban hozza létre a megoldást, hozzon létre tárolórendszerképeket az sqlFunction modulhoz, és leküldéses a rendszerképet a tároló beállításjegyzékbe.

1. A Visual Studio Kód nézetében nyissa meg az integrált terminált a**Terminál** **megtekintése** > lehetőség kiválasztásával.  

1. Jelentkezzen be a tárolóregisztrációs adatbázisba a Visual Studio Code felületén, hogy le tudja küldeni a rendszerképeket a regisztrációs adatbázisba. Használja ugyanazt az Azure Container Registry (ACR) hitelesítő adatokat, amelyeket az .env fájlhoz adott. Az integrált terminálon írja be a következő parancsot:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    A --password-stdin paraméter használatát ajánló biztonsági figyelmeztetés jelenhet meg. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információ: [a docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) parancs hivatkozás.

1. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

Amikor azt mondja a Visual Studio Code-nak a megoldás létrehozásához, először a központi telepítési sablonban lévő információkat hozza létre, és létrehoz egy deployment.json fájlt egy **config**nevű új mappában. Ezután két parancsot futtat az `docker build` `docker push`integrált terminálon: és . A build parancs létrehozza a kódot, és konténerbe foglalja a modult. Ezután a leküldéses parancs leküldéses a kódot a tároló rendszerleíró adatbázisába, amely a megoldás inicializálásakor megadott.

Ellenőrizheti, hogy az sqlFunction modul sikeresen lelett-e a tároló beállításjegyzékébe. Az Azure Portalon keresse meg a tároló beállításjegyzékben. Válassza ki **az adattárakat,** és keressen **az sqlFunction függvényre.** A másik két modul, a SimulatedTemperatureSensor és az sql nem kerül a tároló beállításjegyzékébe, mert a tárolók már a Microsoft-jegyzékekben vannak.

## <a name="deploy-the-solution-to-a-device"></a>A megoldás üzembe helyezése egy eszközön

Az IoT Hub felületén keresztül modulokat állíthat be egy eszközön, de az IoT Hubhoz és az eszközökhöz a Visual Studio Code felületén keresztül is hozzáférhet. Ebben a szakaszban az IoT Hubhoz való hozzáférést fogja beállítani, majd a VS Code használatával üzembe fogja helyezni a megoldást az IoT Edge-eszközön.

1. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt.

2. Kattintson a jobb gombbal az üzembe helyezés céleszközére, majd válassza a **Create deployment for a single device** (Üzemelő példány létrehozása egyetlen eszközhöz) lehetőséget.

3. A fájlkezelőben keresse meg a megoldáson belüli **konfigurációs** mappát, és válassza **a deployment.amd64 parancsot.** Kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre.

   Ne használja a deployment.template.json fájlt telepítési jegyzékfájlként.

Ha az üzembe helyezés sikeres volt, a VS Code kimenetében egy megerősítő üzenet jelenik meg.

Frissítse az eszköz állapotát a VS-kód Azure IoT Hub-eszközök szakaszában. Az új modulok vannak felsorolva, és a következő néhány percben futóként fognak jelenteni, amikor a tárolók telepítve vannak és elindulnak. Azt is ellenőrizheti, hogy üzemel-e az összes modul az eszközön. Futtassa az alábbi parancsot az IoT Edge-eszközön a modulok állapotának megtekintéséhez.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Az SQL-adatbázis létrehozása

Ha alkalmazza az üzembehelyezési jegyzékfájlt az eszközön, akkor három futó modulja lesz. A SimulatedTemperatureSensor modul szimulált környezeti adatokat hoz létre. Az sqlFunction modul az adatbázis számára megfelelő formátumba konvertálja az adatokat. Ez a szakasz az SQL-adatbázis beállítását mutatja be a hőmérsékletadatok mentéséhez.

Futtassa a következő parancsokat az IoT Edge-eszközön. Ezek a parancsok az eszközön futó **sql** modulhoz csatlakoznak, és létrehoznak egy adatbázist és táblát a hozzá küldött hőmérsékleti adatok tárolására.

1. Az IoT Edge-eszköz parancssori eszközében csatlakozzon az adatbázishoz.

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

Ha egy másik tárolási módszert szeretne kipróbálni a peremhálózaton, olvassa el, hogyan használhatja az Azure Blob Storage-t az IoT Edge-en.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton az Azure Blob Storage az IoT Edge-ben segítségével](how-to-store-data-blob.md)
