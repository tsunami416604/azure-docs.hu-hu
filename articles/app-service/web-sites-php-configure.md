---
title: A PHP futtatókörnyezet konfigurálása
description: Megtudhatja, hogyan konfigurálhatja az alapértelmezett PHP-telepítést, illetve hogyan adhat hozzá egyéni PHP-telepítést Azure App Servicehoz.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2d35c31e23da7addcf0b4c341c6925f258d5c232
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688263"
---
# <a name="configure-php-in-azure-app-service"></a>A PHP konfigurálása Azure App Service

## <a name="introduction"></a>Introduction (Bevezetés)

Ez az útmutató bemutatja, hogyan konfigurálhatja a beépített PHP-futtatókörnyezetet a webalkalmazásokhoz, a mobil-és a [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714)API-alkalmazásokhoz, valamint egy egyéni php-futtatókörnyezetet és a bővítmények engedélyezését. App Service használatához regisztráljon az [ingyenes próbaverzióra]. Ahhoz, hogy a legtöbbet hozza ki az útmutatóból, először létre kell hoznia egy PHP-alkalmazást App Serviceban.

## <a name="how-to-change-the-built-in-php-version"></a>Útmutató: a beépített PHP-verzió módosítása

Alapértelmezés szerint a PHP 5,6 telepítve van, és azonnal elérhető a App Service-alkalmazás létrehozásakor. A legjobb lehetőség, hogy megtekintse az elérhető kiadási változatot, az alapértelmezett konfigurációját és az engedélyezett bővítményeket egy olyan parancsfájl üzembe helyezéséhez, amely meghívja a [phpinfo ()] függvényt.

A PHP 7,0 és a PHP 7,2 verziója is elérhető, de alapértelmezés szerint nincs engedélyezve. A PHP verziójának frissítéséhez kövesse az alábbi módszerek egyikét:

### <a name="azure-portal"></a>Azure Portal

