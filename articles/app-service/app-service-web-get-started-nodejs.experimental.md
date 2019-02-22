---
title: Node.js-webalkalmazás – az Azure App Service létrehozása |} A Microsoft Docs
description: Percek alatt üzembe helyezheti első Hello World Node.js-alkalmazását az App Service Web Apps szolgáltatásban.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 237f498d1ebe2b402c86f1a4aed66a7ed443ccfa
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652530"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [Node.js-webalkalmazás létrehozása a Linuxon futó Azure App Service-ben](./containers/quickstart-nodejs.md).
>

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan Node.js-alkalmazás üzembe helyezése az Azure App Service-ben. Létrehozhatja a webalkalmazást a [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), de ezek a parancsok a helyileg is futtathatja [Azure CLI-vel](/cli/azure/install-azure-cli). A Node.js-mintakód üzembe helyezése a web app használatával a [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancsot.  

![Az Azure-ban futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Ezeket a lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti. Körülbelül 3 percenként, majd hajtsa végre a vesz igénybe.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>A minta letöltése

A Cloud Shellben hozzon létre egy quickstart könyvtárat, és lépjen a könyvtárba.

```azurecli-interactive
mkdir quickstart

cd quickstart
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a quickstart könyvtárba való klónozásához.

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> A minta index.js figyelőport process.env.PORT állítja be. Ez a környezeti változó az App Service által hozzárendelt.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal hozzon létre egy webalkalmazást a `myAppServicePlan` App Service-csomagban.

A következő példában cserélje ki az `<app_name>` nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Node.js-futtatókörnyezet beállítása

A csomópont-futtatókörnyezet 10.14.1 beállítása. Az összes támogatott futtatókörnyezet megtekintéséhez futtassa az [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) parancsot.

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Tallózással keresse meg az újonnan létrehozott webalkalmazást. Cserélje le `<app_name>` egy egyedi névre.

```
http://<app_name>.azurewebsites.net
```

Az új webalkalmazásnak így kell kinéznie: ![Üres webalkalmazás oldal](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>ZIP-fájl telepítése

A Cloud shellben keresse meg az alkalmazás gyökérkönyvtárában, hozzon létre egy új, a mintaprojektet a ZIP-fájlba.

```azurecli-interactive
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

A ZIP-fájlt a webalkalmazás segítségével telepítheti a [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancsot.  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást. Ha további egyéni kiépítési folyamatok vannak megadva, azokat is futtatja. További információkért lásd: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```
http://<app_name>.azurewebsites.net
```

A Node.js mintakód az Azure App Service webalkalmazásban fut.

![Az Azure-ban futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> Az Azure App Service-ben az alkalmazás IIS-ben, [iisnode](https://github.com/Azure/iisnode) használatával fut. Az alkalmazás iisnode-dal való futtatásának engedélyezéséhez az alkalmazás gyökérmappája tartalmaz egy web.config fájlt. A fájl IIS-sel olvasható és az iisnode-hoz kapcsolódó beállítások [az iisnode GitHub-adattárban](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config) vannak dokumentálva.

**Gratulálunk!** Elvégezte az első Node.js-app üzembe helyezését az App Service-ben.

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

Írja be a Cloud shellben `code index.js` a Cloud Shell-szerkesztő megnyitásához.

![Kód index.js](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Végezzen el egy kis módosítást a `response.end` hívásának szövegében:

```javascript
response.end("Hello Azure!");
```

Mentse a módosításokat, és zárja be a szerkesztőt. A mentéshez a `^S`, a kilépéshez a `^Q` parancsot használja.

Hozzon létre egy ZIP-fájlt, és használatával telepítheti a [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancsot.  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Az Azure-ban futó frissített mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson a **App Services**, majd kattintson az Azure-alkalmazás neve.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service lap az Azure Portalon](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Node.js és MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
