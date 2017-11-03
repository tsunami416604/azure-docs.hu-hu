---
title: "Közvetlen Azure IoT Hub-módszerekkel (.NET/csomópont) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub közvetlen módszerek. Az Azure IoT-eszközök SDK for Node.js használatával valósítja meg a szimulált eszköz alkalmazást, amely közvetlen módszer és a Azure IoT szolgáltatást a service-alkalmazást, amely hívja meg a közvetlen módszer végrehajtásához .NET SDK tartalmazza."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: nberdy
ms.openlocfilehash: 76f1d32b4afeacae1488b4cf28be6c8cf7f4ea37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnode"></a>Közvetlen módszerekkel (.NET/csomópont)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Ebben az oktatóanyagban fogjuk a .NET- és egy Node.js-Konzolalkalmazás fejlesztéséhez:

* **CallMethodOnDevice.sln**, .NET-háttér-alkalmazás, amely metódus meghívja a szimulált eszköz alkalmazásban, és a válasz megjeleníti.
* **SimulatedDevice.js**, a Node.js-alkalmazás, amely egy eszköz csatlakozik az IoT hub, korábban létrehozott eszköz identitású szimulálja, és válaszol-e a felhő által meghívott metódus.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k][lnk-hub-sdks] című témakörben talál további információt.
> 
> 

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban hozzon létre egy Node.js-Konzolalkalmazás, amely válaszol a végfelhasználók által a megoldás háttérrendszere nevű metódust.

1. Hozzon létre egy új, **simulateddevice** nevű üres mappát. A **simulateddevice** mappában hozza létre a package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorba a **simulateddevice** mappa telepítéséhez a következő parancsot a **azure iot-eszközök** és **azure-iot-eszközök – mqtt** csomagok:
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztővel, a fájl létrehozása a **simulateddevice** mappa, és adjon neki nevet **SimulatedDevice.js**.
4. Adja hozzá a következő `require` utasításokat a **SimulatedDevice.js** fájl elejéhez:
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Adja hozzá a **connectionString** változó, és hozzon létre egy **DeviceClient** példány. Cserélje le **{eszköz kapcsolati karakterlánc}** az eszköz kapcsolattal karakterlánc Ön hozott létre a *hozzon létre egy eszközidentitás* szakasz:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Adja hozzá a következő függvény megvalósításához a közvetlen módszer az eszközön:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Nyissa meg a kapcsolatot az IoT hub, és a metódus figyelő inicializálása:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Mentse és zárja be a **SimulatedDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban, meg kell valósítania újrapróbálkozási szabályzatok (például újrapróbálkozási), az MSDN-cikkben leírtak [átmeneti hiba kezelése][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>A közvetlen metódus hívása az eszközön
Ebben a szakaszban egy .NET-Konzolalkalmazás, amely egy metódus meghívja a szimulált eszköz alkalmazás és a válasz megjeleníti hoz létre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Nevet a projektnek **CallMethodOnDevice**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10]
2. A Megoldáskezelőben kattintson a jobb gombbal a **CallMethodOnDevice** projektre, és kattintson a **NuGet-csomagok kezelése...** .
3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][11]

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncra.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Adja hozzá a **Program** osztályhoz a következő módszert:
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    Ez a metódus meghívja a közvetlen módszer nevű `writeLine` a a `myDeviceId` eszköz. Ezután ír a választ, a konzolon az eszköz által biztosított. Ne feledje, hogy az eszköz válaszára időtúllépési értéket.
7. Végül adja a következő sorokat a **Main** metódushoz:
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A Visual Studio Solution Explorerben kattintson a jobb gombbal a megoldás, és kattintson **indítási projektek beállítása...** . Válassza ki **egyetlen kezdőprojekt**, majd válassza ki a **CallMethodOnDevice** projekt legördülő menüből.

2. A parancsot a parancssorba a **simulateddevice** mappa megkezdeni a figyelést az IoT Hub a metódushívások a következő parancsot:
   
    ```
    node SimulatedDevice.js
    ```
   Várjon, amíg a szimulált eszköz megnyitásához:![][7]
3. Most, hogy az eszköz csatlakoztatva van, és várakozik a metódus meghívásához, futtassa a .NET **CallMethodOnDevice** app a szimulált eszköz alkalmazás metódus meghívására. Az eszköz válasza a konzol írt kell megjelennie.
   
    ![][8]
4. Az eszköz majd reagál a metódus által nyomtatás ezt az üzenetet:
   
    ![][9]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Ez az eszközazonosító segítségével engedélyezheti a felhő által meghívott módszerek reagálni a szimulált eszköz alkalmazás. Létrehozott egy alkalmazást, amely meghívja a módszerek az eszközön, és megjeleníti az eszköz válaszára is. 

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Ismerkedés az IoT-központ]
* [Több eszközön feladatok ütemezése][lnk-devguide-jobs]

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Ismerkedés az IoT-központ]: iot-hub-node-node-getstarted.md
