---
title: Rövid útmutató – Event Grid események küldése az Azure Container Registrybe
description: Ez a rövid útmutatóban a tárolóregisztrációs adatbázis előkészítése a Event Grid-események majd küldött tároló rendszerképet leküldéses és egy mintaalkalmazást események törlése.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/17/2018
ms.author: marsma
ms.openlocfilehash: 85e59e93c084f5e981081952e3b073798baf34e7
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234949"
---
# <a name="quickstart-send-container-registry-events-to-event-grid"></a>Gyors útmutató: Tároló beállításjegyzék események küldése az Event Grid

Az Azure Event Grid egy teljes körűen felügyelt esemény-útválasztó szolgáltatás közzétételi használatával eseményfelhasználást biztosító – előfizetési modellt. Ebben a rövid útmutatóban az Azure CLI használhatja a tároló-beállításjegyzék létrehozása, beállításjegyzék események előfizetni, majd üzembe helyezünk egy mintaalkalmazást webes az események fogadására. Végül indít el a tároló rendszerképét `push` és `delete` események és a mintaalkalmazásban az eseménytartalom megtekintése.

Miután elvégezte a cikkben ismertetett lépések, a mintául szolgáló webalkalmazás Event Grid a tárolóregisztrációs adatbázisból küldött események jelennek meg:

![A mintául szolgáló webalkalmazás három fogadott események megjelenítése webböngészőt][sample-app-01]

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes][azure-account] fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ez a cikk az Azure CLI-parancsok vannak formázva a **Bash** rendszerhéjat. Ha például a PowerShell vagy az parancssor egy másik shell használata esetén szükség lehet sor folytatási karaktert vagy változó-hozzárendelés sorok ennek megfelelően módosítsa. Ez a cikk a parancsot a szükséges szerkesztési minimalizálására változókat használ.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Azure-erőforráscsoport olyan logikai tároló, amelyben üzembe helyezése és kezelése az Azure-erőforrások. A következő [az csoport létrehozása] [ az-group-create] parancs létrehoz egy erőforráscsoportot, nevű *myResourceGroup* a a *eastus* régióban. Ha szeretné használni egy másik nevet az erőforráscsoport, állítsa `RESOURCE_GROUP_NAME` más értékre.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ezután telepítse egy tároló-beállításjegyzéket, az alábbi parancsokkal az erőforrás-csoportba. Futtatása előtt a [az acr létrehozása] [ az-acr-create] parancsot, és állítsa `ACR_NAME` , a beállításjegyzék nevét. A név Azure-on belül egyedinek kell lennie, és 5 – 50 alfanumerikus karaktert korlátozódik.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Miután létrejött a beállításjegyzékben, az Azure CLI hasonló kimenetet ad vissza a következő:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Egy esemény-végpont létrehozása

Ebben a szakaszban használhatja a Resource Manager-sablon egy GitHub-adattárban található egy előre elkészített mintát az Azure App Service webalkalmazás üzembe helyezése. Később fizessen elő a tárolójegyzék Event Grid-események, és adja meg az alkalmazás a végpontot, amelyhez az események küldhetők.

Mintaalkalmazás üzembe helyezése, állítsa `SITE_NAME` egy egyedi nevet a webalkalmazáshoz, és hajtsa végre a következő parancsokat. A helynév Azure-on belül egyedinek kell lennie, mert a webalkalmazás teljesen minősített tartománynevét (FQDN) részét képezi. Egy későbbi szakaszban olvashat, keresse meg az alkalmazás teljes Tartománynevét egy webböngészőben a tárolójegyzék események megtekintéséhez.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/dbarkol/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Miután az üzembe helyezés sikeres volt (néhány percet igénybe vehet), nyisson meg egy böngészőt, és keresse meg, hogy a webalkalmazás fut:

`http://<your-site-name>.azurewebsites.net`

Nem jelenik meg eseményt üzenetek jelennek meg a mintaalkalmazást kell megjelennie:

