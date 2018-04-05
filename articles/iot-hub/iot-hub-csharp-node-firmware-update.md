---
title: Eszköz belső vezérlőprogram frissítése az Azure IoT Hub (.NET/csomópont) |} Microsoft Docs
description: Hogyan használható az eszközkezelés Azure IoT hub eszköz vezérlőprogram-frissítés kezdeményezése. Az Azure IoT-eszközök SDK for Node.js használatával valósítja meg a szimulált eszköz alkalmazások és az Azure IoT szolgáltatás SDK for .NET egy service-alkalmazást, amely elindítja a belső vezérlőprogram-frissítés végrehajtásához.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.openlocfilehash: e54ba3e3015c7175814c7f4e3330ad2de3819136
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>Eszközkezelés használja indításához eszköz vezérlőprogram-frissítés (.NET/csomópont)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Az a [Ismerkedés az eszközkezelés] [ lnk-dm-getstarted] oktatóanyag, megtudhatta, hogyan használható a [eszköz iker] [ lnk-devtwin] és [közvetlen módszerek ] [ lnk-c2dmethod] primitívek távolról az eszköz újraindítását. Ez az oktatóanyag az ugyanazon az IoT-központ primitívek használ, és bemutatja, hogyan hajtsa végre egy végpontok közötti szimulált belső vezérlőprogram-frissítés.  A belső vezérlőprogram frissítési implementációja szerepel ebben a mintában a [málna Pi eszköz megvalósítási minta][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* A .NET-Konzolalkalmazás, amely behívja a firmwareUpdate közvetlen módszer a szimulált eszköz alkalmazás keresztül az IoT hub létrehozása.
* Hozzon létre egy szimulált eszköz alkalmazást, amely egy **firmwareUpdate** közvetlen módszer. Ez a módszer indít el egy többlépcsős folyamat, amely megvárja-e a belső vezérlőprogram lemezképet letölti, letölti a belső vezérlőprogram lemezképet és a belső vezérlőprogram kép végül vonatkozik. A frissítés egyes szakasza alatt az eszköz használatával a jelentésben szereplő tulajdonságok előrehaladásról.

Ez az oktatóanyag végén akkor egy Node.js Konzolalkalmazás eszköz és a .NET (C#) háttér-Konzolalkalmazás:

**dmpatterns_fwupdate_service.js**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazás jeleníti meg a választ, és rendszeres időközönként (minden 500ms) jeleníti meg a frissített jelenteni tulajdonságait.

**TriggerFWUpdate**, amely kapcsolódik az IoT hub, korábban létrehozott eszköz identitású kap egy firmwareUpdate közvetlen módszer esetén az több államot folyamatot, a belső vezérlőprogram frissítési például szimulálásához: Várakozás a lemezkép letöltése az új lemezkép letöltése, és végül a kép alkalmazása.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* NODE.js-verzió 4.0.x vagy újabb verzióját, <br/>  [A fejlesztőkörnyezet előkészítése] [ lnk-dev-setup] ismerteti, hogyan telepítse a Node.js-ebben az oktatóanyagban a Windows vagy Linux.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Kövesse a [Ismerkedés az eszközkezelés](iot-hub-csharp-node-device-management-get-started.md) cikk az IoT hub létrehozása, és az IoT-központ kapcsolati karakterláncot.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Indítás, az eszközön, a közvetlen módszer használatával távoli vezérlőprogram-frissítés
Ebben a szakaszban egy .NET-Konzolalkalmazás (használatával C#), amely indít el egy távoli belső vezérlőprogram frissítése egy eszközön hoz létre. Az alkalmazás közvetlen módszer előtt használja a frissítés, és az eszköz iker lekérdezések rendszeres időközönként a aktív belső vezérlőprogram frissítése a állapot lekérdezése céljából.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **TriggerFWUpdate**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]

1. A Megoldáskezelőben kattintson a jobb gombbal a **TriggerFWUpdate** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a több helyőrző értékeket az előző szakaszban és az eszköz azonosítója a központ IoT-központ kapcsolati karakterlánccal.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. Adja hozzá a **Program** osztályhoz a következő módszert:
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. Adja hozzá a **Program** osztályhoz a következő módszert:

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

1. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. A Solution Explorerben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **TriggerFWUpdate** projekt **Start**.

1. Hozza létre a megoldást.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **manageddevice** mappa, a következő parancsot a rendszer újraindítása közvetlen módszer figyelését.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. A Visual Studióban, kattintson a jobb gombbal a a **TriggerFWUpdate** projektre, válassza **Debug** és **Start új példány**.

3. A közvetlen módszer a konzolon eszköz válasz megjelenik.

    ![Belső vezérlőprogram frissítése sikeres volt][img-fwupdate]

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag elindítása egy távoli belső vezérlőprogram frissítése egy eszközön a közvetlen módszer használatával, és a jelentésben szereplő tulajdonságok segítségével nyomon követheti a belső vezérlőprogram frissítése.

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/