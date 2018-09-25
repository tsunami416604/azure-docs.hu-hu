---
title: Az Azure App Service Web Apps szolgáltatásban PHP konfigurálása
description: Ismerje meg, hogyan konfigurálhatja az alapértelmezett PHP-telepítés, vagy adjon hozzá egy egyéni PHP-telepítés az Azure App Service Web Apps.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 39c40482017ae0c0dedcfb1b65ff3767c4e45169
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999188"
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Az Azure App Service Web Apps szolgáltatásban PHP konfigurálása

## <a name="introduction"></a>Bevezetés

Ez az útmutató bemutatja, hogyan a beépített PHP-futtatókörnyezet konfigurálása a webalkalmazásokhoz [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), adjon meg egyéni PHP-futtatókörnyezet, és engedélyezze a bővítmények. Az App Service használatához Regisztráljon a [az ingyenes próbaidőszak]. Ez az útmutató az lekéréséhez, először hozzon létre egy PHP-webalkalmazás App Service-ben.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Útmutató: a beépített PHP-verzió módosítása

Alapértelmezés szerint a PHP 5.6-os telepítve és vehető használatba azonnal egy App Service-webalkalmazás létrehozásakor. Tekintse meg a rendelkezésre álló kiadási változat, az alapértelmezett konfigurációban és az engedélyezett bővítmények a legjobb módszer az, hogy üzembe helyezése egy szkript, amely meghívja a [phpinfo()] függvény.

PHP 7.0 és a PHP 7.2-verziók is rendelkezésre állnak rendelkezésre, de alapértelmezés szerint nincs engedélyezve. A PHP-verzió módosításához kövesse az alábbi módszerek egyikét:

### <a name="azure-portal"></a>Azure Portal

