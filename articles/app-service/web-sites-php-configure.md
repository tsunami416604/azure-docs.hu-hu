---
title: PHP-futtatókörnyezet konfigurálása
description: Megtudhatja, hogyan konfigurálhatja az alapértelmezett PHP-telepítést, illetve hogyan adhat hozzá egyéni PHP-telepítést Azure App Servicehoz.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 208f4f7b4c2d8562d5237a40f52e4774ea5c5606
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272474"
---
# <a name="configure-php-in-azure-app-service"></a>A PHP konfigurálása Azure App Service

## <a name="introduction"></a>Introduction (Bevezetés)

Ez az útmutató bemutatja, hogyan konfigurálhatja a beépített PHP-futtatókörnyezetet a webalkalmazások és API-alkalmazások számára a [Azure app Serviceban](https://go.microsoft.com/fwlink/?LinkId=529714), egyéni php-futtatókörnyezetet és bővítmények engedélyezését. App Service használatához regisztráljon az [ingyenes próbaverzióra]. Ahhoz, hogy a legtöbbet hozza ki az útmutatóból, először létre kell hoznia egy PHP-alkalmazást App Serviceban.

## <a name="how-to-change-the-built-in-php-version"></a>Útmutató: a beépített PHP-verzió módosítása

Webalkalmazás létrehozásakor kiválaszthatja a konfigurálni kívánt PHP-verziót. A jelenleg támogatott verziók naprakész információit itt tekintheti [meg app Service a PHP](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md) -ben.

Az alkalmazás meglévő futtatókörnyezet-verziójának vizsgálatához telepíthet egy olyan parancsfájlt, amely meghívja a [phpinfo ()] függvényt.

A PHP verziójának frissítéséhez kövesse az alábbi módszerek egyikét:

### <a name="azure-portal"></a>Azure Portal

1. Keresse meg az alkalmazást a [Azure Portalban](https://portal.azure.com) , és görgessen a **konfiguráció** lapra.

2. A **konfiguráció**területen válassza az **általános beállítások** lehetőséget, és válassza ki az új PHP-verziót.

3. Kattintson a **Save (Mentés** ) gombra az **általános beállítások** panel tetején.

### <a name="azure-cli"></a>Azure CLI 

Az Azure parancssori felületének használatához [telepítenie kell az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t a számítógépre.

1. Nyissa meg a terminált, és jelentkezzen be a fiókjába.

        az login

1. Ellenőrizze, hogy megjelenik-e a támogatott futtatókörnyezetek listája.

        az webapp list-runtimes | grep php

2. Állítsa be az alkalmazás PHP-verzióját.

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. A PHP verziója már be van állítva. A következő beállításokat ellenőrizheti:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Útmutató: a beépített PHP-konfigurációk módosítása

Bármely beépített PHP-futtatókörnyezet esetében az alábbi lépéseket követve módosíthatja a konfigurációs beállításokat. (A php. ini direktívával kapcsolatos információkért tekintse meg [a php. ini-irányelvek listáját].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>PHP\_ini-\_felhasználó módosítása,\_php\_ini PERDIR,\_php\_ini minden konfigurációs beállítás

1. Vegyen fel egy [. user. ini] fájlt a gyökérkönyvtárba.
1. Adja hozzá a konfigurációs beállításokat `.user.ini` a fájlhoz ugyanazzal a szintaxissal, amelyet egy `php.ini` fájlban szeretne használni. Ha például be szeretné kapcsolni a `display_errors` beállítást, és a beállítást 10 m `upload_max_filesize` -re állította, a `.user.ini` fájl a következő szöveget fogja tartalmazni:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Az alkalmazás üzembe helyezése.
3. Indítsa újra az alkalmazást. (Az újraindítás azért szükséges, mert a PHP által beolvasott `.user.ini` fájlok gyakoriságát a `user_ini.cache_ttl` beállítás szabályozza, amely a rendszerszintű beállítás, amely alapértelmezés szerint 300 másodperc (5 perc). Az alkalmazás újraindítása a PHP-ben a `.user.ini` fájl új beállításainak olvasásához.)

Egy `.user.ini` fájl használatának alternatívájaként a parancsfájlok [ini_set ()] függvényével olyan konfigurációs beállításokat adhat meg, amelyek nem rendszerszintű irányelvek.

### <a name="changing-php_ini_system-configuration-settings"></a>A PHP\_ini\_rendszerkonfigurációs beállításainak módosítása

1. Adja hozzá az alkalmazáshoz a kulcsot `PHP_INI_SCAN_DIR` és az értéket tartalmazó Alkalmazásbeállítás`d:\home\site\ini`
1. Hozzon `settings.ini` létre egy fájlt a kudu-&lt;konzollal (http://&gt;site-Name. SCM.azurewebsite.net `d:\home\site\ini` ) a címtárban.
1. Adja hozzá a konfigurációs beállításokat `settings.ini` a fájlhoz ugyanazzal a szintaxissal, amelyet egy `php.ini` fájlban szeretne használni. Ha például egy `curl.cainfo` `*.crt` fájlra szeretné beállítani a beállítást, és a "wincache. maxfilesize" beállítást a 512 K értékre állítja, akkor `settings.ini` a fájl a következő szöveget fogja tartalmazni:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. A módosítások újraindításához indítsa újra az alkalmazást.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Útmutató: bővítmények engedélyezése az alapértelmezett PHP-futtatókörnyezetben

Ahogy az előző szakaszban is látható, a legjobb módszer az alapértelmezett PHP-verzió, az alapértelmezett konfiguráció és az engedélyezett bővítmények számára, hogy olyan parancsfájlt helyezzen üzembe, amely meghívja a [phpinfo ()]metódust. További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

### <a name="configure-via-ini-settings"></a>Konfigurálás az ini-beállítások használatával

1. Adjon hozzá `ext` egy könyvtárat a `d:\home\site` címtárhoz.
1. Terjesszen `.dll` a bővítmények `ext` fájljait a könyvtárba ( `php_xdebug.dll`például:). Ügyeljen arra, hogy a bővítmények kompatibilisek legyenek a PHP alapértelmezett verziójával, és a VC9 és a nem szál-biztonságos (Zem) kompatibilisek legyenek.
1. Adja hozzá az alkalmazáshoz a kulcsot `PHP_INI_SCAN_DIR` és az értéket tartalmazó Alkalmazásbeállítás`d:\home\site\ini`
1. Hozzon `ini` létre egy `d:\home\site\ini` fájlt `extensions.ini`a nevű fájlban.
1. Adja hozzá a konfigurációs beállításokat `extensions.ini` a fájlhoz ugyanazzal a szintaxissal, amelyet egy `php.ini` fájlban szeretne használni. Ha például engedélyezni szeretné a MongoDB és a Xdebug bővítmény bővítményt, a `extensions.ini` fájl a következő szöveget fogja tartalmazni:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. A módosítások betöltéséhez indítsa újra az alkalmazást.

### <a name="configure-via-app-setting"></a>Konfigurálás az alkalmazás beállításain keresztül

1. Adjon hozzá `bin` egy könyvtárat a gyökérkönyvtárhoz.
2. Terjesszen `.dll` a bővítmények `bin` fájljait a könyvtárba ( `php_xdebug.dll`például:). Ügyeljen arra, hogy a bővítmények kompatibilisek legyenek a PHP alapértelmezett verziójával, és a VC9 és a nem szál-biztonságos (Zem) kompatibilisek legyenek.
3. Az alkalmazás üzembe helyezése.
4. Keresse meg az alkalmazást a Azure Portalban, és kattintson az alábbi **Beállítások** szakaszban található **konfigurációra** .
5. A **konfiguráció** panelen válassza az **Alkalmazásbeállítások**lehetőséget.
6. Az **Alkalmazásbeállítások** szakaszban kattintson az **+ új alkalmazás beállítása** elemre, és hozzon létre egy **PHP_EXTENSIONS** kulcsot. A kulcs értéke a webhely gyökeréhez viszonyított elérési út lenne: **bin\your-ext-file**.
7. Kattintson a **frissítés** gombra a lap alján, majd kattintson a **Mentés** elemre az **Alkalmazásbeállítások** lapon.

A Zend Extensions szolgáltatást **PHP_ZENDEXTENSIONS** kulccsal is támogatja. Több bővítmény engedélyezéséhez adja meg a `.dll` fájlok vesszővel tagolt listáját az Alkalmazásbeállítások értékéhez.

## <a name="how-to-use-a-custom-php-runtime"></a>Útmutató: egyéni PHP-futtatókörnyezet használata

Az alapértelmezett PHP-futtatókörnyezet helyett a App Service használhat a PHP-parancsfájlok futtatásához megadott PHP-futtatókörnyezetet. Az Ön által megadott futtatókörnyezet konfigurálható egy `php.ini` Ön által megadott fájllal is. Ha App Service használatával egyéni PHP-futtatókörnyezetet szeretne használni, kövesse az alábbi lépéseket.

1. Szerezze be a Windowshoz készült PHP nem biztonságos, VC9 vagy VC11 kompatibilis verzióját. A PHP for Windows legújabb kiadásai itt találhatók: [https://windows.php.net/download/]. A régebbi verziók a következő archívumban találhatók: [https://windows.php.net/downloads/releases/archives/].
2. Módosítsa a `php.ini` futtatókörnyezethez tartozó fájlt. A App Service csak a rendszerszintű irányelvek által figyelmen kívül hagyott konfigurációs beállításokat veszi figyelembe. (A csak rendszerszintű irányelvekkel kapcsolatos információkért tekintse meg [a php. ini-irányelvek listáját]).
3. Opcionálisan hozzáadhat bővítményeket a PHP-futtatókörnyezethez, és engedélyezheti `php.ini` azokat a fájlban.
4. Adjon hozzá `bin` egy könyvtárat a gyökérkönyvtárhoz, és helyezze el azt a könyvtárat, amely tartalmazza a PHP-futtatókörnyezetet ( `bin\php`például).
5. Az alkalmazás üzembe helyezése.
6. Keresse meg az alkalmazást a Azure Portalban, és kattintson a **konfiguráció** panelre.
8. A **konfiguráció** panelen válassza az **elérésiút-hozzárendelések**lehetőséget. 
9. Kattintson az **+ új kezelő** elemre, és adja hozzá `*.php` a kiterjesztés mezőhöz, `php-cgi.exe` és adja hozzá a végrehajtható fájl elérési útját a **parancsfájl-feldolgozóban**. Ha a PHP-futtatókörnyezetet az alkalmazás `bin` gyökérkönyvtárában lévő könyvtárba helyezi, az elérési út `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Az alján kattintson a **frissítés** gombra a kezelő leképezés hozzáadásának befejezéséhez.
11. Kattintson a **Mentés** gombra a módosítások mentéséhez.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Útmutató: a zeneszerzői automatizálás engedélyezése az Azure-ban

Alapértelmezés szerint a App Service nem végez semmit a zeneszerző. JSON fájlon, ha van egy a PHP-projektben. Ha git- [telepítést](deploy-local-git.md)használ, engedélyezheti a zeneszerző. JSON-feldolgozást `git push` a zeneszerzői bővítmény engedélyezésével.

> [!NOTE]
> [Szavazzon az első osztályú zeneszerzői támogatásra app Service itt](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. A [Azure Portal](https://portal.azure.com)php-alkalmazás paneljén kattintson az **eszközök** > **bővítmények**elemre.

    ![Azure Portal beállítások panel a zeneszerzői automatizálás engedélyezéséhez az Azure-ban](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kattintson a **Hozzáadás**, majd a **zeneszerző**elemre.

    ![Composer-bővítmény hozzáadása a zeneszerzői automatizálás engedélyezéséhez az Azure-ban](./media/web-sites-php-configure/composer-extension-add.png)
3. A jogi feltételek elfogadásához kattintson **az OK** gombra. A bővítmény hozzáadásához kattintson ismét **az OK** gombra.

    A **telepített bővítmények** panel a zeneszerzői bővítményt jeleníti meg.
    ![Jogi feltételek elfogadása a zeneszerzői automatizálás engedélyezéséhez az Azure-ban](./media/web-sites-php-configure/composer-extension-view.png)
4. Most pedig a helyi gépen lévő terminál-ablakban hajtsa végre `git add` `git commit`a (z `git push` ) és az alkalmazást. Figyelje meg, hogy a zeneszerző a Composer. JSON fájlban definiált függőségeket telepíti.

    ![Git-üzembe helyezés a zeneszerzői automatizálással az Azure-ban](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>További lépések

További információ: [php fejlesztői központ](https://azure.microsoft.com/develop/php/).

[ingyenes próbaverzió]: https://www.windowsazure.com/pricing/free-trial/
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
