---
title: Windows PHP-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy PHP-alkalmazást a App Service natív Windows-példányaiban. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1eb4e9d349fdd0097cbde4e4cef3d5c61a167193
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908105"
---
# <a name="configure-a-windows-php-app-for-azure-app-service"></a>Windowsos PHP-alkalmazás konfigurálása Azure App Servicehoz

Ez az útmutató bemutatja, hogyan konfigurálhatja a PHP-webalkalmazásokat, a mobil háttér-és API-alkalmazásokat Azure App Serviceban. Ez az útmutató a Windows platformon üzemeltetett alkalmazásokra vonatkozik. A Linux-alkalmazásokkal kapcsolatos információkért lásd: [Linux php-alkalmazás konfigurálása Azure app Servicehoz](containers/configure-language-php.md).

Ez az útmutató a PHP-fejlesztőknek szóló alapfogalmakat és útmutatást tartalmaz, amelyek az alkalmazások App Service való telepítését végzik. Ha még soha nem használta a Azure App Servicet, először kövesse a [php](app-service-web-get-started-php.md) gyors és a [php-t a MySQL-vel foglalkozó oktatóanyagban](app-service-web-tutorial-php-mysql.md) .

## <a name="show-php-version"></a>PHP-verzió megjelenítése

Az aktuális PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Az összes támogatott PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

## <a name="set-php-version"></a>PHP-verzió beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a PHP-verzió 7,4-es értékre való beállításához:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

## <a name="run-composer"></a>Zeneszerző futtatása

