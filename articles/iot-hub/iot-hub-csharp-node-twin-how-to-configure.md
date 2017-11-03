---
title: "Azure IoT Hub eszköz iker tulajdonságait (.NET/csomópont) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub eszköz twins eszközök konfigurálásához. Az Azure IoT-eszközök SDK for Node.js használatával valósítja meg a szimulált eszköz alkalmazások és az Azure IoT szolgáltatás SDK for .NET egy szolgáltatás-alkalmazást, amely módosítja a használatával egy eszközt a két eszköz konfigurációs végrehajtásához."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.openlocfilehash: 8f9626fc47e7d32cb104b960bea9b7de9efa6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Használni kívánt tulajdonságokat eszközök konfigurálása
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Ez az oktatóanyag végén hogy két konzol alkalmazások:

* **SimulateDeviceConfiguration.js**, a szimulált eszköz alkalmazás, amely megvárja-e a szükséges konfiguráció frissítése a jelentést készít egy szimulált konfigurációs frissítési folyamat állapotát.
* **SetDesiredConfigurationAndQuery**, .NET-háttér-alkalmazás, amely beállítja a kívánt konfiguráció egy eszközön, és lekérdezi a konfigurációs frissítési folyamat.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].

Ha követte a [Ismerkedés az eszköz twins] [ lnk-twin-tutorial] oktatóanyagban már rendelkezik egy IoT-központot, és egy eszközidentitás nevű **myDeviceId**. Ebben az esetben ugorjon a [a szimulált eszköz-alkalmazás létrehozása] [ lnk-how-to-configure-createapp] szakasz.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>A szimulált eszköz-alkalmazás létrehozása
Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely kapcsolódik a hub, létrehozhat **myDeviceId**megvárja-e a szükséges konfiguráció frissítése a, majd jelentést készít a frissítések szimulált konfigurációs frissítési folyamat.

1. Hozzon létre egy új üres nevű **simulatedeviceconfiguration**. Az a **simulatedeviceconfiguration** mappa, hozzon létre egy új package.json fájlt parancsot a parancssorba az alábbi parancs segítségével. Fogadja el az alapértelmezett beállításokat.
   
    ```
    npm init
    ```
1. A parancssorba a **simulatedeviceconfiguration** mappa telepítéséhez a következő parancsot a **azure iot-eszközök** és **azure-iot-eszközök – mqtt** csomagok:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Egy szövegszerkesztő használatával hozzon létre egy új **SimulateDeviceConfiguration.js** fájlt a **simulatedeviceconfiguration** mappa.
1. Adja hozzá a következő kódot a **SimulateDeviceConfiguration.js** fájlt, és lecserélni a **{eszköz kapcsolati karakterlánc}** helyőrző az eszköz kapcsolati karakterlánccal létrehozása után másolja a **myDeviceId** eszközidentitás:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    A **ügyfél** vezérlőnek az eszközről eszköz twins együttműködhet szükséges összes módszert. Ez a kód inicializálja a **ügyfél** objektumazonosító, beolvassa az eszköz iker a **myDeviceId**, és a frissítés kezelőjét a *tulajdonságok szükséges*. A kezelő ellenőrzi, hogy egy tényleges Helykonfiguráció-változtatási kérelem a configIds összehasonlításával, akkor hív meg, olyan módszer, amelyik elindul a konfigurációs módosítást.
   
    Vegye figyelembe, hogy az egyszerűség kedvéért, ez a kód egy kódolt alapértelmezett értéket használja, a kezdeti konfiguráció. Egy valós alkalmazás valószínűleg szeretné, hogy a konfigurálás betöltése a helyi tárterület.
   
   > [!IMPORTANT]
   > Kívánt tulajdonság állapotváltozási események mindig egyszer kibocsátott eszköz csatlakozáskor. Győződjön meg arról, hogy ellenőrizze, hogy van-e egy tényleges módosítása a kívánt tulajdonságaiban bármilyen művelet végrehajtása előtt.
   > 
   > 
