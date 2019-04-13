---
title: PHP-alkalmazások – az Azure App Service konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan működik az Azure App Service PHP-alkalmazások konfigurálása
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 11d0648ee5090f02cb96c2d42a8d90cc3ea0ed28
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551337"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Az Azure App Service Linux PHP-alkalmazás konfigurálása

Ez az útmutató bemutatja, hogyan a beépített PHP-futtatókörnyezet, a web apps, mobil háttérrendszerek és API-alkalmazások konfigurálása az Azure App Service-ben.

Ez az útmutató a főbb fogalmakat és a PHP-fejlesztők számára az App Service-ben egy beépített Linux-tárolót használó utasításokat tartalmaz. Ha korábban nem használta az Azure App Service, kövesse a [PHP rövid](quickstart-php.md) és [PHP és MySQL az oktatóanyag](tutorial-php-mysql-app.md) első.

## <a name="show-php-version"></a>PHP-verzió megjelenítése

Az aktuális PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Minden támogatott PHP-verziók megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP-verzió beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) , a PHP verzióját szeretné 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Composer futtatása

Alapértelmezés szerint a nem fut a Kudu [Composer](https://getcomposer.org/). Composer-automatizálás Kudu üzembe helyezés során engedélyezéséhez meg kell adnia egy [egyéni üzembehelyezési szkript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

A helyi terminálablakból módosítsa a könyvtárat az adattár gyökérkönyvtárában. Kövesse a [parancssori telepítési lépéseket](https://getcomposer.org/download/) letöltéséhez *composer.phar*.

Futtassa az alábbi parancsot:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Az adattár gyökérkönyvtárában most már két új fájl mellett *composer.phar*: *.deployment* és *deploy.sh*. Ezek a fájlok egyaránt, a Windows és Linux rendszerű változatban érhetők el az App Service működik.

Nyissa meg *deploy.sh* , és keresse meg a `Deployment` szakaszban. Cserélje le az egész szakasz a következő kódot:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Mentse a módosításait, és újra telepíteni a kódot. Composer most már futnia kell az üzembe helyezési automatizálást részeként.

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a beépített PHP-tárolót, az Apache server futtassa. Induláskor, futtatásuk `apache2ctl -D FOREGROUND"`. Ha szeretné, egy másik parancs futtatható induláskor, a következő parancs futtatásával a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben is [állítsa be az alkalmazásbeállításokat](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) kívül a kódját. Ezután elérheti azokat a standard használatával [getenv()](https://secure.php.net/manual/function.getenv.php) mintát. Például egy alkalmazás-beállítás eléréséhez nevű `DB_HOST`, a következő kóddal:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Módosítsa a hely gyökeréhez

A webes keretrendszer, a választott alkönyvtárban használhatja a webhely gyökeréhez. Ha például [Laravel](https://laravel.com/), használja a `public/` alkönyvtárat, mint a hely gyökeréhez.

Az App Service alapértelmezett PHP lemezképet használja az Apache, és nem engedélyezi a webhely gyökeréhez az alkalmazás testreszabására. A probléma megoldásához, adjon hozzá egy *.htaccess* fájlt az adattár gyökérkönyvtárában, az alábbi tartalommal:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Ha inkább nem használná a *.htaccess* újraírást, üzembe helyezheti a Laravel-alkalmazását egy [egyéni Docker-rendszerképpel](quickstart-docker-go.md) is.

## <a name="detect-https-session"></a>Észleli a HTTPS-KAPCSOLATON keresztül

Az App Service-ben [SSL-lezárást](https://wikipedia.org/wiki/TLS_termination_proxy) történik, ha a hálózati terheléselosztók, így az összes HTTPS-kérelmek elérni az alkalmazás nem titkosított HTTP-kérések. Ha az alkalmazás logikai igényeinek megfelelően, ellenőrizze, hogy ha a felhasználói kérelmek titkosítottak-e vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejléc.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Népszerű webes keretrendszerek, hozzáférést biztosítanak a `X-Forwarded-*` a szabványos mintában információkat. A [CodeIgniter](https://codeigniter.com/), a [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) értékét ellenőrzi `X_FORWARDED_PROTO` alapértelmezés szerint.

## <a name="customize-phpini-settings"></a>A php.ini fájl beállítások testre szabása

Ha módosítania kell a PHP-telepítés, bármelyikét módosíthatja a [php.ini irányelvek](http://www.php.net/manual/ini.list.php) az alábbi lépéseket.

> [!NOTE]
> A legjobb módszer, a PHP-verzió és az aktuális *php.ini* konfigurálás az, hogy a hívás [phpinfo()](https://php.net/manual/function.phpinfo.php) az alkalmazásban.
>

### <a name="customize-non-phpinisystem-directives"></a>Nem PHP_INI_SYSTEM irányelvek testreszabása

PHP_INI_USER PHP_INI_PERDIR és PHP_INI_ALL irányelvek testreszabása (lásd: [php.ini irányelvek](http://www.php.net/manual/ini.list.php)), adjon hozzá egy *.htaccess* fájlt az alkalmazás gyökérkönyvtárára.

Az a *.htaccess* fájlt, adja hozzá a használatával irányelveket a `php_value <directive-name> <value>` szintaxist. Példa:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Telepítse újra az alkalmazást, az a módosításokat, és indítsa újra. Ha az üzembe helyezés a kudu használatával (például [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), üzembe helyezés után automatikusan újraindul.

Az alternatív *.htaccess*, használható [ini_set()](http://www.php.net/manual/function.ini-set.php) ezeket az irányelveket nem PHP_INI_SYSTEM szabhatja testre az alkalmazásban.

### <a name="customize-phpinisystem-directives"></a>PHP_INI_SYSTEM irányelvek testreszabása

PHP_INI_SYSTEM irányelvek testreszabása (lásd: [php.ini irányelvek](http://www.php.net/manual/ini.list.php)), nem használhatja a *.htaccess* megközelítést. Az App Service biztosítja, hogy egy külön mechanizmus használatával a `PHP_INI_SCAN_DIR` alkalmazásbeállítást.

Először futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) nevű beállítása alkalmazás hozzáadása `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` az alapértelmezett könyvtár ahol *php.ini* létezik. `/home/site/ini` egyéni kell hozzáadni az egyéni könyvtár *.ini* fájlt. Szétválasztja az értékeket egy `:`.

Keresse meg a Linux-tárolót a webes SSH-munkamenet (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Hozzon létre egy könyvtárat a `/home/site` nevű `ini`, majd hozzon létre egy *.ini* fájlt a `/home/site/ini` könyvtárat (például *Settings.ini fájlt)* testreszabásához irányelveknek. A használja ugyanazt a szintaxist egy *php.ini* fájlt. 

> [!TIP]
> Az App Service-ben a beépített Linux-tárolókban lévő */home* megőrzött megosztott tárolóként szolgál. 
>

Módosítsa az értéket, például [expose_php](http://php.net/manual/ini.core.php#ini.expose-php) futtassa a következő parancsokat:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

## <a name="enable-php-extensions"></a>PHP-bővítmények engedélyezése

A beépített PHP-telepítés a leggyakrabban használt bővítményeket tartalmaz. Engedélyezheti a további kiterjesztések az egyazon ugyanúgy, [testre szabhatja a php.ini irányelvek](#customize-php_ini_system-directives).

> [!NOTE]
> A legjobb módszer, a PHP-verzió és az aktuális *php.ini* konfigurálás az, hogy a hívás [phpinfo()](https://php.net/manual/function.phpinfo.php) az alkalmazásban.
>

További kiterjesztések engedélyezése az alábbi lépéseket:

Adjon hozzá egy `bin` könyvtárat az alkalmazás és a put gyökérkönyvtárára a `.so` kiterjesztésű fájlokat (például *mongodb.so*). Győződjön meg arról, hogy a bővítmények kompatibilisek az Azure és a rendszer VC9 és a nem szálbiztos (nts) kompatibilis a PHP-verzió.

Telepítse a módosításokat.

Kövesse a [testreszabása PHP_INI_SYSTEM irányelvek](#customize-php_ini_system-directives), adja hozzá a bővítmények az egyéni *.ini* -fájlt a [bővítmény](https://www.php.net/manual/ini.core.php#ini.extension) vagy [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) irányelveknek.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Nyissa meg böngészőben SSH-munkamenet

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Amikor működő PHP-alkalmazás működését eltérően az App Service-ben, vagy hibát, megpróbálkozhat a következőkkel:

- [A naplózási adatfolyam eléréséhez](#access-diagnostic-logs).
- Az alkalmazás helyi tesztelése éles módban. App Service-ben a a Node.js-alkalmazások éles módban fut, ezért győződjön meg arról, hogy a projekt megfelelően működik-e helyi éles módban kell. Példa:
    - Attól függően, a *composer.json*, különböző csomagokban megtalál mindent éles üzemmódhoz előfordulhat, hogy telepíteni (`require` és `require-dev`).
    - Bizonyos webes keretrendszerek statikus fájlok eltérően éles módban helyezheti üzembe.
    - Bizonyos webes keretrendszerek egyéni indítási parancsfájlok felhasználhatja az éles módban való futtatáskor.
- Hibakeresési módban futtatja az alkalmazást az App Service-ben. Például a [Laravel](http://meanjs.org/), konfigurálhatja az alkalmazás hibakeresési üzeneteket által éles környezetben kimeneti [beállítás a `APP_DEBUG` Alkalmazásbeállítás `true` ](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="robots933456"></a>robots933456

A tároló naplóit a következő üzenet jelenhet meg:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Ez az üzenet biztonságosan figyelmen kívül hagyja. `/robots933456.txt` van egy helyőrző URL-cím, amely az App Service segítségével ellenőrizze, hogy a tároló kérelmek kiszolgálására alkalmas. 404-es választ egyszerűen azt jelzi, hogy az elérési út nem létezik, de lehetővé teszi, hogy a tároló kifogástalan állapotú, és készen áll a kérelmek megválaszolásához App Service-ben.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: PHP-alkalmazás és MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)