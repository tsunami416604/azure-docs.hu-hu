---
title: Php-hez készült Azure webes és feldolgozói szerepkörök létrehozása
description: Egy útmutató, amellyel a PHP webes és feldolgozói szerepkörök létrehozása egy Azure cloud service-ben, és a PHP-futtatókörnyezet konfigurálása.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 9d4be08e732127d6da12a9e0367383347f53c796
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2018
ms.locfileid: "34608899"
---
# <a name="create-php-web-and-worker-roles"></a>PHP webes és feldolgozói szerepkörök létrehozása

## <a name="overview"></a>Áttekintés

Ez az útmutató bemutatja, hogyan PHP webes vagy feldolgozói szerepkörök létrehozása a Windows fejlesztői környezetben, a PHP verzióját az elérhető "beépített" verziók közül választhat, a PHP-konfiguráció módosítása, bővítmények engedélyezésével és végül üzembe helyezése az Azure-bA. Emellett bemutatja, hogyan lehet egy webes vagy feldolgozói szerepkör egy PHP-futtatókörnyezet (az egyéni konfigurációs és bővítmények) Ön által használandó konfigurálása.

Az Azure biztosít három számítási modellt alkalmazások futtatásához: az Azure App Service, Azure Virtual Machines és Azure Cloud Servicesben. Mindhárom modell támogatja a PHP. A cloud Services, beleértve a webes és feldolgozói szerepkörök, biztosít *platformszolgáltatás (PaaS)*. Egy felhőszolgáltatásban a webes szerepkör egy külön Internet Information Services (IIS) webkiszolgálót az előtéri webalkalmazásokhoz biztosít. Feldolgozói szerepkör aszinkron, hosszan futó vagy bemenettől feladatok felhasználói interakciótól vagy beviteltől független futtathatja.