1. Adja hozzá a következő metódusokat előtt a `client.open()` hívása:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    A **initConfigChange** módszer a jelentett a helyi eszközön a két objektum tulajdonságainak frissítése a konfigurációs frissítési kérelmet az állapota, **függőben lévő**, majd frissíti az eszköz a két szolgáltatás. Miután sikeresen frissített a eszköz iker, egy hosszú ideig tartó folyamatot, amely végrehajtása során szimulálja **completeConfigChange**. Ez a módszer az állapot helyi jelentett tulajdonságainak frissítése **sikeres** és eltávolítása a **pendingConfig** objektum. Ezután frissíti az eszköz a két szolgáltatás.
   
    Megjegyzés: a, hogy a sávszélességet, hogy csak a módosítani kívánt tulajdonságok megadásával tulajdonságának frissítésekor (nevű **javítás** a fenti kódban), a teljes dokumentum felülírása helyett.
   
   > [!NOTE]
   > Ez az oktatóanyag nem szimulálása egyidejű keresni minden olyan esetben. Néhány konfigurációs frissítési folyamat közben fut-e a frissítés, néhány lehet a várólistába helyezni őket, és néhány sikerült hibát elutasítása a célként megadott konfigurációs módosítások befogadásához lehet. Ügyeljen arra, hogy fontolja meg a kívánt viselkedés, a konfigurációs folyamat, és adja hozzá a megfelelő logikai kezdeményezése a konfiguráció módosítása előtt.
   > 
   > 
1. Az eszköz alkalmazás futtatása:
   
        node SimulateDeviceConfiguration.js
   
    Az üzenet `retrieved device twin`. Tartsa meg az alkalmazás futását.

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása
Ebben a szakaszban egy .NET-Konzolalkalmazás, amely frissíti hoz létre a *szükséges tulajdonságok* meg az eszköz a két társított **myDeviceId** új telemetriai configuration objektummal. Ezután lekérdezi az eszköz twins az IoT hub tárolja, és a kívánt és jelentett konfigurációkat, az eszköz közötti különbséget szemlélteti.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **SetDesiredConfigurationAndQuery**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]
1. A Megoldáskezelőben kattintson a jobb gombbal a **SetDesiredConfigurationAndQuery** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncra.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adja hozzá a **Program** osztályhoz a következő módszert:
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    A **beállításjegyzék** vezérlőnek eszköz twins a szolgáltatás együttműködhet szükséges összes módszert. Ezzel a kóddal inicializálja a **beállításjegyzék** objektumazonosító, beolvassa az eszköz iker a **myDeviceId**, majd frissíti a kívánt tulajdonságát egy új telemetriai configuration objektummal.
    Ezt követően azt lekérdezi az eszköz twins tárolja az IoT hub 10 másodpercenként, és kiírja a kívánt és jelentett telemetriai konfigurációkat. Tekintse meg a [IoT-központ lekérdezési nyelv] [ lnk-query] további információt az eszközök közötti hatékony jelentések létrehozásához.
   
   > [!IMPORTANT]
   > Ez az alkalmazás lekérdezi az IoT-központ 10 másodpercenként szemléltetési célokat szolgál. Lekérdezésekkel számos eszközön keresztül a felhasználók számára is elérhető jelentések létrehozásához, és nem észleli a módosításokat. Ha a megoldás a valós idejű értesítések eszköz események van szüksége, [iker értesítések][lnk-twin-notifications].
   > 
   > 
1. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. A Solution Explorerben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **SetDesiredConfigurationAndQuery** projekt **Start**. A megoldás felépítéséhez.
1. A **SimulateDeviceConfiguration.js** a .NET-alkalmazás fut, futtassa a Visual Studio használatával **F5** és megjelenítheti a jelentésben szereplő konfigurációs módosítást a **sikeres** való **függőben lévő** való **sikeres** újra az új aktív küldés 24 óra helyett öt perces gyakoriságot.

 ![Eszköz sikeresen konfigurálva][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Nincs késleltetést legfeljebb egy percet, az eszköz jelentés művelet és a lekérdezési eredmények között. Ez a nagyon nagy méretekben működéséhez a lekérdezés infrastruktúra engedélyezéséhez. Egy egyetlen eszközt iker használati konzisztens nézetek beolvasása a **getDeviceTwin** metódust a **beállításjegyzék** osztály.
   > 
   > 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy szabványoskonfiguráció mint beállítása *szükséges tulajdonságok* a megoldásban való háttér, és egy eszköz alkalmazás észleli a változást, és egy többlépéses frissítési folyamat állapotának jelentett tulajdonságai reporting szimulálása megírt.

A következő források segítségével megtudhatja, hogyan:

* telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ] [ lnk-iothub-getstarted] oktatóanyagban
* ütemezett vagy műveleteket hajtson végre a nagy mennyiségű eszközök lásd: a [ütemezés és a szórásos feladatok] [ lnk-schedule-jobs] oktatóanyag.
* az interaktív (például bekapcsolásával a felhasználó által felügyelt alkalmazásból ventilátor), eszközök szabályozásának a [közvetlen módszerekkel] [ lnk-methods-tutorial] oktatóanyag.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app
