---
title: "Egy Azure IoT Edge-modul létrehozása a C# |} Microsoft Docs"
description: "Ez az oktatóanyag BLA adatok konverter modul a legújabb Azure IoT peremhálózati NuGet-csomagok, Visual Studio Code és C# írásával bővíthető."
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: "Azure, iot, oktatóanyag, a modul, nuget, vscode, c Sharp, peremhálózati"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.openlocfilehash: 7175ffc8de2c043593d61143b402484d33e4a8cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Egy Azure IoT Edge-modul létrehozása a C & #x23;

Ez az oktatóanyag egy modul a létrehozása bővíthető `Azure IoT Edge` használatával `Visual Studio Code` és `C#`.

Az oktatóanyag azt bízná környezet beállításról és írásával egy [Gedélyezése](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) adatok konverter moduljának használatával. a legújabb `Azure IoT Edge NuGet` csomagok. 

>[!NOTE]
Ez az oktatóanyag használ a `.NET Core SDK`, amely támogatja a platformfüggetlen. Az alábbi oktatóanyag segítségével írása a `Windows 10` operációs rendszer. Ebben az oktatóanyagban parancsainak függően eltérő lehet a `development environment`. 

## <a name="prerequisites"></a>Előfeltételek

Ez a szakasz azt beállításról a környezetet az `Azure IoT Edge` modul fejlesztési. Ez egyaránt vonatkozik **64 bites Windows** és **64 bites Linux (8 Ubuntu/Debian)** operációs rendszerek.

A következő szoftvereket is szükséges:

