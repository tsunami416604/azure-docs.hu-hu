---
title: "Az első függvény létrehozása Linux az Azure parancssori felületen (előzetes verzió) |} Microsoft Docs"
description: "Útmutató az első olyan alapértelmezett Linux lemezkép az Azure parancssori felülettel futó Azure-függvény létrehozása."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: d04e2000f2043e8bb11e15f6b9d7fd06ef5b9da3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Az első függvényét az Azure parancssori felülettel (előzetes verzió) Linux rendszeren futó létrehozása

Az Azure Functions lehetővé teszi a funkciók Linux egy alapértelmezett Azure App Service tárolóban tárolni. Ez a funkció jelenleg előzetes verzió. Emellett [kapcsolja a saját egyéni tároló](functions-create-function-linux-custom-image.md). 

A gyors üzembe helyezés témakör végigvezeti Azure Functions használata az Azure parancssori felület függvény első alkalmazását az App Service-tároló alapértelmezett üzemeltetett Linux létrehozásához. A függvény a forráskód egy minta GitHub-adattár a rendszer a kép.    

Az alábbi lépéseket a Mac, a Windows vagy Linux számítógép támogatottak. 

## <a name="prerequisites"></a>Előfeltételek 

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez a témakör igényel-e az Azure parancssori felület 2.0.21 verzió vagy újabb. Futtatás `az --version` található verzió található. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>A Linux App Service-csomag létrehozása

Linux üzemeltetési funkciók a jelenleg csak az App Service-csomagot támogatják. Felhasználás terv üzemeltető még nem támogatott. Üzemeltetési kapcsolatos további információkért lásd: [összehasonlítása az Azure Functions üzemeltetési tervek](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Függvény-alkalmazás létrehozása Linux rendszeren

Egy függvény alkalmazásnak, hogy az a függvények végrehajtásához szükséges Linux gazdagép kell rendelkeznie. A függvény app függvény kód környezetet biztosít. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. A függvény-alkalmazás létrehozása a [az functionapp létrehozása](/cli/azure/functionapp#create) parancsot egy Linux App Service-csomagot. 

Az alábbi parancs helyettesítse be egy egyedi alkalmazás függvénynév válaszoknál láthatja a `<app_name>` helyőrző és a tárfiók neve `<storage_name>`. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. A _telepítési-forrás-URL-címe_ paraméter tárháza mintát a Githubon, amely a "Hello World" HTTP indított függvényt tartalmaz.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
A függvény után alkalmazás létrehozása és központi telepítése megtörtént, az Azure parancssori felület kapcsolatos adatokat jeleníti meg az alábbi példához hasonló:

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

Mivel `myAppServicePlan` egy Linux-csomagnak a beépített docker lemezképet létrehozni a tárolót, a függvény app futó Linux használja. 

>[!NOTE]  
>A minta-tárház jelenleg magában foglalja a parancsfájl-kezelési két fájlt, [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) és [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). A .deployment fájl meghatározza, hogy a központi telepítési folyamatának deploy.sh, használja a [egyéni telepítési parancsfájl](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). Az aktuális előzetes kiadásban parancsfájlok a Linux-lemezképen függvény alkalmazás telepítéséhez szükséges.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