1. Keresse meg az alkalmazást a [Azure Portalban](https://portal.azure.com) , és görgessen a **konfiguráció** lapra.

2. A **konfiguráció**területen válassza az **általános beállítások** lehetőséget, és válassza ki az új PHP-verziót.

3. Kattintson a **Save (Mentés** ) gombra az **általános beállítások** panel tetején.

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Nyissa meg Azure PowerShell, és jelentkezzen be a fiókjába:

        PS C:\> Connect-AzAccount
2. Állítsa be az alkalmazás PHP-verzióját.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
3. A PHP verziója már be van állítva. A következő beállításokat ellenőrizheti:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>Azure parancssori felület (CLI) 

Az Azure parancssori felületének használatához [telepítenie kell az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t a számítógépre.

1. Nyissa meg a terminált, és jelentkezzen be a fiókjába.

        az login

1. Ellenőrizze, hogy megjelenik-e a támogatott futtatókörnyezetek listája.

        az webapp list-runtimes | grep php

2. Állítsa be az alkalmazás PHP-verzióját.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. A PHP verziója már be van állítva. A következő beállításokat ellenőrizheti:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Útmutató: a beépített PHP-konfigurációk módosítása

Bármely beépített PHP-futtatókörnyezet esetében az alábbi lépéseket követve módosíthatja a konfigurációs beállításokat. (A php. ini direktívával kapcsolatos információkért tekintse meg [A php. ini-irányelvek listája].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>PHP\_INI\_felhasználó, PHP\_INI\_PERDIR, PHP\_INI\_az összes konfigurációs beállítás

1. Vegyen fel egy [. user. ini] fájlt a gyökérkönyvtárba.
1. Adja hozzá a konfigurációs beállításokat a `.user.ini` fájlhoz ugyanazzal a szintaxissal, amelyet egy `php.ini` fájlban fog használni. Ha például be szeretné kapcsolni a `display_errors` beállítást, és a `upload_max_filesize` beállítást 10 m-re állítja, a `.user.ini`-fájl a következő szöveget fogja tartalmazni:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Az alkalmazás üzembe helyezése.
3. Indítsa újra az alkalmazást. (Újraindításra van szükség, mert a PHP `.user.ini`-fájlok olvasásának gyakoriságát a `user_ini.cache_ttl` beállítás szabályozza, amely a rendszerszintű beállítás, amely alapértelmezés szerint 300 másodperc (5 perc). Az alkalmazás újraindítása a PHP-ben a `.user.ini` fájl új beállításainak olvasásához.)

`.user.ini`-fájl használatának alternatívájaként a parancsfájlokban a [ini_set ()] függvényt is használhatja a parancsfájlok segítségével olyan konfigurációs beállítások megadásához, amelyek nem rendszerszintű irányelvek.

### <a name="changing-php_ini_system-configuration-settings"></a>A PHP\_INI\_rendszerkonfigurációs beállításainak módosítása

1. Adjon hozzá egy alkalmazást az alkalmazáshoz a kulcs `PHP_INI_SCAN_DIR` és az érték `d:\home\site\ini`
1. Hozzon létre egy `settings.ini` fájlt a kudu-konzollal (http://&lt;site-Name&gt;. scm.azurewebsite.net) a `d:\home\site\ini` könyvtárban.
1. Adja hozzá a konfigurációs beállításokat a `settings.ini` fájlhoz ugyanazzal a szintaxissal, amelyet egy `php.ini` fájlban fog használni. Ha például az `curl.cainfo` beállítást egy `*.crt` fájlra kívánja beállítani, és az "wincache. MaxFileSize" beállítást a 512K értékre állítja, akkor a `settings.ini` fájl a következő szöveget fogja tartalmazni:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. A módosítások újraindításához indítsa újra az alkalmazást.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Útmutató: bővítmények engedélyezése az alapértelmezett PHP-futtatókörnyezetben

Ahogy az előző szakaszban is látható, a legjobb módszer az alapértelmezett PHP-verzió, az alapértelmezett konfiguráció és az engedélyezett bővítmények számára, hogy olyan parancsfájlt helyezzen üzembe, amely meghívja a [phpinfo ()]metódust. További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

### <a name="configure-via-ini-settings"></a>Konfigurálás az ini-beállítások használatával

1. Vegyen fel egy `ext` könyvtárat a `d:\home\site` könyvtárba.
1. Helyezze `.dll` kiterjesztésű fájlokat a `ext` könyvtárba (például `php_xdebug.dll`). Ügyeljen arra, hogy a bővítmények kompatibilisek legyenek a PHP alapértelmezett verziójával, és a VC9 és a nem szál-biztonságos (Zem) kompatibilisek legyenek.
1. Adjon hozzá egy alkalmazást az alkalmazáshoz a kulcs `PHP_INI_SCAN_DIR` és az érték `d:\home\site\ini`
1. Hozzon létre egy `ini` fájlt a `extensions.ini`nevű `d:\home\site\ini`ban.
1. Adja hozzá a konfigurációs beállításokat a `extensions.ini` fájlhoz ugyanazzal a szintaxissal, amelyet egy `php.ini` fájlban fog használni. Ha például engedélyezni kívánja a MongoDB és a Xdebug bővítmény bővítményt, a `extensions.ini` fájl a következő szöveget fogja tartalmazni:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. A módosítások betöltéséhez indítsa újra az alkalmazást.

### <a name="configure-via-app-setting"></a>Konfigurálás az alkalmazás beállításain keresztül

1. Adjon hozzá egy `bin` könyvtárat a gyökérkönyvtárhoz.
2. Helyezze `.dll` kiterjesztésű fájlokat a `bin` könyvtárba (például `php_xdebug.dll`). Ügyeljen arra, hogy a bővítmények kompatibilisek legyenek a PHP alapértelmezett verziójával, és a VC9 és a nem szál-biztonságos (Zem) kompatibilisek legyenek.
3. Az alkalmazás üzembe helyezése.
4. Keresse meg az alkalmazást a Azure Portalban, és kattintson az alábbi **Beállítások** szakaszban található **konfigurációra** .
5. A **konfiguráció** panelen válassza az **Alkalmazásbeállítások**lehetőséget.
6. Az **Alkalmazásbeállítások** szakaszban kattintson az **+ új alkalmazás beállítása** elemre, és hozzon létre egy **PHP_EXTENSIONS** kulcsot. A kulcs értéke a webhely gyökeréhez viszonyított elérési út lenne: **bin\your-ext-file**.
7. Kattintson a **frissítés** gombra a lap alján, majd kattintson a **Mentés** elemre az **Alkalmazásbeállítások** lapon.

A Zend Extensions szolgáltatást **PHP_ZENDEXTENSIONS** kulccsal is támogatja. Több bővítmény engedélyezéséhez adja meg `.dll` fájlok vesszővel tagolt listáját az Alkalmazásbeállítások értékéhez.

## <a name="how-to-use-a-custom-php-runtime"></a>Útmutató: egyéni PHP-futtatókörnyezet használata

Az alapértelmezett PHP-futtatókörnyezet helyett a App Service használhat a PHP-parancsfájlok futtatásához megadott PHP-futtatókörnyezetet. Az Ön által megadott futtatókörnyezetet egy `php.ini` fájl is konfigurálhatja, amelyet Ön is megadhat. Ha App Service használatával egyéni PHP-futtatókörnyezetet szeretne használni, kövesse az alábbi lépéseket.

1. Szerezze be a Windowshoz készült PHP nem biztonságos, VC9 vagy VC11 kompatibilis verzióját. A PHP for Windows legújabb kiadásai itt találhatók: [https://windows.php.net/download/]. A régebbi kiadásokat itt tekintheti meg az archívumban: [https://windows.php.net/downloads/releases/archives/].
2. Módosítsa a futtatókörnyezet `php.ini` fájlját. A App Service csak a rendszerszintű irányelvek által figyelmen kívül hagyott konfigurációs beállításokat veszi figyelembe. (A csak rendszerszintű irányelvekkel kapcsolatos információkért tekintse meg [A php. ini-irányelvek listája]).
3. Opcionálisan hozzáadhat bővítményeket a PHP-futtatókörnyezethez, és engedélyezheti azokat a `php.ini` fájlban.
4. Vegyen fel egy `bin` könyvtárat a gyökérkönyvtárba, és helyezze el azt a könyvtárat, amely tartalmazza a PHP-futtatókörnyezetet (például `bin\php`).
5. Az alkalmazás üzembe helyezése.
6. Keresse meg az alkalmazást a Azure Portalban, és kattintson a **konfiguráció** panelre.
8. A **konfiguráció** panelen válassza az **elérésiút-hozzárendelések**lehetőséget. 
9. Kattintson az **+ új kezelő** elemre, és vegyen fel `*.php` a bővítmény mezőbe, és adja hozzá a **parancsfájl-feldolgozó**`php-cgi.exe` végrehajtható fájl elérési útját. Ha az alkalmazás gyökerében lévő `bin` könyvtárban helyezi el a PHP-futtatókörnyezetet, az elérési út `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Az alján kattintson a **frissítés** gombra a kezelő leképezés hozzáadásának befejezéséhez.
11. Kattintson a **Mentés** gombra a módosítások mentéséhez.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Útmutató: a zeneszerzői automatizálás engedélyezése az Azure-ban

Alapértelmezés szerint a App Service nem végez semmit a zeneszerző. JSON fájlon, ha van egy a PHP-projektben. Ha git- [telepítést](deploy-local-git.md)használ, a zeneszerző. JSON feldolgozását a `git push` során engedélyezheti a Composer bővítmény engedélyezésével.

> [!NOTE]
> [Szavazzon az első osztályú zeneszerzői támogatásra app Service itt](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. A [Azure Portal](https://portal.azure.com)php-alkalmazás paneljén kattintson az **eszközök** > **bővítmények**elemre.

    ![Azure Portal beállítások panel a zeneszerzői automatizálás engedélyezéséhez az Azure-ban](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kattintson a **Hozzáadás**, majd a **zeneszerző**elemre.

    ![Composer-bővítmény hozzáadása a zeneszerzői automatizálás engedélyezéséhez az Azure-ban](./media/web-sites-php-configure/composer-extension-add.png)
3. A jogi feltételek elfogadásához kattintson **az OK** gombra. A bővítmény hozzáadásához kattintson ismét **az OK** gombra.

    A **telepített bővítmények** panel a zeneszerzői bővítményt jeleníti meg.
    ![fogadja el a jogi feltételeket a zeneszerzői automatizálás engedélyezéséhez az Azure-ban](./media/web-sites-php-configure/composer-extension-view.png)
4. Most, hogy a helyi gépen egy terminál ablakban `git add`, `git commit`és `git push` hajt végre az alkalmazáshoz. Figyelje meg, hogy a zeneszerző a Composer. JSON fájlban definiált függőségeket telepíti.

    ![Git-üzembe helyezés a zeneszerzői automatizálással az Azure-ban](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Következő lépések

További információ: [php fejlesztői központ](https://azure.microsoft.com/develop/php/).

[ingyenes próbaverzióra]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo ()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[A php. ini-irányelvek listája]: https://www.php.net/manual/en/ini.list.php
[. user. ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set ()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
