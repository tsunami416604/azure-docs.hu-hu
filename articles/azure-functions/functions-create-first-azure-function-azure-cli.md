---
title: "Az első függvény létrehozása az Azure parancssori felületen | Microsoft Docs"
description: "Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure CLI használatával."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/08/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 4356d00b2694224f52a9359cd4a57d3a70a34d18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-function-using-the-azure-cli"></a>Az első függvény létrehozása az Azure CLI használatával

A gyors üzembe helyezés témakör bemutatja, hogyan hozzon létre az első függvényét az Azure Functions segítségével. Az Azure parancssori felület használatával hozzon létre egy függvény alkalmazást, amely a [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) infrastruktúra, amelyen a függvényt. Magát a függvénykódot a GitHub tárházában található minta alapján készíti el.    

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépen is követheti. 

## <a name="prerequisites"></a>Előfeltételek 

A minta futtatásához az alábbiakkal kell rendelkeznie:

+ Aktív [GitHub](https://github.com)-fiók. 
+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ez a témakör az Azure parancssori felület 2.0-s vagy újabb verziója szükséges. Futtatás `az --version` található verzió található. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 


[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód kiszolgáló nélküli végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#create) parancs használatával. 

Az alábbi parancs helyettesítse be egy egyedi alkalmazás függvénynév válaszoknál láthatja a `<app_name>` helyőrző és a tárfiók neve `<storage_name>`. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. A _telepítési-forrás-URL-címe_ paraméter tárháza mintát a Githubon, amely a "Hello World" HTTP indított függvényt tartalmaz.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--consumption-plan-location westeurope --deployment-source-url https://github.com/Azure-Samples/functions-quickstart
```
Beállítás a _fogyasztás-csomag-hely_ paraméter azt jelenti, hogy a függvény app fogyasztás üzemeltetési terv van tárolva. Ebben a tervben erőforrások hozzáadása a funkciók által megkövetelt dinamikusan, és csak akkor kell fizetnie, ha függvények futnak. További információ: [A megfelelő szolgáltatási csomag kiválasztása](functions-scale.md). 

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

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]