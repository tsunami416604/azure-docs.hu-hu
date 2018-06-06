---
title: A ZIP-leküldéses telepítése az Azure Functions |} Microsoft Docs
description: A .zip fájl központi telepítési eszközökkel a Kudu telepítési szolgáltatás segítségével az Azure Functions közzététele.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/29/2018
ms.author: glenga
ms.openlocfilehash: 91c16ad5a6bf8babffc0b83d801626932688631e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699954"
---
# <a name="zip-push-deployment-for-azure-functions"></a>Az Azure Functions zip leküldéses telepítés 
Ez a cikk ismerteti a függvény app projektfájlok telepítse az Azure (tömörített) .zip fájlból. További információt a ügyfélleküldéses telepítést, mind az Azure parancssori felület használatával, és a REST API-k használatával. 

Az Azure Functions rendelkezik az összes Azure App Service által biztosított folyamatos és -integrációs beállítások. További információkért lásd: [folyamatos üzembe helyezés az Azure Functions](functions-continuous-deployment.md). 

A fejlesztés során gyorsabb iterációs esetén gyakran egyszerűbb, a függvény app projektfájlok közvetlenül a zip-fájlban. A .zip fájl központi telepítéshez használja ugyanezt a Kudu szolgáltatást, hogy powers folyamatos integráció-alapú környezetekhez, például:

+ A korábbi telepítések maradt fájlok törlése.
+ Telepítés testreszabása, beleértve a telepítési parancsfájlok futtatása.
+ Központi telepítés naplófájljai.
+ A függvény eseményindítók szinkronizálása egy [fogyasztás terv](functions-scale.md) függvény alkalmazást.

További információkért lásd: a [.zip leküldéses telepítési útmutató](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>A telepítési .zip fájl követelményei
A zip-fájlt a leküldéses telepítési használt összes az függvény alkalmazásban, beleértve a funkciókódot projekt fájlt kell tartalmaznia. 

>[!IMPORTANT]
> .Zip ügyfélleküldéses telepítés használatakor a azokat a fájlokat, egy meglévő központi telepítéséből a .zip-fájlban nem található, a függvény alkalmazás törlődnek.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Egy függvény alkalmazást tartalmazza az összes, a fájlok és mappák a `wwwroot` könyvtár. Egy .zip fájl központi telepítés tartalmát magában foglalja a `wwwroot` könyvtár, de nem maga a könyvtárban.  

## <a name="download-your-function-app-files"></a>A függvény app fájlok letöltése

A helyi számítógép fejleszt, esetén könnyen egy zip-fájl, a függvény app projektet mappa létrehozását a fejlesztési számítógépen. 

Azonban előfordulhat, hogy létrehozta a funkciók az Azure-portálon a szerkesztő használatával. Az alábbi módszerek egyikével meglévő függvény alkalmazás projekt tölthető le: 

+ **Az Azure-portálon:** 

    1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), és keresse meg a függvény alkalmazást.

    2. Az a **áttekintése** lapon jelölje be **töltse le az alkalmazástartalom**. A letöltési lehetőségeit, majd válassza ki és **letöltése**.     

        ![A függvény alkalmazásprojektet letöltése](./media/deployment-zip-push/download-project.png)

    A letöltött zip-fájlt a .zip leküldéses központi telepítéssel függvény alkalmazása újból közzé a megfelelő formátumban van. A portál letöltés nyissa meg a függvény alkalmazást közvetlenül a Visual Studio szükséges fájlok is hozzáadhat.

+ **REST API-k használatával:** 

    A következő központi telepítést beszerzése API használatával töltse le a fájlokat a `<function_app>` projekt: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Beleértve `/site/wwwroot/` biztosítja, hogy a zip-fájl csak az függvény alkalmazás projekt fájljai és nem a teljes webhelyet tartalmaz. Ha nincs már bejelentkezve Azure-ba, a rendszer kéri, hogy ehhez. Vegye figyelembe, hogy a FELADÁS egy vagy több küldése elküldeni a kérelmet a `api/zip/` API discoraged helyett a jelen témakörben leírt zip módszer. 

Egy .zip fájl is letölthető a GitHub-tárházban. Ne feledje, hogy a GitHub-tárházban .zip-fájlként letöltésekor GitHub biztosít extra mappa a fiókiroda számára. Ez további mappa szint azt jelenti, hogy a zip-fájlt közvetlenül, ha Ön nem telepíthet központilag le a Githubról. A függvény app fenntartásához használata a GitHub-tárházban, használjon [folyamatos integrációt](functions-continuous-deployment.md) segítségével telepítse az alkalmazást.  

## <a name="cli"></a>Központi telepítése az Azure parancssori felület használatával

Az Azure parancssori felület használatával indul el, a leküldéses telepítés. Leküldéses tartalmazó .zip-fájlt az függvény alkalmazás segítségével telepítheti a [az functionapp telepítési forrás config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) parancsot. Ezen parancs használatához Azure CLI 2.0.21 verziót kell használnia, vagy később. Használja az Azure parancssori felület verziójától megjelenítéséhez használja a `az --version` parancsot.

A következő parancsban cserélje le a `<zip_file_path>` helyőrzőt a .zip fájl helyének elérési útvonalát. Továbbá cserélje le `<app_name>` egyedi nevű, a függvény alkalmazást. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Ez a parancs telepíti projektfájlokat a letöltött .zip fájlból az függvény alkalmazást az Azure-ban. Az alkalmazás majd újraindul. A függvény alkalmazás központi telepítésének listájának megtekintéséhez, a REST API-k kell használnia.

Ha szeretne megtudni az Azure parancssori felület a helyi számítógépen `<zip_file_path>` elérési útját a .zip-fájlt a számítógépen. Az Azure parancssori felület is futtathatja [Azure Cloud rendszerhéj](../cloud-shell/overview.md). Felhő rendszerhéj használata esetén először fel kell tölteni a központi telepítés .zip-fájlt a Azure fájlok fiókot, amely a felhő rendszerhéj van társítva. Ebben az esetben `<zip_file_path>` a tárolási hely, amely a felhő rendszerhéj fiókját használja. További információkért lásd: [megőrizni a fájlok az Azure felhőalapú Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
