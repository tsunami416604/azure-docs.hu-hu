---
title: "Az Azure IoT peremhálózati SQL modul |} Microsoft Docs"
description: "A Microsoft SQL-modulok, az Azure Functions adatok formázásához peremhálózaton adatok tárolására."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban, ebertrams
ms.date: 02/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4b66a699e4c58662cadd799cf6aec83b9d34b7e6
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>SQL Server-adatbázisok a peremhálózaton adatok tárolásához

Azure IoT peremeszközök segítségével adatok tárolására szolgáló jön létre a peremhálózaton. Időszakos internetkapcsolattal rendelkező eszközök is fenntartható a saját adatbázisok és a jelentés módosításai vissza a felhőben, csak a csatlakozáskor. Eszközökre küldendő csak kritikus fontosságú adatok a felhőben is mentheti a rendszeres tömeges egyéb programozott feltöltését. Miután a felhőben a strukturált adatok megoszthatók más Azure-szolgáltatásokkal, például a gépi tanulási modellek létrehozásához. 

Ez a cikk IoT peremhálózati eszköz egy SQL Server-adatbázis telepítését ismerteti. Az Azure Functions, az IoT-peremhálózati eszközön futó szerkezet a bejövő adatokat, majd elküldi azt az adatbázist. A cikkben leírt lépéseket az egyéb adatbázisokra, amelyek működnek, mint a MySQL vagy PostgreSQL-tárolókban lévő is alkalmazhatók. 

## <a name="prerequisites"></a>Előfeltételek 

Mielőtt megkezdené az utasításokat ebben a cikkben, el kell végeznie az alábbi oktatóanyagok:
* A szimulált eszköz Azure IoT peremhálózati telepítése [Windows](tutorial-simulate-device-windows.md) vagy [Linux](tutorial-simulate-device-linux.md)
* [Azure-függvény egy IoT-Edge-modul telepítése](tutorial-deploy-function.md)

A következő cikkekben sikeresen az oktatóanyag elvégzéséhez nem szükséges, de előfordulhat hasznos a környezetben:
* [Futtatás az SQL Server 2017 tárolót a Docker](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Visual Studio Code segítségével fejlesztésekor és telepítésekor az Azure Functions Azure IoT szegélyhez](how-to-vscode-develop-azure-function.md)

