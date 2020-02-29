---
title: PHP-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy előre elkészített PHP-tárolót az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: e805487075499bd4e461a21fffb4c44156ce192b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913871"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Linux PHP-alkalmazás konfigurálása Azure App Servicehoz

Ez az útmutató bemutatja, hogyan konfigurálhatja a beépített PHP-futtatókörnyezetet a webalkalmazásokhoz, a mobil-és a Azure App Service API-alkalmazásokhoz.

Ez az útmutató ismerteti a PHP-fejlesztők számára a App Service beépített Linux-tárolóját használó főbb fogalmakat és útmutatásokat. Ha még soha nem használta a Azure App Servicet, először kövesse a [php](quickstart-php.md) gyors és a [php-t a MySQL-vel foglalkozó oktatóanyagban](tutorial-php-mysql-app.md) .

## <a name="show-php-version"></a>PHP-verzió megjelenítése

Az aktuális PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP-verzió beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a PHP-verzió 7,2-es értékre való beállításához:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>A Build Automation testreszabása

Ha a Build Automation használatával git vagy zip csomagok segítségével helyezi üzembe az alkalmazást, akkor a App Service az alábbi lépésekkel hozhat létre automatizálási lépéseket:

1. A `PRE_BUILD_SCRIPT_PATH`által megadott egyéni parancsfájl futtatása.
1. Futtassa az `php composer.phar install` parancsot.
1. A `POST_BUILD_SCRIPT_PATH`által megadott egyéni parancsfájl futtatása.

a `PRE_BUILD_COMMAND` és az `POST_BUILD_COMMAND` alapértelmezés szerint üres környezeti változók. Az előkészítő parancsok futtatásához adja meg a `PRE_BUILD_COMMAND`. A létrehozás utáni parancsok futtatásához adja meg a `POST_BUILD_COMMAND`.

A következő példa a két változót adja meg egy több parancshoz, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

