---
title: Ruby-alkalmazások konfigurálása – Azure App Service
description: Ez az oktatóanyag a Ruby-alkalmazások Linux rendszeren való Azure App Service létrehozásával és konfigurálásával kapcsolatos lehetőségeket ismerteti.
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
ms.author: cephalin
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: 222ded620610957e752e2081bda638d78eba4867
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619437"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Linux Ruby-alkalmazás konfigurálása a Azure App Servicehoz

Ez a cikk leírja, hogyan futtatja [Azure app Service](app-service-linux-intro.md) a Ruby-alkalmazásokat, és hogyan szabhatja testre a app Service viselkedését, ha szükséges. A Ruby-alkalmazásokat az összes szükséges [pip](https://pypi.org/project/pip/) -modullal telepíteni kell.

Ez az útmutató a App Service beépített linuxos tárolóját használó Ruby-fejlesztők számára biztosít főbb fogalmakat és útmutatást. Ha még soha nem használta a Azure App Servicet, először kövesse a [Ruby](quickstart-ruby.md) - [útmutató és a Ruby with PostgreSQL oktatóanyagot](tutorial-ruby-postgres-app.md) .

## <a name="show-ruby-version"></a>Ruby verziójának megjelenítése

A Ruby aktuális verziójának megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Ruby-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

A Ruby nem támogatott verzióját a saját tároló rendszerképének létrehozásával futtathatja. További információ: [Egyéni Docker-rendszerkép használata](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ruby verziójának beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a Ruby-verzió 2,3-re való beállításához:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Ha az üzembe helyezés ideje alatt a következőhöz hasonló hibák jelennek meg:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> vagy
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Ez azt jelenti, hogy a projektben konfigurált Ruby-verzió eltér a futtatott tárolóban telepített verziótól (`2.3.3` a fenti példában). A fenti példában ellenőrizze mind a *Gemfile* , mind a *. Ruby-Version* jelet, és ellenőrizze, hogy nincs-e beállítva a Ruby verziója, vagy az a verzió, amelyet a futtatott tárolóban`2.3.3` telepített (a fenti példában látható).

## <a name="access-environment-variables"></a>Hozzáférési környezeti változók

App Service az [Alkalmazásbeállítások](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezután a standard [env ['\<Path-Name > ']](https://ruby-doc.org/core-2.3.3/ENV.html) minta használatával érheti el azokat. Ha például egy nevű `WEBSITE_SITE_NAME`alkalmazáshoz szeretne hozzáférni, használja a következő kódot:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Központi telepítés testreszabása

Ha [git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)-tárházat telepít, vagy a létrehozási folyamatokkal rendelkező [ZIP-csomagot](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) , a telepítő motor (kudu) alapértelmezés szerint automatikusan futtatja a következő üzembe helyezés utáni lépéseket:

1. Ellenőrizze, hogy létezik-e *Gemfile* .
1. Futtassa az `bundle clean` parancsot. 
1. Futtassa az `bundle install --path "vendor/bundle"` parancsot.
1. A `bundle package` drágaköveket a vendor/cache mappában futtathatja.

### <a name="use---without-flag"></a>Használat – jelölő nélkül

Ha a `bundle install` [---nélkül](https://bundler.io/man/bundle-install.1.html) szeretné futtatni a parancsot, `BUNDLE_WITHOUT` állítsa be az [alkalmazás beállítását](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a csoportok vesszővel tagolt listájára. Az alábbi parancs például beállítja `development,test`a következőre:.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Ha ez a beállítás meg van adva, akkor az üzembe `bundle install` helyezési motor a következővel fut `--without $BUNDLE_WITHOUT`:.

### <a name="precompile-assets"></a>Eszközök előfordítása

A telepítés utáni lépések alapértelmezés szerint nem fordítják előre az eszközöket. Az eszköz előfordításának bekapcsolásához állítsa `ASSETS_PRECOMPILE` az `true` [alkalmazás beállítást](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a következőre:. Ezután a parancs `bundle exec rake --trace assets:precompile` a telepítés utáni lépések végén fut. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

További információ: a [statikus eszközök](#serve-static-assets)kiszolgálása.

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a Ruby-tároló elindítja a Rails-kiszolgálót a következő sorozatban (további információért lásd az [indítási szkriptet](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. [Secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) érték létrehozása, ha az egyik még nem létezik. Ez az érték szükséges ahhoz, hogy az alkalmazás éles módban fusson.
1. Állítsa a `RAILS_ENV` környezeti `production`változót a következőre:.
1. Törölje az összes olyan *. PID* fájlt a *tmp/PID* könyvtárban, amelyet egy korábban futó Rails-kiszolgáló hagyott.
1. Ellenőrizze, hogy az összes függőség telepítve van-e. Ha nem, próbálja meg telepíteni a drágaköveket a helyi *gyártó/gyorsítótár* könyvtárból.
1. Futtassa az `rails server -e $RAILS_ENV` parancsot.

Az indítási folyamatot a következő módokon szabhatja testre:

- [Statikus eszközök kiszolgálása](#serve-static-assets)
- [Futtatás nem éles módban](#run-in-non-production-mode)
- [Secret_key_base manuális beállítása](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statikus eszközök kiszolgálása

A Ruby-tárolóban lévő Rails-kiszolgáló alapértelmezés szerint éles módban fut, és [azt feltételezi, hogy az eszközök előre le vannak fordítva, és a webkiszolgáló szolgálja ki őket](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Ha a Rails-kiszolgálóról szeretne statikus eszközöket kiszolgálni, két dolgot kell tennie:

- **Az eszközök** - előfordítása a[statikus eszközök helyi](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) előfordítására és manuális üzembe helyezésére. Vagy hagyja, hogy az üzembe helyezési motor kezeli a helyet (lásd: [eszközök](#precompile-assets)előfordítása.
- **Statikus fájlok** kiszolgálása – lehetővé teszi `true` [, `RAILS_SERVE_STATIC_FILES` hogy](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a a Ruby-tárolóban lévő statikus eszközöket szolgálja ki. Példa:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Futtatás nem éles módban

Alapértelmezés szerint a Rails-kiszolgáló éles módban fut. Ha például fejlesztési módban szeretné futtatni az `RAILS_ENV` `development`alkalmazást, állítsa be az [alkalmazás beállítását](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a következőre:.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Ez a beállítás azonban önmagában azt eredményezi, hogy a Rails-kiszolgáló fejlesztési módban indul el, amely csak a localhost kérelmeket fogadja el, és a tárolón kívül nem érhető el. A távoli ügyfelek kéréseinek elfogadásához `APP_COMMAND_LINE` állítsa az `rails server -b 0.0.0.0` [alkalmazás beállítását](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a következőre:. Ez az Alkalmazásbeállítások lehetővé teszi, hogy egyéni parancsot futtasson a Ruby-tárolóban. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a>Secret_key_base manuális beállítása

Ha a app Service létrehozása `secret_key_base` helyett saját értéket szeretne használni, állítsa be az `SECRET_KEY_BASE` [alkalmazás beállításait](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a kívánt értékre. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Rails-alkalmazás és PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linux – gyakori kérdések](app-service-linux-faq.md)
