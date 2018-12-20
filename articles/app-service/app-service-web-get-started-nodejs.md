---
title: Node.js-webalkalmazás – az Azure App Service létrehozása |} A Microsoft Docs
description: Percek alatt üzembe helyezheti első Hello World Node.js-alkalmazását az App Service Web Apps szolgáltatásban.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2018
ms.author: cephalin;msangapu
ms.custom: seodec18
ms.openlocfilehash: 5223a60663e05747b58aa04e7a102f1d350badd4
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632297"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [Node.js-webalkalmazás létrehozása a Linuxon futó Azure App Service-ben](./containers/quickstart-nodejs.md).
>

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan Node.js-alkalmazás üzembe helyezése az Azure App Service-ben. Az [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) létrehozhatja a webalkalmazást, a ZipDeploy szoftver használatával pedig üzembe helyezheti a Node.js-mintakódot a webalkalmazásban.

![Az Azure-ban futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Ezeket a lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti. Az előfeltételek telepítése után a lépések végrehajtása nagyjából öt percet vesz igénybe.   

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* <a href="https://nodejs.org/" target="_blank">Telepítse a Node.js-t és az NPM-et</a>

## <a name="download-the-sample"></a>A minta letöltése

Töltse le a Node.js mintaprojektet a [https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) címről, és bontsa ki a ZIP-archívumot.

Nyissa meg az _index.js_ fájlt, és keresse meg a következő sort:

```javascript
var port = process.env.PORT || 1337;
```

Az App Service a process.env.PORT változót beilleszti az alkalmazásba, így a kód a változó segítségével tudni fogja, hogy melyik portot kell figyelni. 

Egy terminálablakban keresse meg a minta Node.js-projekt gyökérkönyvtárát (ez tartalmazza az _index.js_ fájlt).

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa helyileg az alkalmazást, hogy lássa, hogyan fog kinézni az Azure-ban üzembe helyezve. A beépített Node.js HTTP-kiszolgáló a terminálablak megnyitásával és az `npm start` szkript használatával indítható el.

```bash
npm start
```

Nyisson meg egy webböngészőt, majd keresse meg a mintaalkalmazást a `http://localhost:1337` címen.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![A helyileg futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.

> [!NOTE]
> Az Azure App Service-ben az alkalmazás IIS-ben, [iisnode](https://github.com/Azure/iisnode) használatával fut. Az alkalmazás iisnode-dal való futtatásának engedélyezéséhez az alkalmazás gyökérmappája tartalmaz egy web.config fájlt. A fájl IIS-sel olvasható és az iisnode-hoz kapcsolódó beállítások [az iisnode GitHub-adattárban](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config) vannak dokumentálva.

[!INCLUDE [Create ZIP file](../../includes/app-service-web-create-zip.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

Állítsa a Node-futtatókörnyezetet 8.11.1-re. Az összes támogatott futtatókörnyezet megtekintéséhez futtassa az [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) parancsot.

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=8.11.1
```

Tallózással keresse meg az újonnan létrehozott webalkalmazást. Cserélje le `<app_name>` egy egyedi névre.

```bash
http://<app_name>.azurewebsites.net
```

Az új webalkalmazásnak így kell kinéznie:

![Üres webalkalmazás oldal](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```
http://<app_name>.azurewebsites.net
```

A Node.js mintakód az Azure App Service webalkalmazásban fut.

![Az Azure-ban futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

**Gratulálunk!** Elvégezte az első Node.js-app üzembe helyezését az App Service-ben.

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

Egy szövegszerkesztő használatával nyissa meg a Node.js-alkalmazáson belüli `index.js` fájlt, majd módosítsa annak szövegét a `response.end` hívásán belül:

```nodejs
response.end("Hello Azure!");
```

Egy helyi terminálablakban keresse meg az alkalmazás gyökérkönyvtárát, majd hozzon létre egy új ZIP-fájlt a frissített projekt számára.

```
# Bash
zip -r myUpdatedAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myUpdatedAppFiles.zip
```

Telepítse ezt az új ZIP-fájlt az App Service-be [A ZIP-fájl üzembe helyezése](#deploy-zip-file) részben már ismertetett lépésekkel.

Váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Az Azure-ban futó frissített mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson a **App Services**, majd kattintson az Azure-alkalmazás neve.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service lap az Azure Portalon](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

## <a name="video"></a>Videó

Tekintse meg a videót, amelyben működés közben láthatja ezt a gyorsútmutatót, majd kövesse végig a lépéseket az első Node.js-alkalmazása közzétételéhez az Azure-on.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-Node-Developers/Create-a-Nodejs-app-in-Azure-Quickstart/player]   

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Node.js és MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