Ha azt szeretné, hogy a App Service az üzembe helyezési időpontban futtassa a [zeneszerzőt](https://getcomposer.org/) , a legegyszerűbb módszer a zeneszerző belefoglalása a tárházba.

Egy helyi terminál ablakában váltson át a könyvtárra a tárház gyökerére, és kövesse a [zeneszerző](https://getcomposer.org/download/) *. farmakovigilancia* a könyvtár gyökeréhez való letöltéséhez szükséges utasításokat.

Futtassa a következő parancsokat (telepítenie kell a [NPM](https://www.npmjs.com/get-npm) ):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A tárház gyökerének most két további fájlja van: *. Deployment* és *Deploy.sh*.

Nyissa meg a *Deploy.sh* , és keresse meg az `Deployment` alábbihoz hasonló szakaszt:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Adja hozzá a Code *(kód)* szakaszt, amelyen futtatnia kell a szükséges eszközt a `Deployment` szakasz végén:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Véglegesítse az összes módosítást, és telepítse a kódot a git használatával vagy a zip üzembe helyezésével, ha a Build Automation engedélyezve van. A zeneszerzőnek most már futnia kell az üzembe helyezés automatizálásának részeként.

## <a name="run-gruntbowergulp"></a>Morog/Bower/Nyelő futtatása

Ha azt szeretné, hogy a App Service a népszerű Automation-eszközöket az üzembe helyezési időben, például a morog, a Bower vagy a Nyelő használatával futtassa, [egyéni telepítési parancsfájlt](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)kell megadnia. App Service futtatja ezt a parancsfájlt a git-vel való üzembe helyezéskor, vagy ha engedélyezve van a Build Automation szolgáltatással rendelkező [zip-telepítés](deploy-zip.md) . 

Ha engedélyezni szeretné, hogy a tárház futtassa ezeket az eszközöket, hozzá kell adnia azokat a függőségekhez *package.json.* Például:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Egy helyi terminál ablakban váltson át a tárház gyökerére, és futtassa a következő parancsokat (telepítenie kell a [NPM](https://www.npmjs.com/get-npm) -t):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A tárház gyökerének most két további fájlja van: *. Deployment* és *Deploy.sh*.

Nyissa meg a *Deploy.sh* , és keresse meg az `Deployment` alábbihoz hasonló szakaszt:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ez a szakasz a futtatásával végződik `npm install --production` . Adja hozzá a Code *(kód)* szakaszt, amelyen futtatnia kell a szükséges eszközt a `Deployment` szakasz végén:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Tekintse [meg a MEAN.js minta példáját](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), ahol az üzembe helyezési parancsfájl egy egyéni parancsot is futtat `npm install` .

### <a name="bower"></a>Bower

Ez a kódrészlet fut `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ez a kódrészlet fut `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ez a kódrészlet fut `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

App Service az [Alkalmazásbeállítások](configure-common.md#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezt követően a szabványos [GETENV ()](https://secure.php.net/manual/function.getenv.php) minta használatával érheti el őket. Ha például egy nevű alkalmazáshoz szeretne hozzáférni `DB_HOST` , használja a következő kódot:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Hely gyökerének módosítása

Az Ön által választott webes keretrendszer egy alkönyvtárt is használhat a hely gyökeréhez. Például a [Laravel](https://laravel.com/)a *nyilvános/* alkönyvtárat használja a hely gyökeréhez.

A hely gyökerének testreszabásához a parancs használatával állítsa be a virtuális alkalmazás elérési útját az alkalmazáshoz [`az resource update`](/cli/azure/resource#az-resource-update) . A következő példában a hely gyökerét a tárházban lévő *nyilvános/* alkönyvtárra állítja be. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Alapértelmezés szerint a Azure App Service a virtuális alkalmazás elérési útját ( _/_ ) a telepített alkalmazásfájlok (_sites\wwwroot_) gyökérkönyvtárára mutat.

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

A népszerű webes keretrendszerek lehetővé teszik a `X-Forwarded-*` szabványos alkalmazási mintában lévő információk elérését. A [CodeIgniter](https://codeigniter.com/)-ben a [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi a értéket `X_FORWARDED_PROTO` .

## <a name="customize-phpini-settings"></a>php.ini beállítások testreszabása

Ha módosítania kell a PHP-telepítést, a következő lépésekkel módosíthatja a [php.ini irányelvek](https://www.php.net/manual/ini.list.php) bármelyikét.

> [!NOTE]
> A PHP-verzió és a jelenlegi *php.ini* konfiguráció megtekintésének legjobb módja az [phpinfo ()](https://php.net/manual/function.phpinfo.php) hívása az alkalmazásban.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Testreszabás – nem PHP_INI_SYSTEM irányelvek

PHP_INI_USER, PHP_INI_PERDIR és PHP_INI_ALL irányelvek testreszabásához (lásd: [php.ini irányelvek](https://www.php.net/manual/ini.list.php)), adjon hozzá egy `.user.ini` fájlt az alkalmazás gyökérkönyvtárához.

Adja hozzá a konfigurációs beállításokat a `.user.ini` fájlhoz ugyanazzal a szintaxissal, amelyet egy `php.ini` fájlban szeretne használni. Ha például be szeretné kapcsolni a beállítást, és a `display_errors` `upload_max_filesize` beállítást 10 m-re állította, a `.user.ini` fájl a következő szöveget fogja tartalmazni:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Telepítse újra az alkalmazást a módosításokkal, majd indítsa újra.

Egy fájl használata helyett használhatja a `.user.ini` [ini_set ()](https://www.php.net/manual/function.ini-set.php) alkalmazást az alkalmazásban a nem PHP_INI_SYSTEM irányelvek testreszabásához.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM irányelvek testreszabása

PHP_INI_SYSTEM irányelvek testreszabásához (lásd: [php.ini irányelvek](https://www.php.net/manual/ini.list.php)) nem használhatja a *. htaccess* megközelítést. A App Service az Alkalmazásbeállítások használatával külön mechanizmust biztosít `PHP_INI_SCAN_DIR` .

Először futtassa az alábbi parancsot a [Cloud Shellban](https://shell.azure.com) egy, a következő nevű alkalmazás-beállítás hozzáadásához `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Navigáljon a kudu-konzolhoz ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ), és navigáljon a következőhöz: `d:\home\site` .

Hozzon létre egy könyvtárat a `d:\home\site` néven `ini` , majd hozzon létre egy *. ini* -fájlt a `d:\home\site\ini` könyvtárban (például *settings.ini)* a testreszabni kívánt irányelvek alapján. Használja ugyanazt a szintaxist, amelyet egy *php.ini* fájlban szeretne használni. 

A [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) értékének módosításához például futtassa a következő parancsokat:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

## <a name="enable-php-extensions"></a>PHP-bővítmények engedélyezése

A beépített PHP-telepítések a leggyakrabban használt bővítményeket tartalmazzák. A További bővítmények ugyanúgy engedélyezhetők, mint az [php.ini irányelvek testreszabása](#customize-php_ini_system-directives).

> [!NOTE]
> A PHP-verzió és a jelenlegi *php.ini* konfiguráció megtekintésének legjobb módja az [phpinfo ()](https://php.net/manual/function.phpinfo.php) hívása az alkalmazásban.
>

További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

Adjon hozzá egy `bin` könyvtárat az alkalmazás gyökérkönyvtárához, és helyezze a `.so` bővítmény fájljait (például *mongodb.so*). Győződjön meg arról, hogy a bővítmények kompatibilisek az Azure PHP-verziójával, és VC9 és nem szálon (Zem) kompatibilisek.

Telepítse a módosításokat.

Kövesse az [PHP_INI_SYSTEM irányelvek testreszabása](#customize-php_ini_system-directives)című szakasz lépéseit, adja hozzá a bővítményeket az egyéni *. ini* -fájlhoz a [kiterjesztéssel](https://www.php.net/manual/ini.core.php#ini.extension) vagy [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) irányelvekkel.

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

A standard [error_log ()](https://php.net/manual/function.error-log.php) segédprogrammal megtekintheti a diagnosztikai naplókat a Azure app Serviceban.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha egy működő PHP-alkalmazás másképp viselkedik App Service vagy hibákat tartalmaz, próbálkozzon a következőkkel:

- [A log stream elérése](#access-diagnostic-logs).
- Az alkalmazás helyi tesztelése éles módban. App Service éles módban futtatja az alkalmazást, ezért meg kell győződnie arról, hogy a projekt a várt módon működik a helyi üzemi módban. Például:
    - Bizonyos webes keretrendszerek eltérő üzemi módban telepíthetnek statikus fájlokat.
    - Bizonyos webes keretrendszerek éles módban történő futtatáskor egyéni indítási parancsfájlokat is használhatnak.
- Az alkalmazást hibakeresési módban App Service futtathatja. A [Laravel](https://meanjs.org/)-ben például beállíthatja, hogy az alkalmazás a hibakeresési üzeneteket az éles környezetben állítsa be úgy, hogy [az `APP_DEBUG` `true` alkalmazás beállítását ](configure-common.md#configure-app-settings)adja meg.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: PHP-alkalmazás és MySQL](app-service-web-tutorial-php-mysql.md)
