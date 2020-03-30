---
title: Ruby-alkalmazások konfigurálása – Azure App Service
description: További információ az előre elkészített Ruby-tároló konfigurálásáról az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat mutatja be.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8daebba840223d050a14b4b99cb6ae15472ee4f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046322"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Linux Ruby alkalmazás konfigurálása az Azure App Service-hez

Ez a cikk azt ismerteti, hogy az [Azure App Service](app-service-linux-intro.md) hogyan futtatja a Ruby-alkalmazásokat, és hogyan szabhatja testre az App Service viselkedését, ha szükséges. Ruby alkalmazásokat kell telepíteni az összes szükséges [drágaköveket.](https://rubygems.org/gems)

Ez az útmutató kulcsfontosságú fogalmakat és utasításokat tartalmaz a Ruby fejlesztők számára, akik egy beépített Linux-tárolót használnak az App Service-ben. Ha még soha nem használta az Azure App Service-t, először kövesse a [Ruby rövid útmutatót](quickstart-ruby.md) és a [Ruby postgreSQL-oktatóanyagot.](tutorial-ruby-postgres-app.md)

## <a name="show-ruby-version"></a>Ruby verzió megjelenítése

Az aktuális Ruby verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Ruby-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

A Ruby nem támogatott verzióját saját tárolórendszerkép létrehozásával futtathatja. További információ: [Egyéni Docker-lemezkép használata.](tutorial-custom-docker-image.md)

## <a name="set-ruby-version"></a>Ruby verzió beállítása

A Ruby-verzió 2.3-ra való beállításához futtassa a következő parancsot a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Ha a telepítési idő alatt az alábbihoz hasonló hibákat lát:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> vagy
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Ez azt jelenti, hogy a projektben konfigurált Ruby verzió eltér a futtatott`2.3.3` tárolóban telepített verziótól (a fenti példában). A fenti példában ellenőrizze mind a *Gemfile,* mind a *.ruby-version fájlt,* és ellenőrizze, hogy a Ruby verzió`2.3.3` nincs-e beállítva, vagy a futtatott tárolóban telepített verzióra van-e beállítva (a fenti példában).

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben az [alkalmazáskódokon kívül is megadhat alkalmazásbeállításokat.](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) Ezután elérheti őket a szabványos [ENV["\<elérési út>']](https://ruby-doc.org/core-2.3.3/ENV.html) minta használatával. Például egy alkalmazás nevű `WEBSITE_SITE_NAME`beállítás eléréséhez használja a következő kódot:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Telepítés testreszabása

Amikor [git-tárházat](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)vagy beépített buildelési folyamatokkal rendelkező [Zip-csomagot](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) telepít, a központi telepítési motor (Kudu) alapértelmezés szerint automatikusan futtatja a következő üzembe helyezés utáni lépéseket:

1. Ellenőrizze, hogy *létezik-e Gemfile.*
1. Futtassa az `bundle clean` parancsot. 
1. Futtassa az `bundle install --path "vendor/bundle"` parancsot.
1. Fuss `bundle package` -hoz csomag gems -ba eladó/ elrejt dosszié.

### <a name="use---without-flag"></a>Használata -- jelző nélkül

A `bundle install` [--jelző nélkül](https://bundler.io/man/bundle-install.1.html) való futtatáshoz állítsa az `BUNDLE_WITHOUT` [alkalmazásbeállítást](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a csoportok vesszővel tagolt listájára. A következő parancs például `development,test`a .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Ha ez a beállítás meg van `bundle install` adva, akkor a központi telepítési motor a. `--without $BUNDLE_WITHOUT`

### <a name="precompile-assets"></a>Eszközök előzetes fordítása

A telepítés utáni lépések alapértelmezés szerint nem fejtik ki előre az eszközöket. Az eszköz előfordításának bekapcsolását `ASSETS_PRECOMPILE` állítsa az `true` [alkalmazás beállítására.](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) Ezután `bundle exec rake --trace assets:precompile` a parancs a telepítés utáni lépések végén fut. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

További információ: [Serve static assets](#serve-static-assets).

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a Ruby tároló a következő sorrendben indítja el a Rails kiszolgálót (további információt az [indítási parancsfájlban](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)talál):

1. Hozzon létre egy [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) értéket, ha még nem létezik. Ez az érték szükséges ahhoz, hogy az alkalmazás éles környezetben fusson.
1. Állítsa `RAILS_ENV` a környezeti `production`változót a beállításra.
1. Törölje a *tmp/pids* könyvtárban *lévő.*
1. Ellenőrizze, hogy minden függőség telepítve van-e. Ha nem, próbálja meg telepíteni drágaköveket a helyi *szállító / cache* könyvtárba.
1. Futtassa az `rails server -e $RAILS_ENV` parancsot.

Az indítási folyamatot az alábbi módokon szabhatja testre:

- [Statikus eszközök kiszolgálása](#serve-static-assets)
- [Futtatás nem éles módban](#run-in-non-production-mode)
- [secret_key_base manuális beállítása](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statikus eszközök kiszolgálása

A Ruby tárolóban lévő Rails kiszolgáló alapértelmezés szerint éles üzemmódban fut, és [feltételezi, hogy az eszközök előre le vannak fordítva, és a webkiszolgáló szolgálja ki őket.](https://guides.rubyonrails.org/asset_pipeline.html#in-production) A Rails kiszolgáló statikus eszközeinek kiszolgálásához két dolgot kell tennie:

- **Az eszközök** - előzetes[lefordítása a statikus eszközök helyileg](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) és manuálisan telepíteni. Vagy hagyja, hogy a központi telepítési motor kezelje helyette (lásd: [Előfordítási eszközök](#precompile-assets).
- **Statikus fájlok kiszolgálásának engedélyezése** – A Ruby tárolóstatikus eszközök `true`kiszolgálásához állítsa [ `RAILS_SERVE_STATIC_FILES` az alkalmazásbeállítást](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a beállításra. Példa:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Futtatás nem éles módban

A Rails kiszolgáló alapértelmezés szerint éles üzemmódban fut. Ha például fejlesztési módban szeretne `RAILS_ENV` futni, `development`állítsa az alkalmazás [beállítást](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a -ra.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Ez a beállítás azonban önmagában a Rails-kiszolgáló fejlesztési módban való indítását eredményezi, amely csak a localhost kéréseket fogadja, és nem érhető el a tárolón kívül. A távoli ügyfélkérelmek fogadásához állítsa az `APP_COMMAND_LINE` [alkalmazásbeállítást](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a beállításra. `rails server -b 0.0.0.0` Ez az alkalmazásbeállítás lehetővé teszi egy egyéni parancs futtatását a Ruby tárolóban. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>secret_key_base manuális beállítása

Ha a `secret_key_base` saját értékét szeretné használni ahelyett, hogy `SECRET_KEY_BASE` hagyná, hogy az App Service hozzon létre egyet, állítsa be az [alkalmazásbeállítást](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a kívánt értékkel. Példa:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása a böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Rails alkalmazás a PostgreSQL-lel](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)
