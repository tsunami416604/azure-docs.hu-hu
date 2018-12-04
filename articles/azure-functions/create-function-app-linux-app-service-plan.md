---
title: Egy függvényalkalmazás létrehozása a linuxon futó Azure App Service-csomag
description: Ismerje meg, hogyan hozhat létre egy App Service-csomag az Azure CLI használatával Linux rendszeren futó függvényalkalmazáshoz.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856622"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Egy függvényalkalmazás létrehozása a linuxon futó Azure App Service-csomag (előzetes verzió)

Az Azure Functions lehetővé teszi, hogy a függvényeit Linux rendszerben egy alapértelmezett Azure App Service-tárolóban üzemeltesse. Ez a cikk végigvezeti az Azure CLI használatával az Azure-ban futó Linux-ban üzemeltetett függvényalkalmazás létrehozása egy [App Service-csomag](functions-scale.md#app-service-plan). [Használhatja saját egyéni tárolóját](functions-create-function-linux-custom-image.md) is. Üzemeltetése Linuxon jelenleg csak előzetes formában.

Az App Service-csomag Ön felelős a függvényalkalmazás méretezést. Az Azure Functions kiszolgáló nélküli képességeinek kihasználásához is tartozhat a függvényeit Linux rendszerben egy [Használatalapú csomag](functions-scale.md#consumption-plan).

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépen is követheti.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0.21-es vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service-csomag létrehozása

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Függvényalkalmazás létrehozása Linux rendszerben

Rendelkeznie kell egy függvényalkalmazással a függvények Linux rendszerben való végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) parancs használatával egy Linux App Service-csomag keretén belül.

A következő parancsban a `<app_name>` helyőrző helyett írjon be egy egyedi függvényalkalmazás-nevet, a `<storage_name>` helyőrző helyett pedig a tárfiók nevét. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. Is be kell állítani a `<language>` futásidejű a függvényalkalmazás a `dotnet` (C#), `node` (JavaScript), vagy `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
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

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogyan üzemeltetett Linux függvényalkalmazás létrehozása az Azure-ban. Mostantól [függvény projekt telepítése](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest) a függvényalkalmazásban. Használhatja az Azure Functions Core Tools való [-Functions-projekt létrehozása](functions-run-local.md#create-a-local-functions-project) a helyi számítógépen, és telepítse az új Linux-függvényalkalmazást.  

> [!div class="nextstepaction"] 
> [Az Azure Functions helyi kódolása és tesztelése](functions-run-local.md)
