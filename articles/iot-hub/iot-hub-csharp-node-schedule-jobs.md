---
title: "Az Azure IoT Hub (.NET/csomópont) feladatok ütemezése |} Microsoft Docs"
description: "How to Schedule a több eszközre közvetlen metódus egy Azure IoT Hub-feladat ütemezése. Az Azure IoT-eszközök a Node.js SDK használatával a szimulált eszköz alkalmazások és az Azure IoT-szolgáltatás a feladat futtatásához a .NET-hez, a szolgáltatás-alkalmazások végrehajtásához SDK megvalósításához."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: juanpere
ms.openlocfilehash: 1f335b8b04821e1c0ab26af3df20c3318e9c26cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="schedule-and-broadcast-jobs-netnodejs"></a>Ütemezés és a feladatok (.NET/Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub használatával ütemezése, illetve követheti nyomon, hogy az eszközök millióira frissítése feladatok. Feladatok használata:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok

Egy feladat becsomagolja az alábbi műveletek egyikét, és nyomon követi a végrehajtás egy eszközt a két lekérdezést által meghatározott eszközök készlete alapján. Például egy háttér-alkalmazás segítségével egy feladat meghívni egy 10 000 közvetlen metódust, amely az eszköz újraindul. Adjon meg az eszközök eszköz iker lekérdezéssel, és a feladat egy későbbi időpontban futtatásának ütemezését. A feladat nyomon követi előrehaladását, mint a eszközökhöz kapja meg, és a rendszer újraindítása közvetlen metódus végrehajtására.

Az egyes képességek kapcsolatos további információkért lásd:

* A két eszköz és a tulajdonságok: [Ismerkedés az eszköz twins] [ lnk-get-started-twin] és [oktatóanyag: kettős eszköztulajdonságok használata][lnk-twin-props]
* A közvetlen módszer: [IoT Hub fejlesztői útmutató - közvetlen módszerek] [ lnk-dev-methods] és [oktatóanyag: közvetlen módszerekkel][lnk-c2d-methods]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy eszköz-alkalmazást, amely egy közvetlen a hívott metódus **lockDoor** , hogy a háttér-alkalmazás által hívható. Az eszköz alkalmazás is fogad kívánt tulajdonságok módosítását a háttér-alkalmazást.
* Hozzon létre egy háttér-alkalmazást, amely létrehoz egy feladatot, amely hívja az **lockDoor** közvetlen módszer több eszközön. Egy másik feladat kívánt tulajdonság frissítései több eszközre elküldi.

Ez az oktatóanyag végén akkor egy Node.js Konzolalkalmazás eszköz és a .NET (C#) háttér-Konzolalkalmazás:

**simDevice.js** , amely csatlakozik az IoT hub valósít meg a **lockDoor** közvetlen metódust, és kezeli a szükséges tulajdonságok módosítását.

**ScheduleJob** használó feladatok hívni a **lockDoor** közvetlen módszer, és frissítse a eszköztulajdonságok iker szükséges több eszközön.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* A Node.js 4.0.x vagy újabb verziója. A cikk [a fejlesztőkörnyezet előkészítése] [ lnk-dev-setup] ismerteti, hogyan telepítse a Node.js-ebben az oktatóanyagban a Windows vagy Linux.
* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Közvetlen metódus hívása, és az eszköz iker frissítések küldése feladatok ütemezése

Ebben a szakaszban egy .NET-Konzolalkalmazás (használatával C#) használó feladatok hívására hoz létre a **lockDoor** közvetlen módszer és a kívánt tulajdonság frissítések küldése több eszközre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **ScheduleJob**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]

1. A Megoldáskezelőben kattintson a jobb gombbal a **ScheduleJob** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez a lépés tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT szolgáltatás SDK] [ lnk-nuget-service-sdk] NuGet csomag és annak függőségeit.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adja hozzá a következő `using` utasítást, ha az alapértelmezett utasításokban még nincs letöltve.

    ```csharp
    using System.Threading.Tasks;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző az IoT-központ kapcsolati karakterlánc, amely az előző szakaszban létrehozott központ.

    ```csharp
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static JobClient jobClient;
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            "deviceId='myDeviceId'",
            directMethod,
            DateTime.Now,
            10);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        var twin = new Twin();
        twin.Properties.Desired["Building"] = "43";
        twin.Properties.Desired["Floor"] = "3";
        twin.ETag = "*";

        JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
            "deviceId='myDeviceId'",
            twin,
            DateTime.Now,
            10);

        Console.WriteLine("Started Twin Update Job");
    }
    ```

1. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. A Solution Explorerben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **ScheduleJob** projekt **Start**. A megoldás felépítéséhez.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely válaszol a felhőbe, amely elindítja a szimulált eszköz újraindítás és a jelentésben szereplő tulajdonságok segítségével engedélyezése az eszköz iker lekérdezések azonosíthatja azokat az eszközöket, és ha azok utolsó újraindítása után hívja a közvetlen módszer hoz létre.

1. Hozzon létre egy új üres nevű **simDevice**.  Az a **simDevice** mappa, hozzon létre egy package.json fájlt parancsot a parancssorba az alábbi parancs segítségével.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

1. A parancssorba a **simDevice** mappa telepítéséhez a következő parancsot a **azure iot-eszközök** és **azure-iot-eszközök – mqtt** csomagok:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Egy szövegszerkesztő használatával hozzon létre egy új **simDevice.js** fájlt a **simDevice** mappa.

1. Adja hozzá a következő "szükséges" utasítások elején a **simDevice.js** fájlt:

    ```nodejs
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

1. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt. Győződjön meg arról, hogy a helyőrzőket cserélje le a telepítő megfelelő értékeket.

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Adja hozzá a következő függvény kezelni a **lockDoor** metódust.

    ```nodejs
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

1. Az alábbi kódot a kezelőt regisztrálni a **lockDoor** metódust.

    ```nodejs
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```

1. Mentse és zárja be a **simDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **simDevice** mappa, a következő parancsot a rendszer újraindítása közvetlen módszer figyelését.

    ```cmd/sh
    node simDevice.js
    ```

1. Futtassa a C# Konzolalkalmazás **ScheduleJob** kattintson a jobb gombbal a a **ScheduleJob** projekt, jelölje be **Debug** és **Start új példány**.

1. A kimenet az eszköz és a háttér-alkalmazások jelenik meg.

    ![Az alkalmazások ütemezett feladatok futtatása][img-schedulejobs]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy feladat ütemezése a közvetlen módszer egy eszköz és a két eszköz tulajdonságok frissítése használt.

Folytatja, a légkondicionáló frissítést keresztül Ismerkedés az IoT-központ és az eszköz felügyeleti minták például távolról, olvassa el a [oktatóanyag: a belső vezérlőprogram frissítésének menete][lnk-fwupdate].

Ismerkedés az IoT-központ a folytatáshoz tekintse meg a [Ismerkedés az IoT-Edge][lnk-iot-edge].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
