---
title: "Node.js-webalkalmazás létrehozása az Azure-ban | Microsoft Docs"
description: "Percek alatt üzembe helyezheti első Hello World Node.js-alkalmazását az App Service Web Apps szolgáltatásban."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/05/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: e41c6f544ace374eb1f059c115e585a70265a571
ms.contentlocale: hu-hu
ms.lasthandoff: 07/06/2017

---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban

Az [Azure Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a gyorsútmutató a Node.js-alkalmazások Azure Web Apps szolgáltatásban történő üzembe helyezésén vezeti végig. Az [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) létrehozhatja a webalkalmazást, a Git szoftver használatával pedig üzembe helyezheti a Node.js-mintakódot a webalkalmazásban.

![Az Azure-ban futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti. Az előfeltételek telepítése után a lépések végrehajtása nagyjából öt percet vesz igénybe.   

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-Node-Developers/Create-a-Nodejs-app-in-Azure-Quickstart/player]   


## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a Node.js-t és az NPM-et](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakban futtassa a következő parancsot a mintaalkalmazás-tárház helyi számítógépre történő klónozásához.

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

Nyisson meg egy webböngészőt, majd keresse meg a mintaalkalmazást a http://localhost:1337 címen.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![A helyileg futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)] 

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)] 

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app.md)] 

![Üres webalkalmazás oldal](media/app-service-web-get-started-php/app-service-web-service-created.png)

Ezzel létrehozott egy üres, új webalkalmazást az Azure-ban.

[!INCLUDE [Configure local git](../../includes/app-service-web-configure-local-git.md)] 

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

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

A Node.js mintakód az Azure App Service webalkalmazásban fut.

![Az Azure-ban futó mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

**Gratulálunk!** Elvégezte az első Node.js-app üzembe helyezését az App Service-ben.

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

![Az Azure-ban futó frissített mintaalkalmazás](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Az új Azure-webapp kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service panel az Azure Portalon](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Node.js és MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