- [Git-ügyfél](https://git-scm.com/downloads)
- [.NET Core SDK](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

Nem kell az ebben a példában a tárház klónozása azonban ebben az oktatóanyagban tárgyalja példakód mindegyikét található, a következő tárházat:

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>Bevezetés

1. Telepítés `.NET Core SDK`.
2. Telepítés `Visual Studio Code` és a `C# extension` a Visual Studio Code piactérről.

Ezek megtekintéséhez [gyors videó oktatóanyag](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows) használatának módjáról `Visual Studio Code` és a `.NET Core SDK`.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Azure IoT peremhálózati konverter modul létrehozása

1. Egy új inicializálása `.NET Core` C# hordozhatóosztálytár-projektjének:
    - Nyisson meg egy parancssort (`Windows + R` -> `cmd` -> `enter`).
    - Lépjen abba a mappába, hol szeretné létrehozni a `C#` projekt.
    - Típus **dotnet új classlib -o IoTEdgeConverterModule -f netstandard1.3**. 
    - Ez a parancs létrehoz egy üres osztályt `Class1.cs` a projekteket tartalmazó könyvtárban található.
2. Lépjen abba a mappába, amelyben imént létrehozott a hordozhatóosztálytár-projektjének beírásával **cd IoTEdgeConverterModule**.
3. Nyissa meg a projektet a `Visual Studio Code` beírásával **kód .**.
4. Miután a projekt meg van nyitva, a `Visual Studio Code`, kattintson a a **IoTEdgeConverterModule.csproj** megnyitni a fájlt a következő ábrán látható módon:

    ![A Visual Studio Code Szerkesztés ablak](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. Helyezze be a `XML` záró között a következő kódrészletben látható blob `PropertyGroup` címke és a záró `Project` címkét; hat az előző ábrán. sor, és mentse a fájlt billentyűkombináció lenyomásával `Ctrl`  +  `S`.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. Miután menti a `.csproj` fájl, `Visual Studio Code` kell kérni a egy `unresolved dependencies` párbeszédpanel az alábbi képen látható módon: 

    ![A Visual Studio Code visszaállítási függőségek párbeszédpanel](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) kattintson `Restore` összes elemben a projektek visszaállításához `.csproj` fájl többek között a `PackageReferences` jelentek meg. 

    b) `Visual Studio Code` automatikusan létrehozza a `project.assets.json` fájlt a projektben `obj` mappát. Ez a fájl a Projektfüggőségek későbbi visszaállítások gyorsabb végrehajtásához információkat tartalmaz.
 
    >[!NOTE]
    `.NET Core Tools`jelenleg MSBuild-alapú. Ez azt jelenti a `.csproj` projekt fájlt hoz létre ahelyett, hogy egy `project.json`.

    - Ha `Visual Studio Code` nem kéri a felhasználót, hogy így megfelelő, azt megteheti manuálisan. Nyissa meg a `Visual Studio Code` billentyűkombináció lenyomásával integrált terminálablakot a `Ctrl`  +  `backtick` kulcsok vagy menüket `View`  ->  `Integrated Terminal`.
    - Az a `Integrated Terminal` időszak típusa **dotnet visszaállítási**.
    
7. Nevezze át a `Class1.cs` fájlt `BleConverterModule.cs`. 

    a) nevezze át a fájlt először kattintson a fájlra nyomja le az `F2` kulcs.
    
    b) az új nevet írja be **BleConverterModule**, az alábbi képen látható módon:

    ![A Visual Studio Code osztály átnevezése](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. Cserélje le a meglévő kódot a `BleConverterModule.cs` másolása és beillesztése a következő kódrészletet a fájlt a `BleConverterModule.cs` fájlt.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. Mentse a fájlt billentyűkombináció lenyomásával `Ctrl`  +  `S`.

10. Hozzon létre egy új fájlt nevű `Untitled-1` billentyűkombináció lenyomásával a `Ctrl`  +  `N` a kulcsok az alábbi képen látható módon:

    ![A Visual Studio Code új fájl](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. Deszerializálni a `JSON` objektum, amelyet a szimulált `BLE` eszköz, másolja az alábbi kódot a `Untitled-1` fájl kód szerkesztő ablakban. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. Mentse a fájlt `BleData.cs` billentyűkombináció lenyomásával `Ctrl`  +  `Shift`  +  `S` kulcsok.
    - A Mentés másként párbeszédpanel az a `Save as Type` legördülő menüben válassza `C# (*.cs;*.csx)` az alábbi képen látható módon:

    ![A Visual Studio Code Mentés másként párbeszédpanel](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. Hozzon létre egy új fájlt nevű `Untitled-1` billentyűkombináció lenyomásával a `Ctrl`  +  `N` kulcsok.

14. Másolja és illessze be a következő kódrészletet a a `Untitled-1` fájlt. Ez az osztály egy `Azure IoT Edge` modult, amely használatával a kimeneti érkező adatokat az `BleConverterModule`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. Mentse a fájlt `DotNetPrinterModule.cs` billentyűkombináció lenyomásával `Ctrl`  +  `Shift`  +  `S`.
    - A Mentés másként párbeszédpanel az a `Save as Type` legördülő menüben válassza `C# (*.cs;*.csx)`.

16. Hozzon létre egy új fájlt nevű `Untitled-1` billentyűkombináció lenyomásával a `Ctrl`  +  `N` kulcsok.

17. Deszerializálni a `JSON` objektum, amelyet a `BleConverterModule`, másolási és beillesztési műveleteket, a következő kódrészletet a kódot a `Untitled-1` fájlt. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. Mentse a fájlt `BleConverterData.cs` billentyűkombináció lenyomásával `Ctrl`  +  `Shift`  +  `S`.
    - A Mentés másként párbeszédpanel az a `Save as Type` legördülő menüben válassza `C# (*.cs;*.csx)`.

19. Hozzon létre egy új fájlt nevű `Untitled-1` billentyűkombináció lenyomásával a `Ctrl`  +  `N` kulcsok.

20. Másolja és illessze be a következő kódrészletet a a `Untitled-1` fájlt.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. Mentse a fájlt `gw-config.json` billentyűkombináció lenyomásával `Ctrl`  +  `Shift`  +  `S`.
    - A Mentés másként párbeszédpanel az a `Save as Type` legördülő menüben válassza `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`.

22. A konfigurációs fájl másolása a kimeneti könyvtár engedélyezéséhez frissítse a `IoTEdgeConverterModule.csproj` a következő XML-blobbal együtt:

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - A frissített `IoTEdgeConverterModule.csproj` az alábbi képen hasonlóan kell kinéznie:

    ![A Visual Studio Code .csproj fájl frissítése](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. Hozzon létre egy új fájlt nevű `Untitled-1` billentyűkombináció lenyomásával a `Ctrl`  +  `N` kulcsok.

24. Másolja és illessze be a következő kódrészletet a a `Untitled-1` fájlt.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. Mentse a fájlt `binplace.ps1` billentyűkombináció lenyomásával `Ctrl`  +  `Shift`  +  `S`.
    - A Mentés másként párbeszédpanel az a `Save as Type` legördülő menüben válassza `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`.

26. A projekt felépítéséhez billentyűkombináció lenyomásával a `Ctrl`  +  `Shift`  +  `B` kulcsok. Amikor első alkalommal a projekt buildjének elkészítéséhez `Visual Studio Code` kéri a a `No build task defined.` párbeszédpanel az alábbi képen látható módon:

    ![A Visual Studio Code build a feladat párbeszédpanelen](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) kattintson a `Configure Build Task` gombra.

    b) a a `Select a Task Runner` párbeszédpanel legördülő menüre. Válassza ki `.NET Core` az alábbi képen látható módon: 

    ![A Visual Studio Code, válassza ki a feladat párbeszédpanelen](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) kattintva a `.NET Core` elemet hoz létre a `tasks.json` fájlt a `.vscode` könyvtár, és megnyitja a fájlt a `code editor` ablakban. Nincs szükség a következő fájl módosításához, zárja be a lapon.

27.  Nyissa meg a `Visual Studio Code` billentyűkombináció lenyomásával integrált terminálablakot a `Ctrl`  +  `backtick` kulcsok vagy menüket `View`  ->  `Integrated Terminal` és típus **.\binplace.ps1** be a `PowerShell` parancssort. Ez a parancs átmásolja a függőségek a kimeneti könyvtár.

28. Keresse meg a projektek kimeneti könyvtár a `Integrated Terminal` ablakban írja be a **cd.\bin\Debug\netstandard1.3**.

29. A minta projekt futtatásához írja be a **. \gw.exe gw-config.json** azokat a `Integrated Terminal` ablak kérdés. 
    - Ha már elvégezte a lépést ebben az oktatóanyagban szorosan, hogy most rendszerűnek kell lennie a `Azure IoT Edge BLE Data Converter Module` mintaprojektet az alábbi képen látható módon:
    
        ![Visual Studio Code-beli szimulált eszköz – példa](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - Ha azt szeretné, az alkalmazás befejezéséhez nyomja meg a `<Enter>` kulcs.

>[!IMPORTANT]
Nem ajánlott használandó `Ctrl`  +  `C` le kell állítanunk a `IoT Edge` átjáró alkalmazás (Ez azt jelenti, hogy **gw.exe**). Mivel ez a művelet a folyamat rendellenesen eredményezheti.