![Webes böngésző ábrázoló mintául szolgáló webalkalmazás-események nem jelenik meg][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Fizessen elő a beállításjegyzék-események

Az Event Gridben, az előfizetett egy *témakör* amely megszabja, hogy mely eseményeket kívánja nyomon követni, és hol el őket. A következő [az eventgrid esemény-előfizetés létrehozása] [ az-eventgrid-event-subscription-create] parancs feliratkozik a létrehozott, és adja meg a webalkalmazás URL-címet a végpontot, amelyhez eseményt küldjön a tárolóregisztrációs adatbázisba. A környezeti változókat, hogy kitölti a korábbi szakaszokban vannak lapkészlettel, így nem módosítások szükségesek.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Az előfizetés befejezése után kimenete az alábbihoz hasonló:

```JSON
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Az eseményindító beállításjegyzék események

Most, hogy működik a mintaalkalmazást, és fut, és a regisztrációs adatbázisba, az Event Griddel feliratkozott, készen áll az egyes eseményeket létrehozásához. Ebben a szakaszban ACR Builddel hozhat létre és használhat továbbít egy rendszerképet a tárolójegyzékbe. ACR Builddel funkciója az Azure Container Registry, amely lehetővé teszi, hogy hozhat létre tárolórendszerképeket a felhőben anélkül, hogy a Docker-motor telepítve a helyi gépen.

### <a name="build-and-push-image"></a>Hozhat létre, és a rendszerkép leküldése

Hajtsa végre a következő Azure CLI-paranccsal hozhat létre a tartalmát egy GitHub-adattárban található tárolórendszerképet. Alapértelmezés szerint ACR Builddel automatikusan leküldi a sikeresen összeállított rendszerképet a beállításjegyzékbe, amely létrehozza a `ImagePushed` esemény.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

ACR Builddel épít, és majd leküldi a lemezképet a következőhöz hasonló kimenetnek kell megjelennie. Az alábbi kimeneti példa kivonatosan csonkolva lettek.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 https://github.com/Azure-Samples/acr-build-helloworld-node.git
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Annak ellenőrzéséhez, hogy az összeállított rendszerképet a regisztrációs adatbázis, hajtsa végre a "myimage" tárházban címkéinek megtekintéséhez a következő parancsot:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A "v1" címke a rendszerkép létrehozott meg kell jelennie a kimenet az alábbihoz hasonló:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>A lemezkép törlése

Most, hozzon létre egy `ImageDeleted` esemény törlésével a rendszerképet a [az acr-adattár törlése] [ az-acr-repository-delete] parancsot:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

A következőhöz hasonló kimenetnek kell megjelennie a jegyzékfájlt és a hozzá kapcsolódó képek törlése megerősítés:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Beállításjegyzék-események megtekintése

Hogy egy kép most már leküldte a tárolójegyzékbe, és törölte azt. Keresse meg az Event Grid megjelenítő webalkalmazását, és mindkettő kell megjelennie `ImageDeleted` és `ImagePushed` eseményeket. Egy előfizetés érvényesítési esemény, a parancs végrehajtása által generált is megjelenhet a [előfizetni a beállításjegyzék-események](#subscribe-to-registry-events) szakaszban.

Az alábbi képernyőfelvételen a mintaalkalmazást a három eseményekhez, és a `ImageDeleted` esemény ki van bontva, a részleteinek megjelenítéséhez.

![A mintaalkalmazás ImagePushed és ImageDeleted események megjelenítése webböngészőt][sample-app-03]

Gratulálunk! Ha megjelenik a `ImagePushed` és `ImageDeleted` események, a beállításjegyzék eseményeket küld az Event Gridbe, és Event Grid továbbító események a web app-végpontra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett, az ebben a rövid útmutatóban létrehozott erőforrásokat, az Azure CLI-parancsot a törölheti azokat. Ha töröl egy erőforráscsoportot, az összes benne található erőforrást véglegesen törlődnek.

**Figyelmeztetés**: Ez a művelet nem vonható vissza. Győződjön meg arról, hogy már nincs szüksége a az erőforrások a csoport a parancs futtatása előtt.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid eseménysémája

Az Azure Container Registry esemény üzenet séma referencia az Event Grid dokumentációjában találhatja meg:

[Tároló-beállításjegyzék Azure Event Grid eseménysémája](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>További lépések

Ebben a rövid, üzembe helyezett egy tároló-beállításjegyzéket, beépített lemezkép ACR builddel, törölte azt és használt fel, a beállításjegyzék események Event Grid egy mintaalkalmazással. Ezután helyezze át a tudhat meg többet a felhőben, a tárolórendszerképek létrehozása automatikus többek között az ACR Builddel oktatóanyag rendszerkép alapszintű frissítésének épül:

> [!div class="nextstepaction"]
> [A felhőben tárolórendszerképek létrehozása ACR builddel](container-registry-tutorial-quick-build.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository#az-acr-create
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
