---
title: "Statikus HTML-webalkalmazás létrehozása az Azure-ban | Microsoft Docs"
description: "Egy statikus HTML-webalkalmazás üzembe helyezésével megtudhatja, hogy miként futtathat webalkalmazásokat az Azure App Service-ben."
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/26/2017
ms.author: riande
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: c13108ec70f5613be711c622ab68a7fdfc467300
ms.contentlocale: hu-hu
ms.lasthandoff: 06/21/2017

---
# <a name="create-a-static-html-web-app-in-azure"></a>Statikus HTML-webalkalmazás létrehozása az Azure-ban

Az [Azure Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a gyorsútmutató egy alapszintű HTML+CSS hely Azure Web Apps szolgáltatásban történő üzembe helyezésén vezeti végig. Az [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) létrehozhatja a webalkalmazást, a Git szoftver használatával pedig üzembe helyezheti a HTML mintatartalmat a webalkalmazásban.

![Mintaalkalmazás kezdőlapja](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti. Az előfeltételek telepítése után a lépések végrehajtása nagyjából öt percet vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

- [A Git telepítése](https://git-scm.com/)
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakban futtassa a következő parancsot a mintaalkalmazás-tárház helyi számítógépre történő klónozásához.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

Ezt a terminálablakot használhatja az összes parancs gyorsútmutatóban történő futtatásához.

## <a name="view-the-html"></a>HTML megjelenítése

Váltson a minta HTML-t tartalmazó könyvtárra. Nyissa meg a *index.html* fájlt a böngészőben.

![Mintaalkalmazás kezdőlapja](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)] 

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)] 

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app.md)] 

![Üres webalkalmazás oldal](media/app-service-web-get-started-html/app-service-web-service-created.png)

Ezzel létrehozott egy üres, új webalkalmazást az Azure-ban.

[!INCLUDE [Configure local git](../../includes/app-service-web-configure-local-git.md)] 

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Nyissa meg böngészőben az Azure webalkalmazás URL-címét:

```
http://<app_name>.azurewebsites.net
```

Az oldal Azure App Service webalkalmazásként fut.

![Mintaalkalmazás kezdőlapja](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

**Gratulálunk!** Elvégezte az első HTML-webapp üzembe helyezését az App Service-ben.

## <a name="update-and-redeploy-the-app"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

Egy szövegszerkesztőben nyissa meg az *index.html* fájlt, és hajtson végre módosítást a jelölőnyelvi kódon. Például, módosítsa az „Azure App Service – statikus HTML-mintahely” nevű H1 fejlécet egyszerűen „Azure App Service”-re.

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated HTML"
git push azure master
```

A telepítés befejezése után frissítse a lapot a böngészőben, hogy lássa a változásokat.

![Frissített mintaalkalmazás kezdőlapja](media/app-service-web-get-started-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-web-app"></a>Az új Azure-webapp kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-get-started-html/portal1.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service panel az Azure Portalon](./media/app-service-web-get-started-html/portal2.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)

