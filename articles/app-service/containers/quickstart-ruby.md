---
title: 'Rövid útmutató: Linux Ruby-alkalmazás létrehozása'
description: Ismerkedjen meg a Linux-alkalmazásokkal Azure App Service az első Ruby-alkalmazás üzembe helyezésével a App Service Linux-tárolójában.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 33723e6dd8fbcd91d2d5863816786c0f08cdf923
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80045828"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Ruby on Rails-alkalmazás létrehozása a Linuxon futó App Service-ben

A [linuxon app Service](app-service-linux-intro.md) a Linux operációs rendszert használó, jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy Ruby on Rails-alkalmazást a Linuxon a [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)használatával Azure app Service.

> [!NOTE]
> A Ruby fejlesztői verem jelenleg csak a Ruby on Railst támogatja. Ha más platformot, például Sinatra-t szeretne használni, vagy ha nem [támogatott Ruby-verziót](app-service-linux-intro.md)szeretne használni, [azt egy egyéni tárolóban kell futtatnia](quickstart-docker-go.md).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">A Ruby 2,6 vagy újabb telepítése</a>
* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

Futtassa helyileg az alkalmazást, hogy lássa, hogyan fog kinézni az Azure-ban üzembe helyezve. Nyisson meg egy terminálablakot, váltson át a `hello-world` könyvtárra, és használja a `rails server` parancsot a kiszolgáló elindításához.

Az első lépés a szükséges gemek telepítése. A minta `Gemfile` tartalmazza a mintát, ezért csak futtassa a következő parancsot:

```bash
bundle install
```

Ha a gemek telepítése befejeződött, az alkalmazás indítása csomagolóval történik:

```bash
bundle exec rails server
```

Webböngészőjével a `http://localhost:3000` hely megkeresésével helyileg tesztelheti az alkalmazást.

![Hello world konfigurálva](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Nyissa meg az alkalmazást, és tekintse meg az újonnan létrehozott, beépített rendszerképpel rendelkező webalkalmazást. Cserélje le _ &lt;az alkalmazás nevét>_ a webalkalmazás nevére.

```bash
http://<app_name>.azurewebsites.net
```

Az új webalkalmazásnak így kell kinéznie:

![Kezdőlap](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Az alkalmazás üzembe helyezése

Futtassa a következő parancsokat a helyi alkalmazás üzembe helyezéséhez az Azure-webalkalmazásban:

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

Erősítse meg, hogy az üzembehelyezési műveletek sikerrel jártak. A parancsok eredménye a következő szöveghez fog hasonlítani:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

Miután az üzembe helyezés befejeződött, várjon körülbelül 10 másodpercet a webalkalmazás újraindításához, majd navigáljon a webalkalmazáshoz, és ellenőrizze az eredményeket.

```bash
http://<app-name>.azurewebsites.net
```

![frissített webalkalmazás](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Az alkalmazás újraindítása közben megfigyelheti a HTTP-állapotkódot `Error 503 Server unavailable` a böngészőben, vagy az `Hey, Ruby developers!` alapértelmezett lapot is. Az alkalmazás teljes újraindítása eltarthat néhány percig.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Ruby on Rails és postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Ruby-alkalmazás konfigurálása](configure-language-ruby.md)
