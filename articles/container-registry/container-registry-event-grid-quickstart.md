---
title: Rövid útmutató – Események küldése az Eseményrácsra
description: Ebben a rövid útmutatóban engedélyezi az Event Grid-eseményeket a tároló beállításjegyzékéhez, majd elküldi a tárolórendszerkép-leküldéses és az eseményeket egy mintaalkalmazásba.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: dbeba56820a520e3435eeb0c5c8dbc5aae981241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403240"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Rövid útmutató: Események küldése a magántároló beállításjegyzékéből az Event Gridbe

Az Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egységes eseményfelhasználást biztosít egy közzétételi-előfizetési modell használatával. Ebben a rövid útmutatóban az Azure CLI használatával hozzon létre egy tároló beállításjegyzéket, iratkozzon fel a beállításjegyzék-eseményekre, majd telepítsen egy minta webalkalmazást az események fogadásához. Végül elindítja a `push` `delete` tárolórendszerképet és az eseményeket, és megtekintheti az esemény hasznos adatát a mintaalkalmazásban.

A cikkben ismertetett lépések elvégzése után a tároló beállításjegyzékéből az Event Gridbe küldött események megjelennek a mintawebalkalmazásban:

![Webböngésző teszi a minta webalkalmazás három fogadott események][sample-app-01]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,][azure-account] mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Az Azure CLI-parancsok ebben a cikkben vannak formázva a **Bash** shell. Ha egy másik shell, például a PowerShell vagy a parancssor, előfordulhat, hogy ennek megfelelően módosítania kell a sor folytatási karaktereit vagy a változó hozzárendelési sorokat. Ez a cikk változókat használ a szükséges parancsszerkesztési mennyiség minimalizálásához.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport egy logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat. A következő [csoportlétrehozási][az-group-create] parancs létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* régióban. Ha más nevet szeretne használni az erőforráscsoporthoz, állítsa be `RESOURCE_GROUP_NAME` egy másik értékre.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ezután telepítsen egy tárolóbeállításjegyzéket az erőforráscsoportba a következő parancsokkal. Az az [acr create][az-acr-create] parancs `ACR_NAME` futtatása előtt állítsa be a rendszerleíró adatbázis nevét. A névnek egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karakterre van korlátozva.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

A beállításjegyzék létrehozása után az Azure CLI a következőhöz hasonló kimenetet ad vissza:

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

A telepítés sikeressítése után (ez eltarthat néhány percig), nyisson meg egy böngészőt, és keresse meg a webalkalmazást, és ellenőrizze, hogy fut-e:

`http://<your-site-name>.azurewebsites.net`

A mintaalkalmazásnak eseményüzenetek nélkül kell megjelennie:

![Mintawebalkalmazást megjelenítő webböngésző események nélkül][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Feliratkozás a rendszerleíró adatbázis eseményeire

Az Event Gridben előfizet egy *témakörre,* amely ből megmondja, hogy mely eseményeket szeretné nyomon követni, és hová küldje őket. A következő [az eventgrid esemény-előfizetés létrehozása][az-eventgrid-event-subscription-create] parancs előfizet a létrehozott tároló beállításjegyzékre, és megadja a webalkalmazás URL-címét, mint a végpontot, amelyre eseményeket kell küldenie. A korábbi szakaszokban feltöltött környezeti változókat a rendszer itt újra felhasználja, így nincs szükség szerkesztésre.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Az előfizetés befejezése után a következőhöz hasonló kimenetnek kell lennie:

```json
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

## <a name="trigger-registry-events"></a>Beállításjegyzék-események aktiválása

Most, hogy a mintaalkalmazás működik, és előfizetett a rendszerleíró adatbázisra az Event Grid segítségével, készen áll bizonyos események létrehozására. Ebben a szakaszban az ACR-feladatok segítségével hozhat létre, és leküldéses egy tárolórendszerképet a rendszerleíró adatbázisba. Az ACR-feladatok az Azure Container Registry szolgáltatása, amely lehetővé teszi, hogy tárolórendszerképeket hozzon létre a felhőben anélkül, hogy a Docker-motortelepítve lenne a helyi számítógépen.

### <a name="build-and-push-image"></a>Kép létrehozása és leküldése

Hajtsa végre a következő Azure CLI-parancsot egy tárolórendszerkép létrehozásához egy GitHub-tárház tartalmából. Alapértelmezés szerint az ACR-feladatok automatikusan lelökegy sikeresen `ImagePushed` létrehozott lemezképet a rendszerleíró adatbázisba, amely létrehozza az eseményt.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Az a következőhöz hasonló kimenetnek kell lennie, amikor az ACR-feladatok felépítik, majd lelökik a lemezképet. A következő mintakimenet rövidségre csonkolva lett.

```output
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

Annak ellenőrzéséhez, hogy a beépített lemezkép a rendszerleíró adatbázisban van-e, hajtsa végre a következő parancsot a címkék megtekintéséhez a "myimage" tárházban:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A beépített kép "v1" címkéjének a következőhöz hasonlóan meg kell jelennie a kimenetben:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>A kép törlése

Most hozzon `ImageDeleted` létre egy eseményt a kép törlésével az [az acr tárház törlési][az-acr-repository-delete] paranccsal:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

A következőhöz hasonló kimenetnek kell lennie, és megerősítést kell kérnie a jegyzékfájl és a kapcsolódó képek törléséhez:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Rendszerleíró események megtekintése

Most leadott egy lemezképet a rendszerleíró adatbázisba, majd törölte azt. Nyissa meg az Eseményrács-megjelenítő webalkalmazását, `ImageDeleted` `ImagePushed` és mind az eseményeket, mind az eseményeket látnia kell. A parancs nak a Feliratkozás a [beállításjegyzék-eseményekre](#subscribe-to-registry-events) című szakaszban történő végrehajtásával létrehozott előfizetés-érvényesítési esemény is megjelenhet.

A következő képernyőképen látható a minta `ImageDeleted` alkalmazás a három esemény, és az esemény ki van bontva, hogy a részleteket.

![A mintaalkalmazást megjelenítő webböngésző ImagePushed és ImageDeleted eseményekkel][sample-app-03]

Gratulálunk! Ha látja `ImagePushed` a `ImageDeleted` és az eseményeket, a rendszerleíró adatbázis eseményeket küld az Event Gridbe, és az Event Grid továbbítja ezeket az eseményeket a webalkalmazás végpontjára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután elkészült a rövid útmutatóban létrehozott erőforrásokkal, törölheti őket a következő Azure CLI paranccsal. Erőforráscsoport törlésekor a benne lévő összes erőforrás véglegesen törlődik.

**FIGYELMEZTETÉS**: Ez a művelet visszafordíthatatlan. Győződjön meg arról, hogy a parancs futtatása előtt már nincs szüksége a csoport egyetlen erőforrására sem.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Eseményrács eseménysémája

Az Azure Container Registry eseményüzenet-sémahivatkozása az Event Grid dokumentációjában található:

[Azure Event Grid eseménysémája a tárolóbeállítási rendszerhez](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy tároló beállításjegyzéket, egy lemezképet az ACR-feladatokkal, törölte, és felhasználta a rendszerleíró adatbázis eseményeit az Event Gridből egy mintaalkalmazással. Ezután lépjen tovább az ACR-feladatok oktatóanyagra, amelyből többet szeretne megtudni a tárolóképek felhőben történő létrehozásáról, beleértve az alaplemezkép frissítésére épülő automatikus buildeket is:

> [!div class="nextstepaction"]
> [Tárolórendszerképek létrehozása a felhőben az ACR-feladatokkal](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
