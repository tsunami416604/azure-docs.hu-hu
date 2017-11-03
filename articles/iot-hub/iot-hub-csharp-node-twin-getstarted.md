---
title: "Ismerkedés az Azure IoT Hub eszköz twins (.NET/csomópont) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub eszköz twins címkéket, majd az IoT Hub-lekérdezést. Az Azure IoT-eszközök SDK for Node.js használatával valósítja meg a szimulált eszköz alkalmazás és az Azure IoT szolgáltatás SDK for .NET egy szolgáltatás-alkalmazást, amely hozzáadja a címkéket és az IoT Hub-lekérdezés futtatása végrehajtásához."
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: elioda
ms.openlocfilehash: 4cf607e8e0ccd3aab06be54d715c2bf3777caeb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-netnode"></a>Ismerkedés az eszköz twins (.NET/csomópont)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ez az oktatóanyag végén hogy a .NET- és egy Node.js-Konzolalkalmazás:

* **AddTagsAndQuery.sln**, a .NET-háttér-alkalmazás, amely címkét ad hozzá, és lekérdezi az eszköz twins.
* **TwinSimulatedDevice.js**, a Node.js-alkalmazás, amely egy eszköz, amely összeköti az IoT hub korábban létrehozott eszköz identitású szimulálja, és jelenti a kapcsolat állapotát.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása
Ebben a szakaszban egy .NET-Konzolalkalmazás (használatával C#) a társított eszközök a két hely metaadatok hozzáadó létrehozása **myDeviceId**. Ezután lekérdezi az eszköz twins tárolja az IoT hub, az eszközök az Egyesült Államok, és a gazdarendszerhez a mobilhálózat kapcsolat jelentett kiválasztása.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **AddTagsAndQuery**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]
1. A Megoldáskezelőben kattintson a jobb gombbal a **AddTagsAndQuery** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncra.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adja hozzá a **Program** osztályhoz a következő módszert:
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    A **RegistryManager** osztály eszköz twins a szolgáltatás együttműködhet szükséges összes módszert mutatja. Az előző kód először inicializálja a **registryManager** objektumot, majd beolvassa az eszköz iker a **myDeviceId**, és végül frissíti a címkék a kívánt helyre információkkal.
   
    Miután frissített, két lekérdezést hajt végre: az első csak az eszköz twins található eszközök kiválasztja a **Redmond43** gépek és a második rendszerint a lekérdezést csak azokat az eszközöket is keresztül mobilhálózati kapcsolódó kiválasztásához.
   
    Vegye figyelembe, hogy az előző kód, amikor létrehozza a **lekérdezés** objektumazonosító, a visszaadott dokumentumok maximális számát határozza meg. A **lekérdezés** objektum tartalmaz egy **HasMoreResults** logikai tulajdonság, amely segítségével meghívni a **GetNextAsTwinAsync** módszerek több alkalommal fordult elő az összes eredmények beolvasásához. A metódus hívása **GetNextAsJson** eredmények, amelyek például nem eszköz twins, összesítési-lekérdezések eredményének érhető el.
1. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. A Solution Explorerben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **AddTagsAndQuery** projekt **Start**. A megoldás felépítéséhez.
1. Az alkalmazás futtatásához a jobb gombbal a **AddTagsAndQuery** projektet, majd válassza **Debug**, utána pedig **Start új példányt**. Megjelenik az eredmények között egy eszközön a lekérdezés kérni a minden eszköz a mappában lévő **Redmond43** és a lekérdezés, amely korlátozza az eredmények mobilhálózati használó eszközök sem.
   
    ![Lekérdezés eredményei ablakban][img-addtagapp]

A következő szakaszban hozzon létre egy eszköz alkalmazást, amely a kapcsolódási adatokat, és módosítja az előző szakaszban a lekérdezés eredménye.

## <a name="create-the-device-app"></a>Az eszköz-alkalmazás létrehozása
Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely kapcsolódik a hub, létrehozhat **myDeviceId**, majd frissíti a jelentett tulajdonságait, hogy mobilhálózat használata csatlakozik a információkat tartalmazzák.

1. Hozzon létre egy új üres nevű **reportconnectivity**. Az a **reportconnectivity** mappa, hozzon létre egy új package.json fájlt parancsot a parancssorba az alábbi parancs segítségével. Fogadja el az alapértelmezett beállításokat.
   
    ```
    npm init
    ```
1. A parancssorba a **reportconnectivity** mappa telepítéséhez a következő parancsot a **azure iot-eszközök**, és **azure-iot-eszközök – mqtt** csomag:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Egy szövegszerkesztő használatával hozzon létre egy új **ReportConnectivity.js** fájlt a **reportconnectivity** mappa.
1. Adja hozzá a következő kódot a **ReportConnectivity.js** fájlt, és az eszköz kapcsolati karakterlánc létrehozása után másolt helyőrző helyettesítse a **myDeviceId** eszközidentitás:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    A **ügyfél** vezérlőnek az eszköz twins az eszközről interaktív szükséges összes módszert. Az előző kód után állíthatja a **ügyfél** objektumazonosító, beolvassa az eszköz iker a **myDeviceId** , és frissíti a jelentett tulajdonsága a kapcsolati információ.
1. Az eszköz alkalmazás futtatása
   
        node ReportConnectivity.js
   
    Az üzenet `twin state reported`.
1. Most, hogy az eszköz jelentette a kapcsolat adatait, akkor mindkét lekérdezések meg kell jelennie. Futtassa a .NET **AddTagsAndQuery** alkalmazásnak, hogy futtassa újból a lekérdezést. Most **myDeviceId** mindkét lekérdezési eredmények jelenjenek meg.
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Fel van véve eszköz metaadatait címkék egy háttér-alkalmazásból, és a szimulált eszköz alkalmazásának megírt az eszköz a két jelentés eszköz kapcsolódási adatok. Megtudta, ezt az információt az SQL-szerű IoT Hub lekérdezési nyelv lekérdezése is.

A következő források segítségével megtudhatja, hogyan:

* telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ] [ lnk-iothub-getstarted] oktatóanyagban
* eszköz iker kívánt tulajdonságokkal rendelkező eszközök konfigurálása a [használata szükséges eszközök tulajdonságok] [ lnk-twin-how-to-configure] oktatóanyagban
* az interaktív (például egy felhasználó által felügyelt alkalmazásból ventilátor bekapcsolása) eszközeinek vezérléséhez a [közvetlen módszerekkel] [ lnk-methods-tutorial] oktatóanyag.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

