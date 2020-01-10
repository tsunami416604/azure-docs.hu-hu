---
title: Az első függvény létrehozása az Azure CLI használatával
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure CLI és az Azure Functions Core Tools használatával.
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 222e4a98974a1af40ff860cfc4fdb246d9c97bca
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769387"
---
# <a name="quickstart-create-your-first-function-from-the-command-line-using-azure-cli"></a>Rövid útmutató: az első függvény létrehozása a parancssorból az Azure CLI használatával

Ez a rövid útmutató lépésről lépésre bemutatja, hogyan hozhatja létre az első függvényét a parancssorból vagy a terminálból. Az Azure CLI használatával létrehoz egy függvényalkalmazást, amely az a [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) infrastruktúra lesz, amelyen a függvény működni fog. A függvénykód-projekt egy függvényből lett létrehozva az [Azure Functions Core Tools](functions-run-local.md) használatával, ennek a használatával történik a függvényalkalmazás-projekt Azure-beli üzembe helyezése is.

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépen is követheti.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához az alábbiakkal kell rendelkeznie:

+ Telepítse [Azure functions Core Tools](./functions-run-local.md#v2) 2.6.666 vagy újabb verziót.

+ Telepítse az [Azure CLI-t](/cli/azure/install-azure-cli). Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Használhatja az [Azure Cloud Shellt](https://shell.azure.com/bash) is.

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód kiszolgáló nélküli végrehajtásához. Lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) parancs használatával.

A következő parancsban a `<APP_NAME>` helyőrző helyett írjon be egy egyedi függvényalkalmazás-nevet, a `<STORAGE_NAME>` helyőrző helyett pedig a tárfiók nevét. Az `<APP_NAME>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. A Function alkalmazáshoz a `<language>` futtatókörnyezetet is be kell állítania `dotnet` (C#) vagy `node` (JavaScript) értékről.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

A _consumption-plan-location_ paraméter beállítása azt jelenti, hogy a függvényalkalmazás egy Használatalapú futtatási csomagban fut. Ebben a kiszolgáló nélküli csomagban az erőforrások hozzáadása a függvények igényei szerint dinamikusan történik, és csak akkor kell fizetnie, amikor a függvények futnak. További információ: [A megfelelő szolgáltatási csomag kiválasztása](functions-scale.md).

A függvény létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
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

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

