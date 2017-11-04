---
title: "Ismerkedés az Azure IoT Hub kezelés (.NET/csomópont) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub kezelés távoli eszköz újraindítás kezdeményezése. Az Azure IoT-eszközök SDK for Node.js használatával valósítja meg a szimulált eszköz alkalmazást, amely közvetlen módszer és a Azure IoT szolgáltatást a service-alkalmazást, amely hívja meg a közvetlen módszer végrehajtásához .NET SDK tartalmazza."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: juanpere
ms.openlocfilehash: 5d0b7b1ab5893e55a6e2aa16451b6a9fc1481966
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="get-started-with-device-management-netnode"></a>Ismerkedés az Eszközfelügyelet (.NET/csomópont)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure-portál használatával létrehoz egy IoT-központot, és az IoT hub hozzon létre egy eszközidentitás.
* Létrehoz egy szimulált eszköz alkalmazást, amely tartalmazza a közvetlen módszer, amely az eszköz újraindul. Közvetlen módszerek a felhőből hívják.
* A .NET-Konzolalkalmazás, amely behívja az újraindítás közvetlen módszer a szimulált eszköz alkalmazás keresztül az IoT hub létrehozása.

Ez az oktatóanyag végén akkor egy Node.js Konzolalkalmazás eszköz és a .NET (C#) háttér-Konzolalkalmazás:

**dmpatterns_getstarted_device.js**, amely kapcsolódik az IoT hub korábban létrehozott eszköz identitású kap egy újraindítás közvetlen módszer, szimulálja a fizikai számítógép újraindítása, és jelentést az idő az utolsó újraindítás.

**TriggerReboot**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazás jeleníti meg a választ, és jeleníti meg a frissített jelentett tulajdonságok.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* NODE.js-verzió 4.0.x vagy újabb verzióját, <br/>  [A fejlesztőkörnyezet előkészítése] [ lnk-dev-setup] ismerteti, hogyan telepítse a Node.js-ebben az oktatóanyagban a Windows vagy Linux.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Az eszközön, a közvetlen módszer használatával távoli újraindítás eseményindító
Ebben a szakaszban egy .NET-Konzolalkalmazás (használatával C#) közvetlen metódussal eszköz távoli újraindítást kezdeményezett hoz létre. Az alkalmazás számára az eszköz legutóbbi újraindítás eszköz iker lekérdezések használ.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az új megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Nevet a projektnek **TriggerReboot**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]

2. A Megoldáskezelőben kattintson a jobb gombbal a **TriggerReboot** projektre, és kattintson a **NuGet-csomagok kezelése**.
3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét "Hozzon létre egy IoT-központot." szakaszban létrehozott központ IoT-központ kapcsolati karakterlánccal 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "myDeviceId";
        
6. Adja hozzá a következő metódust a **Program** osztály.  Ezt a kódot az eszköz iker lekérdezi az újraindítás eszköz, és kiírja a jelentésben szereplő tulajdonságok.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Adja hozzá a következő metódust a **Program** osztály.  Ez a kód indít el az újraindítást követően az eszközön, a közvetlen módszer használatával.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. A megoldás felépítéséhez.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ez a szakasz tartalma

* Egy Node.js-konzolalkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol
* A szimulált eszköz újraindítását eseményindító
* A jelentésben szereplő tulajdonságok használatával engedélyezhető az eszköz iker lekérdezések azonosíthatja azokat az eszközöket, és ha tartanak újraindítása után

1. Hozzon létre egy új üres nevű **manageddevice**.  A **manageddevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. Parancsot a parancssorba a a **manageddevice** mappa telepítéséhez a következő parancsot a **azure iot-eszközök** eszköz SDK-csomagot és **azure-iot-eszközök – mqtt** csomag:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy új **dmpatterns_getstarted_device.js** fájlt a **manageddevice** mappa.
4. Adja hozzá a következő "szükséges" utasítások elején a **dmpatterns_getstarted_device.js** fájlt:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le a kapcsolati karakterlánc az eszköz kapcsolati karakterláncot.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Az eszközön a közvetlen módszer végrehajtásához a következő függvény hozzáadása
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Adja hozzá a következő kódot az IoT hub a kapcsolat megnyitásához, és indítsa el a közvetlen módszer figyelő:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Mentse és zárja be a **dmpatterns_getstarted_device.js** fájlt.
   
> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.


## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **manageddevice** mappa, a következő parancsot a rendszer újraindítása közvetlen módszer figyelését.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Futtassa a C# Konzolalkalmazás **TriggerReboot**. Kattintson a jobb gombbal a **TriggerReboot** projektre, válassza **Debug**, majd válassza ki **Start új példány**.

3. A közvetlen módszer a konzolon eszköz válasz megjelenik.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