1. Tallózással keresse meg a web app szolgáltatásban a [az Azure portal](https://portal.azure.com) , majd kattintson a a **beállítások** gombra.

    ![A webalkalmazás-beállítások][settings-button]
1. Az a **beállítások** panelen válassza ki **Alkalmazásbeállítások** , és válassza ki az új PHP-verzió.

    ![Alkalmazásbeállítások][application-settings]
1. Kattintson a **mentése** gombot a felső részén a **WebApp beállításai** panelen.

    ![Konfigurációs beállítások mentése][save-button]

### <a name="azure-powershell-windows"></a>Az Azure PowerShell (Windows)

1. Nyissa meg az Azure PowerShell, és jelentkezzen be a fiókjába:

        PS C:\> Connect-AzureRmAccount
1. Állítsa be a PHP verzióját a webalkalmazáshoz.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. A PHP-verzió már van beállítva. Ezek a beállítások ellenőrizheti:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>Azure CLI 

Az Azure parancssori felület használatához be kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a számítógépen.

1. Nyissa meg terminált, és jelentkezzen be a fiókjába.

        az login

1. Ellenőrizze, hogy a támogatott futtatókörnyezet megtekintéséhez.

        az webapp list-runtimes | grep php

1. Állítsa be a PHP verzióját a webalkalmazáshoz.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. A PHP-verzió már van beállítva. Ezek a beállítások ellenőrizheti:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Útmutató: a beépített PHP-konfiguráció módosítása

Minden olyan beépített PHP-futtatókörnyezet, az alábbi lépéseket követve módosíthatja a konfigurációs beállításokat. (A php.ini fájl irányelvek kapcsolatos információkért lásd: [A php.ini fájl irányelvek].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>PHP módosítása\_INI\_felhasználó, a PHP\_INI\_PERDIR, a PHP\_INI\_összes konfigurációs beállításai

1. Adjon hozzá egy [. user.ini] fájlt a gyökérkönyvtárban.
1. Adja hozzá a konfigurációs beállításokat a `.user.ini` ugyanazt a szintaxist használja a fájlt egy `php.ini` fájlt. Például, ha szeretne kapcsolja be a `display_errors` beállítása és a beállított `upload_max_filesize` 10 millió beállítást a `.user.ini` fájl tartalmazza egyrészt az ezt a szöveget:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. A webes alkalmazás üzembe helyezése.
1. Indítsa újra a webalkalmazást. (Újraindítás szükség, mert a gyakoriságot, mely a PHP-olvasó `.user.ini` fájlok szabályozzák a `user_ini.cache_ttl` beállítás, amely egy rendszerszintű beállítás, és alapértelmezés szerint 300 másodpercig (5 perc). A webalkalmazás újraindítása a PHP használatával, olvassa el az új beállítások az kényszeríti a `.user.ini` fájl.)

Használata helyett egy `.user.ini` fájlt, használhatja a [ini_set()] függvény a konfigurációs beállítások, amelyek nem rendszerszintű irányelvek megadása a parancsfájlokat.

### <a name="changing-phpinisystem-configuration-settings"></a>A PHP módosítása\_INI\_rendszer konfigurációs beállításai

1. Az Alkalmazásbeállítás hozzáadása a webalkalmazáshoz a kulccsal `PHP_INI_SCAN_DIR` és érték `d:\home\site\ini`
1. Hozzon létre egy `settings.ini` fájlt a Kudu konzol használata (http://&lt;site-name&gt;. scm.azurewebsite.net) az a `d:\home\site\ini` könyvtár.
1. Adja hozzá a konfigurációs beállításokat a `settings.ini` ugyanazt a szintaxist használja a fájlt egy `php.ini` fájlt. Például, ha szeretné, mutasson a `curl.cainfo` beállítást egy `*.crt` fájlt, és állítsa be a "wincache.maxfilesize" 512 KB, a `settings.ini` fájl tartalmazza egyrészt az ezt a szöveget:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Töltse be újra a módosításokat, hogy indítsa újra a webalkalmazást.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Útmutató: az alapértelmezett PHP-futtatókörnyezet engedélyezéséhez

Az előző szakaszban feljegyzett-e a legjobb módszer az alapértelmezett PHP-verzió, az alapértelmezett konfigurációban és az engedélyezett bővítmények telepítéséhez egy parancsprogramot, amely meghívja ezt [phpinfo()]. További kiterjesztések engedélyezése az alábbi lépéseket:

### <a name="configure-via-ini-settings"></a>Keresztül ini-beállítások konfigurálása

1. Adjon hozzá egy `ext` mappában a `d:\home\site` könyvtár.
1. PUT `.dll` kiterjesztésű fájlokat a `ext` könyvtárat (például `php_xdebug.dll`). Győződjön meg arról, hogy a bővítmények a PHP és a rendszer VC9 és a nem szálbiztos (nts) kompatibilis alapértelmezett verziójával kompatibilis.
1. Az Alkalmazásbeállítás hozzáadása a webalkalmazáshoz a kulccsal `PHP_INI_SCAN_DIR` és érték `d:\home\site\ini`
1. Hozzon létre egy `ini` fájlt `d:\home\site\ini` nevű `extensions.ini`.
1. Adja hozzá a konfigurációs beállításokat a `extensions.ini` ugyanazt a szintaxist használja a fájlt egy `php.ini` fájlt. Például, ha szeretne a mongodb-hez és az XDebug bővítmény engedélyezése a `extensions.ini` fájl tartalmazza egyrészt az ezt a szöveget:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Indítsa újra a webalkalmazást, a módosítások betölteni.

### <a name="configure-via-app-setting"></a>Alkalmazásbeállítás konfigurálás

1. Adjon hozzá egy `bin` könyvtárat a gyökérkönyvtárba.
1. PUT `.dll` kiterjesztésű fájlokat a `bin` könyvtárat (például `php_xdebug.dll`). Győződjön meg arról, hogy a bővítmények a PHP és a rendszer VC9 és a nem szálbiztos (nts) kompatibilis alapértelmezett verziójával kompatibilis.
1. A webes alkalmazás üzembe helyezése.
1. Keresse meg a webalkalmazást az Azure Portalon, majd kattintson a a **beállítások** gombra.

    ![A webalkalmazás-beállítások][settings-button]
1. Az a **beállítások** panelen válassza ki **Alkalmazásbeállítások** , és görgessen a **Alkalmazásbeállítások** szakaszban.
1. Az a **Alkalmazásbeállítások** szakaszban, hozzon létre egy **PHP_EXTENSIONS** kulcsot. Ez a kulcs értéke lenne a webhely gyökeréhez viszonyítva elérési útja: **bin\your-ext-fájl**.

    ![Az alkalmazásbeállítások bővítmény engedélyezése][php-extensions]
1. Kattintson a **mentése** gombot a felső részén a **WebApp beállításai** panelen.

    ![Konfigurációs beállítások mentése][save-button]

A Zend bővítmények használatával is támogatottak egy **PHP_ZENDEXTENSIONS** kulcsot. Ahhoz, hogy több bővítményt, vesszővel elválasztott listáját tartalmazza `.dll` fájlok esetében az alkalmazás-beállítás értékét.

## <a name="how-to-use-a-custom-php-runtime"></a>Útmutató: egyéni PHP-futtatókörnyezet használata

Helyett az alapértelmezett PHP-futtatókörnyezet az App Service Web Apps használhatja egy PHP-futtatókörnyezet, a PHP-szkriptek végrehajtása egészíti ki. Az Ön által megadott modul is konfigurálható, hogy egy `php.ini` fájlt, amely azt adja meg. Egyéni PHP-futtatókörnyezet használata a Web Apps, a következő lépéseket.

1. Szerezzen be egy nem szálbiztos, VC9 vagy VC11 PHP for Windows kompatibilis verziója. A PHP for Windows verziók itt található: [ http://windows.php.net/download/ ]. Régebbi kiadásokban az archívum itt található: [ http://windows.php.net/downloads/releases/archives/ ].
1. Módosítsa a `php.ini` a modulhoz tartozó fájl. Web Apps figyelmen kívül hagyja a rendszer szint – csak irányelvek konfigurációs beállításokat. (Rendszer-szint – csak irányelvek kapcsolatos információkért lásd: [A php.ini fájl irányelvek]).
1. Igény szerint adhat bővítményeket a PHP-futtatókörnyezet, és engedélyezze azokat a `php.ini` fájlt.
1. Adjon hozzá egy `bin` a gyökérkönyvtár, és a PHP-futtatókörnyezet, az azt tartalmazó könyvtárba put könyvtárat (például `bin\php`).
1. A webes alkalmazás üzembe helyezése.
1. Keresse meg a webalkalmazást az Azure Portalon, majd kattintson a a **beállítások** gombra.

    ![A webalkalmazás-beállítások][settings-button]
1. Az a **beállítások** panelen válassza ki **Alkalmazásbeállítások** , és görgessen a **kezelőleképezések** szakaszban. Adjon hozzá `*.php` a bővítménynek mezőben, majd adja hozzá az elérési útját a `php-cgi.exe` végrehajtható. Ha helyezi a PHP-futtatókörnyezet a `bin` könyvtárat az alkalmazás gyökérkönyvtárában, az elérési út `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Adja meg kezelőleképezések kezelőt][handler-mappings]
1. Kattintson a **mentése** gombot a felső részén a **WebApp beállításai** panelen.

    ![Konfigurációs beállítások mentése][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Útmutató: az Azure-ban Composer-automatizálás engedélyezése

Alapértelmezés szerint az App Service nem csinál semmi a composer.json, ha rendelkezik ilyennel, a PHP-projekt. Ha [Git üzemelő példánnyal](app-service-deploy-local-git.md), engedélyezheti a composer.json feldolgozása során `git push` Composer bővítményt engedélyezésével.

> [!NOTE]
> Is [első osztályú Composer támogatás az App Service-ben itt vote](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. A php webes alkalmazás paneljének a [az Azure portal](https://portal.azure.com), kattintson a **eszközök** > **bővítmények**.

    ![Composer-automatizálás az Azure-beli engedélyezéséhez az Azure portál beállításai panel](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kattintson a **Hozzáadás**, majd kattintson a **Composer**.

    ![Adja hozzá az Azure-ban Composer-automatizálás engedélyezése Composer bővítményt](./media/web-sites-php-configure/composer-extension-add.png)
3. Kattintson a **OK** jogi feltételek elfogadásához. Kattintson a **OK** , ismét adja hozzá a bővítményt.

    A **bővítményeket telepített** panelen jelenik meg a Composer bővítményt.
    ![Az Azure-ban Composer-automatizálás engedélyezése a jogi feltételek elfogadása](./media/web-sites-php-configure/composer-extension-view.png)
4. Most egy terminálablakban a helyi gépén, hajtsa végre `git add`, `git commit`, és `git push` a webalkalmazáshoz. Figyelje meg, hogy Composer telepíti a composer.json meghatározott függőségeket.

    ![Composer-automatizálás az Azure-ban a Git-telepítés](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>További lépések

További információkért lásd: a [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
>

[az ingyenes próbaidőszak]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[A php.ini fájl irányelvek]: http://www.php.net/manual/en/ini.list.php
[. user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
