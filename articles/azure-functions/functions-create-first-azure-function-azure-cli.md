---
title: "Az első függvény létrehozása az Azure parancssori felületen | Microsoft Docs"
description: "Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure CLI használatával."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: hero-article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 2292b35819c5a98b690041e10f6e6d1a93fa7837
ms.contentlocale: hu-hu
ms.lasthandoff: 06/20/2017

---

<a id="create-your-first-function-using-the-azure-cli" class="xliff"></a>

# Az első függvény létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan hozhatja létre az első függvényét az Azure Functions használatával. Az Azure CLI használatával létrehoz egy függvényalkalmazást, amely az a kiszolgáló nélküli infrastruktúra lesz, amelyen a függvény működni fog. Magát a függvénykódot a GitHub tárházában található minta alapján készíti el.    

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépen is követheti. 

<a id="prerequisites" class="xliff"></a>

## Előfeltételek 

A minta futtatásához az alábbiakkal kell rendelkeznie:

+ Aktív [GitHub](https://github.com)-fiók. 
+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 


<a id="create-a-resource-group" class="xliff"></a>

## Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például a függvényalkalmazásokat, az adatbázisokat és a tárfiókokat).

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
<a id="create-an-azure-storage-account" class="xliff"></a>

## Azure Storage-fiók létrehozása

A Functions szolgáltatás Azure Storage-fiókot használ a függvények állapotának és egyéb adatainak kezeléséhez. Az [az storage account create](/cli/azure/storage/account#create) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban.

A következő parancsban a `<storage_name>` helyőrző helyett írja be a saját globálisan egyedi tárfióknevét. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

A tárfiók létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

<a id="create-a-function-app" class="xliff"></a>

## Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód kiszolgáló nélküli végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, telepítése és megosztása érdekében. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#create) parancs használatával. 

A következő parancsban a `<app_name>` helyőrző helyett írja be a saját globálisan egyedi tárfióknevét, a `<storage_name>` helyőrző helyett pedig a tárfiók nevét. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
Alapértelmezés szerint a függvényalkalmazást a Használatalapú futtatási csomaggal együtt hozza létre, ami azt jelenti, hogy az erőforrások hozzáadása dinamikusan történik a függvények igényei szerint, és csak akkor kell fizetnie, amikor a függvények futnak. További információ: [A megfelelő szolgáltatási csomag kiválasztása](functions-scale.md). 

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

Miután már van egy függvényalkalmazása, elkészítheti a tényleges függvénykódot a GitHub tárházában található mintákból.

<a id="deploy-your-function-code" class="xliff"></a>

## A függvénykód létrehozása  

A függvénykódot többféleképpen is elkészítheti az új függvényalkalmazásban. Ez a témakör GitHub mintatárházához kapcsolódik. Az eddigiekhez hasonlóan a `<app_name>` helyőrzőt cserélje le a létrehozott függvényalkalmazás nevére. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
A forrás beállítása után az Azure CLI a következő példában szereplőkhöz hasonló információkat jelenít meg (a null értékeket eltávolítottuk az olvashatóság érdekében):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

<a id="test-the-function" class="xliff"></a>

## A függvény tesztelése

Ellenőrizze az üzembe helyezett függvényt a cURL használatával Mac vagy Linux rendszerű számítógépen vagy a Bash használatával Windows rendszerű számítógépen. Hajtsa végre a következő cURL parancsot úgy, hogy a `<app_name>` helyőrzőt lecseréli a függvényalkalmazás nevére. Fűzze hozzá a `&name=<yourname>` lekérdezési karakterláncot az URL-hez.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![A függvény válasza a böngészőben jelenik meg.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

Ha a parancssorban nem érhető el a cURL, egyszerűen adja meg az adott URL-címet a böngésző címsorában. Most is cserélje le a `<app_name>` helyőrzőt a függvényalkalmazás nevére, fűzze hozzá a `&name=<yourname>` lekérdezési karakterláncot az URL-hez, és hajtsa végre a kérést. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![A függvény válasza a böngészőben jelenik meg.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

<a id="clean-up-resources" class="xliff"></a>

## Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti az ehhez a rövid útmutatóhoz létrehozott összes erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup
```
Amikor a rendszer kéri, írja be a `y` parancsot.

<a id="next-steps" class="xliff"></a>

## Következő lépések

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

