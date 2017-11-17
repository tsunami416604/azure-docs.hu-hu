---
title: "PHP-webalkalmazás létrehozása és telepítése az App Service Linux |} Microsoft Docs"
description: "Telepítse az első PHP Hello World az App Service Linux perc múlva."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 34da73e5feca1782f3e107fd30d268df4f9883d8
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="create-a-php-web-app-in-app-service-on-linux"></a>Hozzon létre egy PHP webalkalmazást az App Service Linux rendszeren

[App Service Linux](app-service-linux-intro.md) biztosít egy jól skálázható, önálló javítási webhelyszolgáltató a Linux operációs rendszert használ. Gyors üzembe helyezési oktatóanyag bemutatja, hogyan telepítheti a PHP-alkalmazások az Azure App Service Linux. A webalkalmazás létrehozásához beépített lemezkép használata a [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) a felhő rendszerhéjat, és a Git segítségével telepítse a PHP kód a webalkalmazásban.

! [Mintaalkalmazás Azure-beli]] (media/quickstart-php/hello-world-in-browser.png)

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [A PHP telepítése](https://php.net)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakot futtassa a következő parancsok futtatásával klónozza a mintaalkalmazást a helyi gépére, és keresse meg azt a mintakódot tartalmazó könyvtárat.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Az alkalmazás a terminálablak megnyitásával és a `php` parancs használatával helyben futtatható a beépített PHP-webkiszolgáló indításához.

```bash
php -S localhost:8080
```

Nyisson meg egy webböngészőt, és navigáljon a minta alkalmazást `http://localhost:8080`.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World!** üzenet jelenik meg.

![A helyileg futó mintaalkalmazás](media/quickstart-php/localhost-hello-world-in-browser.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-no-h.md)] 

Keresse meg azt a helyet, az újonnan létrehozott webes alkalmazás a beépített kép megjelenítéséhez. Cserélje le  _&lt;alkalmazás neve >_ és a webes alkalmazás neve.

```bash
http://<app_name>.azurewebsites.net
```

![Üres webalkalmazás oldal](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

A PHP mintakód fut a webes alkalmazás beépített lemezképpel.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-php/hello-world-in-browser.png)

**Gratulálunk!** Az első PHP-alkalmazás az App Service Linux telepítése.

## <a name="update-locally-and-redeploy-the-code"></a>A kód frissítése helyileg és ismételt üzembe helyezése

A helyi címtárban, nyissa meg a `index.php` fájlt a PHP-alkalmazásban, és olyan módosítást kicsi a karakterláncon belüli szöveg melletti `echo`:

```php
echo "Hello Azure!";
```

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated output"
git push azure master
```

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Az új Azure-webapp kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service lap az Azure Portalon](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [PHP és MySQL](tutorial-php-mysql-app.md)
