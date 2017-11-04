---
title: "Hozzon létre egy Node.js az Azure App Service Linux |} Microsoft Docs"
description: "Telepítse az első Node.js Hello World az Azure App Service Linux perc múlva."
services: app-service\web
documentationcenter: 
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a40dc540884454fed9c374ba1365fdf7b85e4531
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="create-a-nodejs-web-app-in-azure-app-service-on-linux"></a>Node.js-webalkalmazás létrehozása az Azure App Service Linux rendszeren

[App Service Linux](app-service-linux-intro.md) biztosít egy jól skálázható, önálló javítási webhelyszolgáltató a Linux operációs rendszert használ. A gyors üzembe helyezés üzembe helyezését mutatja be egy Node.js-alkalmazás az App Service Linux beépített lemezképpel. A webalkalmazás létrehozásához beépített lemezkép használata a [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), és a Git használatával telepíti a Node.js-kód a webalkalmazásban.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-nodejs/hello-world-in-browser.png)

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a Node.js-t és az NPM-et](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakot a számítógépen futtassa a következő parancs futtatásával klónozza a sample app tárházat a helyi számítógépre.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Ezt a terminálablakot használhatja az összes parancs gyorsútmutatóban történő futtatásához.

Váltson arra a könyvtárra, amelyben a mintakód megtalálható.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Az alkalmazás a terminálablak megnyitásával és a `npm start` szkript használatával helyben futtatható a beépített Node.js HTTP-kiszolgáló indításához.

```bash
npm start
```

Nyisson meg egy webböngészőt, és navigáljon a minta alkalmazást `http://localhost:1337`.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![A helyileg futó mintaalkalmazás](media/quickstart-nodejs/localhost-hello-world-in-browser.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-with-built-in-image"></a>Beépített lemezképpel webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-no-h.md)]

Keresse meg az újonnan létrehozott webalkalmazáshoz. Cserélje le  _&lt;alkalmazás neve >_ egy egyedi alkalmazásnévvel rendelkező.

```bash
http://<app name>.azurewebsites.net
```

![Üres webalkalmazás oldal](media/quickstart-nodejs/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)]

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

A Node.js mintakód fut a webes alkalmazás beépített lemezképpel.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-nodejs/hello-world-in-browser.png)

**Gratulálunk!** Az első Node.js-alkalmazás az App Service Linux telepítése.

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

Egy szövegszerkesztő használatával nyissa meg a Node.js-alkalmazáson belüli `index.js` fájlt, majd módosítsa annak szövegét a `response.end` hívásán belül:

```nodejs
response.end("Hello Azure!");
```

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated output"
git push azure master
```

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse azt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Az új Azure-webapp kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service lap az Azure Portalon](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Node.js és MongoDB](tutorial-nodejs-mongodb-app.md)
