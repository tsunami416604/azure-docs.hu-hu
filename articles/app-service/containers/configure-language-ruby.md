---
title: Ruby-alkalmazások – az Azure App Service konfigurálása
description: Ez az oktatóanyag leírja a szerzői és Ruby-appok konfigurálása az Azure App Service Linux rendszeren.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 402c85e7902c8c2f612ad6c777d8f6773a4d0ca3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549979"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Egy Linux-Ruby-alkalmazás konfigurálása az Azure App Service-ben

Ez a cikk azt ismerteti, hogyan [Azure App Service](app-service-linux-intro.md) fut, a Ruby-alkalmazásokat, és hogyan szabhatja testre a szükség esetén az App Service viselkedését. Ruby-alkalmazásokat telepíteni kell az összes szükséges [pip](https://pypi.org/project/pip/) modulok.

Ez az útmutató a főbb fogalmakat és a Ruby-fejlesztőknek az App Service-ben egy beépített Linux-tárolót használó utasításokat tartalmaz. Ha korábban nem használta az Azure App Service, kövesse a [Ruby rövid](quickstart-ruby.md) és [Ruby PostgreSQL – oktatóanyag](tutorial-ruby-postgres-app.md) első.

## <a name="show-ruby-version"></a>Ruby-verzió megjelenítése

Az aktuális Ruby-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Ruby-verziók megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Ehelyett saját tárolórendszerkép létrehozásával Ruby nem támogatott verzióját is futtathatja. További információkért lásd: [egyéni Docker-rendszerkép használata](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ruby-verzió beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) 2.3-as, a Ruby-verzió beállítása:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Ha hibába ütközik, üzembe helyezési idő alatt a következőhöz hasonló:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> vagy
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Azt jelenti, hogy konfigurálni projektbe Ruby-verzió eltér a tároló futtatja a telepített verzió (`2.3.3` a fenti példában). A fenti példában Ellenőrizze mindkét *Gemfile* és *.ruby-verzió* , és ellenőrizze, hogy a Ruby-verzió nincs beállítva, vagy a verzió van telepítve, a tárolóban fut értékre van állítva (`2.3.3` a a a példában a fenti).

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben is [állítsa be az alkalmazásbeállításokat](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) kívül a kódját. Ezután elérheti azokat a standard használatával [ENV ['< elérési út-neve >']](https://ruby-doc.org/core-2.3.3/ENV.html) mintát. Például egy alkalmazás-beállítás eléréséhez nevű `WEBSITE_SITE_NAME`, a következő kóddal:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Egyéni igényekre szabott környezet

Telepítésekor meg kell adnia egy [Git-tárház](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), vagy egy [Zip-csomagját](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) az összeállítási folyamatairól be van kapcsolva, az üzembe helyezési motorban (Kudu) automatikusan futtatja a telepítés utáni lépések alapértelmezés szerint:

1. Ellenőrizze, hogy egy *Gemfile* létezik.
1. Futtassa az `bundle clean` parancsot. 
1. Futtassa az `bundle install --path "vendor/bundle"` parancsot.
1. Futtatás `bundle package` a csomag gems szállítói/cache mappába.

### <a name="use---without-flag"></a>Használja a--jelző nélkül

Futtatásához `bundle install` az a [--nélkül](https://bundler.io/man/bundle-install.1.html) jelzőt, állítsa be a `BUNDLE_WITHOUT` [Alkalmazásbeállítás](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) csoportok vesszővel elválasztott listáját. Például a következő parancs beállítja azt `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Ha ez a beállítás, akkor az üzembe helyezési motorban fut `bundle install` a `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Üzembehelyezésnél fordítson elő objektumokat

Az üzembe helyezés utáni lépéseket nem üzembehelyezésnél fordítson elő objektumokat alapértelmezés szerint. Előfordítás eszközintelligencia bekapcsolásához állítsa be a `ASSETS_PRECOMPILE` [Alkalmazásbeállítás](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) való `true`. Ezután a parancs `bundle exec rake --trace assets:precompile` fut, az üzembe helyezés utáni lépéseket végén. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

További információkért lásd: [szolgálja ki a statikus objektumokat](#serve-static-assets).

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint, a Ruby tárolót indítja el a Rails-kiszolgálót az alábbi sorrendben (további információkért lásd: a [indítási parancsfájl](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Hozzon létre egy [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) értéket, ha egy már nem létezik. Az értéket kötelező megadni, az alkalmazás éles módban történő futtatására.
1. Állítsa be a `RAILS_ENV` környezeti változót, `production`.
1. Törli a *.pid* fájlt a *tmp/folyamatneveiben* könyvtár egy korábban elindított Rails-kiszolgáló van hátra.
1. Ellenőrizze, hogy ha az összes függősége telepítve van-e. Ha nem, próbálja meg telepíteni a gems a helyi *szállítói/gyorsítótár* könyvtár.
1. Futtassa az `rails server -e $RAILS_ENV` parancsot.

Testre szabhatja az indítási folyamat a következő módon:

- [Statikus objektumokat kiszolgálása](#serve-static-assets)
- [Nem éles módban](#run-in-non-production-mode)
- [Secret_key_base manuális beállítása](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statikus objektumokat kiszolgálása

Alapértelmezés szerint éles módban futtatja a Rails-kiszolgálót, a Ruby-tárolóban és [feltételezi, hogy az eszközök előre lefordított vannak, és a webkiszolgáló által szolgáltatott](https://guides.rubyonrails.org/asset_pipeline.html#in-production). A Rails-kiszolgálót a statikus objektumokat kiszolgálására, kell tennie a két dolgokat:

- **Üzembehelyezésnél fordítson elő objektumokat a** - [üzembehelyezésnél fordítson elő a statikus objektumokat helyileg](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) és üzembe helyezheti őket manuálisan. Vagy lehetővé teszik az üzembe helyezési motorban inkább kezelni azt (lásd: [üzembehelyezésnél fordítson elő objektumokat](#precompile-assets).
- **Engedélyezze a statikus fájlok kiszolgálása** – az szolgálja ki a statikus objektumokat, a Ruby-tárolóból, és állítsa a `RAILS_SERVE_STATIC_FILES` [állítsa be a `RAILS_SERVE_STATIC_FILES` Alkalmazásbeállítás](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) való `true`. Példa:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Nem éles módban

Alapértelmezés szerint a éles módban futtatja a Rails-kiszolgálót. Fejlesztői módban fusson, például állítsa be a `RAILS_ENV` [Alkalmazásbeállítás](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) való `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Azonban ez a beállítás hatására a Rails-kiszolgálót, a fejlesztői módban, amely elfogadja a localhost csak kéréseket, és nem lesz elérhető a tárolón kívüli elindításához. Távoli ügyfél-kérelmek fogadásához, állítsa be a `APP_COMMAND_LINE` [Alkalmazásbeállítás](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) való `rails server -b 0.0.0.0`. Ennek az alkalmazásbeállításnak lehetővé teszi egyéni parancsokat futtathatnak a Ruby-tárolóban. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secretkeybase-manually"></a>Secret_key_base manuális beállítása

Használatához a saját `secret_key_base` helyett, ami lehetővé teszi az App Service-ben hozzon létre egyet, és állítsa a `SECRET_KEY_BASE` [Alkalmazásbeállítás](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) a kívánt értéket. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Nyissa meg böngészőben SSH-munkamenet

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Rails-alkalmazás és PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)