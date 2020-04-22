---
title: PHP-alkalmazások konfigurálása
description: További információ az alkalmazás előre elkészített PHP-tárolójának konfigurálásáról. Ez a cikk a leggyakoribb konfigurációs feladatokat mutatja be.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758873"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Linux PHP alkalmazás konfigurálása az Azure App Service szolgáltatáshoz

Ez az útmutató bemutatja, hogyan konfigurálhatja a beépített PHP-futásidőt a webalkalmazásokhoz, a mobil háttérrendszerekhez és az API-alkalmazásokhoz az Azure App Service-ben.

Ez az útmutató kulcsfontosságú fogalmakat és utasításokat tartalmaz azoknak a PHP fejlesztőknek, akik beépített Linux-tárolót használnak az App Service szolgáltatásban. Ha még soha nem használta az Azure App Service-t, először kövesse a [PHP rövid útmutatóját](quickstart-php.md) és a [PHP-t a MySQL oktatóanyaggal.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>PHP verzió megjelenítése

Az aktuális PHP verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com)ben:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott PHP verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com)ben:

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP verzió beállítása

A KÖVETKEZŐ parancs futtatása a [Cloud Shellben](https://shell.azure.com) a PHP-verzió 7.2-re való beállításához:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Build-automatizálás testreszabása

Ha git- vagy zip-csomagok használatával telepíti az alkalmazást, és be van kapcsolva az alkalmazás, az App Service a következő sorrendben építi fel az automatizálási lépéseket:

1. Ha a program ezt `PRE_BUILD_SCRIPT_PATH`a.
1. Futtassa az `php composer.phar install` parancsot.
1. Ha a program ezt `POST_BUILD_SCRIPT_PATH`a.

`PRE_BUILD_COMMAND`és `POST_BUILD_COMMAND` olyan környezeti változók, amelyek alapértelmezés szerint üresek. Az előbuild parancsok futtatásához definiálja a programot. `PRE_BUILD_COMMAND` A létrehozás utáni parancsok `POST_BUILD_COMMAND`futtatásához definiálja a programot.

A következő példa a két változót egy parancssorozatra adja meg, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

A buildautomatizálás testreszabásához további környezeti változókat az [Oryx konfigurációja](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)című témakörben tava látható.

Az App Service linuxos rendszerének futásáról és felépítéséről az [Oryx dokumentációja: A PHP-alkalmazások észlelése és összeállítása](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)című témakörben olvashat bővebben.

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a beépített PHP tároló futtatja az Apache szervert. Az indításkor fut `apache2ctl -D FOREGROUND"`. Ha szeretné, az indításkor egy másik parancsot is futtathat, ha a következő parancsot futtatja a [Cloud Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben az [alkalmazáskódokon kívül is megadhat alkalmazásbeállításokat.](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) Ezután elérheti őket a szabványos [getenv()](https://secure.php.net/manual/function.getenv.php) minta használatával. Például egy alkalmazás nevű `DB_HOST`beállítás eléréséhez használja a következő kódot:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Webhely gyökérének módosítása

Az Ön által választott webes keretrendszer egy alkönyvtárat használhat a webhely gyökérkönyvtáraként. A [Laravel](https://laravel.com/)például `public/` az alkönyvtárat használja a hely gyökérkönyvtáraként.

Az App Service alapértelmezett PHP lemezképe apache-t használ, és nem teszi lehetővé az alkalmazás webhelygyökérének testreszabását. A korlátozás megkerüléséhez vegyen fel egy *.htaccess* fájlt a tárológyökérbe a következő tartalommal:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Ha inkább nem használná a *.htaccess* újraírást, üzembe helyezheti a Laravel-alkalmazását egy [egyéni Docker-rendszerképpel](quickstart-docker-go.md) is.

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

Az App Service-ben [az SSL-végződtetés](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztóknál történik, így minden HTTPS-kérelem titkosítatlan HTTP-kérelemként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy `X-Forwarded-Proto` a felhasználói kérelmek titkosítva vannak-e vagy sem, ellenőrizze a fejlécet.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

A népszerű webes keretrendszerek `X-Forwarded-*` lehetővé teszik a szabványos alkalmazásmintában szereplő információk elérését. A [CodeIgniter](https://codeigniter.com/)ben a [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi az értékét. `X_FORWARDED_PROTO`

## <a name="customize-phpini-settings"></a>A php.ini beállítások testreszabása

Ha módosítania kell a PHP telepítését, az alábbi lépések végrehajtásával módosíthatja a [php.ini direktívákbármelyikét.](https://www.php.net/manual/ini.list.php)

> [!NOTE]
> A legjobb módja annak, hogy a PHP verzió és a jelenlegi *php.ini* konfiguráció, hogy hívja [phpinfo()](https://php.net/manual/function.phpinfo.php) az alkalmazásban.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Nem PHP_INI_SYSTEM irányelvek testreszabása

A PHP_INI_USER, PHP_INI_PERDIR és PHP_INI_ALL direktívák testreszabásához (lásd: [php.ini direktívák)](https://www.php.net/manual/ini.list.php)vegyen fel egy *.htaccess fájlt* az alkalmazás gyökérkönyvtárába.

A *.htaccess* fájlban adja hozzá `php_value <directive-name> <value>` a direktívákat a szintaxis sal. Például:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Telepítse újra az alkalmazást a módosításokkal, majd indítsa újra. Ha üzembe kudu (például [a Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)használatával), akkor automatikusan újraindul a telepítés után.

A *.htaccess*használata alternatívájaként az alkalmazás [ini_set()](https://www.php.net/manual/function.ini-set.php) segítségével testreszabhatja ezeket a nem PHP_INI_SYSTEM irányelveket.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM irányelvek testreszabása

A PHP_INI_SYSTEM direktívák testreszabásához (lásd [php.ini direktívák)](https://www.php.net/manual/ini.list.php)nem használhatja a *.htaccess* megközelítést. Az App Service külön `PHP_INI_SCAN_DIR` mechanizmust biztosít az alkalmazásbeállítás használatával.

Először futtassa a következő parancsot a `PHP_INI_SCAN_DIR` [Cloud Shellben](https://shell.azure.com) egy alkalmazásbeállítás hozzáadásához:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`az alapértelmezett könyvtár, ahol *php.ini* létezik. `/home/site/ini`az az egyéni könyvtár, amelyben egyéni *.ini* fájlt ad hozzá. Az értékeket a `:`.

Keresse meg a webes SSH munkamenetet a Linux tárolóval (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Hozzon létre `/home/site` `ini`egy könyvtárat a nevesben, majd hozzon létre egy *.ini* fájlt a `/home/site/ini` könyvtárban (például *settings.ini)* a testre szabni kívánt irányelvekkel. Használja ugyanazt a szintaxist, mint amit egy *php.ini* fájlban használna. 

> [!TIP]
> Az App Service beépített Linux-tárolóiban a */home* a megőrzött megosztott tárolóként használatos. 
>

Ha például módosítani szeretné a [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) értékének módosításához futtassa a következő parancsokat:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

## <a name="enable-php-extensions"></a>PHP-bővítmények engedélyezése

A beépített PHP telepítések tartalmazzák a leggyakrabban használt bővítményeket. Engedélyezheti a további kiterjesztéseket ugyanúgy, hogy [testre php.ini direktívák](#customize-php_ini_system-directives).

> [!NOTE]
> A legjobb módja annak, hogy a PHP verzió és a jelenlegi *php.ini* konfiguráció, hogy hívja [phpinfo()](https://php.net/manual/function.phpinfo.php) az alkalmazásban.
>

További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

Adjon `bin` hozzá egy könyvtárat az alkalmazás `.so` gyökérkönyvtárához, és helyezze a bővítményfájlokat (például *mongodb.so).* Győződjön meg arról, hogy a bővítmények kompatibilisek az Azure-beli PHP-verzióval, és VC9-kompatibilisek és nem szálbiztosak (nts).

Telepítse a módosításokat.

Kövesse a [PHP_INI_SYSTEM irányelvek testreszabása](#customize-php_ini_system-directives)című részben leírt lépéseket, adja hozzá a bővítményeket az egyéni *.ini* fájlhoz a [kiterjesztés-](https://www.php.net/manual/ini.core.php#ini.extension) vagy [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) direktívákkal.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása a böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha egy működő PHP alkalmazás eltérően viselkedik az App Service szolgáltatásban, vagy hibákat tapasztal, próbálkozzon a következőkkel:

- [A naplófolyam elérése](#access-diagnostic-logs).
- Tesztelje az alkalmazást helyileg éles módban. Az App Service éles környezetben futtatja a Node.js alkalmazásokat, ezért meg kell győződnie arról, hogy a projekt az éles környezetben helyileg a várt módon működik. Például:
    - A *zeneszerzőtől.json*koordinátáktól függően`require` különböző `require-dev`csomagok telepíthetők a termelési módhoz ( vs. ).
    - Bizonyos webes keretrendszerek éles módban eltérő módon telepíthetik a statikus fájlokat.
    - Bizonyos webes keretrendszerek éles módban való futtatáskor egyéni indítási parancsfájlokat is használhatnak.
- Az alkalmazás futtatása az App Service-ben hibakeresési módban. A Laravel alkalmazásban például beállíthatja, hogy az alkalmazás éles környezetben lévő hibakeresési üzeneteket állítson le, [ha az alkalmazás beállítást a `APP_DEBUG` beállításra `true`állítja. ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) [Laravel](https://meanjs.org/)

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató: PHP alkalmazás a MySQL-rel](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)
