---
title: Azure-függvény beállítása az adatok feldolgozásához
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhat létre olyan Azure-függvényt, amely hozzáférhet a digitális ikrekhöz, és aktiválhatja azokat.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0f5a921ad9eba82f27b3a8945643737b6d76030
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614069"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Azure Functions alkalmazások összekötése az adatfeldolgozáshoz

A digitális ikrek adatok alapján történő frissítése a számítási [**erőforrásokon keresztül történik**](concepts-route-events.md) , például [Azure functions](../azure-functions/functions-overview.md). Egy Azure-függvény a következőre adott válaszként használható a digitális kettős frissítésére:
* az eszköz telemetria érkező adatok IoT Hub
* a tulajdonság változása vagy más, a Twin gráfon belüli digitális Twin-ből származó adatok

Ez a cikk bemutatja, hogyan hozhat létre Azure-függvényeket az Azure Digital Twins szolgáltatással való használatra. 

Az itt látható lépések áttekintése:

1. Azure Functions-alkalmazás létrehozása a Visual Studióban
2. Azure-függvény írása [Event Grid](../event-grid/overview.md) triggerrel
3. Hitelesítési kód hozzáadása a függvényhez (az Azure digitális Twins eléréséhez)
4. A Function alkalmazás közzététele az Azure-ban
5. [Biztonsági](concepts-security.md) hozzáférés beállítása az Azure Function alkalmazáshoz

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Azure Functions-alkalmazás létrehozása a Visual Studióban

A Visual Studio 2019-ben válassza a _fájl > új > projekt_ elemet, és keresse meg a _Azure functions_ sablont, majd kattintson a _Tovább gombra_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: új projekt párbeszédpanel":::

Adja meg a Function alkalmazás nevét, majd válassza a _Létrehozás_lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: új projekt konfigurálása":::

Válassza ki a Function app *Event Grid trigger* típusát, és válassza a _Létrehozás_lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: Azure Function Project trigger párbeszédpanel":::

Miután létrehozta a Function alkalmazást, a Visual Studio automatikusan feltölti a kódot a **function.cs** -fájlban a projekt mappájába. Ez a rövid Azure-függvény az események naplózására szolgál.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: Project-ablak mintakód-kóddal":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Azure-függvény írása Event Grid triggerrel

