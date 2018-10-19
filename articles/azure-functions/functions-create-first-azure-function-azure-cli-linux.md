---
title: Az első függvény létrehozása Linux rendszerben az Azure CLI-vel (előzetes verzió) | Microsoft Docs
description: Megismerkedhet vele, hogyan hozhatja létre első, alapértelmezett Linux-rendszerképen futó Azure-függvényét az Azure CLI használatával.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 1cf20a4a93ef1b5bfb9c7818f35be5e75e45a3d2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901090"
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Az első függvény létrehozása Linux rendszerben az Azure CLI használatával (előzetes verzió)

Az Azure Functions lehetővé teszi, hogy a függvényeit Linux rendszerben egy alapértelmezett Azure App Service-tárolóban üzemeltesse. [Használhatja saját egyéni tárolóját](functions-create-function-linux-custom-image.md) is. Ez a szolgáltatás jelenleg csak előzetes verzióban érhető el, és a használatához [Functions 2.0 futtatókörnyezet](functions-versions.md) szükséges.

Ez a rövid útmutató bemutatja, hogyan hozhatja létre az első, az alapértelmezett App Service-tárolóban üzemeltetett függvényalkalmazását Linux rendszerben az Azure Functions és az Azure CLI segítségével. Magát a függvénykódot a GitHub mintaadattárából helyezheti üzembe a rendszerképen.    

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépeken követheti. 

## <a name="prerequisites"></a>Előfeltételek 

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0.21-es vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service-csomag létrehozása

A Functions üzemeltetése Linuxon jelenleg csak App Service-csomagok részeként támogatott. A használatalapú csomaghoz kötődő üzemeltetés még nem támogatott. További információk az üzemeltetésről: [Azure Functions szolgáltatási csomagok összehasonlítása](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Függvényalkalmazás létrehozása Linux rendszerben

Rendelkeznie kell egy függvényalkalmazással a függvények Linux rendszerben való végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) parancs használatával egy Linux App Service-csomag keretén belül. 

A következő parancsban a `<app_name>` helyőrző helyett írjon be egy egyedi függvényalkalmazás-nevet, a `<storage_name>` helyőrző helyett pedig a tárfiók nevét. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. A _deployment-source-url_ paraméter egy GitHubon található mintaadattár, amely tartalmaz egy „Hello World” HTTP-eseményindító által aktivált függvényt.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
A függvény létrehozása és üzembe helyezése után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Mivel a `myAppServicePlan` egy Linux-csomag, a rendszer a beépített Docker-rendszerképet használja a függvényalkalmazást Linuxon futtató tároló létrehozásához. 

>[!NOTE]  
>A mintaadattár jelenleg két parancsfájlt tartalmaz [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) és [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment) néven. A .deployment fájl utasítja az üzembehelyezési folyamatot, hogy a deploy.sh fájlt használja [egyéni üzembehelyezési szkriptként](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). A jelenlegi előzetes kiadásban szükség van szkriptekre a függvényalkalmazás Linux-rendszerképen való üzembe helyezéséhez.  

## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

A GitHub-adattárban található projekthez a Functions futtatókörnyezet 1.x verziója szükséges. A `FUNCTIONS_WORKER_RUNTIME` alkalmazásbeállítás `~1` értékre állítása a legújabb 1.x verzióra rögzíti a függvényalkalmazást. Adja meg az alkalmazásbeállításokat az [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) paranccsal.

A következő Azure CLI-parancsban az <app_name> a függvényalkalmazás neve.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~1
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
