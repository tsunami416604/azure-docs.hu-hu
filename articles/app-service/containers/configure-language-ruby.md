---
title: Ruby-alkalmazások konfigurálása – Azure App Service
description: Megtudhatja, hogyan konfigurálhat egy előre elkészített Ruby-tárolót az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 804e6d562322eff20de8eb7e33caae98418ea3fe
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905680"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Linux Ruby-alkalmazás konfigurálása a Azure App Servicehoz

Ez a cikk leírja, hogyan futtatja [Azure app Service](app-service-linux-intro.md) a Ruby-alkalmazásokat, és hogyan szabhatja testre a app Service viselkedését, ha szükséges. A Ruby-alkalmazásokat az összes szükséges [drágakövevel](https://rubygems.org/gems)telepíteni kell.

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
> Ez azt jelenti, hogy a projektben konfigurált Ruby-verzió eltér a futtatott tárolóban telepített verziótól ( `2.3.3` a fenti példában). A fenti példában ellenőrizze mind a *Gemfile* , mind a *. Ruby-Version* jelet, és ellenőrizze, hogy nincs-e beállítva a Ruby verziója, vagy az a verzió, amelyet a futtatott tárolóban telepített (a `2.3.3` fenti példában látható).

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

App Service az [Alkalmazásbeállítások](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezután a standard [env [' \<path-name> ']](https://ruby-doc.org/core-2.3.3/ENV.html) minta használatával érheti el őket. Ha például egy nevű alkalmazáshoz szeretne hozzáférni `WEBSITE_SITE_NAME` , használja a következő kódot:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Központi telepítés testreszabása

Ha [git-tárházat](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)telepít, vagy a létrehozási folyamatokkal rendelkező [ZIP-csomagot](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) , a telepítő motor (kudu) alapértelmezés szerint automatikusan futtatja a következő üzembe helyezés utáni lépéseket:

1. Ellenőrizze, hogy létezik-e *Gemfile* .
1. Futtassa az `bundle clean` parancsot. 
1. Futtassa az `bundle install --path "vendor/bundle"` parancsot.
1. `bundle package`A drágaköveket a vendor/cache mappában futtathatja.

### <a name="use---without-flag"></a>Használat – jelölő nélkül

Ha `bundle install` a [---nélkül](https://bundler.io/man/bundle-install.1.html) szeretné futtatni a parancsot, állítsa be az `BUNDLE_WITHOUT` [alkalmazás beállítását](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a csoportok vesszővel tagolt listájára. Az alábbi parancs például beállítja a következőre: `development,test` .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Ha ez a beállítás meg van adva, akkor az üzembe helyezési motor a következővel fut: `bundle install` `--without $BUNDLE_WITHOUT` .

### <a name="precompile-assets"></a>Eszközök előfordítása

A telepítés utáni lépések alapértelmezés szerint nem fordítják előre az eszközöket. Az eszköz előfordításának bekapcsolásához állítsa az `ASSETS_PRECOMPILE` [alkalmazás beállítást](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a következőre: `true` . Ezután a parancs a `bundle exec rake --trace assets:precompile` Telepítés utáni lépések végén fut. Például:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

További információ: a [statikus eszközök kiszolgálása](#serve-static-assets).

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a Ruby-tároló elindítja a Rails-kiszolgálót a következő sorozatban (további információért lásd az [indítási szkriptet](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. [Secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) érték létrehozása, ha az egyik még nem létezik. Ez az érték szükséges ahhoz, hogy az alkalmazás éles módban fusson.
1. Állítsa a `RAILS_ENV` környezeti változót a következőre: `production` .
1. Törölje az összes olyan *. PID* fájlt a *tmp/PID* könyvtárban, amelyet egy korábban futó Rails-kiszolgáló hagyott.
1. Ellenőrizze, hogy az összes függőség telepítve van-e. Ha nem, próbálja meg telepíteni a drágaköveket a helyi *gyártó/gyorsítótár* könyvtárból.
1. Futtassa az `rails server -e $RAILS_ENV` parancsot.

Az indítási folyamatot a következő módokon szabhatja testre:

- [Statikus eszközök kiszolgálása](#serve-static-assets)
- [Futtatás nem éles módban](#run-in-non-production-mode)
- [Secret_key_base manuális megadása](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statikus eszközök kiszolgálása

A Ruby-tárolóban lévő Rails-kiszolgáló alapértelmezés szerint éles módban fut, és [azt feltételezi, hogy az eszközök előre le vannak fordítva, és a webkiszolgáló szolgálja ki őket](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Ha a Rails-kiszolgálóról szeretne statikus eszközöket kiszolgálni, két dolgot kell tennie:

- **Az eszközök**  -  előfordítása [A statikus eszközök helyi előfordítása](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) és manuális telepítése. Vagy hagyja, hogy az üzembe helyezési motor kezeli a helyet (lásd: [eszközök előfordítása](#precompile-assets).
- **Statikus fájlok** kiszolgálása – lehetővé teszi, hogy a a Ruby- [ `RAILS_SERVE_STATIC_FILES` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) tárolóban lévő statikus eszközöket szolgálja ki `true` . Például:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Futtatás nem éles módban

Alapértelmezés szerint a Rails-kiszolgáló éles módban fut. Ha például fejlesztési módban szeretné futtatni az alkalmazást, állítsa be az `RAILS_ENV` [alkalmazás beállítását](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a következőre: `development` .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Ez a beállítás azonban önmagában azt eredményezi, hogy a Rails-kiszolgáló fejlesztési módban indul el, amely csak a localhost kérelmeket fogadja el, és a tárolón kívül nem érhető el. A távoli ügyfelek kéréseinek elfogadásához állítsa az `APP_COMMAND_LINE` [alkalmazás beállítását](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a következőre: `rails server -b 0.0.0.0` . Ez az Alkalmazásbeállítások lehetővé teszi, hogy egyéni parancsot futtasson a Ruby-tárolóban. Például:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Secret_key_base manuális megadása

Ha a `secret_key_base` app Service létrehozása helyett saját értéket szeretne használni, állítsa be az `SECRET_KEY_BASE` [alkalmazás beállításait](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a kívánt értékre. Például:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Rails-alkalmazás és PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linux – gyakori kérdések](app-service-linux-faq.md)
