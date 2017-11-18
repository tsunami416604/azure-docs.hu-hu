---
title: "Az erőforrás-szolgáltató REST API-t használó Azure IoT hub létrehozása |} Microsoft Docs"
description: "Hogyan lehet az erőforrás-szolgáltató REST API segítségével létrehoz egy IoT-központot."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 52814ee5-bc10-4abe-9eb2-f8973096c2d8
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: d9372f8345257c45ae6b3b915383788f698a0e35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Létrehoz egy IoT-központot, az erőforrás-szolgáltató REST API (.NET) használata

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Használhatja a [IoT-központ erőforrás-szolgáltató REST API] [ lnk-rest-api] létrehozásához és kezeléséhez programozott módon Azure IoT-központok. Ez az oktatóanyag bemutatja, hogyan IoT hub létrehozása a C# programban az IoT-központ erőforrás-szolgáltató REST API használatával.

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk az Azure Resource Manager telepítési modell használatát bemutatja.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure PowerShell 1.0] [ lnk-powershell-install] vagy újabb.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Készítse elő a Visual Studio-projekt

1. A Visual Studio project using Visual C# klasszikus Windows asztal létrehozása a **Konzolalkalmazás (.NET-keretrendszer)** projektsablon. Nevet a projektnek **CreateIoTHubREST**.

2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és kattintson a **NuGet-csomagok kezelése**.

3. Ellenőrizze a NuGet Package Manager **Include prerelease**, és a a **Tallózás** lapon keresse meg **Microsoft.Azure.Management.ResourceManager**. Válassza ki a csomagot, kattintson a **telepítése**, a **változások áttekintése** kattintson **OK**, majd kattintson a **elfogadom** fogadására a licencek.

4. Keressen rá a NuGet Package Manager **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kattintson a **telepítése**, a **változások áttekintése** kattintson **OK**, majd kattintson a **elfogadom** elfogadja a licencfeltételeket.

5. A productscontract.cs fájlban cserélje le a meglévő **használatával** utasítások a következő kóddal:

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

6. A program.cs fájlban adja hozzá a következő statikus változó a helyőrző értékeket. A feljegyezte **ApplicationId**, **SubscriptionId**, **TenantId**, és **jelszó** az oktatóanyag korábbi. **Az erőforráscsoport neve** az IoT hub létrehozása során használ az erőforráscsoport neve. Használhat egy korábban létező vagy egy új erőforráscsoportot. **IoT Hub name** hoz létre, mint például az IoT-központ neve **MyIoTHub**. Az IoT hub nevét globálisan egyedinek kell lennie. **A központi telepítés neve** van, mint a központi telepítés nevét **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Az erőforrás-szolgáltató REST API használatával létrehoz egy IoT-központot

Használja a [IoT-központ erőforrás-szolgáltató REST API] [ lnk-rest-api] az IoT-központ létrehozásához az erőforráscsoportban. Az erőforrás-szolgáltató REST API segítségével módosíthatja egy meglévő IoT-központot.

1. Adja hozzá a következő metódust a program.cs fájlt:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adja hozzá a következő kódot a **CreateIoTHub** metódust. Ez a kód létrehoz egy **HttpClient** a hitelesítési jogkivonat a fejlécekben objektum:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adja hozzá a következő kódot a **CreateIoTHub** metódust. Ez a kód ismerteti az IoT hub létrehozása, és létrehoz egy JSON-megjelenítés. A helyek, amelyek támogatják az IoT-központ aktuális listáját lásd: [Azure állapot][lnk-status]:

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

4. Adja hozzá a következő kódot a **CreateIoTHub** metódust. Ez a kód elküldi a REST-kérelmet az Azure-bA. A kód majd ellenőrzi a választ, és lekéri az URL-cím segítségével a központi telepítés a feladat állapotának figyelése:

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

5. Adja hozzá a következő kódot végén a **CreateIoTHub** metódust. Ezt a kódot használja a **asyncStatusUri** történő várakozás a telepítés befejezéséhez az előző lépésben beolvasott cím:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adja hozzá a következő kódot végén a **CreateIoTHub** metódust. Ez a kód lekéri az IoT hub létrehozása és a konzolablakba írja őket a konzol kulcsának:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Végezze el, és futtassa az alkalmazást

Meghívásával hajthatja végre az alkalmazás most már a **CreateIoTHub** metódus előtt most felépíti és futtatja azt.

1. Adja hozzá a következő kódot végén a **fő** módszert:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Kattintson a **Build** , majd **megoldás**. Javíthatja az esetleges hibákat.

3. Kattintson a **Debug** , majd **Start Debugging** az alkalmazás futtatásához. A központi telepítés futtatása több percig is eltarthat.

4. Győződjön meg arról, hogy az alkalmazás fel van véve az új IoT hub, látogasson el a [Azure-portálon] [ lnk-azure-portal] és tekintse meg az erőforrások listáját. Másik megoldásként használhatja a **Get-AzureRmResource** PowerShell-parancsmagot.

> [!NOTE]
> A mintaalkalmazás ad hozzá egy S1 Standard IoT-központot, amelynek kell fizetni. Ha elkészült, az IoT hub használatával törölheti a [Azure-portálon] [ lnk-azure-portal] vagy használatával a **Remove-AzureRmResource** PowerShell-parancsmag, amikor elkészült.

## <a name="next-steps"></a>Következő lépések
Most telepítette az IoT-központ az erőforrás-szolgáltató REST API-t használ, érdemes lehet további felfedezése:

* Olvassa el a képességeit a [IoT-központ erőforrás-szolgáltató REST API][lnk-rest-api].
* Olvasási [Azure Resource Manager áttekintése] [ lnk-azure-rm-overview] tudhat meg többet az Azure Resource Manager képességeit.

Az IoT-központ fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [C SDK bemutatása][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Az IoT-központ képességeit további megismeréséhez lásd:

* [Az Azure IoT peremhálózati peremeszközök AI központi telepítése][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