Az SDK-nak a Function alkalmazásba való felvételével írhat egy Azure-függvényt. A Function app a [.net-hez készült Azure IoT Digital Twin ügyféloldali kódtár (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)használatával kommunikál az Azure Digital Twins szolgáltatással. 

Az SDK használatához a következő csomagokat kell felvennie a projektbe. A csomagokat telepítheti a Visual Studio NuGet csomagkezelő használatával, vagy hozzáadhatja a csomagokat a `dotnet` parancssori eszköz használatával. Válassza ki az alábbi módszerek egyikét: 

**1. lehetőség. Csomagok hozzáadása a Visual Studio Package Managerrel:**
    
Ehhez kattintson a jobb gombbal a projektre, és válassza a _NuGet-csomagok kezelése_ elemet a listából. Ezután a megnyíló ablakban válassza a _Tallózás_ fület, és keresse meg a következő csomagokat. Válassza a _telepítés_ lehetőséget, és _fogadja el_ a licencszerződést a csomagok telepítéséhez.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Ahhoz, hogy az Azure SDK-folyamat megfelelően beállítható legyen a Azure Functionshoz, a következő csomagokra is szüksége lesz. Az összes csomag telepítéséhez ismételje meg a fenti eljárást.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**2. lehetőség. Csomagok hozzáadása a `dotnet` parancssori eszközzel:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Ezután a Visual Studio Megoldáskezelő nyissa meg a _function.cs_ fájlt, ahol a mintakód szerepel, és adja hozzá a következő _using_ utasításokat az Azure-függvényhez. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-azure-function"></a>Hitelesítési kód hozzáadása az Azure-függvényhez

Ekkor deklarálja az osztály szintjének változóit, és adja hozzá a hitelesítési kódot, amely lehetővé teszi, hogy a függvény hozzáférhessen az Azure digitális Twins szolgáltatáshoz. A következőt adja hozzá az Azure-függvényhez a (z) {a Function Name}. cs fájlban.

* A ADT szolgáltatás URL-címének beolvasása környezeti változóként. Érdemes beolvasni a szolgáltatás URL-címét egy környezeti változóból, nem pedig a függvényben rögzített kódolással.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Egy statikus változó, amely egy HttpClient-példányt tart fenn. A HttpClient viszonylag költséges, és azt szeretnénk elkerülni, hogy minden függvény meghívásakor ezt el kellene végezni.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* Használhatja a felügyelt identitás hitelesítő adatait az Azure-függvényben.
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* Vegyen fel egy helyi változót a _DigitalTwinsClient_ belül, hogy az Azure digitális Twins-ügyfél példányát a Function projekthez tartsa. Ez a változó *ne* legyen statikus az osztályban.
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* Vegyen fel egy NULL értékű _adtInstanceUrl_ , és zárja be a függvény logikáját egy try catch blokkban a kivételek megfogásához.

A módosítások után a függvény kódja a következőhöz hasonló lesz:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>A Function alkalmazás közzététele az Azure-ban

A Function alkalmazás Azure-beli közzétételéhez kattintson a jobb gombbal a Function projekt (nem a megoldás) elemre a Megoldáskezelő, majd válassza a **Közzététel**lehetőséget.

> [!IMPORTANT] 
> Ha közzétesz egy Azure-függvényt, további díjakat számítunk fel az előfizetéshez, amely független az Azure Digital Twins-től.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: az Azure-függvény közzététele ":::

Válassza ki az **Azure** -t közzétételi célként, és válassza a **tovább**lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: az Azure-függvény közzététele párbeszédpanel, válassza az Azure lehetőséget. ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: a függvény közzététele párbeszédpanel, válassza az Azure függvényalkalmazás (Windows) vagy (Linux) lehetőséget a gép alapján":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: a függvény közzététele párbeszédpanel, új Azure-függvény létrehozása":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: a függvény közzététele párbeszédpanel, töltse ki a mezőket, majd válassza a létrehozás lehetőséget.":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: közzétételi függvény párbeszédpanel, válassza ki a függvény alkalmazást a listából, és fejezze be":::

A következő lapon adja meg az új függvény alkalmazás, egy erőforráscsoport és egyéb részletek kívánt nevét.
Ahhoz, hogy a functions alkalmazás hozzáférhessen az Azure digitális Twins-hoz, rendelkeznie kell egy rendszer által felügyelt identitással, és engedéllyel kell rendelkeznie az Azure Digital Twins-példányhoz való hozzáféréshez.

Következő lépésként beállíthatja a függvény biztonsági hozzáférését a parancssori felület vagy a Azure Portal használatával. Válassza ki az alábbi módszerek egyikét:

## <a name="set-up-security-access-for-the-azure-function-app"></a>Biztonsági hozzáférés beállítása az Azure Function alkalmazáshoz
A következő lehetőségek egyikével állíthatja be az Azure Function alkalmazás biztonsági hozzáférését:

### <a name="option-1-set-up-security-access-for-the-azure-function-app-using-cli"></a>1. lehetőség: biztonsági hozzáférés beállítása az Azure Function alkalmazáshoz a parancssori felület használatával

A korábbi példákból származó Azure Function csontváz megköveteli, hogy egy tulajdonosi jogkivonatot továbbítson az Azure Digital Twins szolgáltatással való hitelesítéshez. A tulajdonosi jogkivonat átadásának biztosítása érdekében be kell állítania [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) funkciót a Function alkalmazáshoz. Ezt csak egyszer kell elvégezni az egyes functions-alkalmazásokhoz.

Létrehozhatja a rendszer által felügyelt identitást, és hozzárendelheti a Function alkalmazás identitását az Azure Digital _Twins tulajdonos (előzetes verzió)_ szerepkörhöz az Azure Digital Twins-példányhoz. Ezzel a művelettel a példányban az adatsík-tevékenységek elvégzéséhez a függvény alkalmazás engedélyének kell megadnia. Ezután az Azure Digital Twins-példány URL-címét elérhetővé teheti a függvény számára egy környezeti változó beállításával.

 A parancsok futtatásához használja a [Azure Cloud Shell](https://shell.azure.com) .

A rendszerfelügyelt identitás létrehozásához használja a következő parancsot. Jegyezze fel a kimenet _principalId_ mezőjét.

```azurecli 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Az alábbi parancs _principalId_ értékének használatával rendelje hozzá a Function alkalmazás identitását az Azure Digital _Twins tulajdonos (előzetes verzió)_ szerepkörhöz az Azure Digital Twins-példányhoz.

```azurecli 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```
Végül elérhetővé teheti az Azure Digital Twins-példány URL-címét a függvény számára egy környezeti változó beállításával. A környezeti változók beállításával kapcsolatos további információkért lásd: [*környezeti változók*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> Az Azure digitális Twins-példány URL-címe az Azure digitális Twins-példány *állomásneve* *https://* hozzáadásával történik. Az állomásnév, valamint a példány összes tulajdonságának megtekintéséhez futtathatja a parancsot `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-azure-function-app-using-azure-portal"></a>2. lehetőség: biztonsági hozzáférés beállítása az Azure Function alkalmazáshoz Azure Portal használatával

A rendszerhez rendelt felügyelt identitás lehetővé teszi, hogy az Azure-erőforrások hitelesítsék magukat a Cloud Servicesben (például Azure Key Vault) a hitelesítő adatok kódban való tárolása nélkül. Ha engedélyezve van, az összes szükséges engedély az Azure szerepköralapú hozzáférés-vezérlés használatával adható meg. Az ilyen típusú felügyelt identitás életciklusa az erőforrás életciklusához van kötve. Emellett minden erőforrás (például a virtuális gép) csak egy rendszerhez rendelt felügyelt identitással rendelkezhet.

A [Azure Portal](https://portal.azure.com/)keressen rá a _Function app_ kifejezésre a keresési sávban a korábban létrehozott Function alkalmazás nevével. Válassza ki a *függvényalkalmazás* a listából. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure Portal: Keresés a függvény alkalmazásban":::

A felügyelt identitás engedélyezéséhez kattintson a bal oldali navigációs sávban az _Identity (identitás_ ) elemre a függvény alkalmazás ablakban.
A _rendszer-hozzárendelés_ lapon állítsa be az _állapotot_ a be értékre, és _mentse_ azt. Egy előugró ablak jelenik meg, amely _lehetővé teszi a rendszerhez rendelt felügyelt identitás engedélyezését_.
Válassza az _Igen_ gomb lehetőséget. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure Portal: a rendszerfelügyelt identitás engedélyezése":::

Az értesítésekben ellenőrizheti, hogy a függvény sikeresen regisztrálva van-e Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure Portal: értesítések":::

Jegyezze fel az _Identity (identitás_ ) lapon megjelenő **objektumazonosítót** is, ahogy azt a következő szakaszban fogja használni.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="A jövőben használandó objektumazonosító másolása":::

### <a name="assign-access-roles-using-azure-portal"></a>Hozzáférési szerepkörök kiosztása Azure Portal használatával

Válassza az _Azure szerepkör-hozzárendelések_ gombot, amely megnyitja az Azure szerepkör-hozzárendelések lapot. Ezután válassza a _+ szerepkör-hozzárendelés hozzáadása (előzetes verzió)_ lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure Portal: szerepkör-hozzárendelés hozzáadása":::

A megnyíló _szerepkör-hozzárendelés hozzáadása (előzetes verzió)_ ablakban válassza a

* _Hatókör_: Erőforráscsoport
* _Előfizetés_: válassza ki az Azure-előfizetését
* _Erőforráscsoport_: válassza ki az erőforráscsoportot a legördülő listából
* _Szerepkör_: válassza az _Azure Digital Twins-tulajdonos (előzetes verzió)_ lehetőséget a legördülő listából

Mentse a részleteket a _Save (Mentés_ ) gombra kattintva.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure Portal: szerepkör-hozzárendelés hozzáadása (előzetes verzió) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Alkalmazásbeállítások konfigurálása Azure Portal használatával

Az Azure Digital Twins-példány URL-címét elérhetővé teheti a függvény számára egy környezeti változó beállításával. Erről további információt a [*környezeti változók*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)című témakörben talál. Az Alkalmazásbeállítások környezeti változókként vannak kitéve a digitális Twins-példány eléréséhez. 

Alkalmazás-beállítás létrehozásához ADT_INSTANCE_URL szükséges.

ADT_INSTANCE_URL a **_https://_** a példány állomásneve való hozzáfűzésével kérheti le. A Azure Portalban megtalálhatja a digitális ikrek példányának állomásnevét úgy, hogy megkeresi a példányt a keresősávban. Ezután a bal oldali navigációs sávon az _Áttekintés_ elemre kattintva megtekintheti a _gazdagép nevét_. Másolja ezt az értéket egy Alkalmazásbeállítás létrehozásához.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure Portal: Áttekintés – > a _Value_ mezőben használandó hostname (állomásnév).":::

Mostantól az alábbi lépéseket követve létrehozhat egy alkalmazás-beállítást:

* Keresse meg az Azure-függvényt a keresési sávban a Function Name paranccsal, és válassza ki a függvényt a listából.
* Válassza ki a bal oldali navigációs sávon a _konfiguráció_ elemet egy új Alkalmazásbeállítás létrehozásához
* Az _Alkalmazásbeállítások_ lapon válassza az _+ új alkalmazás beállítása_ lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure Portal: meglévő Azure-függvény keresése":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure Portal: Alkalmazásbeállítások konfigurálása":::

A megnyíló ablakban a fentiből másolt érték használatával hozzon létre egy Alkalmazásbeállítás-beállítást. \
_Név_  : ADT_SERVICE_URL \
_Érték_ : https://{saját Azure-digitális-Twins-hostname}

Az alkalmazás beállításainak létrehozásához kattintson _az OK gombra_ .

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure Portal: Alkalmazásbeállítások hozzáadása.":::

Az Alkalmazásbeállítások a _Name (név_ ) mezőben tekinthetők meg az alkalmazás nevével. Ezután mentse az alkalmazás beállításait a Save ( _Mentés_ ) gombra kattintva.

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure Portal: megtekintheti a létrehozott alkalmazást, és újraindíthatja az alkalmazást":::

Az Alkalmazásbeállítások módosítására az alkalmazás újraindítása szükséges. Válassza a _Folytatás_ lehetőséget az alkalmazás újraindításához.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure Portal: Alkalmazásbeállítások mentése":::

Az Alkalmazásbeállítások az _értesítések_ ikon kiválasztásával tekinthetők meg. Ha az alkalmazás beállítása nincs létrehozva, akkor a fenti folyamat követésével újra megpróbálkozhat az alkalmazás hozzáadásával.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure Portal: az Alkalmazásbeállítások frissítésére vonatkozó értesítések":::

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Azure-függvény Azure-beli digitális Ikrekhez való használatához szükséges lépéseket követte. Ezután előfizethet az Azure-függvény Event Gridre, hogy figyelje a végpontot. A végpont a következő lehet:
* Az Azure digitális Twins-hoz csatlakoztatott Event Grid végpontok az Azure digitális ikrektől érkező üzenetek feldolgozására (például a telemetria üzenetek, a [digitális ikrek](concepts-twins-graph.md) által generált üzenetek a Twin Graph-ban vagy a életciklus-üzenetek esetében)
* A IoT Hub által a telemetria és az egyéb IoT küldéséhez használt rendszertémakörök
* Más szolgáltatásokból érkező üzeneteket fogadó Event Grid végpont

Következő lépésként tekintse meg az alapszintű Azure-függvény létrehozása a IoT Hub adatok Azure digitális Ikrekbe való betöltéséhez:
* [*Útmutató: telemetria beolvasása IoT Hubból*](how-to-ingest-iot-hub-data.md)