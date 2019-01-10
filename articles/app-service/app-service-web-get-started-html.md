---
title: Statikus HTML-webalkalmazás – az Azure App Service létrehozása |} A Microsoft Docs
description: Egy statikus HTML-webalkalmazás üzembe helyezésével megtudhatja, hogy miként futtathat webalkalmazásokat az Azure App Service-ben.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/20/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2a6378406272f502af107a151e69fe33954f7780
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187859"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Statikus HTML-webalkalmazás létrehozása az Azure-ban

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. A rövid útmutató bemutatja, hogyan helyezhet üzembe egy egyszerű HTML + CSS webhelyet az Azure App Service-ben. Ezt a rövid útmutatót a [Cloud Shellben](https://docs.microsoft.com/azure/cloud-shell/overview) végezzük el, a parancsok azonban helyileg is futtathatók az [Azure CLI](/cli/azure/install-azure-cli) használatával.

![Mintaalkalmazás kezdőlapja](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>A minta letöltése

A Cloud Shellben hozzon létre egy quickstart könyvtárat, és lépjen a könyvtárba.

```bash
mkdir quickstart

cd quickstart
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a quickstart könyvtárba való klónozásához.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Lépjen abba a könyvtárba, amelyben a mintakód található, és futtassa az `az webapp up` parancsot.

A következő példában az <app_name> helyett adjon meg egy egyedi alkalmazásnevet.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name>
```

Az `az webapp up` parancs a következő műveleteket hajtja végre:

- Egy alapértelmezett erőforráscsoport létrehozása.

- Egy alapértelmezett App Service-csomag létrehozása.

- Egy alkalmazás létrehozása a megadott néven.

- Fájlok [tömörített üzembe helyezése](https://docs.microsoft.com/azure/app-service/deploy-zip) az aktuális munkakönyvtárból a webalkalmazásba.

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

```json
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "westeurope",
  "name": "<app_name>",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/<username>/quickstart/html-docs-hello-world ",
  < JSON data removed for brevity. >
}
```

Jegyezze fel a `resourceGroup` értékét. Szüksége lesz rá [az erőforrások eltávolításával](#clean-up-resources) kapcsolatos szakaszban.

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Egy böngészőben nyissa meg az alkalmazás URL-címe: `http://<app_name>.azurewebsites.net`.

Az oldal Azure App Service webalkalmazásként fut.

![Mintaalkalmazás kezdőlapja](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

**Gratulálunk!** Elvégezte az első HTML-webapp üzembe helyezését az App Service-ben.

## <a name="update-and-redeploy-the-app"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A Cloud Shellben írja be a `nano index.html` parancsot a nano szövegszerkesztő megnyitásához. A `<h1>` fejléccímkében az „Azure App Service – Sample Static HTML Site” szöveget módosítsa „Azure App Service”-re a lent látható módon.

![Nano index.html](media/app-service-web-get-started-html/nano-index-html.png)

Mentse a módosításokat, és lépjen ki a nanóból. A mentéshez a `^O`, a kilépéshez a `^X` parancsot használja.

Most újra üzembe helyezzük az alkalmazást ugyanazzal az `az webapp up` paranccsal.

```bash
az webapp up --location westeurope --name <app_name>
```

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Frissített mintaalkalmazás kezdőlapja](media/app-service-web-get-started-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson a **App Services**, majd kattintson az Azure-alkalmazás neve.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-html/portal1.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service panel az Azure Portalon](./media/app-service-web-get-started-html/portal2.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs Cloud Shellben történő futtatásával. Ne feledje, hogy az erőforráscsoport neve automatikusan jött létre a [webalkalmazást létrehozó](#create-a-web-app) lépés során.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

A parancs futtatása egy percig is eltarthat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)
