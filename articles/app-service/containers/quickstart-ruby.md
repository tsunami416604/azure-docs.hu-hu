---
title: Ruby alkalmazás létrehozása és telepítése a Linux App Service-be | Microsoft Docs
description: Ruby alkalmazások létrehozásának megismerése a Linuxon futó App Service segítségével.
keywords: azure app service, linux, oss, ruby
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: cfowler
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: def1e3f3de25478e51a069bc64c98d650f59072f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-ruby-app-in-app-service-on-linux"></a>Ruby alkalmazás létrehozása a Linux App Service-ben | Microsoft Docs

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan skálázható, önjavító webes üzemeltetési szolgáltatást nyújt. Az alábbi gyors útmutató megmutatja, hogyan készíthet alapszintű Ruby on Rails alkalmazást, amelyet aztán a Linux rendszeren futó webalkalmazásként telepíthet az Azure-ba.

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">A Ruby 2.3-as vagy újabb verziójának telepítése</a>
* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

Az alkalmazás működéséhez futtassa a Rails-kiszolgálót. Váltson át a *hello-world* könyvtárra és a `rails server` parancs elindítja a kiszolgálót.

```bash
cd hello-world\bin
rails server
```

Webböngészőjével a `http://localhost:3000` hely megkeresésével helyileg tesztelheti az alkalmazást.

![Hello world konfigurálva](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Az újonnan létrehozott, beépített rendszerképpel rendelkező webalkalmazás megtekintéséhez tallózással keresse meg a helyet. Az _&lt;app name>_ helyett adja meg a webalkalmazása nevét.

```bash
http://<app_name>.azurewebsites.net
```

Az új webalkalmazásnak így kell kinéznie:

![Kezdőlap](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Az alkalmazás üzembe helyezése

Futtassa az alábbi parancsokat helyi alkalmazásának Azure webhelyen történő üzembehelyezéséhez:

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Erősítse meg, hogy az üzembehelyezési műveletek sikerrel jártak. A parancsok eredménye a következő szöveghez fog hasonlítani:

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

Az üzembe helyezés befejezését követően az [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_restart) paranccsal indítsa újra a webalkalmazást az üzembe helyezés érvénybe léptetéséhez az itt látottak szerint:

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Keresse fel a webhelyet, és ellenőrizze az eredményeket.

```bash
http://<app name>.azurewebsites.net
```

![frissített webalkalmazás](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> Az alkalmazás újraindulása alatt az oldal böngészése a `Error 503 Server unavailable` HTTP-állapotkód megjelenését eredményezi. A teljes újraindulás eltarthat néhány percig.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ruby on Rails és MySQL](tutorial-ruby-mysql-app.md)