A szükséges oktatóanyagok elvégzése után készen áll az összes szükséges előfeltételeket kell a számítógépre: 
* Egy aktív Azure IoT-központot.
* Legalább 2 GB RAM és a 2 GB-os meghajtó IoT peremhálózati eszköz.
* [A Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/)
* [.NET 2.0 SDK alapvető](https://www.microsoft.com/net/core#windowscmd). 
* [Python 2.7](https://www.python.org/downloads/)
* [Az IoT-Edge vezérlő parancsfájl](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* AzureIoTEdgeFunction template (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* Egy aktív IoT hubot legalább egy IoT peremhálózati eszköz.

Az oktatóanyag működjön processzorarchitektúrák x64 Windows és Linux tárolók. SQL Server nem támogatja az ARM-processzort.

## <a name="deploy-a-sql-server-container"></a>Egy SQL Server-tároló üzembe

Ebben a szakaszban hozzáadja a szimulált IoT peremhálózati eszköz egy MS-SQL-adatbázis. Az SQL Server 2017 docker tároló rendszerképet használja, elérhető [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) és [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/). 

### <a name="deploy-sql-server-2017"></a>Az SQL Server 2017 telepítéséhez

Alapértelmezés szerint ez a szakasz a kód tárolót hoz létre az SQL Server 2017 szabad fejlesztői kiadásával. Ha éles kiadások futni, lásd: [futtatása az üzemi tároló képek](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production) részletes információkat. 

3. lépésben, adja hozzá a beállítások az SQL Server-tárolóhoz, fontos környezeti változókat és persistant tároló létrehozása. A megadott [környezeti változók](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) fogadja el a végfelhasználói licencszerződést, és adja meg a jelszót. A [persistant tárolási](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) segítségével konfigurálható: [csatlakoztatja](https://docs.docker.com/storage/volumes/). Csatlakoztatások létrehozása az SQL Server 2017-tárolóban egy *sqlvolume* kötettároló csatolt, hogy az adatok továbbra is fennáll, akkor is, ha a rendszer törli a tárolót. 

1. Nyissa meg a `deployment.json` fájlt a Visual Studio Code. 
2. Cserélje le a **modulok** a fájl a következő kódot: 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "localhost:5000/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
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
        }
   ```

3. Attól függően, hogy az operációs rendszer Ön által futtatott frissítse az SQL-modul beállításait az alábbi kódra: 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\r\n\t"Env": [\r\n\t\t"ACCEPT_EULA=Y",\r\n\t\t"sa_password=Strong!Passw0rd"\r\n\t],\r\n\t"HostConfig": {\r\n\t\t"Mounts": [{\r\n\t\t\t"Target": "C:\\\\mssql",\r\n\t\t\t"Source": "sqlVolume",\r\n\t\t\t"Type": "volume"\r\n\t\t}],\r\n\t\t"PortBindings": {\r\n\t\t\t"1433/tcp": [{\r\n\t\t\t\t"HostPort": "1401"\r\n\t\t\t}]\r\n\t\t}\r\n\t}\r\n}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\r\n\t"Env": [\r\n\t\t"ACCEPT_EULA=Y",\r\n\t\t"MSSQL_SA_PASSWORD=Strong!Passw0rd"\r\n\t],\r\n\t"HostConfig": {\r\n\t\t"Mounts": [{\r\n\t\t\t"Target": "/var/opt/mssql",\r\n\t\t\t"Source": "sqlVolume",\r\n\t\t\t"Type": "volume"\r\n\t\t}],\r\n\t\t"PortBindings": {\r\n\t\t\t"1433/tcp": [{\r\n\t\t\t\t"HostPort": "1401"\r\n\t\t\t}]\r\n\t\t}\r\n\t}\r\n}"
      ```

4. Mentse a fájlt. 
5. Válassza ki a Visual STUDIO Code parancs paletta **peremhálózati: peremhálózati eszköz a központi telepítés létrehozásához**. 
6. Válassza ki az IoT-peremhálózati eszköz azonosítójával.
7. Válassza ki a `deployment.json` , amelyeket azért frissített fájlt. A kimeneti ablakban megjelenik az üzembe helyezéshez megfelelő kimenetek. 
8. A peremhálózati futásidejű elindításához válassza ki a **peremhálózati: Start peremhálózati** a parancs paletta.

>[!TIP]
>Minden alkalommal, SQL Server tárolókat hozhat létre, éles környezetben, akkor [alapértelmezett rendszergazdai jelszavának módosítása](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

## <a name="create-the-sql-database"></a>Az SQL-adatbázis létrehozása

Ez a szakasz végigvezeti az SQL-adatbázis beállítása a IoT peremhálózati eszköz csatlakozik az érzékelők kapott hőmérséklet adattároláshoz. A szimulált eszköz használata, ezek az adatok származik a *tempSensor* tároló. 

A parancssori eszköz csatlakozni az adatbázishoz: 

* Windows
   ```cmd
   Docker exec -it sql cmd
   ```

* Linux    
   ```bash
   Docker exec -it sql 'bash'
   ```

Nyissa meg az SQL-parancs eszköz: 

* Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Az adatbázis létrehozása: 

* Windows
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Linux
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

A tábla megadása: 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Testre szabhatja az SQL Server docker fájl automatikusan hozzon létre az SQL Server több IoT peremhálózati eszközön telepíteni. További információkért lásd: a [Microsoft SQL Server tároló bemutató projekt](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Az SQL-kapcsolatot ismertetése

Egyéb oktatóanyagok útvonalak használjuk engedélyezéséhez tárolók való kommunikációhoz közben különítve egymástól. SQL Server-adatbázis használata, azonban szorosabb kapcsolatot szükség. 

IoT peremhálózati automatikusan létrehozza a híd (Linux) vagy a NAT (Windows) hálózat kialakítására. A hálózati neve **azure iot-él**. Ha bármikor át kívánja ezt a kapcsolatot hibakeresési, megtekintheti a tulajdonságait, a parancssorban:

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

IoT peremhálózati megoldhatja a DNS, a tároló neve keresztül docker, így nem kell az IP-címét az SQL Server adatbázis hivatkozik. 

Tegyük fel ez a kapcsolati karakterláncot, amely a következő szakaszban használjuk: 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

Láthatja, hogy a kapcsolati karakterlánc neve, a tároló hivatkozik **sql**. Ha módosította a modul neve valami mást, frissítése, valamint a kapcsolati karakterlánc. Egyéb esetben folytassa a következő szakaszban. 

## <a name="update-your-azure-function"></a>Az Azure-függvény frissítése
Küldheti az adatokat az adatbázisba, frissítse az előző oktatóanyag végrehajtott FilterFunction Azure-függvény. Módosítsa ezt a fájlt, hogy az érzékelők által fogadott adatok szerkezet, majd egy SQL-tábla tárolja azokat. 

1. A Visual Studio Code nyissa meg a FilterFunction mappáját. 
2. Cserélje le a run.csx fájlt a következő kóddal, amely tartalmazza az előző szakaszából SQL kapcsolódási karakterlánc: 

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
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
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

## <a name="update-your-container-image"></a>A tároló kép frissítése

A végrehajtott módosítások alkalmazásához a tároló lemezképét, tegye közzé, és indítsa újra a IoT peremhálózati.

1. A Visual Studio Code, bontsa ki a **Docker** mappa. 
2. A platform-használata alapján, bontsa ki a **windows-nano** vagy **linux-x64** mappa. 
3. Kattintson a jobb gombbal a **Dockerfile** fájlt, és válassza ki **Build IoT peremhálózati modul Docker kép**.
4. Keresse meg a **FilterFunction** projektmappa, és kattintson **jelölje ki a mappát, EXE_DIR**.
5. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filterfunction:latest`. Ha egy helyi beállításjegyzék telepíti, a névnek kell lennie `<localhost:5000/filterfunction:latest>`.
6. Válassza ki a Visual STUDIO Code parancs paletta **peremhálózati: leküldéses IoT peremhálózati modul Docker kép**. 
7. Előugró szövegmezőben adja meg a lemezkép néven. 
8. Válassza ki a Visual STUDIO Code parancs paletta **peremhálózati: Indítsa újra a peremhálózati**.

## <a name="view-the-local-data"></a>A helyi adatok megtekintése

Után indítsa újra a tárolókban, a hőmérséklet-érzékelő érkező adatokat az IoT-peremhálózati eszköz egy helyi SQL Server 2017 adatbázisban tárolódik. 

A parancssori eszköz csatlakozni az adatbázishoz: 

* Windows
   ```cmd
   Docker exec -it sql cmd
   ```

* Linux    
   ```bash
   Docker exec -it sql 'bash'
   ```

Nyissa meg az SQL-parancs eszköz: 

* Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Megtekintheti az adatokat: 

   ```sql
   Select * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [SQL Server 2017 tároló képek konfigurálja a Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Látogasson el a [mssql-docker GitHub-tárházban](https://github.com/Microsoft/mssql-docker) erőforrások, visszajelzések és ismert problémákat.