Ezek a beállítások kapcsolatos további információkért lásd: [számítási-üzemeltetési lehetőségeinek Azure által biztosított](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>A PHP-hoz készült Azure SDK letöltése

A [php-hez készült Azure SDK-t](php-download-sdk.md) több összetevőből áll. Ez a cikk két fogja használni: az Azure PowerShell és az Azure-emulátorok. Ez a két összetevő a Microsoft Webplatform-telepítőn keresztül is telepíthető. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

## <a name="create-a-cloud-services-project"></a>A Cloud Services-projekt létrehozása

A PHP webes vagy feldolgozói szerepkör létrehozásának első lépése, hogy hozzon létre egy Azure Service-projektet. egy Azure Service-projektet a webes és feldolgozói szerepkörök logikai tárolójaként szolgál, és tartalmazza a projekt [szolgáltatás definíciós (.csdef)] és [szolgáltatás konfigurációs (.cscfg)] fájlokat.

Hozzon létre egy új Azure-szolgáltatási projektet, az Azure PowerShell futtatása rendszergazdaként, és hajtsa végre a következő parancsot:

    PS C:\>New-AzureServiceProject myProject

Ez a parancs létrehoz egy új könyvtárat (`myProject`), amelyhez hozzáadhat webes és feldolgozói szerepköröket.

## <a name="add-php-web-or-worker-roles"></a>PHP webes vagy feldolgozói szerepkörök hozzáadása

PHP webes szerepkör hozzáadása a projekthez, futtassa a következő parancsot a legfelső szintű projektmappából:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Feldolgozói szerepkör használja ezt a parancsot:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> A `roleName` paramétert nem kötelező megadni. Ha ki van hagyva, a szerepkör neve automatikusan létrejön. Az első webes szerepkör létrehozása lesz `WebRole1`, a második lesz `WebRole2`, és így tovább. Az első létrehozott feldolgozói szerepkör lesz `WorkerRole1`, a második lesz `WorkerRole2`, és így tovább.
>
>

## <a name="specify-the-built-in-php-version"></a>Adja meg a beépített PHP-verzió

Amikor egy PHP webes vagy feldolgozói szerepkör ad hozzá egy projektet, a projekt konfigurációs fájlok módosítják, hogy a PHP települjön az egyes webes vagy feldolgozói példányok, az alkalmazás telepítése. A PHP alapértelmezés szerint telepítendő verzióját megtekintéséhez futtassa a következő parancsot:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

A fenti parancs kimenete az alábbiakhoz hasonlóan néz ki. Ebben a példában a `IsDefault` jelző értéke `true` php 5.3.17, amely azt jelzi, hogy az alapértelmezett PHP-verzió telepítve lesz.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

A PHP-futtatókörnyezet verziója és a PHP verzióinak felsorolt megadható. Ha például a PHP-verzió beállítása (nevű szerepkör `roleName`) 5.4.0-s verziójával, használja a következő parancsot:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Elérhető a PHP-verziók a későbbiekben változhatnak.
>
>

## <a name="customize-the-built-in-php-runtime"></a>A beépített PHP-futtatókörnyezet testreszabása

Teljes körű, a PHP-futtatókörnyezet, amely telepíti a rendszer, kövesse a fenti lépéseket, beleértve a módosítása a konfigurációs van `php.ini` beállításait és a bővítmény engedélyezése.

Testre szabhatja a beépített PHP-futtatókörnyezet, kövesse az alábbi lépéseket:

1. Adjon hozzá egy új mappát, `php`, az a `bin` a webes szerepkör könyvtárába. Egy feldolgozói szerepkör esetében adja hozzá a szerepkört gyökérkönyvtárában.
2. Az a `php` mappában hozzon létre egy másik nevű mappát `ext`. Helyezzen minden `.dll` szerepkörbővítmény-fájlok (pl. `php_mongo.dll`), amely engedélyezi az ebben a mappában.
3. Adjon hozzá egy `php.ini` fájlt a `php` mappát. Engedélyezze az egyéni bővítményeket, és állítsa be a bármely PHP-irányelvek a fájlban. Például, ha szeretne kapcsolni `display_errors` a, és engedélyezze a `php_mongo.dll` kiterjesztése, a tartalmát a `php.ini` fájlt a következő lenne:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Azokat a beállításokat, nincs explicit módon állítsa be a `php.ini` fájl lesz automatikusan meg beállítani az alapértelmezett értékekre. Ugyanakkor vegye figyelembe, hogy adhat hozzá egy teljes `php.ini` fájlt.
>
>

## <a name="use-your-own-php-runtime"></a>Használja a saját PHP-futtatókörnyezet

Bizonyos esetekben, jelölje ki a beépített PHP-futtatókörnyezet, és konfigurálja úgy a fent leírtak helyett érdemes lehet, hogy adja meg a saját PHP-futtatókörnyezet. Például használhatja ugyanazt a PHP-futtatókörnyezet egy webes vagy feldolgozói szerepkörben, amely a fejlesztési környezet használata. Ez megkönnyíti az győződjön meg arról, hogy az alkalmazás nem változik meg a viselkedést az éles környezetben.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Használja a saját PHP-futtatókörnyezet a webes szerepkör konfigurálása

Használja a PHP-futtatókörnyezet, amely azt adja meg a webes szerepkör konfigurálásához kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-szolgáltatási projektet, és adja hozzá a PHP webes szerepkör ebben a témakörben korábban leírt módon.
2. Hozzon létre egy `php` mappájában a `bin` mappát, amely a webes szerepkör gyökérkönyvtárában van, és adja hozzá a (minden bináris fájljai, konfigurációs fájlok, almappák stb.) a PHP-futtatókörnyezet az `php` mappát.
3. (NEM KÖTELEZŐ) Ha a PHP-futtatókörnyezet-használja a [SQL Serverhez készült php-hoz készült Microsoft Drivers][sqlsrv drivers], kell telepítenie a webes szerepkör konfigurálása [SQL Server Native Client 2012] [ sql native client] Ha ki van építve. Ehhez adja hozzá a [sqlncli.msi x64 telepítő] , a `bin` mappát a webes szerepkör gyökérkönyvtárban. Az indítási szkript a következő lépésben leírt csendes fog futtassa a telepítőt, amikor a szerepkör van kiépítve. Ha a PHP-futtatókörnyezet nem használja a Microsoft Drivers php-hoz készült SQL Server, a szkript a következő lépésben látható, távolítsa el a következő sort:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Adja meg egy indítási feladat, amely beállítja [Internet Information Services (IIS)] [ iis.net] a PHP-futtatókörnyezet használatára vonatkozó kérések kezelésére `.php` oldalakat. Ehhez nyissa meg a `setup_web.cmd` fájlt (a a `bin` a webes szerepkör gyökérkönyvtár fájl) egy szövegszerkesztőben, és cserélje le annak tartalmát a következő parancsfájlt:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Adja hozzá az alkalmazásfájlokat a webes szerepkör gyökérkönyvtárára. Ez lesz a webkiszolgáló gyökérkönyvtárában.
6. Az alkalmazás közzététele a leírtak szerint a [közzéteheti az alkalmazását](#publish-your-application) szakaszt.

> [!NOTE]
> A `download.ps1` parancsfájl (az a `bin` a webes szerepkör gyökérkönyvtár mappa) a saját PHP-futtatókörnyezet a fenti lépések végrehajtását követően törölhető.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Feldolgozói szerepkör használata a saját PHP-futtatókörnyezet konfigurálása

Feldolgozói szerepkör használata egy PHP-futtatókörnyezet, Ön által megadott konfigurálásához kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-szolgáltatási projektet, és adjon hozzá egy PHP-feldolgozói szerepkör ebben a témakörben korábban leírt módon.
2. Hozzon létre egy `php` mappát a feldolgozói szerepkör gyökérkönyvtárában, és adja hozzá a PHP-futtatókörnyezet (az összes bináris fájljai, konfigurációs fájlok, almappák stb.) a `php` mappát.
3. (NEM KÖTELEZŐ) Ha a PHP-futtatókörnyezet-használja [SQL Serverhez készült php-hoz készült Microsoft Drivers][sqlsrv drivers], a feldolgozói szerepkör telepítéséhez konfigurálni kell [SQL Server Native Client 2012] [ sql native client] Ha ki van építve. Ehhez adja hozzá a [sqlncli.msi x64 telepítő] a feldolgozói szerepkör gyökérkönyvtárára. Az indítási szkript a következő lépésben leírt csendes fog futtassa a telepítőt, amikor a szerepkör van kiépítve. Ha a PHP-futtatókörnyezet nem használja a Microsoft Drivers php-hoz készült SQL Server, a szkript a következő lépésben látható, távolítsa el a következő sort:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Adja meg egy indítási feladat, amely hozzáadja a `php.exe` végrehajtható fájljának a szerepkör ki van építve a feldolgozói szerepkör PATH környezeti változóhoz. Ehhez nyissa meg a `setup_worker.cmd` fájlt (a feldolgozói szerepkör a gyökérkönyvtárba) egy szövegszerkesztőben, és cserélje le annak tartalmát a következő parancsfájlt:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Adja hozzá az alkalmazásfájlokat a feldolgozói szerepkör gyökérkönyvtárára.
6. Az alkalmazás közzététele a leírtak szerint a [közzéteheti az alkalmazását](#publish-your-application) szakaszt.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Futtassa az alkalmazást a számítási és tárolási emulátory systému a

Az Azure-emulátorok adjon meg egy helyi környezetben, ahol tesztelheti az Azure-alkalmazásokat a felhőbe a telepítése előtt. Nincsenek az emulátorok és az Azure-környezet közötti különbségeket. Ez jobb megértéséhez, lásd: [az Azure storage emulator használata a fejlesztési és tesztelési](storage/common/storage-use-emulator.md).

Vegye figyelembe, hogy a PHP telepítése a compute emulator használatával helyben kell rendelkeznie. A compute emulator a helyi PHP-telepítés használatával futtassa az alkalmazást.

Az emulátorok a projekt futtatásához hajtsa végre a következő parancsot a projekt gyökérkönyvtárában:

    PS C:\MyProject> Start-AzureEmulator

Ehhez hasonló kimenetet fog látni:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Az alkalmazás futtatása az emulátorban megnyitása egy webböngészőben, és keresse meg a helyi cím látható a kimenetben láthatja (`http://127.0.0.1:81` a fenti példa kimenetben).

Az emulátorok leállításához hajtsa végre a parancsot:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Az alkalmazás közzététele

Az alkalmazás közzétételéhez először importálnia kell a közzétételi beállítások használatával a [Import-AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) parancsmagot. Az alkalmazás használatával közzétehet, majd a [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) parancsmagot. Bejelentkezéssel kapcsolatos információkért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

## <a name="next-steps"></a>További lépések

További információkért lásd: a [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[szolgáltatás definíciós (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[szolgáltatás konfigurációs (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 telepítő]: http://go.microsoft.com/fwlink/?LinkID=239648
