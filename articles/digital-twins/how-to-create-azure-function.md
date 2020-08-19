---
title: Azure-függvény beállítása az adatok feldolgozásához
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhat létre olyan Azure-függvényt, amely hozzáférhet a digitális ikrekhöz, és aktiválhatja azokat.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d9f9957209c6df91185059085f57636a16a3961c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589401"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Azure Functions alkalmazások összekötése az adatfeldolgozáshoz

Az [**előzetes verzióban**](concepts-route-events.md) az adatok alapján a digitális ikrek frissítése a számítási erőforrásokon keresztül történik, például [Azure functions](../azure-functions/functions-overview.md). Lehetséges, hogy egy Azure-függvény a következőre válaszul egy digitális Twin-et frissít:
* az eszköz telemetria érkező adatok IoT Hub
* a tulajdonság változása vagy más, a Twin gráfon belüli digitális Twin-ből származó adatok

Ez a cikk bemutatja, hogyan hozhat létre Azure-függvényeket az Azure Digital Twins szolgáltatással való használatra. 

Az alábbiakban áttekintheti a benne található lépéseket:

1. Azure Functions-alkalmazás létrehozása a Visual Studióban
2. Azure-függvény írása [Event Grid](../event-grid/overview.md) triggerrel
3. Hitelesítési kód hozzáadása a függvényhez (az Azure digitális Twins eléréséhez)
4. A Function alkalmazás közzététele az Azure-ban
5. [Biztonsági](concepts-security.md) hozzáférés beállítása. Ahhoz, hogy az Azure-függvény futásidőben hozzáférési tokent kapjon a szolgáltatás eléréséhez, konfigurálnia kell Managed Service Identity a Function alkalmazáshoz.

A cikk hátralévő része az Azure Function telepítési lépéseit mutatja be egyszerre.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Azure Functions-alkalmazás létrehozása a Visual Studióban

A Visual Studio 2019-ben válassza a *fájl > új projekt*lehetőséget. Keresse meg a *Azure functions* sablont, válassza ki, majd kattintson a "tovább" gombra.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: új projekt párbeszédpanel":::

Adja meg a Function alkalmazás nevét, majd kattintson a "létrehozás" gombra.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: projekt konfigurálása párbeszédpanel":::

Válassza ki a *Event Grid triggert* , majd kattintson a "létrehozás" gombra.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: Azure Function Project trigger párbeszédpanel":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Azure-függvény írása Event Grid triggerrel

A következő kód egy rövid Azure-függvényt hoz létre, amely az események naplózására használható: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Ez az Ön alapszintű Azure-függvénye.

### <a name="run-and-debug-the-azure-function-app"></a>Az Azure Function alkalmazás futtatása és hibakeresése

Most már lefordíthatja és futtathatja a függvényt. Habár az Azure functions végül a felhőben fut, az Azure functions-t helyileg is futtathatja és hibakeresést végezhet.

További információ erről: [*Event Grid trigger helyi hibakeresése*](../azure-functions/functions-debug-event-grid-trigger-local.md).

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Az Azure Digital Twins SDK hozzáadása az Azure Function-alkalmazáshoz

