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
ms.openlocfilehash: 78c2f599ba7d22e6de070f5867398e111a396d45
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849737"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Az első függvény létrehozása Linux rendszerben a Core Tools és az Azure CLI használatával (előzetes verzió)

Az Azure Functions lehetővé teszi a kód végrehajtását [kiszolgáló nélküli](https://azure.com/serverless) Linux-környezetben anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. Linux-üzemeltetéséhez szükséges [Functions 2.0 futtatókörnyezet](functions-versions.md). A támogatási függvényalkalmazást linuxon fut, az a kiszolgáló nélküli [Használatalapú csomag](functions-scale.md#consumption-plan) jelenleg előzetes verzióban érhető el. További tudnivalókért lásd: [előzetes szempontok cikkben](https://aka.ms/funclinux).

Ez a rövid útmutató bemutatja, hogyan hozhatja létre Linuxon futó első függvényalkalmazását az Azure CLI-vel. A függvénykód helyben jön létre, és az [Azure Functions Core Tools](functions-run-local.md) segítségével lehet üzembe helyezni az Azure-ban.

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépeken követheti. Ez a cikk a függvények JavaScript vagy C# használatával való létrehozásához nyújt útmutatást. Python-függvények létrehozásával kapcsolatban lásd: [hozzon létre az első Python-függvény Core Tools és az Azure CLI (előzetes verzió) használatával](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához az alábbiakkal kell rendelkeznie:

+ Telepítse az [Azure Core Tools 2.x verzióját](functions-run-local.md#v2).

+ Telepítse az [Azure CLI-t]( /cli/azure/install-azure-cli). Ehhez a cikkhez az Azure CLI 2.0-ás vagy újabb verziója szükséges. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Használhatja az [Azure Cloud Shellt](https://shell.azure.com/bash) is.

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>A helyi függvényalkalmazás-projekt létrehozása

Futtassa a következő parancsot a parancssorból, hogy létrehozzon az aktuális helyi könyvtár `MyFunctionProj` mappájába egy függvényalkalmazás-projektet. A `MyFunctionProj` mappában egy GitHub-adattár is létrejön.

```bash
func init MyFunctionProj
```

Amikor a rendszer kéri, a nyíl billentyűk segítségével válasszon egy feldolgozói futtatókörnyezetet a következő nyelvek közül:

+ `dotnet`: létrehoz egy .NET osztálytárprojektet (.csproj).
+ `node`: egy JavaScript- vagy TypeScript-projektet hoz létre. Amikor a rendszer kéri, válassza ki a `JavaScript`.
+ `python`: egy Python-projektet hoz létre. Python-funkciók, lásd: a [Python rövid](functions-create-first-function-python.md).

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

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Linux-függvényalkalmazás létrehozása az Azure-ban

Rendelkeznie kell egy függvényalkalmazással a függvények Linux rendszerben való végrehajtásának biztosításához. A függvényalkalmazás egy kiszolgáló nélküli környezetet biztosít a függvénykód végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. Hozzon létre egy Linuxon futó függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) paranccsal.

A következő parancsban az `<app_name>` helyőrzőt cserélje le egy függvényalkalmazás egyedi nevére, a `<storage_name>` helyőrzőt pedig a tárfiók nevére. Az `<app_name>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, ezért az Azure összes alkalmazásában csak egyszer használhatja. Is be kell állítani a `<language>` futásidejű a függvényalkalmazás a `dotnet` (C#), `node` (JavaScript/TypeScript), vagy `python`.

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