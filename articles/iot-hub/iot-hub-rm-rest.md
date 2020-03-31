---
title: Hozzon létre egy Azure IoT-központot a REST API erőforrás-szolgáltató használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az erőforrás-szolgáltató C# REST API-t egy IoT Hub programozott módon történő létrehozásához és kezeléséhez.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c4cb230c9f0b56e3ff9d81e0d85134a7f192e6e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429172"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>IoT-központ létrehozása az erőforrás-szolgáltató REST API (.NET) használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Az [IoT Hub-erőforrás-szolgáltató REST API használatával](https://docs.microsoft.com/rest/api/iothub/iothubresource) programozott módon hozhat létre és kezelhet Azure IoT-központokat. Ez az oktatóanyag bemutatja, hogyan használhatja az IoT Hub-erőforrás-szolgáltató REST API-t egy IoT hub egy C# programból.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* [Azure PowerShell 1.0-s](https://docs.microsoft.com/powershell/azure/install-Az-ps) vagy újabb verzió.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>A Visual Studio-projekt előkészítése

1. A Visual Studio-ban hozzon létre egy Visual C# Windows Classic Desktop projektet a **Console App (.NET Framework)** projektsablon használatával. Nevezze el a **projectet CreateIoTHubREST**.

2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, majd kattintson **a NuGet-csomagok kezelése parancsra.**

3. A NuGet csomagkezelőben jelölje **be az Előzetes kiadás felvétele**jelölőnégyzetet, és a **Tallózás** lapon keresse meg a **Microsoft.Azure.Management.ResourceManager programot.** Jelölje ki a csomagot, kattintson a **Telepítés**gombra a **Módosítások áttekintése** csoportban **kattintson az OK**gombra, majd a licencek elfogadásához kattintson az **Elfogadás** parancsra.

4. A NuGet csomagkezelőben keresse meg a **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kattintson a **Telepítés**gombra a **Módosítások áttekintése** területen kattintson **az OK**gombra, majd a licenc elfogadásához kattintson az **Elfogadom** parancsra.

5. A Program.cs cserélje le a meglévő **ketreneket** a következő kódra:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. A Program.cs adja hozzá a következő statikus változókat a helyőrző értékek helyett. Az oktatóanyag korábbi részében feljegyezte **az ApplicationId**, **SubscriptionId**, **TenantId**és **Password** azonosítót. **Az erőforráscsoport neve** az IoT hub létrehozásakor használt erőforráscsoport neve. Használhat egy már meglévő vagy egy új erőforráscsoportot. **Az IoT Hub neve** a létrehozott IoT Hub neve, például **a MyIoTHub.** Az IoT hub nevének globálisan egyedinek kell lennie. **A központi telepítés neve** a központi telepítés neve, például **Deployment_01.**

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>IoT-központ létrehozása az erőforrás-szolgáltató REST API-jával

Az [IoT Hub-erőforrás-szolgáltató REST API-val](https://docs.microsoft.com/rest/api/iothub/iothubresource) hozzon létre egy IoT hubot az erőforráscsoportban. Az erőforrás-szolgáltató REST API-t is használhatja egy meglévő IoT-központ módosításaihoz.

1. A Program.cs a következő módszerrel egészítse ki:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz. Ez a kód létrehoz egy **HttpClient** objektumot a hitelesítési jogkivonattal a fejlécekben:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz. Ez a kód ismerteti az IoT hub létrehozásához és egy JSON-ábrázolás létrehozásához. Az IoT Hubot támogató helyek aktuális listáját az Azure Állapota című témakörben [tetthet:](https://azure.microsoft.com/status/)

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz. Ez a kód elküldi a REST-kérelmet az Azure-ba. A kód ezután ellenőrzi a választ, és lekéri a telepítési feladat állapotának figyeléséhez használható URL-címet:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Adja hozzá a következő kódot a **CreateIoTHub** metódus végéhez. Ez a kód az előző lépésben beolvasott **asyncStatusUri** címet használja a telepítés befejezésére várva:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adja hozzá a következő kódot a **CreateIoTHub** metódus végéhez. Ez a kód lekéri a létrehozott IoT hub kulcsait, és kinyomtatja őket a konzolra:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Az alkalmazás befejezése és futtatása

Most már befejezheti az alkalmazást a **CreateIoTHub** metódus meghívásával, mielőtt létrehozna és futtatna.

1. Adja hozzá a következő kódot a **fő** módszer végéhez:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Kattintson **a Build,** majd **a Build Solution (Megoldás készítése) elemre.** Javítsa ki a hibákat.

3. Kattintson **a Hibakeresés gombra,** majd az alkalmazás futtatásához indítsa el a **hibakeresést.** A központi telepítés futtatása több percig is eltarthat.

4. Annak ellenőrzéséhez, hogy az alkalmazás hozzáadta-e az új IoT-központot, keresse fel az [Azure Portalt,](https://portal.azure.com/) és tekintse meg az erőforrások listáját. Másik lehetőségként használja a **Get-AzResource** PowerShell parancsmag.

> [!NOTE]
> Ez a példaalkalmazás hozzáad egy S1 standard IoT Hub, amelynek számlázása. Ha elkészült, törölheti az IoT hubot az [Azure Portalon](https://portal.azure.com/) keresztül, vagy az **Eltávolítás-AzResource** PowerShell-parancsmag használatával, ha elkészült.

## <a name="next-steps"></a>További lépések

Most, hogy üzembe helyezett egy IoT-központot az erőforrás-szolgáltató REST API használatával, további vizsgálatokat is érdemes tárnia:

* Az [IoT Hub-erőforrás-szolgáltató REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)képességeiről olvashat.

* Olvassa el [az Azure Resource Manager áttekintését,](../azure-resource-manager/management/overview.md) ha többet szeretne megtudni az Azure Resource Manager képességeiről.

Ha többet szeretne tudni az IoT Hub fejlesztéseiről, olvassa el az alábbi cikkeket:

* [Bevezetés a C SDK-ba](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)