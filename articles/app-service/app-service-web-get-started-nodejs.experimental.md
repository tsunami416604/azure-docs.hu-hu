---
title: 'Rövid útmutató: Node.js webalkalmazás létrehozása'
description: Az első Node.js Hello World alkalmazást percek alatt telepítheti az Azure App Service szolgáltatásba. Egy ZIP-csomag használatával telepíti, amely az egyik számos módon telepítheti az App Service-ben.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: a185565ec125e327297883c63b1d50a2548c7853
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086001"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [Node.js-webalkalmazás létrehozása a Linuxon futó Azure App Service-ben](./containers/quickstart-nodejs.md).
>

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan telepíthet egy Node.js alkalmazást az Azure App Service-ben. A webalkalmazást a [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)használatával hozza létre, de ezeket a parancsokat helyileg is futtathatja az [Azure CLI](/cli/azure/install-azure-cli)segítségével. A minta Node.js kódot a webapp az [az webapp telepítési forrás config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancs használatával telepíti.  

![Az Azure-ban futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Ezeket a lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti. A lépések körülbelül három percet vesz igénybe.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>A minta letöltése

A Cloud Shellben hozzon létre egy quickstart könyvtárat, és lépjen a könyvtárba.

```console
mkdir quickstart

cd $HOME/quickstart
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a quickstart könyvtárba való klónozásához.

```console
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

```output
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> A mintaindex.js a process.env.PORT beállítását a process.env.PORT beállítással állítja be. Ezt a környezeti változót az App Service rendeli hozzá.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal hozzon létre egy webalkalmazást`az webapp create` a `myAppServicePlan` App Service-csomagban.

A következő példában cserélje ki az `<app_name>` nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

<pre>
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="set-nodejs-runtime"></a>Node.js-futtatókörnyezet beállítása

Állítsa a csomópont futásidejét 10.14.1-re. Az összes támogatott futásidő [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)megtekintéséhez futtassa a futtassa a futtassa a futtassa a futtassa a futtass

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Tallózással keresse meg az újonnan létrehozott webalkalmazást. Cserélje `<app_name>` le egy egyedi alkalmazásnévre.

```http
http://<app_name>.azurewebsites.net
```

Az új webalkalmazásnak így kell ![kinéznie: A webalkalmazás üres oldala](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>ZIP-fájl telepítése

A Cloud Shellben keresse meg az alkalmazás gyökérkönyvtárát, és hozzon létre egy új ZIP-fájlt a mintaprojekthez.

```console
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

Telepítse a ZIP-fájlt a webalkalmazásba az [az webapp telepítési forrás config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancs használatával.  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást. Ha további egyéni kiépítési folyamatok vannak megadva, azokat is futtatja. További információ: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```http
http://<app_name>.azurewebsites.net
```

A Node.js mintakód az Azure App Service webalkalmazásban fut.

![Az Azure-ban futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> Az Azure App Service-ben az alkalmazás IIS-ben, [iisnode](https://github.com/Azure/iisnode) használatával fut. Az alkalmazás iisnode-dal való futtatásának engedélyezéséhez az alkalmazás gyökérmappája tartalmaz egy web.config fájlt. A fájl IIS-sel olvasható és az iisnode-hoz kapcsolódó beállítások [az iisnode GitHub-adattárban](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config) vannak dokumentálva.

**Gratulálok!** Elvégezte az első Node.js-app üzembe helyezését az App Service-ben.

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

A Cloud Shell `code index.js` beírása a Cloud Shell-szerkesztő megnyitásához.

![Index.js kód](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Végezzen el egy kis módosítást a `response.end` hívásának szövegében:

```javascript
response.end("Hello Azure!");
```

Mentse a módosításokat, és lépjen ki a szerkesztőből. A mentéshez a `^S`, a kilépéshez a `^Q` parancsot használja.

Hozzon létre egy ZIP-fájlt, és telepítse az [az webapp telepítési forrás config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) paranccsal.  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Az Azure-ban futó frissített mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson az **App Services**elemre, majd az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service lap az Azure Portalon](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Node.js és MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
