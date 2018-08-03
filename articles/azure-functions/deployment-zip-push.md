---
title: Azure Functions leküldéses üzembe helyezés zip |} A Microsoft Docs
description: A .zip fájl központi telepítési eszközök a Kudu telepítési szolgáltatás használatával elvégezhető a közzététel az Azure Functions.
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
ms.openlocfilehash: 3ff02816cdd5641cdcd78a12206b80be6d518373
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423708"
---
# <a name="zip-push-deployment-for-azure-functions"></a>Azure Functions zip leküldéses üzembe helyezés 
Ez a cikk ismerteti, hogyan helyezhet üzembe a függvény alkalmazás projektfájlok (tömörített) zip-fájlból az Azure-bA. További információt a leküldéses telepítési mind az Azure CLI-vel, és a REST API-k használatával. 

Az Azure Functions az Azure App Service által biztosított folyamatos üzembe helyezési és integrációs lehetőségek széles rendelkezik. További információkért lásd: [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md). 

Gyorsabb fejlesztés során az iteráció célszerű gyakran egyszerűbb, a függvény alkalmazás soubory projektu közvetlenül a tömörített .zip fájl. A .zip fájl központi telepítés használja ugyanazt a Kudu szolgáltatást, hogy powers folyamatos integráció-alapú környezetekhez, például:

+ A korábbi telepítések maradt fájlok törlése.
+ Telepítés testreszabása, beleértve a telepítési parancsfájlok futtatásához.
+ Telepítési naplók.
+ A függvény eseményindítóit szinkronizálása egy [Használatalapú csomag](functions-scale.md) függvényalkalmazást.

További információkért lásd: a [.zip leküldéses telepítési útmutató](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Telepítési .zip fájl követelményei
A .zip-fájlt, amely a leküldéses telepítéshez használ a projektfájlokat a függvényalkalmazásban, beleértve a függvénykódban mindegyikét tartalmaznia kell. 

>[!IMPORTANT]
> .Zip ügyfélleküldéses telepítés használatakor egy meglévő központi telepítéséből a .zip-fájlban nem található fájlok törlődnek a függvényalkalmazást.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Függvényalkalmazás tartalmazza az összes, a fájlok és mappák a `wwwroot` könyvtár. Egy .zip fájl központi telepítés tartalmát magában foglalja a `wwwroot` könyvtár, de nem maga a címtárban.  

## <a name="download-your-function-app-files"></a>A függvény alkalmazás fájlok letöltése

A helyi számítógép fejleszt, esetén egyszerűen hozhat létre egy .zip fájlt, a függvény-alkalmazás projekt mappa a fejlesztési számítógépen. 

Azonban előfordulhat, hogy hozott létre a függvények az Azure Portalon editor eszközével. Letöltheti egy meglévő függvényalkalmazás projektjét a következő módszerek egyikével: 

+ **Az Azure Portalról:** 

    1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és folytassa a függvényalkalmazáshoz.

    2. Az a **áttekintése** lapon jelölje be **alkalmazás tartalmának letöltése**. A letöltési beállítások, majd válassza ki és **letöltése**.     

        ![Töltse le a függvényalkalmazás projektjét](./media/deployment-zip-push/download-project.png)

    A letöltött zip-fájlt kell tenni a függvényalkalmazáshoz .zip ügyfélleküldéses telepítés használatával a megfelelő formátumban van. A portál letöltés is hozzáadhat a fájlok közvetlenül a Visual Studióban a függvényalkalmazás megnyitásához szükséges.

+ **REST API-k használatával:** 

    Az alábbi üzembe helyezési beolvasása API használatával töltse le a fájlokat a `<function_app>` projekt: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Beleértve a `/site/wwwroot/` biztosítja, hogy a zip-fájlt tartalmaz, csak a függvény alkalmazás projektfájlok, és nem a teljes helyre. Ha Ön már aláírással nem Azure-ba, a rendszer kéri, ennek a végrehajtására. Vegye figyelembe, hogy küld egy POST kérelmet a `api/zip/` API egy discoraged értéke a zip-telepítési módszer, a jelen témakörben. 

A GitHub-adattárból is letöltheti egy .zip-fájlban. Ne feledje, hogy egy GitHub-adattár .zip-fájlként tölti le, amikor GitHub egy külön mappát szint ágának hozzáad. Az extra mappa szint azt jelenti, hogy, hogy a zip-fájlt közvetlenül, nem telepíthet központilag letöltése a Githubról. Ha egy GitHub-tárházat a függvényalkalmazás fenntartásához használja, akkor célszerű használni [folyamatos integráció](functions-continuous-deployment.md) helyezze üzembe az alkalmazást.  

## <a name="cli"></a>Azure CLI-vel üzembe helyezése

Azure CLI használatával aktiválhat egy leküldéses telepítés. Leküldéses tartalmazó .zip-fájlt a függvényalkalmazás segítségével telepítheti a [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) parancsot. Használja ezt a parancsot, használja az Azure CLI 2.0.21-es vagy újabb. Milyen Azure CLI-vel a jelenleg használt verzió megtekintéséhez használja a `az --version` parancsot.

A következő parancsban cserélje le a `<zip_file_path>` helyőrző az elérési útját a .zip fájl helyét. Továbbá cserélje le `<app_name>` a függvényalkalmazás egyedi nevét. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Ez a parancs üzembe helyezi a projektfájlokat a következő helyről a letöltött zip-fájlt a függvényalkalmazáshoz az Azure-ban. Majd újraindítja az alkalmazást. Ez a funkció az alkalmazás központi telepítésének listájának megtekintéséhez, a REST API-kat kell használnia.

Ha a helyi számítógépen, használja az Azure CLI `<zip_file_path>` elérési útja a .zip-fájlt a számítógépen. Azure CLI-vel is futtathatja [Azure Cloud Shell](../cloud-shell/overview.md). A Cloud Shell használata esetén a központi telepítési .zip fájl először fel kell tölteni az Azure Files-fiókot, amely a Cloud Shell van társítva. Ebben az esetben `<zip_file_path>` a Cloud Shell-fiókot használó tárolási helyét. További információkért lásd: [fájlok az Azure Cloud Shellben megtartása](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
