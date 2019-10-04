---
title: Az első függvény létrehozása Linux rendszeren az Azure-ban
description: Arra vonatkozó tudnivalók, hogyan hozhatja létre Linuxon futó első Azure-függvényét az Azure Functions Core Tools és az Azure CLI használatával.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 03/12/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc, fasttrack-edit
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: 40a2d3ab4ec358b5b2d0105703cdc25cdb777c29
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562971"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli"></a>A Linuxon üzemeltetett első függvény létrehozása a Core Tools és az Azure CLI használatával

Az Azure Functions lehetővé teszi a kód végrehajtását [kiszolgáló nélküli](https://azure.com/serverless) Linux-környezetben anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. A Linux-üzemeltetéshez [a 2. x](functions-versions.md)-es függvények szükségesek. A kiszolgáló nélküli függvények a használati [tervben](functions-scale.md#consumption-plan)futnak.

Ez a rövid útmutató bemutatja, hogyan hozhatja létre Linuxon futó első függvényalkalmazását az Azure CLI-vel. A függvénykód helyben jön létre, és az [Azure Functions Core Tools](functions-run-local.md) segítségével lehet üzembe helyezni az Azure-ban.

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépeken követheti. Ez a cikk a függvények JavaScript vagy C# használatával való létrehozásához nyújt útmutatást. A Python-függvények létrehozásával kapcsolatos információkért lásd: [az első Python-függvény létrehozása a Core Tools és az Azure CLI használatával](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához az alábbiakkal kell rendelkeznie:

- Telepítse [Azure functions Core Tools](./functions-run-local.md#v2) 2.6.666 vagy újabb verziót.

+ Telepítse az [Azure CLI-t]( /cli/azure/install-azure-cli). Ehhez a cikkhez az Azure CLI 2.0-ás vagy újabb verziója szükséges. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Használhatja az [Azure Cloud Shellt](https://shell.azure.com/bash) is.

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Linux-függvényalkalmazás létrehozása az Azure-ban

Rendelkeznie kell egy függvényalkalmazással a függvények Linux rendszerben való végrehajtásának biztosításához. A függvényalkalmazás egy kiszolgáló nélküli környezetet biztosít a függvénykód végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. Hozzon létre egy Linuxon futó függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) paranccsal.

A következő parancsban az `<app_name>` helyőrzőt cserélje le egy függvényalkalmazás egyedi nevére, a `<storage_name>` helyőrzőt pedig a tárfiók nevére. Az `<app_name>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, ezért az Azure összes alkalmazásában csak egyszer használhatja. `<language>` A Function alkalmazás `dotnet` futtatókörnyezetét a (C#), `node` (JavaScript/írógéppel) vagy `python`a () függvény futtatásával is be kell állítania.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Az egyéni függvényalkalmazás létrehozása után következő üzenet jelenik meg:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Most már közzéteheti az új függvényalkalmazás projektjét az Azure-ban.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]