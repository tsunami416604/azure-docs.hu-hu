---
title: "Közvetlen Azure IoT Hub-módszerekkel (.NET/.NET) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub közvetlen módszerek. Az Azure IoT-eszközök a .NET SDK használatával valósítja meg a szimulált eszköz alkalmazást, amely közvetlen módszer és a Azure IoT szolgáltatást a service-alkalmazást, amely hívja meg a közvetlen módszer végrehajtásához .NET SDK tartalmazza."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: dkshir
ms.openlocfilehash: 9ce1fbebb6417c10618aa182e3c1d9ddf8132fb6
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2017
---
# <a name="use-direct-methods-netnet"></a>Közvetlen módszerekkel (.NET/.NET)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Ebben az oktatóanyagban fogjuk két .NET konzol alkalmazások fejlesztéséhez:

* **CallMethodOnDevice**, egy háttér-alkalmazást, amely metódus meghívja a szimulált eszköz alkalmazásban, és a válasz megjeleníti.
* **SimulateDeviceMethods**, korábban létrehozott egy konzolalkalmazás szimulálja egy eszköz csatlakoztatása az IoT hub eszköz azonosítóját és a felhő által meghívott metódus megválaszolja.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k][lnk-hub-sdks] című témakörben talál további információt.
> 
> 

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Ha szeretne létrehozni az eszközidentitást programozott módon helyette, olvassa el a megfelelő részt a [a szimulált eszköz csatlakoztatása az IoT hub .NET használatával] [ lnk-device-identity-csharp] cikk.


## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy .NET-Konzolalkalmazás, amely válaszol a végfelhasználók által a megoldás háttérrendszere nevű metódust hoz létre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **SimulateDeviceMethods**.
   
    ![Új Visual C# klasszikus Windows-eszköz alkalmazás][img-createdeviceapp]
    
1. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateDeviceMethods** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT-eszközök SDK] [ lnk-nuget-client-sdk] NuGet csomag és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az előző szakaszban feljegyzett eszköz kapcsolati karakterlánccal.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Adja hozzá a következőt valósítja meg a közvetlen az eszközön:

        static Task<MethodResponse> WriteLineToConsole(MethodRequest methodRequest, object userContext)
        {
            Console.WriteLine();
            Console.WriteLine("\t{0}", methodRequest.DataAsJson);
            Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

            string result = "'Input was written to log.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

1. Végül adja hozzá az alábbi kódot a **fő** módszert, nyissa meg a kapcsolatot az IoT hub és a metódus figyelő inicializálása:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "writeLine" method
            Client.SetMethodHandlerAsync("writeLine", WriteLineToConsole, null).Wait();
            Console.WriteLine("Waiting for direct method call\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "writeLine" handler
            Client.SetMethodHandlerAsync("writeLine", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
1. A Visual Studio Solution Explorerben kattintson a jobb gombbal a megoldás, és kattintson **indítási projektek beállítása...** . Válassza ki **egyetlen kezdőprojekt**, majd válassza ki a **SimulateDeviceMethods** projekt legördülő menüből.        

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban, meg kell valósítania újrapróbálkozási szabályzatok (például újrapróbálkozási), az MSDN-cikkben leírtak [átmeneti hiba kezelése][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>A közvetlen metódus hívása az eszközön
Ebben a szakaszban egy .NET-Konzolalkalmazás, amely egy metódus meghívja a szimulált eszköz alkalmazás és a válasz megjeleníti hoz létre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Nevet a projektnek **CallMethodOnDevice**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createserviceapp]
2. A Megoldáskezelőben kattintson a jobb gombbal a **CallMethodOnDevice** projektre, és kattintson a **NuGet-csomagok kezelése...** .
3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]

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

1. A Visual Studio Solution Explorerben kattintson a jobb gombbal a megoldás, és kattintson **indítási projektek beállítása...** . Válassza ki **egyetlen kezdőprojekt**, majd válassza ki a **CallMethodOnDevice** projekt legördülő menüből.

## <a name="run-the-applications"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A .NET-eszköz alkalmazás futtatása **SimulateDeviceMethods**. Akkor kell megkezdeni a figyelést az IoT Hub a metódushívások: 

    ![Eszköz-alkalmazás futtatása][img-deviceapprun]
1. Most, hogy az eszköz csatlakoztatva van, és várakozik a metódus meghívásához, futtassa a .NET **CallMethodOnDevice** app a szimulált eszköz alkalmazás metódus meghívására. Az eszköz válasza a konzol írt kell megjelennie.
   
    ![Szolgáltatás-alkalmazás futtatása][img-serviceapprun]
1. Az eszköz majd reagál a metódus által nyomtatás ezt az üzenetet:
   
    ![Az eszközön meghívott közvetlen módszer][img-directmethodinvoked]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Ez az eszközazonosító segítségével engedélyezheti a felhő által meghívott módszerek reagálni a szimulált eszköz alkalmazás. Létrehozott egy alkalmazást, amely meghívja a módszerek az eszközön, és megjeleníti az eszköz válaszára is. 

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Ismerkedés az IoT-központ]
* [Több eszközön feladatok ütemezése][lnk-devguide-jobs]

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

<!-- Images. -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-direct-methods/device-app-nuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-service-app.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-direct-methods/service-app-nuget.png
[img-deviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-device-app.png
[img-serviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-service-app.png
[img-directmethodinvoked]: ./media/iot-hub-csharp-csharp-direct-methods/direct-method-invoked.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[Ismerkedés az IoT-központ]: iot-hub-node-node-getstarted.md
