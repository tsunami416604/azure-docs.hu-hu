---
title: Többtárolós (előzetes verzió) alkalmazás létrehozása az Azure Web App for Containers szolgáltatásban egy Docker Compose konfigurációval
description: Néhány perc alatt telepítheti az első többtárolós alkalmazását az Azure Web App for Containersben
keywords: azure app service, webalkalmazás, linux, docker, összeállítás, többtárolós, több tárolós, webalkalmazás tárolókhoz, több tároló, tároló, kubernetes, wordpress, mysql-hez készült azure db, éles adatbázis tárolókkal
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: bf567402a66f9152c7eb9b97925fec2a159ffe56
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127420"
---
# <a name="create-a-multi-container-preview-app-using-web-app-for-containers"></a>Többtárolós (előzetes verzió) alkalmazás létrehozása a Web App for Containers szolgáltatással

A [Web App for Containers](app-service-linux-intro.md) segítségével rugalmasan használhatók a Docker-rendszerképek. Ez a rövid útmutató a többtárolós alkalmazásoknak a Web App for Containersben történő üzembe helyezésén vezeti végig a [Cloud Shellben](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) egy Docker Compose-konfiguráció használatával. A Kuberneteshez és a MySQL-hez készült Azure DB teljes körű megoldásához kövesse a [többtárolós alkalmazásokról szóló oktatóanyag lépéseit](tutorial-multi-container-app.md).

Ezt a rövid útmutatót a Cloud Shellben követjük végig, a parancsok azonban helyileg is futtathatók az [Azure CLI](/cli/azure/install-azure-cli) (2.0.32-es vagy újabb verzió) használatával. 

![Minta többtárolós alkalmazás a Web Apps for Containersben][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>A minta letöltése

Ebben a rövid útmutatóban a [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) Compose-fájlját fogja használni. A konfigurációs fájl az [Azure-minták](https://github.com/Azure-Samples/multicontainerwordpress) között található.

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

A Cloud Shellben hozzon létre egy quickstart könyvtárat, és lépjen a könyvtárba.

```bash
mkdir quickstart

cd quickstart
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a quickstart könyvtárba való klónozásához. Ezután lépjen a `multicontainerwordpress` könyvtárba.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

A Cloud Shellben hozzon létre egy erőforráscsoportot az [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *USA déli középső régiója* helyen. A Linuxon futó, **Standard** szintű App Service-t támogató összes hely megtekintéséhez futtassa az [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations) parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre.

A parancs befejeződésekor a JSON-kimenet megjeleníti az erőforráscsoport tulajdonságait.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service-csomag létrehozása

A Cloud Shellben hozzon létre egy App Service-csomagot az erőforráscsoportban az [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) paranccsal.

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be a **Standard** tarifacsomagban (`--sku S1`) és Linux-tárolóban (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Az App Service-csomag létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Docker Compose-alkalmazás létrehozása

A Cloud Shell-terminálban hozzon létre egy többtárolós [webalkalmazást](app-service-linux-intro.md) az `myAppServicePlan` App Service-csomagban az [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) paranccsal. Ne felejtse el kicserélni az _\<app_name>_ nevet egy egyedi alkalmazásnévre.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Keresse meg az üzembe helyezett alkalmazást a következő helyen: `http://<app_name>.azurewebsites.net`. Az alkalmazás betöltése eltarthat néhány percig. Ha hibaüzenet jelenik meg, várjon néhány percet, majd frissítse a böngészőt.

![Minta többtárolós alkalmazás a Web Apps for Containersben][1]

**Gratulálunk!** Létrehozott egy többtárolós alkalmazást a Web App for Containersben.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Többtárolós WordPress-alkalmazás létrehozása a Web App for Containersben](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png