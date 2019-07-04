---
title: Az első függvény létrehozása az Azure CLI használatával
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure CLI és az Azure Functions Core Tools használatával.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 625fcda4b612fdeda940f9219238ac79354dd056
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444666"
---
# <a name="create-your-first-function-from-the-command-line"></a>Az első függvény létrehozása parancssorból

Ez a rövid útmutató lépésről lépésre bemutatja, hogyan hozhatja létre az első függvényét a parancssorból vagy a terminálból. Az Azure CLI használatával létrehoz egy függvényalkalmazást, amely az a [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) infrastruktúra lesz, amelyen a függvény működni fog. A függvénykód-projekt egy függvényből lett létrehozva az [Azure Functions Core Tools](functions-run-local.md) használatával, ennek a használatával történik a függvényalkalmazás-projekt Azure-beli üzembe helyezése is.

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépen is követheti.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához az alábbiakkal kell rendelkeznie:

+ Telepítés [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666 verzió vagy újabb.

+ Telepítse az [Azure CLI-t]( /cli/azure/install-azure-cli). Ehhez a cikkhez az Azure CLI 2.0-ás vagy újabb verziója szükséges. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Használhatja az [Azure Cloud Shellt](https://shell.azure.com/bash) is.

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>A helyi függvényalkalmazás-projekt létrehozása

Futtassa a következő parancsot a parancssorból, hogy létrehozzon az aktuális helyi könyvtár `MyFunctionProj` mappájába egy függvényalkalmazás-projektet. A `MyFunctionProj` mappában egy GitHub-adattár is létrejön.

```bash
func init MyFunctionProj
```

Amikor a rendszer kéri, válasszon egy feldolgozói futtatókörnyezetet a következő nyelvi lehetőségek közül:

+ `dotnet`: létrehoz egy .NET osztálytárprojektet (.csproj).
+ `node`: létrehoz egy JavaScript-projektet.

A parancs végrehajtásakor a következő kimenethez hasonlót fog látni:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Az alábbi paranccsal léphet az új `MyFunctionProj` projektmappára.

```bash
cd MyFunctionProj
```

## <a name="enable-extension-bundles"></a>Bővítmény csomagjaiból engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód kiszolgáló nélküli végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) parancs használatával. 

A következő parancsban a `<APP_NAME>` helyőrző helyett írjon be egy egyedi függvényalkalmazás-nevet, a `<STORAGE_NAME>` helyőrző helyett pedig a tárfiók nevét. Az `<APP_NAME>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. Is be kell állítani a `<language>` futásidejű a függvényalkalmazás a `dotnet` (C#) vagy `node` (JavaScript).

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

