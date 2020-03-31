---
title: Az Azure SignalR-szolgáltatás eseményeinek küldése az Event Gridbe
description: Útmutató, amely bemutatja, hogyan engedélyezheti az Event Grid-eseményeket a SignalR szolgáltatáshoz, majd hogyan küldhet ügyfélkapcsolati csatlakoztatott/leválasztott eseményeket egy mintaalkalmazásba.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710829"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Események küldése az Azure SignalR Service-ből az Event Gridbe

Az Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egységes eseményfelhasználást biztosít egy pub-sub modell használatával. Ebben az útmutatóban az Azure CLI használatával hozzon létre egy Azure SignalR-szolgáltatást, iratkozzon fel a kapcsolati eseményekre, majd telepítsen egy minta webalkalmazást az események fogadásához. Végül csatlakozhat, és bonthatja, és láthatja az esemény hasznos adatát a mintaalkalmazásban.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,][azure-account] mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Az Azure CLI-parancsok ebben a cikkben vannak formázva a **Bash** shell. Ha egy másik shell, például a PowerShell vagy a parancssor, előfordulhat, hogy ennek megfelelően módosítania kell a sor folytatási karaktereit vagy a változó hozzárendelési sorokat. Ez a cikk változókat használ a szükséges parancsszerkesztési mennyiség minimalizálásához.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport egy logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat. A következő [csoportlétrehozási][az-group-create] parancs létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* régióban. Ha más nevet szeretne használni az erőforráscsoporthoz, állítsa be `RESOURCE_GROUP_NAME` egy másik értékre.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR szolgáltatás létrehozása

Ezután telepítsen egy Azure Signalr-szolgáltatást az erőforráscsoportba a következő parancsokkal.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

A SignalR szolgáltatás létrehozása után az Azure CLI a következőhöz hasonló kimenetet ad vissza:

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

## <a name="create-an-event-endpoint"></a>Eseményvégpont létrehozása

Ebben a szakaszban egy GitHub-tárházban található Resource Manager-sablonhasználatával telepíthet egy előre elkészített minta webalkalmazást az Azure App Service-be. Később előfizet a rendszerleíró adatbázis Event Grid-eseményeire, és ezt az alkalmazást adja meg végpontként, amelyre az eseményeket elküldi.

A mintaalkalmazás központi `SITE_NAME` telepítéséhez állítsa be a webalkalmazás egyedi nevét, és hajtsa végre a következő parancsokat. A webhelynévnek egyedinek kell lennie az Azure-on belül, mert a webalkalmazás teljesen minősített tartománynevének (FQDN) részét képezi. Egy későbbi szakaszban egy webböngészőben az alkalmazás teljes tartománynát megnyithatja a rendszerleíró adatbázis eseményeinek megtekintéséhez.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Miután a központi telepítés sikeres (eltarthat néhány percig), nyisson meg egy böngészőt, és keresse meg a webalkalmazást, és ellenőrizze, hogy fut-e:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Feliratkozás a rendszerleíró adatbázis eseményeire

Az Event Gridben előfizet egy *témakörre,* amely ből megmondja, hogy mely eseményeket szeretné nyomon követni, és hová küldje őket. A következő [az eventgrid esemény-előfizetés létrehozása][az-eventgrid-event-subscription-create] parancs előfizet az Azure SignalR szolgáltatás létrehozott, és megadja a webalkalmazás URL-címét, mint a végpont, amelyre eseményeket kell küldenie. A korábbi szakaszokban feltöltött környezeti változókat a rendszer itt újra felhasználja, így nincs szükség szerkesztésre.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Az előfizetés befejezése után a következőhöz hasonló kimenetnek kell lennie:

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

## <a name="trigger-registry-events"></a>Beállításjegyzék-események aktiválása

Váltson a `Serverless Mode` szolgáltatási módra, és állítson be egy ügyfélkapcsolatot a SignalR szolgáltatással. Referenciaként a Kiszolgáló nélküli mintát is [használhatja.](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Rendszerleíró események megtekintése

Most egy ügyfelet csatlakoztatott a SignalR szolgáltatáshoz. Nyissa meg az Eseményrács-megjelenítő webalkalmazását, `ClientConnectionConnected` és meg kell jelennie egy eseménynek. Ha megszünteti az ügyfelet, egy `ClientConnectionDisconnected` esemény is megjelenik.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