További környezeti változók a Build Automation testreszabásához: [Oryx-konfiguráció](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

A PHP-alkalmazások Linux rendszeren való futtatásáról és App Serviceáról további információt a [Oryx dokumentációjában talál: a PHP-alkalmazások észlelése és építése](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a beépített PHP-tároló futtatja az Apache-kiszolgálót. Indításkor `apache2ctl -D FOREGROUND"`fut. Ha szeretné, egy másik parancsot is futtathat az indításkor, ha a következő parancsot futtatja a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

App Service az [Alkalmazásbeállítások](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezt követően a szabványos [GETENV ()](https://secure.php.net/manual/function.getenv.php) minta használatával érheti el őket. Ha például egy `DB_HOST`nevű alkalmazás-beállítást szeretne elérni, használja a következő kódot:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Hely gyökerének módosítása

Az Ön által választott webes keretrendszer egy alkönyvtárt is használhat a hely gyökeréhez. Például a [Laravel](https://laravel.com/)a `public/` alkönyvtárat használja a hely gyökeréhez.

A App Service alapértelmezett PHP-rendszerképe Apache-t használ, és nem teszi lehetővé a hely gyökerének testreszabását az alkalmazáshoz. A korlátozás megkerüléséhez adjon hozzá egy *. htaccess* -fájlt a tárház gyökeréhez a következő tartalommal:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Ha inkább nem használná a *.htaccess* újraírást, üzembe helyezheti a Laravel-alkalmazását egy [egyéni Docker-rendszerképpel](quickstart-docker-go.md) is.

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécét.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

A népszerű webes keretrendszerek lehetővé teszik a szabványos alkalmazási mintában lévő `X-Forwarded-*` információk elérését. A [CodeIgniter](https://codeigniter.com/)-ben a [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi `X_FORWARDED_PROTO` értékét.

## <a name="customize-phpini-settings"></a>A php. ini beállításainak testreszabása

Ha módosítania kell a PHP-telepítést, a következő lépésekkel módosíthatja a [php. ini-irányelvek](https://www.php.net/manual/ini.list.php) bármelyikét.

> [!NOTE]
> A PHP-verzió és az aktuális *php. ini* -konfiguráció megtekintéséhez a legjobb módszer a [phpinfo ()](https://php.net/manual/function.phpinfo.php) meghívása az alkalmazásban.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Testreszabás – nem PHP_INI_SYSTEM irányelvek

PHP_INI_USER, PHP_INI_PERDIR és PHP_INI_ALL irányelvek testreszabásához (lásd a [php. ini direktívát](https://www.php.net/manual/ini.list.php)), adjon hozzá egy *. htaccess* -fájlt az alkalmazás gyökérkönyvtárához.

A *. htaccess* fájlban adja hozzá az irányelveket a `php_value <directive-name> <value>` szintaxis használatával. Például:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Telepítse újra az alkalmazást a módosításokkal, majd indítsa újra. Ha a kudu (például a [git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)használatával) telepíti azt, akkor a telepítés után automatikusan újraindul.

A *. htaccess*használatának alternatívájaként a [ini_set ()](https://www.php.net/manual/function.ini-set.php) alkalmazással is testreszabhatja ezeket a nem PHP_INI_SYSTEM irányelveket.

### <a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM irányelvek testreszabása

PHP_INI_SYSTEM irányelvek testreszabásához (lásd a [php. ini-irányelveket](https://www.php.net/manual/ini.list.php)) nem használhatja a *. htaccess* megközelítést. A App Service a `PHP_INI_SCAN_DIR` alkalmazás beállításával külön mechanizmust biztosít.

Először futtassa az alábbi parancsot a [Cloud Shellban](https://shell.azure.com) egy `PHP_INI_SCAN_DIR`nevű Alkalmazásbeállítás hozzáadásához:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

a `/usr/local/etc/php/conf.d` az alapértelmezett könyvtár, ahol a *php. ini fájl* létezik. `/home/site/ini` az az egyéni könyvtár, amelyben hozzá kell adnia egy egyéni *. ini* -fájlt. Az értékeket egy `:`választja el egymástól.

Navigáljon a web SSH-munkamenethez a Linux-tárolóval (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Hozzon létre egy könyvtárat a `ini`nevű `/home/site`ban, majd hozzon létre egy *. ini* -fájlt a `/home/site/ini` könyvtárban (például *Settings. ini)* a testreszabni kívánt irányelvek használatával. Használja ugyanazt a szintaxist, amelyet egy *php. ini* fájlban is használni fog. 

> [!TIP]
> A App Service beépített Linux-tárolókban a */Home* a megőrzött megosztott tárolóként szolgál. 
>

A [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) értékének módosításához például futtassa a következő parancsokat:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

## <a name="enable-php-extensions"></a>PHP-bővítmények engedélyezése

A beépített PHP-telepítések a leggyakrabban használt bővítményeket tartalmazzák. A [php. ini-irányelvek testreszabásához](#customize-php_ini_system-directives)ugyanúgy engedélyezheti a további bővítményeket.

> [!NOTE]
> A PHP-verzió és az aktuális *php. ini* -konfiguráció megtekintéséhez a legjobb módszer a [phpinfo ()](https://php.net/manual/function.phpinfo.php) meghívása az alkalmazásban.
>

További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

Vegyen fel egy `bin` könyvtárat az alkalmazás gyökérkönyvtárához, és helyezze a `.so` kiterjesztésű fájlokat (például *mongodb.so*). Győződjön meg arról, hogy a bővítmények kompatibilisek az Azure PHP-verziójával, és VC9 és nem szálon (Zem) kompatibilisek.

Telepítse a módosításokat.

Kövesse az [PHP_INI_SYSTEM irányelvek testreszabása](#customize-php_ini_system-directives)című szakasz lépéseit, adja hozzá a bővítményeket az egyéni *. ini* -fájlhoz a [kiterjesztéssel](https://www.php.net/manual/ini.core.php#ini.extension) vagy [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) irányelvekkel.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibakeresés

Ha egy működő PHP-alkalmazás másképp viselkedik App Service vagy hibákat tartalmaz, próbálkozzon a következőkkel:

- [A log stream elérése](#access-diagnostic-logs).
- Az alkalmazás helyi tesztelése éles módban. App Service a Node. js-alkalmazásokat éles módban futtatja, ezért a projektnek a várt módon kell működnie a helyi üzemi módban. Például:
    - A *Composer. JSON*fájltól függően különböző csomagok is telepíthetők éles üzemmódba (`require` vagy `require-dev`).
    - Bizonyos webes keretrendszerek eltérő üzemi módban telepíthetnek statikus fájlokat.
    - Bizonyos webes keretrendszerek éles módban történő futtatáskor egyéni indítási parancsfájlokat is használhatnak.
- Az alkalmazást hibakeresési módban App Service futtathatja. A [Laravel](https://meanjs.org/)-ben például beállíthatja, hogy az alkalmazás a hibakeresési üzeneteket az éles környezetben állítsa be úgy, hogy [az `APP_DEBUG` alkalmazás beállítását `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

Előfordulhat, hogy a következő üzenet jelenik meg a tároló naplóiban:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Nyugodtan figyelmen kívül hagyhatja ezt az üzenetet. a `/robots933456.txt` egy olyan dummy URL-cím elérési útja, amelyet a App Service használ annak vizsgálatára, hogy a tároló képes-e kérések kiszolgálására. Egy 404-es válasz egyszerűen azt jelzi, hogy az elérési út nem létezik, de lehetővé teszi, hogy App Service tudja, hogy a tároló kifogástalan állapotú, és készen áll a kérelmekre való válaszadás

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: PHP-alkalmazás és MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux – gyakori kérdések](app-service-linux-faq.md)