A Function app a [.net-hez készült Azure IoT Digital Twin ügyféloldali kódtár (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)használatával kommunikál az Azure Digital Twins szolgáltatással. Az SDK használatához a következő csomagokat kell tartalmaznia a projektben:
* `Azure.DigitalTwins.Core` (verzió `1.0.0-preview.2` )
* `Azure.Identity` (verzió `1.1.1` )

Az Azure SDK-folyamat konfigurálásához a Azure Functions megfelelő beállításához a következőkre is szüksége lesz:
* `Azure.Net.Http`
* `Azure.Core`

A választott eszközöktől függően a Visual Studio Package Managerrel vagy a parancssori eszközzel teheti meg `dotnet` . 

Adja hozzá a következő using utasításokat az Azure-függvényhez.

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Hitelesítési kód hozzáadása az Azure-függvényhez

Ezután adja hozzá a hitelesítési kódot, amely lehetővé teszi a függvény számára az Azure digitális Twins elérését.

Adjon hozzá változókat a Function osztályhoz az alábbi értékekhez: 
* Az Azure digitális Twins-alkalmazás azonosítója
* Az Azure Digital Twins-példány URL-címe. Érdemes beolvasni a szolgáltatás URL-címét egy környezeti változóból, nem pedig a függvényben rögzített kódolással.
* Egy statikus változó, amely egy HttpClient-példányt tart fenn. A HttpClient viszonylag költséges, és azt szeretnénk elkerülni, hogy minden függvény meghívásakor ezt el kellene végezni.

Emellett vegyen fel egy helyi változót a függvényen belül, hogy az Azure digitális Twins-ügyfél példányát a Function projekthez tartsa. Ne *tegye ezt* a statikus változót az osztályon belül.

Végül módosítsa a függvény aláírását aszinkron értékre.

A módosítások után a függvény kódja a következőhöz hasonló lesz:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Ahhoz, hogy a functions alkalmazás hozzáférhessen az Azure digitális Twins-hoz, rendelkeznie kell egy rendszer által felügyelt identitással, és engedéllyel kell rendelkeznie az Azure Digital Twins-példányhoz való hozzáféréshez.

A rendszerfelügyelt identitás létrehozásához használja a következő parancsot. Jegyezze fel a kimenet *principalId* mezőjét.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

A következő parancs *principalId* értékének használatával rendelje hozzá a Function alkalmazás identitását az Azure Digital Twins-példány *tulajdonosi* szerepköréhez:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

A felügyelt identitással kapcsolatos további információkért lásd: a [*felügyelt identitások használata app Service és Azure Functionshoz*](../app-service/overview-managed-identity.md).

Végül elérhetővé teheti az Azure Digital Twins-példány URL-címét a függvény számára egy környezeti változó beállításával. Erről további információt a [*környezeti változók*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)című témakörben talál.

> [!TIP]
> Az Azure digitális Twins-példány URL-címe az Azure digitális Twins-példány *állomásneve* *https://* hozzáadásával történik. Az állomásnév, valamint a példány összes tulajdonságának megtekintéséhez futtathatja a parancsot `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Az Azure Function alkalmazás közzététele

A Function alkalmazás Azure-beli közzétételéhez kattintson a jobb gombbal a Function projekt (nem a megoldás) elemre a Megoldáskezelő, majd válassza a *Közzététel ()* elemet.

Ekkor megjelenik a következő lap:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: a függvény közzététele párbeszédpanel, 1. oldal":::

Válasszon ki vagy hozzon létre egy App Service-csomagot, amelyet a Azure Functionshoz szeretne használni. Ha nem biztos, kezdje az alapértelmezett használati terv használatával.

> [!IMPORTANT] 
> Ha közzétesz egy Azure-függvényt, további díjakat számítunk fel az előfizetéshez, amely független az Azure Digital Twins-től.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: a függvény közzététele párbeszédpanel, 2. oldal":::

A következő lapon adja meg az új függvény alkalmazás, egy erőforráscsoport és egyéb részletek kívánt nevét.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Biztonsági hozzáférés beállítása az Azure Function alkalmazáshoz

A korábbi példákból származó Azure Function csontváz megköveteli, hogy egy tulajdonosi jogkivonatot továbbítson a rendszernek, hogy az Azure digitális Twins használatával tudjon hitelesíteni. A tulajdonosi jogkivonat átadásának biztosítása érdekében be kell állítania [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) funkciót a Function alkalmazáshoz. Ezt csak egyszer kell elvégezni az egyes functions-alkalmazásokhoz.

Ennek beállításához nyissa meg a [Azure Portal](https://portal.azure.com/) , és navigáljon a Function alkalmazáshoz.

A *platform szolgáltatásai* lapon válassza az *identitás*elemet:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure Portal: identitás kiválasztása egy Azure-függvényhez":::

Az identitás lapon állítsa be az *állapot* kapcsolót be *értékre.* 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure Portal: az identitás állapotának bekapcsolása":::

Jegyezze fel a lapon látható **objektumazonosítót** is, ahogy azt a következő szakaszban fogja használni.

### <a name="assign-access-roles"></a>Hozzáférési szerepkörök kiosztása

Mivel az Azure Digital Twins szerepköralapú hozzáférés-vezérlést használ a hozzáférés kezeléséhez (lásd a [*fogalmakat: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md) című témakört), emellett hozzá kell adnia egy szerepkört minden olyan alkalmazáshoz, amelyet engedélyezni szeretne az Azure digitális Twins eléréséhez.

Szerepkör hozzárendeléséhez szüksége lesz a létrehozott Azure digitális Twins-példány **erőforrás-azonosítójára** . Ha a példány létrehozásakor korábban nem jegyezte fel, akkor a következő paranccsal kérheti le:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
Az erőforrás-azonosító a kimenet részét képezi, és egy "id" nevű hosszú sztring lesz, amely a "/Subscriptions/..." betűvel kezdődik.

Használja az erőforrás-azonosítót az Azure Function objektumazonosító mellett az alábbi parancs korábbi részében:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Azure-függvény Azure-beli digitális Ikrekhez való használatához szükséges lépéseket követte. Ezután előfizethet az Azure-függvény Event Gridre, hogy figyelje a végpontot. A végpont a következő lehet:
* Az Azure digitális Twins-hoz csatlakoztatott Event Grid végpontok az Azure digitális ikrektől érkező üzenetek feldolgozására (például a telemetria üzenetek, a [digitális ikrek](concepts-twins-graph.md) által generált üzenetek a Twin Graph-ban vagy a életciklus-üzenetek esetében)
* A IoT Hub által a telemetria és az egyéb IoT küldéséhez használt rendszertémakörök
* Más szolgáltatásokból érkező üzeneteket fogadó Event Grid végpont

Következő lépésként tekintse meg az alapszintű Azure-függvény létrehozása a IoT Hub adatok Azure digitális Ikrekbe való betöltéséhez:
* [*Útmutató: telemetria beolvasása IoT Hubból*](how-to-ingest-iot-hub-data.md)