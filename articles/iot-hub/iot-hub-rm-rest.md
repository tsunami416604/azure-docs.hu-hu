---
title: Az erőforrás-szolgáltató REST API használatával az Azure IoT hub létrehozása |} A Microsoft Docs
description: Hogyan hozzon létre egy IoT hubot az erőforrás-szolgáltató REST API használatával.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: d87ca39eb27673be8d5cd0feece3eabe3e214de1
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011092"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Az erőforrás-szolgáltató REST API (.NET) használatával IoT hub létrehozása

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Használhatja a [az IoT Hub erőforrás-szolgáltató REST API-val] [ lnk-rest-api] hozhat létre, és az Azure IoT hubs programozással felügyelheti. Ez az oktatóanyag bemutatja, hogyan hozzon létre egy IoT hubra a C#-program az IoT Hub erőforrás-szolgáltató REST API használatával.

> [!NOTE]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik:  [Az Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk ismerteti az Azure Resource Manager üzemi modell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure PowerShell 1.0] [ lnk-powershell-install] vagy újabb.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>A Visual Studio-projektek előkészítése

1. A Visual Studióban hozzon létre egy Visual C# Windows klasszikus Asztalialkalmazás-projektet az a **Console App (.NET Framework)** projektsablonnal. Adja a projektnek **CreateIoTHubREST**.

2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és kattintson a **NuGet-csomagok kezelése**.

3. Ellenőrizze a NuGet Package Manager **Include prerelease**, és az a **Tallózás** lapon keresse meg **Microsoft.Azure.Management.ResourceManager**. Válassza ki a csomagot, kattintson a **telepítése**, a **változások áttekintése** kattintson **OK**, majd kattintson **elfogadom** fogadására a licenceket.

4. A NuGet-Csomagkezelőt, keressen **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kattintson a **telepítése**, a **változások áttekintése** kattintson **OK**, majd kattintson a **elfogadom** elfogadásához.

5. A program.cs fájlban cserélje le a meglévő **használatával** utasítások a következő kóddal:

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

6. A program.cs fájlban adja hozzá a következő statikus változókat, és cserélje le a helyőrző értékeket. Jegyezze fel végzett **ApplicationId**, **SubscriptionId**, **TenantId**, és **jelszó** Ez az oktatóanyag korábbi. **Erőforráscsoport neve** használhatja az IoT hub létrehozásakor az erőforráscsoport neve. Használhat egy korábban létező vagy új erőforráscsoportot. **Az IoT Hub nevét** hoz létre, mint például az IoT-központ neve **MyIoTHub**. Az IoT hub nevére globálisan egyedinek kell lennie. **A központi telepítés neve** el például egy nevet a központi telepítéshez **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>IoT hub létrehozása az erőforrás-szolgáltató REST API használatával

Használja a [az IoT Hub erőforrás-szolgáltató REST API-val] [ lnk-rest-api] hozhat létre egy IoT hubot az erőforráscsoportban. Az erőforrás-szolgáltató REST API segítségével módosíthatja egy meglévő IoT hubbal.

1. A program.cs fájlhoz adja hozzá a következő metódust:

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

3. Adja hozzá a következő kódot a **CreateIoTHub** metódust. Ez a kód bemutatja az IoT hub létrehozása és hoz létre egy JSON-reprezentációval. A helyek, amelyek támogatják az IoT Hub aktuális listáját lásd: [Azure állapotlapján][lnk-status]:

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

4. Adja hozzá a következő kódot a **CreateIoTHub** metódust. Ez a kód elküldi a REST-kérelmet az Azure-bA. A kód ezután ellenőrzi a választ, és olvassa be az URL-cím segítségével a szolgáltatástelepítési feladat állapotának figyelése:

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

5. Adja hozzá a következő kódot végéhez a **CreateIoTHub** metódust. Ez a kód a **asyncStatusUri** , várjon, amíg az üzembe helyezés befejeződik az előző lépésben lekért címmel:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adja hozzá a következő kódot végéhez a **CreateIoTHub** metódust. Ez a kód lekéri a kulcsok az IoT hub hoztunk létre, és megjeleníti őket a konzolhoz:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Írja be az alkalmazás futtatása

Az alkalmazás meghívásával már végrehajthatók a **CreateIoTHub** módszer előtt felépíti és futtatja azt.

1. Adja hozzá a következő kódot végéhez a **fő** módszer:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Kattintson a **összeállítása** , majd **megoldás**. Javítsa az esetleges hibákat.

3. Kattintson a **Debug** , majd **Start Debugging** az alkalmazás futtatásához. Futtassa az üzembe helyezés több percig is eltarthat.

4. Győződjön meg arról, hogy az alkalmazás hozzáadása az új IoT hubot, látogasson el a [az Azure portal] [ lnk-azure-portal] és erőforrások listájának megtekintése. Másik megoldásként használhatja a **Get-AzResource** PowerShell-parancsmagot.

> [!NOTE]
> Ez a példa az alkalmazás hozzáadása egy S1 Standard IoT hubot, amelyhez számítunk fel díjat. Amikor elkészült, törölheti az IoT hubon keresztül a [az Azure portal] [ lnk-azure-portal] vagy a **Remove-AzResource** PowerShell-parancsmagot, ha elkészült.

## <a name="next-steps"></a>További lépések
Most egy IoT hubra, az erőforrás-szolgáltató REST API használatával telepített, akkor érdemes vizsgálódáshoz:

* Olvassa el a képességeit a [az IoT Hub erőforrás-szolgáltató REST API-val][lnk-rest-api].
* Olvasási [Azure Resource Manager áttekintése] [ lnk-azure-rm-overview] további információ ezekről a képességekről az Azure Resource Manager.

Az IoT Hub fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Bevezetés a C SDK-t][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
