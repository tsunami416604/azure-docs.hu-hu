---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883738"
---
## <a name="create-a-module-identity"></a>Modulidentitás létrehozása

Ebben a szakaszban egy .NET konzolalkalmazást hoz létre, amely létrehoz egy eszközidentitást és egy modulidentitást a hub identitás-beállításjegyzékében. Egy eszköz vagy modul csak akkor tud csatlakozni a hubhoz, ha rendelkezik bejegyzéssel az identitásjegyzékben. További információt az [IoT Hub fejlesztői útmutatójának Identitás-beállításjegyzék című részében talál.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)

A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Az eszköz és a modul ezeket az értékeket használja, hogy azonosítsa magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az azonosítók megkülönböztetik a kis- és nagybetűket.

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.

1. Az **Új projekt létrehozása**csoportban válassza a Console App **(.NET Framework) lehetőséget.**

1. Válassza a **Tovább** gombot **az Új projekt konfigurálása**lehetőséget. Adja a projektnek a *CreateIdentities* nevet, a megoldásnak pedig az *IoTHubGetStarted* nevet. A Microsoft .NET-keretrendszer 4.6.1-es vagy újabb verzióját használja.

    ![A Visual Studio-megoldás nevének és keretrendszerének megadása](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. A Visual Studióban nyissa meg az **Eszközök** > **NuGet csomagkezelő** > **a Megoldáshoz csomagkezelése segédprogramot.** Válassza ki a **Browse** (Tallózás) lapot.

1. Keressen a **Microsoft.Azure.Devices**. Jelölje ki, majd válassza a **Telepítés**lehetőséget.

    ![Az Azure IoT Hub .NET szolgáltatás SDK aktuális verziójának telepítése](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati sztringre.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Adja hozzá a **Main** következő kódot a főosztályhoz.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Adja hozzá a **Program** osztályhoz a következő metódusokat:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    A `AddDeviceAsync` metódus létrehoz egy eszközidentitást **myD myFirstDevice**azonosítóval. Ha az eszközazonosító már létezik az identitásjegyzékben, a kód egyszerűen lekéri a meglévő eszközadatokat. Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált eszközalkalmazásban használhatja a hubhoz való csatlakozáshoz.

    A `AddModuleAsync` metódus létrehoz egy modul identitási azonosítót **myFirstModule** eszköz alatt **myFirstDevice**. Ha a modulazonosító már létezik az identitásjegyzékben, a kód egyszerűen beolvassa a meglévő moduladatokat. Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált modulalkalmazásban használhatja a hubhoz való csatlakozáshoz.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Futtassa ezt az alkalmazást, és jegyezze fel az eszközkulcsot és a modulkulcsot.

> [!NOTE]
> Az IoT Hub identitásbeállítási beállítása csak tárolja az eszköz és a modul identitások biztonságos hozzáférést biztosít a hubhoz. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információ: [IoT Hub fejlesztői útmutató.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)
