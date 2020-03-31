---
title: Zip push üzembe helyezés az Azure Functions-hez
description: Használja a .zip fájl telepítési lehetőségek a Kudu telepítési szolgáltatás az Azure Functions közzétételéhez.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: 6bda0859ca4741fe74f572b204e40130c56c46fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769667"
---
# <a name="zip-deployment-for-azure-functions"></a>Azure Functions üzembe helyezése ZIP-fájlokkal

Ez a cikk ismerteti, hogyan telepítheti a függvényalkalmazás-projektfájlokat az Azure-ban egy .zip (tömörített) fájlból. Megtudhatja, hogyan hajthet leküldéses központi telepítést, az Azure CLI használatával és a REST API-k használatával. [Az Azure Functions Core Tools](functions-run-local.md) ezeket a központi API-kat is használja, amikor egy helyi projektet tesz közzé az Azure-ban.

Az Azure Functions az Azure App Service által biztosított folyamatos üzembe helyezési és integrációs lehetőségek teljes választékával rendelkezik. További információ: [Folyamatos üzembe helyezés az Azure Functions számára.](functions-continuous-deployment.md)

A fejlesztés felgyorsítása érdekében egyszerűbben telepítheti a függvényalkalmazás-projektfájlokat közvetlenül egy .zip fájlból. A .zip központi telepítési API egy .zip fájl tartalmát `wwwroot` veszi fel, és kibontja a tartalmat a függvényalkalmazás mappájába. Ez a .zip fájltelepítés ugyanazt a Kudu szolgáltatást használja, amely folyamatos integrációalapú telepítéseket hajt elő, többek között a következőket:

+ A korábbi központi telepítésekből megmaradt fájlok törlése.
+ A telepítés testreszabása, beleértve a központi telepítési parancsfájlok futtatását is.
+ Telepítési naplók.
+ A szinkronizálási függvény egy [fogyasztási terv](functions-scale.md) függvényalkalmazásban aktiválódik.

További információt a [.zip telepítési útmutatóban](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)talál.

## <a name="deployment-zip-file-requirements"></a>A központi telepítési .zip fájl követelményei

A leküldéses telepítéshez használt .zip fájlnak tartalmaznia kell a funkció futtatásához szükséges összes fájlt.

>[!IMPORTANT]
> A .zip központi telepítés használatakor a .zip fájlban nem található meglévő központi telepítésből származó fájlok törlődnek a függvényalkalmazásból.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A függvényalkalmazás a `wwwroot` címtárban lévő összes fájlt és mappát tartalmazza. A .zip fájl központi telepítése `wwwroot` tartalmazza a könyvtár tartalmát, de magát a könyvtárat nem. C# osztálykönyvtár-projekt telepítésekor a lefordított könyvtárfájlokat és -függőségeket a .zip csomag almappájába `bin` kell foglalnia.

## <a name="download-your-function-app-files"></a>A függvényalkalmazás-fájlok letöltése

Ha helyi számítógépen fejleszt, egyszerűen létrehozhat egy .zip fájlt a függvényalkalmazás-projekt mappából a fejlesztőszámítógépen.

Azonban előfordulhat, hogy az Azure Portal szerkesztője segítségével hozta létre a függvényeket. Egy meglévő függvényalkalmazás-projektet az alábbi módokon tölthet le:

+ **Az Azure portalról:**

  1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)majd nyissa meg a függvényalkalmazást.

  2. Az **Áttekintés** lapon válassza **az Alkalmazástartalom letöltése**lehetőséget. Adja meg a letöltési beállításokat, majd kattintson a **Letöltés gombra.**

      ![A függvényalkalmazás-projekt letöltése](./media/deployment-zip-push/download-project.png)

     A letöltött .zip fájl a megfelelő formátumú, hogy újra közzé kell tenni a függvényalkalmazás segítségével .zip push deployment. A portál letöltése is hozzáadhatja a függvényalkalmazás közvetlen megnyitásához szükséges fájlokat közvetlenül a Visual Studióban.

+ **REST API-k használata:**

    A következő központi telepítési GET API-val töltse le a fájlokat a `<function_app>` projektből: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Beleértve `/site/wwwroot/` biztosítja, hogy a zip fájl csak a függvény alkalmazás projekt fájlokat, és nem az egész oldalon. Ha még nem jelentkezett be az Azure-ba, a rendszer megkéri, hogy tegye meg.  

A .zip fájlt egy GitHub-tárházból is letöltheti. Amikor egy GitHub-tárházat .zip fájlként tölt le, a GitHub egy további mappaszintet ad hozzá az ághoz. Ez az extra mappaszint azt jelenti, hogy nem telepítheti a .zip fájlt közvetlenül, amikor letöltötte a GitHubról. Ha egy GitHub-tárházat használ a függvényalkalmazás karbantartásához, [folyamatos integrációt](functions-continuous-deployment.md) kell használnia az alkalmazás üzembe helyezéséhez.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Üzembe helyezés az Azure CLI-vel

Az Azure CLI használatával leküldéses üzembe helyezést indíthat el. Az [az functionapp telepítési forrás config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) parancs használatával leküldéses.deploy a .zip file to your function app by using the functionapp deployment source config-zip command. A parancs használatához az Azure CLI 2.0.21-es vagy újabb verzióját kell használnia. A használt Azure CLI-verzió megtekintéséhez `az --version` használja a parancsot.

A következő parancsban `<zip_file_path>` cserélje le a helyőrzőt a .zip fájl helyére vezető elérési útra. Emellett cserélje `<app_name>` le a függvényalkalmazás egyedi `<resource_group>` nevét, és cserélje le az erőforráscsoport nevét.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Ez a parancs a letöltött .zip fájlból származó projektfájlokat telepíti az Azure-beli függvényalkalmazásba. Ezután újraindítja az alkalmazást. A függvényalkalmazás központi telepítései listájának megtekintéséhez a REST API-kat kell használnia.

Ha az Azure CLI-t használja `<zip_file_path>` a helyi számítógépen, ez a számítógépen lévő .zip fájl elérési útja. Az Azure CLI-t az [Azure Cloud Shellben](../cloud-shell/overview.md)is futtathatja. A Cloud Shell használatakor először fel kell töltenie a központi telepítési .zip fájlt a Cloud Shellhez társított Azure Files-fiókba. Ebben az `<zip_file_path>` esetben a Cloud Shell-fiók által használt tárolási hely. További információ: [Fájlok megőrzése az Azure Cloud Shellben](../cloud-shell/persisting-shell-storage.md)című témakörben talál.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Függvények futtatása a központi telepítési csomagból

Azt is választhatja, hogy a függvényeket közvetlenül a központi telepítési csomag fájlból futtatja. Ez a módszer kihagyja a fájlok másolásának telepítési `wwwroot` lépését a csomagból a függvényalkalmazás könyvtárába. Ehelyett a csomagfájlt a Functions futásidő csatlakoztatja, és `wwwroot` a könyvtár tartalma írásvédetté válik.  

A zip-telepítés integrálódik ezzel a funkcióval, amelyet a `1`függvényalkalmazás beállításának `WEBSITE_RUN_FROM_PACKAGE` beállításával engedélyezhet. További információt [a Függvények futtatása központi telepítési csomagfájlból](run-functions-from-deployment-package.md)című témakörben talál.

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
