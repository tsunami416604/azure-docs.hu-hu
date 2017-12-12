---
title: "A ZIP-leküldéses telepítése az Azure Functions |} Microsoft Docs"
description: "A .zip fájl központi telepítési eszközökkel a Kudu telepítési szolgáltatás segítségével az Azure Functions közzététele."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/06/2017
ms.author: glenga
ms.openlocfilehash: 5c8c608126f20aa5f5dc52bb8e24cfec14fd00f5
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Az Azure Functions zip leküldéses telepítés 
Ez a témakör ismerteti a függvény app projektfájlok telepítse az Azure (tömörített) .zip fájlból. Megtanulhatja az Azure parancssori felület és a REST API-k használatával leküldéses telepítésére. Az Azure Functions rendelkezik az összes folyamatos üzembe helyezés és integrációs megoldásait Azure App Service-ben további információkért lásd: [folyamatos üzembe helyezés az Azure Functions](functions-continuous-deployment.md). Azonban a fejlesztés során gyorsabb iterációs célszerű gyakran egyszerűbb, a függvény app projektfájlok közvetlenül a zip-fájlban.

A .zip fájl központi telepítés használja ugyanezt a Kudu szolgáltatást, hogy powers folyamatos integráció-alapú telepítések esetén, amely a következő funkciókat tartalmazza:

+ Egy korábbi telepítésből csatlakoztatási kísérletéből fájlok törlése.
+ Telepítés testreszabása, beleértve a telepítési parancsfájlok futtatása.
+ Központi telepítés naplófájljai.
+ A függvény eseményindítók szinkronizálása egy [fogyasztás terv](functions-scale.md) függvény alkalmazást.

További információkért lásd: a [.zip leküldéses telepítési referencia-témakör]. 

## <a name="requirements-of-the-deployment-zip-file"></a>Az üzembe helyezési .zip fájl követelményei 
A zip-fájlt, használhatja a leküldéses telepítés összes a projektfájlok, a függvény alkalmazásban, beleértve a funkciókódot kell tartalmazza. 

>[!IMPORTANT]
> .Zip ügyfélleküldéses telepítés használatakor a .zip-fájlban nem található egy meglévő központi telepítéséből a fájlok törlődnek, a függvény alkalmazás.  

### <a name="function-app-folder-structure"></a>Függvény app mappaszerkezet

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="downloading-your-function-app-project"></a>A függvény alkalmazás projekt letöltése

Helyi fejlesztési során, akkor egyszerűen hozzon létre egy .zip fájlt, a függvény app projektet mappa a fejlesztési számítógépen. Azonban előfordulhat, hogy is létrehozta a Funkciók, a portál szerkesztő használatával. A függvény alkalmazásprojektet letöltése a portálról: 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és keresse meg a függvény alkalmazást.

2. A a **áttekintése** lapon jelölje be **töltse le az alkalmazástartalom**, válassza ki a letöltés beállításait, és kattintson **letöltése**.     

    ![A függvény alkalmazásprojektet letöltése](./media/deployment-zip-push/download-project.png)

A letöltött zip-fájlt a függvény az alkalmazásnak .zip leküldéses telepítési használatával újból közzé a megfelelő formátumban van.

GitHub lehetővé teszi egy .zip fájl letöltése a tárházból. Ne feledje, hogy a GitHub-tárházban .zip-fájlként letöltésekor GitHub ad hozzá egy külön mappát a fiókiroda, ami azt jelenti, hogy nem telepíthet központilag a .zip fájl le a Githubról, szint. Ha egy GitHub-tárházban segítségével végzi a függvény alkalmazás karbantartása, használjon [folyamatos integrációt](functions-continuous-deployment.md) segítségével telepítse az alkalmazást.  

## <a name="cli"></a>Telepítheti az Azure parancssori felület használatával

Az Azure parancssori felület használatával indul el, a leküldéses telepítés. Leküldéses tartalmazó .zip-fájlt az függvény alkalmazás segítségével telepítheti a [az functionapp telepítési forrás config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) parancsot. Azure CLI 2.0.21 verziót kell használnia, vagy később. Használja a `az --version` parancsot mely verzióját használja.

A következő parancsban cserélje le a `<zip_file_path>` helyőrzőt a .zip fájl helyének elérési útvonalát. Továbbá cserélje le `<app_name>` egyedi nevű, a függvény alkalmazást. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Ez a parancs telepíti projektfájlokat a letöltött .zip fájlból az függvény alkalmazást az Azure-ban, és az alkalmazás újraindul. A függvény alkalmazás központi telepítésének listájának megtekintéséhez, a REST API-k kell használnia.

A helyi számítógépen, az Azure parancssori felület használata közben `<zip_file_path>` elérési útját a .zip-fájlt a számítógépen. Azure parancssori felület is futtathatja a [Azure Cloud rendszerhéj](../cloud-shell/overview.md). Felhő rendszerhéj használata esetén először fel kell tölteni a központi telepítés .zip-fájlt a felhő rendszerhéj társított Azure File fiók. Ebben az esetben `<zip_file_path>` a tárolási hely felhő rendszerhéj fiókját használják. További információkért lásd: [megőrizni a fájlok az Azure felhőalapú Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[.zip leküldéses telepítési referencia-témakör]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
