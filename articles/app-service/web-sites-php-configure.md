---
title: PHP-futtatókörnyezet konfigurálása
description: Ismerje meg, hogyan konfigurálhatja az alapértelmezett PHP-telepítést, vagy hogyan adhat hozzá egyéni PHP-telepítést az Azure App Service szolgáltatáshoz.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 208f4f7b4c2d8562d5237a40f52e4774ea5c5606
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272474"
---
# <a name="configure-php-in-azure-app-service"></a>A PHP konfigurálása az Azure App Service szolgáltatásban

## <a name="introduction"></a>Introduction (Bevezetés)

Ez az útmutató bemutatja, hogyan konfigurálhatja a beépített PHP-futásidőt a webalkalmazásokhoz és API-alkalmazásokhoz az [Azure App Service-ben,](https://go.microsoft.com/fwlink/?LinkId=529714)egyéni PHP-futásidejűt biztosíthat, és bővítményeket engedélyez. Az App Service használatához regisztráljon az [ingyenes próbaverzióra.] Ahhoz, hogy a legtöbbet hozd ki ebből az útmutatóból, először hozzon létre egy PHP alkalmazást az App Service szolgáltatásban.

## <a name="how-to-change-the-built-in-php-version"></a>Hogyan: A beépített PHP verzió módosítása

Amikor létrehoz egy webalkalmazást, kiválaszthatja a PHP konfigurálandó verzióját. A jelenleg támogatott verziók naprakész információiért lásd [a PHP on App Service](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md) szolgáltatásban található naprakész információkat.

Az alkalmazás meglévő futásidejű verziójának ellenőrzéséhez telepíthet egy parancsfájlt, amely meghívja a [phpinfo()] függvényt.

A PHP verzió frissítéséhez kövesse az alábbi módszerek egyikét:

### <a name="azure-portal"></a>Azure Portal

1. Tallózzon az alkalmazás ban az [Azure Portalon,](https://portal.azure.com) és görgessen a **Konfiguráció lapra.**

2. A **Konfiguráció lapon**válassza az Általános **beállítások** lehetőséget, és válassza az új PHP verziót.

3. Kattintson a **Mentés** gombra az **Általános beállítások** panel tetején.

### <a name="azure-cli"></a>Azure CLI 

Az Azure parancssori felületének használatához telepítenie kell [az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a számítógépre.

1. Nyissa meg a Terminált, és jelentkezzen be a fiókjába.

        az login

1. Ellenőrizze a támogatott futásidők listáját.

        az webapp list-runtimes | grep php

2. Állítsa be az alkalmazás PHP verzióját.

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. A PHP verzió most be van állítva. Ezeket a beállításokat a következőkben erősítheti meg:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Útmutató: A beépített PHP konfigurációk módosítása

Bármely beépített PHP-futásidejű esetén az alábbi lépések végrehajtásával módosíthatja a konfigurációs beállítások bármelyikét. (A php.ini irányelvekről a [php.ini irányelvek listája]című témakörben talál.)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Változó\_PHP\_INI\_USER,\_PHP INI PERDIR, PHP\_INI\_MINDEN konfigurációs beállítások

1. Vegyen fel egy [.user.ini] fájlt a gyökérkönyvtárba.
1. Adja hozzá a `.user.ini` konfigurációs beállításokat a fájlhoz `php.ini` a fájlban használt szintaxissal. Ha például be szeretné kapcsolni `display_errors` a `upload_max_filesize` beállítást, és 10 `.user.ini` M-re szeretné állítani a beállítást, a fájl a következő szöveget tartalmazza:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Telepítse az alkalmazást.
3. Indítsa újra az alkalmazást. (Újraindításra azért van szükség, mert `.user.ini` a PHP fájlolvasásának gyakoriságát a `user_ini.cache_ttl` beállítás szabályozza, amely egy rendszerszintű beállítás, és alapértelmezés szerint 300 másodperc (5 perc). Az alkalmazás újraindítása arra kényszeríti a `.user.ini` PHP-t, hogy elolvassa a fájl új beállításait.)

A `.user.ini` fájl használatának alternatívájaként a [parancsfájlok ini_set()] függvényével olyan konfigurációs beállításokat adhat meg, amelyek nem rendszerszintű irányelvek.

### <a name="changing-php_ini_system-configuration-settings"></a>A\_PHP\_INI SYSTEM konfigurációs beállításainak módosítása

1. Alkalmazásbeállítás hozzáadása az alkalmazáshoz `PHP_INI_SCAN_DIR` a kulccsal és az értékkel`d:\home\site\ini`
1. Hozzon `settings.ini` létre egy fájlt&lt;a Kudu konzollal (http:// .scm.azurewebsite.net)&gt;a `d:\home\site\ini` könyvtárban.
1. Adja hozzá a `settings.ini` konfigurációs beállításokat a fájlhoz `php.ini` a fájlban használt szintaxissal. Ha például a beállítást `curl.cainfo` egy `*.crt` fájlra szeretné mutatni, és a "wincache.maxfilesize" `settings.ini` beállítást 512 K-ra szeretné állítani, a fájl a következő szöveget tartalmazza:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. A módosítások újratöltéséhez indítsa újra az alkalmazást.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Útmutató: Bővítmények engedélyezése az alapértelmezett PHP futásidőben

Amint azt az előző szakaszban, a legjobb módja annak, hogy az alapértelmezett PHP verzió, az alapértelmezett konfiguráció, és az engedélyezett kiterjesztések, hogy telepíteni egy script, hogy kéri [phpinfo()]. További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

### <a name="configure-via-ini-settings"></a>Konfigurálás ini beállításokkal

1. `ext` Könyvtár hozzáadása a `d:\home\site` könyvtárhoz.
1. Helyezzen `.dll` a `ext` kiterjesztésfájlokat `php_xdebug.dll`a könyvtárba (például ). Győződjön meg arról, hogy a bővítmények kompatibilisek a PHP alapértelmezett verziójával, és VC9 és nem szálbiztos (nts) kompatibilisek.
1. Alkalmazásbeállítás hozzáadása az alkalmazáshoz `PHP_INI_SCAN_DIR` a kulccsal és az értékkel`d:\home\site\ini`
1. Hozzon `ini` létre `d:\home\site\ini` `extensions.ini`egy fájlt a rendszerben.
1. Adja hozzá a `extensions.ini` konfigurációs beállításokat a fájlhoz `php.ini` a fájlban használt szintaxissal. Ha például engedélyezni szeretné a MongoDB és az XDebug kiterjesztést, a `extensions.ini` fájl a következő szöveget tartalmazza:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. A módosítások betöltéséhez indítsa újra az alkalmazást.

### <a name="configure-via-app-setting"></a>Konfigurálás az alkalmazásbeállítással

1. `bin` Könyvtár hozzáadása a gyökérkönyvtárhoz.
2. Helyezzen `.dll` a `bin` kiterjesztésfájlokat `php_xdebug.dll`a könyvtárba (például ). Győződjön meg arról, hogy a bővítmények kompatibilisek a PHP alapértelmezett verziójával, és VC9 és nem szálbiztos (nts) kompatibilisek.
3. Telepítse az alkalmazást.
4. Tallózással keresse meg az alkalmazást az Azure Portalon, és kattintson a **Beállítások** csoport alatt található **Konfiguráció** elemre.
5. A **Konfiguráció** panelen válassza az **Alkalmazásbeállítások**lehetőséget.
6. Az **Alkalmazás beállításai** csoportban kattintson a **+ Új alkalmazás beállításra,** és hozzon létre egy **PHP_EXTENSIONS** kulcsot. Ennek a kulcsnak az értéke a webhely gyökéréhez viszonyított elérési út lenne: **bin\your-ext-file**.
7. Kattintson a **frissítés** gombra az alján, majd kattintson a **Mentés gombra** az **Alkalmazás beállításai** lap felett.

A Zend kiterjesztéseket **PHP_ZENDEXTENSIONS** kulccsal is támogatja. Több bővítmény engedélyezéséhez adjon meg egy vesszővel `.dll` tagolt fájllistát az alkalmazásbeállítási értékhez.

## <a name="how-to-use-a-custom-php-runtime"></a>Hogyan: Egyéni PHP futásidejű használata

Az alapértelmezett PHP-futásidő helyett az App Service egy PHP-futásidejű t a PHP-parancsfájlok végrehajtásához használható. A megadott futásidejű t egy `php.ini` ön által megadott fájl konfigurálhatja. Egyéni PHP-futásidejű használata az App Service szolgáltatással az alábbi lépéseket követve.

1. Szerezze be a PHP for Windows nem szálbiztos, VC9 vagy VC11 kompatibilis verzióját. A WINDOWS PHP legújabb kiadásai itt [https://windows.php.net/download/]találhatók: . Régebbi kiadások találhatók az archívumban [https://windows.php.net/downloads/releases/archives/]itt: .
2. Módosítsa `php.ini` a futásidejű fájlt. Az App Service figyelmen kívül hagyja a csak rendszerszintű direktívákat. (A csak rendszerszintű irányelvekről a [php.ini irányelvek listája]című témakörben talál.
3. Szükség esetén bővítményeket adhat hozzá a PHP futásórához, és engedélyezheti őket a `php.ini` fájlban.
4. Adjon `bin` hozzá egy könyvtárat a gyökérkönyvtárhoz, és tegye bele a `bin\php`PHP futásidejű könyvtárat (például ).
5. Telepítse az alkalmazást.
6. Tallózzon az alkalmazás az Azure Portalon, és kattintson a **konfiguráció** panelen.
8. A **Konfiguráció** panelen válassza az **Útvonal-hozzárendelések**lehetőséget. 
9. Kattintson **a + Új kezelő gombra,** és adja hozzá `*.php` a Bővítmény mezőhöz, és adja hozzá az elérési utat a `php-cgi.exe` parancsfájlprocesszor végrehajtható **fájljához.** Ha a PHP futásidejét `bin` az alkalmazás gyökerében lévő `D:\home\site\wwwroot\bin\php\php-cgi.exe`könyvtárba helyezi, az elérési út .
10. Alul kattintson a **Frissítés** gombra a kezelőleképezés hozzáadásának befejezéséhez.
11. Kattintson a **Mentés** gombra a módosítások mentéséhez.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Útmutató: A zeneszerzők automatizálásának engedélyezése az Azure-ban

Alapértelmezés szerint az App Service nem csinál semmit composer.json, ha van egy a PHP projekt. Ha [git központi telepítést](deploy-local-git.md)használ, engedélyezheti a `git push` composer.json feldolgozást a Zeneszerző bővítmény engedélyezésével.

> [!NOTE]
> Az [App Service első osztályú zeneszerzői támogatására itt szavazhat!](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)
>

1. A PHP-alkalmazás paneljén az [Azure Portalon](https://portal.azure.com)kattintson az **Eszközbővítmények** > **Extensions**elemre.

    ![Az Azure Portal beállításai panel az Azure-ban a zeneszerző automatizálásának engedélyezéséhez](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kattintson **a Hozzáadás**gombra, majd a **Zeneszerző parancsra.**

    ![A Composer bővítmény hozzáadása a Zeneszerző automatizálásának engedélyezéséhez az Azure-ban](./media/web-sites-php-configure/composer-extension-add.png)
3. A jogi feltételek elfogadásához kattintson az **OK** gombra. A bővítmény hozzáadásához kattintson ismét az **OK** gombra.

    A **Telepített bővítmények** panelen látható a Composer kiterjesztés.
    ![Jogi kifejezések elfogadása a zeneszerzők automatizálásának engedélyezéséhez az Azure-ban](./media/web-sites-php-configure/composer-extension-view.png)
4. Most a helyi számítógép terminálablakában `git add`hajtsa végre a `git commit`, a , és `git push` az alkalmazást. Figyelje meg, hogy a Composer a composer.json nyelven definiált függőségeket telepíti.

    ![Git-üzembe helyezés a Zeneszerző automatizálásával az Azure-ban](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>További lépések

További információt a [PHP Fejlesztői központban talál.](https://azure.microsoft.com/develop/php/)

[ingyenes próbaverzió]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[A php.ini irányelvek listája]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
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
