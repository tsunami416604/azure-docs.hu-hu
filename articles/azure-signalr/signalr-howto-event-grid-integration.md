---
title: Az Azure SignalR Service események küldése az Event Gridbe
description: Egy útmutató, amellyel bemutatják, hogyan engedélyezhető a SignalR Service Event Grid-események majd ügyfélkapcsolat csatlakoztatott vagy leválasztott eseményeket küldeni egy mintaalkalmazást.
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789175"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Események küldése az Azure SignalR Service-ből az Event Gridbe

Az Azure Event Grid egy teljes körűen felügyelt esemény-útválasztó szolgáltatás, amely egy pub-sub-modell használatával eseményfelhasználást biztosít. Ez az útmutató használatával az Azure parancssori felület létrehozása az Azure SignalR Service, kapcsolateseményei előfizetni, és üzembe helyezünk egy mintaalkalmazást webes az események fogadására. Végül csatlakozhat, és válassza le és tekintse meg a mintaalkalmazásban az eseménytartalom.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][azure-account] a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ez a cikk az Azure CLI-parancsok vannak formázva a **Bash** rendszerhéjat. Ha például a PowerShell vagy az parancssor egy másik shell használata esetén szükség lehet sor folytatási karaktert vagy változó-hozzárendelés sorok ennek megfelelően módosítsa. Ez a cikk a parancsot a szükséges szerkesztési minimalizálására változókat használ.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Azure-erőforráscsoport olyan logikai tároló, amelyben üzembe helyezése és kezelése az Azure-erőforrások. A következő [az csoport létrehozása][az-group-create] parancs létrehoz egy erőforráscsoportot, nevű *myResourceGroup* a a *eastus* régióban. Ha szeretné használni egy másik nevet az erőforráscsoport, állítsa `RESOURCE_GROUP_NAME` más értékre.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR szolgáltatás létrehozása

Ezután helyezze üzembe az Azure Signalr Service az erőforráscsoport, az alábbi parancsokkal.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Ha a SignalR Service létrejött, az Azure CLI hasonló kimenetet ad vissza a következő:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Egy esemény-végpont létrehozása

Ebben a szakaszban használhatja a Resource Manager-sablon egy GitHub-adattárban található egy előre elkészített mintát az Azure App Service webalkalmazás üzembe helyezése. Később fizessen elő a tárolójegyzék Event Grid-események, és adja meg az alkalmazás a végpontot, amelyhez az események küldhetők.

Mintaalkalmazás üzembe helyezése, állítsa `SITE_NAME` egy egyedi nevet a webalkalmazáshoz, és hajtsa végre a következő parancsokat. A helynév Azure-on belül egyedinek kell lennie, mert a webalkalmazás teljesen minősített tartománynevét (FQDN) részét képezi. Egy későbbi szakaszban olvashat, keresse meg az alkalmazás teljes Tartománynevét egy webböngészőben a tárolójegyzék események megtekintéséhez.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Miután az üzembe helyezés sikeres (néhány percet igénybe vehet), nyisson meg egy böngészőt, és keresse meg, hogy a webalkalmazás fut:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Fizessen elő a beállításjegyzék-események

Az Event Gridben, az előfizetett egy *témakör* amely megszabja, hogy mely eseményeket kívánja nyomon követni, és hol el őket. A következő [az eventgrid esemény-előfizetés létrehozása][az-eventgrid-event-subscription-create] parancs feliratkozik az Azure SignalR Service létrehozott, és adja meg a webalkalmazás URL-címet a végpontot, amelyhez eseményeket küldjön. A környezeti változókat, hogy kitölti a korábbi szakaszokban vannak lapkészlettel, így nem módosítások szükségesek.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Az előfizetés befejezése után az alábbihoz hasonló kimenetnek kell megjelennie:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Az eseményindító beállításjegyzék események

Váltson át a szolgáltatás módba `Serverless Mode` és a SignalR Service egy ügyfél kapcsolat beállításához. Elvégezhető [kiszolgáló nélküli minta](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) referenciaként.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Beállításjegyzék-események megtekintése

A SignalR Service most már csatlakozott egy ügyfél. Keresse meg az Event Grid megjelenítő webalkalmazását, és megtekintheti az egy `ClientConnectionConnected` esemény. Ha az ügyfél, látni fogja emellett egy `ClientConnectionDisconnected` esemény.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
